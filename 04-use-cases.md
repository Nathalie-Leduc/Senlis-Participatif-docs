# Use cases — Senlis Participatif

> Les use cases étoffent les user stories du cahier des charges : acteurs, préconditions, scénario nominal, alternatives et exceptions. Sept cas couvrent les mécaniques clés du système (UC-06 et UC-07 ajoutés le 23/09/2026 : double authentification admin, publication et segmentation des résultats).

---

## UC-01 — S'inscrire et vérifier son email (Lot 1)

| | |
|---|---|
| **Acteur principal** | Visiteur |
| **Acteur secondaire** | Système d'envoi d'emails |
| **Préconditions** | Aucune |
| **Déclencheur** | Le visiteur clique sur « Créer un compte » |
| **Postconditions** | Compte créé, email vérifié, citoyen apte à participer |

**Scénario nominal**
1. Le visiteur saisit pseudo, email, mot de passe (12 caractères, 4 familles — CNIL) et sa confirmation, puis sa **situation** (centre historique / autre quartier → lequel / hors Senlis) et, s'il travaille à Senlis, le quartier et son rôle (dirigeant·e ou salarié·e)
2. Le système valide les données (Zod : format email, force du mot de passe, cohérence situation/quartier et travail)
3. Le système crée le compte (`emailVerified = false`) avec le mot de passe haché en Argon2
4. Le système génère un jeton `VERIFY_EMAIL`, en stocke l'empreinte (hash) et envoie le lien par email
5. Le visiteur clique sur le lien dans l'heure
6. Le système vérifie le jeton (empreinte connue, non expiré, non utilisé), marque `emailVerified = true` et consomme le jeton (`usedAt`)
7. Le citoyen est invité à se connecter

**Alternatives / exceptions**
- 2a. Email ou pseudo déjà pris → `409 EMAIL_TAKEN` / `PSEUDO_TAKEN` avec message explicite, retour au formulaire — y compris si deux inscriptions identiques arrivent au même instant (la contrainte d'unicité de la base tranche)
- 5a. Jeton expiré → proposition de renvoyer un email de vérification (rate limité)
- 5b. Jeton déjà consommé → message « lien déjà utilisé », redirection connexion
- \* Tentatives répétées d'inscription depuis une même IP → rate limiting (anti-spam de comptes)

---

## UC-02 — Voter sur une proposition (Lot 1)

| | |
|---|---|
| **Acteur principal** | Citoyen (connecté, email vérifié) |
| **Préconditions** | Proposition au statut `PUBLISHED`, date `closesAt` non dépassée |
| **Déclencheur** | Clic sur POUR, CONTRE ou NEUTRE |
| **Postconditions** | Exactement un vote enregistré pour ce citoyen sur cette proposition |

**Scénario nominal**
1. Le citoyen consulte une proposition et clique sur un des trois boutons de vote
2. Le système authentifie la requête (JWT) et vérifie `emailVerified`
3. Le système valide la valeur (`POUR`/`CONTRE`/`NEUTRE`) et le statut de la proposition
4. Le système enregistre le vote en **upsert** : création s'il n'existe pas, mise à jour sinon (changement d'avis)
5. Le système renvoie le nouvel agrégat (totaux par camp) ; l'interface se met à jour

**Alternatives / exceptions**
- 2a. Non connecté → invitation à se connecter (le vote n'est pas perdu : rejoué après connexion)
- 2b. Email non vérifié → `403 EMAIL_NOT_VERIFIED`, message expliquant pourquoi (intégrité des résultats) + lien de renvoi
- 3a. Proposition `CLOSED` → 403, votes clos
- 4a. Deux votes simultanés du même citoyen (double clic, deux onglets) → la contrainte `UNIQUE(userId, proposalId)` ne laisse passer qu'une ligne ; l'upsert absorbe le conflit

---

## UC-03 — Répondre à une enquête (Lot 1) ⭐ *fonctionnalité la plus complexe*

| | |
|---|---|
| **Acteur principal** | Citoyen (connecté, email vérifié) |
| **Préconditions** | Enquête au statut `OPEN`, citoyen n'ayant pas encore répondu |
| **Déclencheur** | Soumission du questionnaire complété |
| **Postconditions** | Un bulletin (`SurveyResponse`) et toutes ses réponses (`Answer`) enregistrés **atomiquement** |

**Scénario nominal**
1. Le citoyen ouvre l'enquête ; le système renvoie questions et options dans l'ordre. Les questions **conditionnelles** ne s'affichent que si l'option déclencheuse a été choisie ; celles qui correspondent à un champ déjà connu du profil (`syncsToProfile`) s'affichent préremplies, modifiables
2. Le citoyen complète le questionnaire et soumet
3. Le système authentifie (JWT + `emailVerified`)
4. Le système valide le payload (Zod) : toutes les questions `required` couvertes, cohérence type/valeur (une option pour un choix unique, un nombre pour `NOMBRE`…), options appartenant bien à leurs questions
5. Le système vérifie que l'enquête est `OPEN` (et dans sa fenêtre `opensAt`/`closesAt`)
6. Le système ouvre une **transaction** : insertion du `SurveyResponse` puis de toutes les `Answer`
7. La transaction est validée (COMMIT) ; les réponses liées au profil mettent à jour le compte (hors transaction) ; réponse `201 Created`
8. L'interface remercie et affiche, le cas échéant, le nombre de participants

**Alternatives / exceptions**
- 4a. Payload invalide → `400` avec le détail par question, rien n'est écrit
- 5a. Enquête close entre l'ouverture du formulaire et la soumission → `403` avec message clair
- 6a. Le citoyen a déjà répondu (contrainte `UNIQUE(userId, surveyId)`) → la transaction échoue entièrement, `409 Conflict` — aucune réponse partielle ne peut exister
- 6b. Erreur en cours d'insertion des `Answer` → ROLLBACK total : c'est tout le bulletin ou rien (intégrité statistique)

---

## UC-04 — Soumettre une proposition citoyenne (Lot 2)

| | |
|---|---|
| **Acteur principal** | Citoyen (connecté, email vérifié) |
| **Acteur secondaire** | Administratrice (modération) |
| **Préconditions** | — |
| **Déclencheur** | Le citoyen complète le formulaire « Proposer » |
| **Postconditions** | Proposition en file de modération, jamais visible publiquement avant validation |

**Scénario nominal**
1. Le citoyen rédige titre, résumé, argumentaire, et localise sa proposition (point et/ou périmètre sur la carte)
2. Le système valide (Zod) et crée la proposition au statut `PENDING_REVIEW`
3. L'administratrice consulte la file de modération
4. Elle approuve : statut `PUBLISHED`, la proposition devient visible et votable
5. (Lot 2 notifications) Les citoyens abonnés sont notifiés par email

**Alternatives / exceptions**
- 4a. Elle rejette avec un motif (`moderationNote`) : statut `REJECTED`, le motif est visible par l'auteur seul
- 4b. L'auteur corrige et soumet à nouveau → retour à l'étape 2
- \* L'auteur supprime son compte après publication → la proposition survit, anonymisée (`authorId = NULL`)

---

## UC-05 — Modérer un commentaire (Lot 2)

| | |
|---|---|
| **Acteur principal** | Administratrice |
| **Préconditions** | Des commentaires au statut `PENDING` existent |
| **Déclencheur** | Consultation de la file de modération |
| **Postconditions** | Chaque commentaire traité est `APPROVED` (visible) ou `REJECTED` (jamais affiché) |

**Scénario nominal**
1. L'administratrice ouvre `/admin/moderation`
2. Le système liste les commentaires `PENDING` (plus anciens d'abord), avec leur proposition et leur position (pour/contre/neutre)
3. Elle approuve un commentaire → `APPROVED`, il apparaît dans la colonne correspondant à sa position
4. Elle répète jusqu'à vider la file

**Alternatives / exceptions**
- 3a. Contenu inapproprié → `REJECTED` : le commentaire n'est **jamais** apparu publiquement (modération a priori)
- \* Afflux inhabituel de commentaires → possibilité de suspendre temporairement les commentaires sur une proposition (mesure du registre des risques)

---

## UC-06 — Se connecter en tant qu'administratrice (double authentification) (Lot 1)

| | |
|---|---|
| **Acteur principal** | Administratrice |
| **Acteur secondaire** | Système d'envoi d'emails |
| **Préconditions** | Compte au rôle `ADMIN`, email vérifié |
| **Déclencheur** | Saisie de l'email et du mot de passe sur `/connexion` |
| **Postconditions** | Session admin ouverte ; navigateur reconnu pendant 1 h |

**Scénario nominal**
1. L'administratrice saisit email et mot de passe
2. Le système vérifie le mot de passe (Argon2) ; le compte étant `ADMIN`, il **ne délivre pas** de session mais un jeton de défi (10 min, sans le rôle) et envoie un code à 6 chiffres par email (empreinte SHA-256 stockée, `TWO_FACTOR_LOGIN`)
3. L'administratrice recopie le code
4. Le système vérifie le code (bon compte, non expiré, non utilisé) et le consomme
5. Le système délivre la session (JWT) **et** un jeton « appareil de confiance » valable 1 h

**Alternatives / exceptions**
- 2a. Ce navigateur présente un jeton « appareil de confiance » valide pour ce compte → le code est sauté, **le mot de passe reste exigé**
- 4a. Code faux ou déjà utilisé → `400 INVALID_CODE` (nombre d'essais limité par code : S5A-06)
- 4b. Code ou jeton de défi expiré → retour à l'étape 1
- \* Tentatives répétées → rate limiting (10 / 15 min / IP)

---

## UC-07 — Publier et analyser les résultats d'une enquête (Lot 1)

| | |
|---|---|
| **Acteur principal** | Administratrice |
| **Préconditions** | Enquête existante avec au moins une réponse |
| **Déclencheur** | Ouverture de `/admin/enquetes/:id/stats` |
| **Postconditions** | Résultats analysés ; éventuellement rendus publics |

**Scénario nominal**
1. L'administratrice ouvre la vue détaillée : résultats agrégés, réponses libres, écart entre l'audience visée et la situation déclarée des répondants
2. Elle choisit une question à réponse unique pour **segmenter** (ex. « Où résidez-vous ? ») : chaque question est alors présentée avec la comparaison par segment juste en dessous
3. Elle imprime ou exporte en PDF (impression navigateur, navigation et boutons masqués)
4. Elle coche « Publier les résultats » : `/enquetes/:slug/resultats` devient accessible à tous

**Alternatives / exceptions**
- 2a. Question à choix multiple ou texte choisie pour segmenter → `400 INVALID_SEGMENT_QUESTION` (un répondant pourrait appartenir à plusieurs segments)
- 2b. Segment de moins de 5 répondants → masqué à l'écran et à l'export (risque de ré-identification — S5-21)
- 4a. Résultats non publiés → un visiteur reçoit un refus, même si l'enquête est close
