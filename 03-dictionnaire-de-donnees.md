# Dictionnaire de données — Senlis Participatif

> Dérivé de `schema.prisma` v1.2 — état au 23/09/2026 (10 entités, 12 énumérations). Convention : tous les identifiants sont des UUID **générés par Prisma côté application** (`@default(uuid())`) et stockés en `TEXT` ; toutes les dates sont des `DateTime` UTC (`TIMESTAMP(3)` en base).

## USER — compte citoyen ou administratrice

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| email | String | UNIQUE, NOT NULL | Adresse de connexion — jamais affichée publiquement |
| passwordHash | String | NOT NULL | Empreinte Argon2 du mot de passe (jamais le mot de passe en clair) |
| pseudo | String | UNIQUE, NOT NULL | Identité publique (votes, commentaires) — minimisation RGPD |
| role | Role | défaut `CITIZEN` | Niveau de droits (promotion/rétrogradation par un admin : S5-19) |
| situation | Situation | NULL | Résidence déclarée (auto-déclaratif, sans justificatif). Nullable pour les comptes antérieurs au champ ; **exigée par Zod** à toute nouvelle inscription |
| quartier | Quartier | NULL | Quartier IRIS de résidence — renseigné **uniquement** si `situation = AUTRE_QUARTIER`, remis à `NULL` sinon |
| travailleQuartier | Quartier | NULL | Quartier où la personne travaille (axe indépendant de la résidence). `NULL` = « ne travaille pas à Senlis » **ou** « jamais demandé » — ambiguïté assumée |
| travailType | TravailType | NULL | `COMMERCANT` (dirige/gère) ou `SALARIE` — significatif seulement si `travailleQuartier` est renseigné |
| emailVerified | Boolean | défaut `false` | Email confirmé par jeton — condition pour voter et répondre |
| notifyNewProposal | Boolean | défaut `true` | Préférence : être notifié des nouvelles propositions (Lot 2) |
| notifySurveyClosed | Boolean | défaut `true` | Préférence : être notifié des clôtures d'enquête (Lot 2) |
| createdAt / updatedAt | DateTime | auto | Traçabilité |

## AUTH_TOKEN — jeton email à usage unique

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| tokenHash | String | UNIQUE, NOT NULL | Empreinte du jeton (le jeton en clair n'est jamais stocké) |
| type | TokenType | NOT NULL | `VERIFY_EMAIL`, `RESET_PASSWORD` ou `TWO_FACTOR_LOGIN` |
| expiresAt | DateTime | NOT NULL | Péremption : 60 min pour les liens email (`TOKEN_TTL_MINUTES`), 10 min pour le code 2FA (`TWO_FACTOR_TTL_MINUTES`) |
| usedAt | DateTime | NULL | Renseigné à la consommation → jeton à usage unique |
| userId | UUID | FK → USER, CASCADE | Propriétaire du jeton |
| createdAt | DateTime | auto | Traçabilité |

## PROPOSAL — proposition d'aménagement

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| slug | String | UNIQUE, NOT NULL | Identifiant d'URL lisible (`pietonnisation-centre-historique`) |
| title | String | NOT NULL | Titre |
| summary | String | NOT NULL | Accroche (~200 caractères) pour les cartes de liste |
| content | Text | NOT NULL | Argumentaire complet en Markdown (chiffres, sources) |
| status | ProposalStatus | défaut `DRAFT` | Cycle de vie (cf. diagramme d'activité) |
| lat / lng | Float | NULL | Point d'ancrage du marqueur sur la carte |
| geoJson | Json | NULL | Périmètre dessiné (polygone GeoJSON, format natif Leaflet) |
| imagePath | String | NULL | Chemin **relatif** de l'image (`/uploads/proposals/<uuid>.webp`), jamais l'URL complète — recompressée en WebP 1200 px par Sharp (métadonnées EXIF/GPS supprimées au passage) |
| authorId | UUID | FK → USER, NULL, SET NULL | Auteur — anonymisé si le compte est supprimé |
| moderationNote | String | NULL | Motif communiqué à l'auteur en cas de rejet (Lot 2) |
| createdAt | DateTime | auto | Création |
| publishedAt | DateTime | NULL | Date de publication |
| closesAt | DateTime | NULL | Clôture programmée des votes |

## VOTE — expression d'un citoyen sur une proposition

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| value | VoteValue | NOT NULL | `POUR`, `CONTRE` ou `NEUTRE` |
| userId | UUID | FK → USER, CASCADE | Votant |
| proposalId | UUID | FK → PROPOSAL, CASCADE | Proposition visée |
| createdAt / updatedAt | DateTime | auto | Un changement d'avis met à jour, ne duplique pas |
| — | — | **UNIQUE(userId, proposalId)** | Un citoyen = un vote par proposition (garanti par PostgreSQL) |

## COMMENT — argument structuré sous une proposition (Lot 2)

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| content | Text | NOT NULL | Texte de l'argument |
| stance | Stance | NOT NULL | Position défendue : `POUR` / `CONTRE` / `NEUTRE` (affichage en colonnes) |
| status | CommentStatus | défaut `PENDING` | Modération a priori : visible seulement si `APPROVED` |
| authorId | UUID | FK → USER, NULL, SET NULL | Auteur — anonymisé si compte supprimé |
| proposalId | UUID | FK → PROPOSAL, CASCADE | Proposition commentée |
| createdAt | DateTime | auto | Traçabilité |

## SURVEY — enquête citoyenne

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| slug | String | UNIQUE, NOT NULL | Identifiant d'URL |
| title | String | NOT NULL | Titre (« Stationnement et déplacements dans le centre historique ») |
| description | Text | NOT NULL | Contexte affiché en tête du questionnaire |
| audience | Audience | défaut `TOUS` | Cible : `TOUS` / `RESIDENTS` / `COMMERCANTS` |
| status | SurveyStatus | défaut `DRAFT` | `DRAFT` → `OPEN` → `CLOSED` |
| resultsPublished | Boolean | défaut `false` | Distinct du statut : l'admin décide quand les résultats deviennent publics (avant : visibles par l'admin seul) |
| opensAt / closesAt | DateTime | NULL | Fenêtre de collecte |
| createdAt | DateTime | auto | Traçabilité |

## QUESTION — question d'une enquête

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| label | String | NOT NULL | Intitulé (« Combien de voitures compte votre foyer ? ») |
| helpText | String | NULL | Précision affichée sous la question |
| type | QuestionType | NOT NULL | Type de saisie (cf. énumérations) |
| required | Boolean | défaut `true` | Réponse obligatoire ou non |
| order | Int | NOT NULL | Position dans le questionnaire |
| uiHint | String | NULL | Indicateur de rendu, seulement pour `TEXTE_LIBRE` — ex. `VILLE_FR` : suggestions de communes via l'API officielle `geo.api.gouv.fr` (stockage et agrégation inchangés) |
| syncsToProfile | String | NULL | `situation` \| `quartier` \| `travailleQuartier` \| `travailType` — la réponse met aussi à jour ce champ du profil (après le COMMIT, jamais à sa place). Réservé à `CHOIX_UNIQUE` (Zod) ; `OUI_NON` sert au seul préremplissage |
| showIfOptionId | UUID | FK → QUESTION_OPTION, NULL, SET NULL | Branchement : la question ne s'affiche que si cette option d'une question **antérieure** a été choisie (une seule condition par question) |
| surveyId | UUID | FK → SURVEY, CASCADE | Enquête parente |
| — | — | **UNIQUE(surveyId, order)** | Pas deux questions au même rang |

## QUESTION_OPTION — option de réponse d'une question fermée

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| label | String | NOT NULL | Libellé (« Box ou garage privé », « Voirie payante »…) |
| order | Int | NOT NULL | Position d'affichage |
| syncValue | String | NULL | Valeur d'enum exacte écrite dans le profil quand cette option est choisie (ex. `CENTRE_RESIDENT`) — pont explicite entre un libellé français et une valeur technique |
| questionId | UUID | FK → QUESTION, CASCADE | Question parente |
| — | — | **UNIQUE(questionId, order)** | Pas deux options au même rang |

## SURVEY_RESPONSE — « bulletin » d'un citoyen pour une enquête

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| submittedAt | DateTime | auto | Horodatage de soumission |
| surveyId | UUID | FK → SURVEY, CASCADE | Enquête concernée |
| userId | UUID | FK → USER, NULL, SET NULL | Répondant — **pseudonymisation** : le lien sert uniquement à empêcher la double réponse ; il est rompu (NULL) si le compte est supprimé, les statistiques survivent |
| — | — | **UNIQUE(userId, surveyId)** | Un citoyen = une réponse par enquête |

## ANSWER — réponse à une question donnée

| Attribut | Type | Contraintes | Description |
|---|---|---|---|
| id | UUID | PK | Identifiant unique |
| responseId | UUID | FK → SURVEY_RESPONSE, CASCADE | Bulletin parent |
| questionId | UUID | FK → QUESTION, CASCADE | Question visée |
| optionId | UUID | FK → QUESTION_OPTION, NULL, CASCADE | Option choisie (questions fermées) — N lignes pour un choix multiple |
| valueText | String | NULL | Réponse libre (type `TEXTE_LIBRE`) |
| valueNumber | Float | NULL | Réponse numérique (type `NOMBRE`) |
| — | — | **UNIQUE(responseId, questionId, optionId)** | Pas de double coche |

> Règle d'intégrité applicative (validée par Zod, en complément des contraintes SQL) : **une seule** des trois valeurs (`optionId`, `valueText`, `valueNumber`) est renseignée, en cohérence avec le `type` de la question.

## Énumérations

| Énumération | Valeurs | Usage |
|---|---|---|
| Role | CITIZEN, ADMIN | Droits du compte |
| ProposalStatus | DRAFT, PENDING_REVIEW, PUBLISHED, REJECTED, CLOSED, ARCHIVED | Cycle de vie d'une proposition |
| VoteValue | POUR, CONTRE, NEUTRE | Sens du vote |
| Stance | POUR, CONTRE, NEUTRE | Position d'un argument (Lot 2) |
| CommentStatus | PENDING, APPROVED, REJECTED | Modération a priori (Lot 2) |
| SurveyStatus | DRAFT, OPEN, CLOSED | Cycle de vie d'une enquête |
| Audience | TOUS, RESIDENTS, COMMERCANTS | Ciblage d'une enquête |
| QuestionType | CHOIX_UNIQUE, CHOIX_MULTIPLE, NOMBRE, OUI_NON, TEXTE_LIBRE | Type de saisie d'une question |
| TokenType | VERIFY_EMAIL, RESET_PASSWORD, TWO_FACTOR_LOGIN | Nature d'un jeton email (lien ou code 2FA admin) |
| Situation | CENTRE_RESIDENT, AUTRE_QUARTIER, HORS_SENLIS | Résidence déclarée (`CENTRE_COMMERCANT` retiré le 22/09/2026 : redondant avec l'axe travail) |
| Quartier | CENTRE_HISTORIQUE, BRICHEBAY, BON_SECOURS, VAL_AUNETTE_GATELIERE, ZONE_INDUSTRIELLE, VILLEVERT, JARDINIERS | Quartiers IRIS INSEE (`CENTRE_HISTORIQUE` utilisé uniquement pour le lieu de travail) |
| TravailType | COMMERCANT, SALARIE | Rôle dans l'activité exercée à Senlis |

## Données personnelles — classement RGPD (rappel)

| Donnée | Catégorie | Visibilité publique | Commentaire |
|---|---|---|---|
| email | Identifiant direct | Jamais | Sert à la connexion et aux emails transactionnels |
| pseudo | Identifiant indirect | Oui | Choisi librement — conseiller de ne pas utiliser son nom réel |
| situation / quartier / travailleQuartier / travailType | Profil déclaratif | Jamais individuellement | Utilisés seulement en agrégat (segmentation) — attention aux petits effectifs (voir audit 21) |
| votes | Opinion sur un projet local | Jamais individuellement | Seuls les totaux sont publiés |
| réponses d'enquête | Données déclaratives | Jamais individuellement | Pseudonymisées ; les `TEXTE_LIBRE` peuvent contenir des données identifiantes |
| passwordHash, tokenHash | Secrets | Jamais | Empreintes (Argon2id / SHA-256), jamais le secret en clair |
