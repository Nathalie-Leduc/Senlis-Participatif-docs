# Cahier des charges — Senlis Participatif

> *Version 1.3 — 14 juin 2026 — Document de travail, à faire évoluer au fil du projet*
>
> **Journal des décisions** — 14/06/2026 : adoption d'une **direction artistique joyeuse et immersive** : mascotte cerf animée (« le cerf de Senlis »), sections colorées à identité propre, micro-interactions, widget guide-citoyen flottant. Implémentée progressivement en **trois paliers** intégrés aux lots existants (aucun lot supplémentaire). La charte graphique passe de « la pierre et la rivière » à **« la pierre, la rivière et le cerf »**. Décision motivée par le risque de faible participation (R9 ci-dessous) : un site civique joyeux abaisse la barrière d'entrée.
> — 12/06/2026 : le MVP est organisé en **deux lots**. Lot 1 = socle (propositions de l'administratrice, votes, enquêtes, carte avec couche IRIS, emails transactionnels). Lot 2 = participation (commentaires-arguments, propositions citoyennes, modération, notifications broadcast). Le périmètre est **re-gelé** : toute idée nouvelle rejoint la section « Évolutions ».
> — 12/06/2026 : ajout d'un **Lot 3 « Offre collectivités »** en roadmap post-MVP (développement payant, conformité réglementaire collectivités). Sans impact sur le périmètre du MVP, hormis trois anticipations gratuites (API versionnée, configuration externalisée, exports prévus à la conception).

---

## 1. Présentation du projet

**Senlis Participatif** est une plateforme web citoyenne indépendante permettant aux habitants et commerçants de Senlis (Oise) de découvrir des propositions d'aménagement de leur ville, de voter pour ou contre, et de répondre à des enquêtes destinées à objectiver le débat par des données de terrain.

Le projet est porté à titre personnel, hors de tout cadre institutionnel, avec l'ambition d'en faire un **démonstrateur** : si la plateforme fonctionne et fédère, elle sera proposée à la mairie de Senlis comme outil de concertation.

**Premier cas d'usage** (qui structure tout le MVP) : une proposition de **piétonnisation du centre historique le samedi**, argumentée par des données publiques (INSEE, ville de Senlis) et complétée par une enquête auprès des résidents et commerçants du centre sur leurs pratiques de stationnement.

---

## 2. Besoins et objectifs

### Besoins (les problèmes constatés)

| # | Problème |
|---|----------|
| B1 | Les habitants n'ont pas de canal structuré pour proposer et débattre d'aménagements urbains entre deux élections |
| B2 | Les débats locaux (ex : piétonnisation) reposent sur des impressions, pas sur des données : les statistiques publiques (INSEE) s'arrêtent à l'échelle de la commune entière et ignorent les pratiques réelles (où se gare-t-on vraiment ? quel usage de la voiture le samedi ?) |
| B3 | Les propositions citoyennes existantes (réseaux sociaux, pétitions) sont dispersées, non géolocalisées et non quantifiables |
| B4 | Aucun outil ne permet de visualiser spatialement une proposition (périmètre concerné, zones de report de stationnement) |
| B5 | Les plateformes civiques existantes sont perçues comme froides et administratives, ce qui décourage la participation citoyenne — en particulier des publics moins familiers du numérique |

### Objectifs (les solutions apportées)

| # | Objectif | Répond à |
|---|----------|----------|
| O1 | Publier des propositions argumentées et sourcées, ouvertes au vote (pour / contre / neutre) | B1, B3 |
| O2 | Collecter des données de terrain via des enquêtes ciblées (résidents / commerçants), analysables statistiquement | B2 |
| O3 | Garantir la crédibilité des résultats : un compte = un vote = une réponse par enquête, données pseudonymisées | B2, B3 |
| O4 | Visualiser propositions et périmètres sur une carte interactive de Senlis | B4 |
| O5 | Offrir une expérience joyeuse et engageante grâce à une mascotte guide, des micro-interactions ludiques et un univers visuel coloré, pour encourager la participation de tous les publics | B5 |

---

## 3. Spécifications fonctionnelles

### 3.1 MVP (Minimum Viable Product) — organisé en deux lots

> Règle appliquée : à chaque étape, le produit fonctionne de bout en bout, même incomplet. Le Lot 1 livre un site **utile et démontrable** ; le Lot 2 le rend **pleinement participatif**. Les deux lots font partie du MVP ; le schéma de données couvre les deux dès le départ (on conçoit en une fois, on implémente en deux temps).

#### Lot 1 — Le socle : informer, voter, enquêter

**Visiteur (non connecté)**
- Consulter la liste des propositions publiées et leur détail (argumentaire, sources)
- Visualiser la carte interactive : marqueurs des propositions, périmètre de la zone piétonne, parkings de report, **couche des quartiers IRIS** (découpage infra-communal INSEE permettant d'isoler le centre historique)
- Consulter les résultats agrégés des votes
- S'inscrire / se connecter — l'email est vérifié par jeton avant toute participation (barrière anti-comptes jetables : la crédibilité des résultats en dépend)
- Être accueilli par la **mascotte cerf** qui contextualise chaque page et guide la navigation via un widget flottant

**Citoyen (connecté, email vérifié)**
- Voter POUR / CONTRE / NEUTRE sur une proposition (un seul vote, modifiable)
- Répondre à une enquête ouverte (une seule réponse par enquête)
- Gérer son compte : modifier pseudo / email / mot de passe, « mot de passe oublié », supprimer son compte (droit à l'effacement RGPD)

**Administratrice**
- Créer / éditer / publier / clore une proposition (point géolocalisé + périmètre GeoJSON)
- Créer une enquête : questions typées (choix unique, choix multiple, nombre, oui/non, texte libre), audience ciblée (tous / résidents / commerçants)
- Ouvrir / clore une enquête, consulter les résultats agrégés (comptages par option, moyennes)

**Emails transactionnels** : vérification d'inscription, réinitialisation de mot de passe — indispensables au socle, à ne pas confondre avec les notifications du Lot 2.

**Expérience joyeuse — intégrée progressivement au Lot 1** (paliers 1 et 2) :
- Palier 1 (Sprint 0-1) : fondations visuelles — sections colorées à identité propre, séparateurs ondulés SVG, micro-interactions CSS (cartes, boutons, jauges animées au scroll), mascotte en SVG statique avec bulles de texte contextuelles, ton éditorial chaleureux
- Palier 2 (derniers sprints du Lot 1) : mascotte animée (clignement des yeux, oreilles, queue) en CSS, 3-4 poses contextuelles (accueil, explication, félicitation, encouragement), widget guide-citoyen flottant avec messages et actions rapides, confettis au vote, compteur animé sur les enquêtes

#### Lot 2 — La participation : débattre, proposer

**Citoyen**
- Commenter une proposition en affichant sa **position** (argument POUR / CONTRE / NEUTRE) : le débat s'affiche en colonnes structurées, pas en fil de discussion
- Soumettre sa propre proposition → file de modération avant toute publication
- Gérer ses préférences de notification — lien de désinscription dans chaque email (obligation légale)

**Administratrice**
- Modérer **a priori** commentaires et propositions citoyennes : approuver / rejeter avec motif communiqué à l'auteur
- Notifications email broadcast : nouvelle proposition publiée, clôture d'enquête (prérequis : SPF/DKIM configurés)

**Expérience joyeuse — Palier 3** (intégré au Lot 2) : widget cerf enrichi (guidage interactif), animation de la mascotte au scroll, confettis de publication des résultats d'enquête, transitions de page élaborées

#### Transverse (exigences non fonctionnelles, valables pour les deux lots)
- Responsive (mobile-first) — l'usage attendu est majoritairement sur smartphone
- Accessible (cible RGAA / WCAG 2.1 AA) : sémantique HTML, navigation clavier, contrastes, skip link
- **Animations dégradables** : `prefers-reduced-motion` respecté — aucune animation, micro-interaction ou comportement de la mascotte n'est indispensable à la compréhension ou à l'utilisation du site. Un utilisateur qui désactive les animations peut voter et répondre aux enquêtes sans perte fonctionnelle
- Écoresponsable : images WebP, lazy loading, fonds de carte OSM, mascotte en SVG inline (zéro requête supplémentaire), pas de dépendances superflues
- Sécurisé : HTTPS, Argon2, JWT, validation Zod systématique, Helmet, rate limiting (dont auth), CORS restreint
- RGPD : consentement, mentions légales, pseudonymisation des réponses d'enquête, droit à l'effacement
- API REST documentée (Swagger), consommable telle quelle par une future application mobile

### 3.2 Lot 3 — Offre collectivités (post-MVP, développement payant)

> **Statut** : hors MVP. Phase de roadmap **conditionnée au succès des Lots 1 et 2** (plateforme stable + participation réelle démontrée). Le Lot 3 transforme le démonstrateur en **produit commercialisable auprès des collectivités**, en commençant par la mairie de Senlis.

**Mises en conformité réglementaires spécifiques aux collectivités**
- **Accessibilité RGAA 4** : ce qui est une bonne pratique au MVP devient une obligation légale formelle — audit d'accessibilité, déclaration d'accessibilité publiée, schéma pluriannuel
- **RGPD renforcé** : contrat de sous-traitance (article 28 RGPD) avec la collectivité responsable de traitement, coopération avec son DPO, registre des traitements, analyse d'impact (AIPD) si requise
- **Hébergement souverain** : migration de Railway (États-Unis) vers un hébergeur français ou européen (ex : OVHcloud, Scaleway) — les données de participation citoyenne ne doivent pas relever d'un droit extra-européen
- **Sécurité des téléservices** : journalisation des accès, sauvegardes contractualisées, plan de reprise d'activité simplifié
- **Open data** : export des résultats anonymisés en formats ouverts (les communes de plus de 3 500 habitants ont des obligations d'ouverture des données — Senlis en fait partie)
- **Réversibilité** : clause d'export complet des données dans un format ouvert en fin de contrat (exigence systématique des acheteurs publics)

**Adaptations techniques**
- Configuration par commune (nom, logo, **mascotte personnalisable**, emprise de la carte, couches IRIS, comptes agents) : instance dédiée par collectivité dans un premier temps, multi-tenant si plusieurs communes clientes
- Rôles affinés côté collectivité : élu, agent, modérateur (au lieu du seul rôle ADMIN)
- Engagements de service : disponibilité, monitoring, support

**Cadre d'achat et modèle économique**
- Depuis le 1er avril 2026, une collectivité peut acheter de gré à gré (sans publicité ni mise en concurrence) un marché de fournitures ou services **inférieur à 60 000 € HT** ; le dispositif « achat innovant » porte ce plafond à **100 000 € HT** pour les solutions nouvelles ou sensiblement améliorées — une plateforme de concertation appuyée sur des enquêtes structurées peut légitimement y prétendre (sources : décret n° 2025-1386 du 29/12/2025 ; art. R2122-9-1 du code de la commande publique)
- Modèle envisagé : mise en service + abonnement annuel (hébergement, maintenance, support) + formation des agents

**Anticipations gratuites dès le Lot 1** (décisions qui ne coûtent rien aujourd'hui et économisent une refonte demain) :
- API versionnée (`/api/v1/...`) : un client mobile ou une mairie pourra coexister avec une future v2
- Configuration « commune » externalisée (variables d'environnement / table de config, jamais en dur dans le code)
- Endpoints d'export anonymisé prévus dans la conception (même si livrés plus tard)
- Mascotte en composant SVG paramétrable (couleurs, nom) : adaptable à chaque commune sans refonte

### 3.3 Évolutions potentielles (hors MVP et hors Lot 3)

- Export CSV / PDF des résultats d'enquête (pour le dossier à présenter à la mairie)
- Tableau de bord statistique dynamique croisant réponses d'enquête et quartiers IRIS
- Module actualités / événements
- Budget participatif
- Application mobile native (React Native) consommant la même API

---

## 4. Architecture et justification

```
[ Client React (SPA) ] ←—— JSON / HTTPS ——→ [ API REST Express ] ←——→ [ PostgreSQL ]
        Vite + Sass              CORS              Prisma ORM
        react-leaflet            JWT
        mascotte SVG inline
```

**Architecture découplée client / API / BDD**, en monorepo (`client/` + `api/`), retenue pour trois raisons :

1. **Mobile-ready par construction** : l'API ne renvoie que du JSON ; le site web n'est qu'un client parmi d'autres. Une application mobile ou tablette future consommera les mêmes endpoints sans modification du back (objectif explicite du projet).
2. **Séparation des responsabilités** : le front affiche, l'API décide. Toute règle métier (unicité du vote, validation) est garantie côté serveur et en base, jamais déléguée au navigateur.
3. **Capitalisation** : architecture identique au projet Cinés-Délices (réalisé et déployé) — outillage, CI, Docker et réflexes de sécurité directement réutilisables, ce qui réduit le risque projet en développement solo.

> **Note sur la mascotte** : le cerf est un pur composant front (SVG inline + CSS animations). Il ne touche ni l'API, ni la base de données, ni le schéma Prisma. Zéro impact sur l'architecture back.

---

## 5. Technologies et justification

| Couche | Technologie | Justification |
|--------|-------------|---------------|
| Front | React 19 + Vite 6 | SPA réactive, écosystème mature, stack maîtrisée ; Vite pour des builds rapides et légers |
| Routage front | React Router 7 | Standard de fait pour les SPA React |
| Styles | Sass (modules) | Variables, mixins de breakpoints mobile-first, scoping par composant ; les animations CSS de la mascotte sont centralisées dans un fichier dédié |
| Cartographie | Leaflet + react-leaflet + OpenStreetMap | Gratuit, open source, léger (~42 Ko vs ~200 Ko pour Google Maps), pas de clé bancaire, pas de fuite de données vers un tiers — argument de souveraineté pour une collectivité |
| Back | Node.js 22+ / Express 5 | API REST minimaliste et éprouvée, stack maîtrisée |
| ORM | Prisma 7 | Schéma déclaratif typé, migrations versionnées, protection native contre l'injection SQL |
| BDD | PostgreSQL | Relationnel robuste, contraintes d'unicité au cœur du modèle (un vote / personne), type JSON natif pour le GeoJSON |
| Validation | Zod | Validation et nettoyage de toute entrée avant tout traitement |
| Auth | JWT + Argon2 | Authentification stateless (compatible mobile) ; Argon2 = état de l'art du hachage de mots de passe |
| Sécurité HTTP | Helmet, express-rate-limit, CORS | En-têtes durcis, anti force brute (auth incluse), origine restreinte |
| Documentation API | Swagger (OpenAPI) | Contrat d'interface lisible, testable, indispensable pour le client mobile futur |
| Tests | Vitest + Testing Library (front), tests d'intégration API (back) | Continuité avec l'outillage Cinés-Délices |
| Conteneurisation | Docker + docker-compose | Environnement de dev reproductible (API + BDD) |
| Hébergement | Railway | Déploiement continu maîtrisé sur le projet précédent |

---

## 6. Cible du projet

**Public visé** : habitants et commerçants de Senlis — 15 238 habitants, 6 865 ménages, 371 artisans-commerçants-chefs d'entreprise (INSEE, RP 2022).

**Contraintes induites** :
- **Population senior significative** (25,6 % de 60 ans ou plus) → accessibilité non négociable : tailles de police confortables, contrastes élevés, parcours simples, formulaires tolérants
- **Tous niveaux de littératie numérique** → vocabulaire courant, aide contextuelle sur les questions d'enquête, aucune étape superflue ; **la mascotte guide-citoyen** offre un compagnon visuel rassurant qui explique chaque section en langage simple
- **Usage majoritairement mobile** attendu (consultation d'une carte, réponse à une enquête relayée par QR code ou réseaux sociaux) → conception mobile-first
- **Confiance** = condition de la participation → transparence sur l'usage des données (page dédiée), pseudonymat public (seul le pseudo est visible)
- **Engagement** = condition de la masse critique → design joyeux et récompenses visuelles (confettis au vote, compteur animé, mascotte encourageante) pour abaisser la barrière psychologique de la participation civique

---

## 7. Navigateurs compatibles

Deux dernières versions majeures de : Chrome, Firefox, Safari, Edge — sur desktop, tablette et mobile (iOS Safari, Chrome Android). Pas de support Internet Explorer.

Note : les animations CSS de la mascotte utilisent exclusivement des propriétés largement supportées (`transform`, `opacity`, `@keyframes`). Aucun polyfill requis.

---

## 8. Arborescence de l'application (routes front)

```
/                          Accueil : hero avec mascotte + carte interactive + propositions à la une
/propositions              Liste des propositions (filtres : statut, tri par votes)
/propositions/:slug        Détail : argumentaire, carte du périmètre, votes, CTA enquête
/enquetes                  Liste des enquêtes (ouvertes / closes)
/enquetes/:slug            Répondre au questionnaire (citoyen connecté)
/enquetes/:slug/resultats  Résultats agrégés (si publiés)
/inscription               Création de compte
/connexion                 Connexion
/mon-compte                Profil : édition, suppression de compte (RGPD), préférences de notification (Lot 2)
/proposer                  Soumettre une proposition citoyenne (Lot 2)
/admin                     Tableau de bord (ADMIN uniquement)
/admin/propositions        CRUD propositions
/admin/enquetes            CRUD enquêtes + constructeur de questionnaire
/admin/enquetes/:id/stats  Résultats détaillés
/admin/moderation          File de modération : commentaires + propositions citoyennes (Lot 2)
/mentions-legales          Mentions légales
/confidentialite           Politique de confidentialité (RGPD)
/*                         Page 404 (avec mascotte « perdue » pour un ton bienveillant)
```

---

## 9. Routes API (endpoints REST)

> Préfixe commun : `/api/v1` — versionnée dès le Lot 1 : décision gratuite aujourd'hui qui permettra demain à un client mobile ou une collectivité (Lot 3) de coexister avec une future v2 — Auth : 🔓 public · 🔐 connecté · 👑 admin

**Auth**
| Méthode | Route | Accès | Description |
|---|---|---|---|
| POST | /auth/register | 🔓 | Inscription (validation Zod, hash Argon2) |
| POST | /auth/login | 🔓 | Connexion → JWT (rate limité) |
| GET | /auth/me | 🔐 | Profil courant |
| PATCH | /auth/me | 🔐 | Modifier pseudo / email |
| PUT | /auth/me/password | 🔐 | Modifier le mot de passe |
| DELETE | /auth/me | 🔐 | Suppression de compte (effacement RGPD : votes supprimés, réponses anonymisées) |
| POST | /auth/verify-email | 🔓 | Confirmation d'email par jeton à usage unique |
| POST | /auth/forgot-password | 🔓 | Demande de réinitialisation (rate limité) |
| POST | /auth/reset-password | 🔓 | Nouveau mot de passe via jeton |
| PATCH | /auth/me/notifications | 🔐 | Préférences de notification (Lot 2) |

**Propositions**
| Méthode | Route | Accès | Description |
|---|---|---|---|
| GET | /proposals | 🔓 | Liste paginée des propositions publiées |
| GET | /proposals/:slug | 🔓 | Détail + agrégat des votes |
| POST | /proposals | 👑 | Créer une proposition |
| POST | /proposals/submit | 🔐 | Soumettre une proposition citoyenne → statut PENDING_REVIEW (Lot 2) |
| PATCH | /proposals/:id/moderate | 👑 | Approuver / rejeter avec motif (Lot 2) |
| PATCH | /proposals/:id | 👑 | Éditer / changer le statut |
| DELETE | /proposals/:id | 👑 | Supprimer |
| PUT | /proposals/:id/vote | 🔐 | Voter ou changer son vote (upsert) |
| DELETE | /proposals/:id/vote | 🔐 | Retirer son vote |

**Commentaires (Lot 2)**
| Méthode | Route | Accès | Description |
|---|---|---|---|
| GET | /proposals/:id/comments | 🔓 | Arguments approuvés, groupés par position (pour / contre / neutre) |
| POST | /proposals/:id/comments | 🔐 | Poster un argument → statut PENDING (modération a priori) |
| DELETE | /comments/:id | 🔐 | Supprimer son propre argument |
| PATCH | /comments/:id/moderate | 👑 | Approuver / rejeter |

**Enquêtes**
| Méthode | Route | Accès | Description |
|---|---|---|---|
| GET | /surveys | 🔓 | Liste des enquêtes |
| GET | /surveys/:slug | 🔓 | Détail : questions + options |
| POST | /surveys | 👑 | Créer une enquête (questions imbriquées) |
| PATCH | /surveys/:id | 👑 | Éditer / ouvrir / clore |
| POST | /surveys/:id/responses | 🔐 | Soumettre sa réponse complète (transaction, unicité garantie) |
| GET | /surveys/:id/results | 👑* | Résultats agrégés (*🔓 si publiés) |

**Transverse**
| Méthode | Route | Accès | Description |
|---|---|---|---|
| GET | /health | 🔓 | Health check (monitoring) |

---

## 10. User stories

**Visiteur**
- En tant que visiteur, je peux consulter les propositions et leur argumentaire afin de me faire une opinion informée.
- En tant que visiteur, je peux visualiser sur une carte le périmètre d'une proposition et les parkings de report afin de comprendre son impact concret.
- En tant que visiteur, je peux créer un compte afin de participer aux votes et enquêtes.
- En tant que visiteur, je suis accueilli par une mascotte animée qui me guide et m'explique le fonctionnement de la plateforme, afin de me sentir à l'aise même si je ne suis pas familier avec le numérique.

**Citoyen**
- En tant que citoyen, je peux voter pour, contre ou neutre sur une proposition afin d'exprimer ma position.
- En tant que citoyen, je peux modifier mon vote afin de changer d'avis après lecture des arguments.
- En tant que citoyen, je peux répondre une seule fois à une enquête afin que les résultats restent représentatifs.
- En tant que citoyen résident du centre, je peux indiquer mes pratiques réelles de stationnement afin que le débat repose sur des faits.
- En tant que citoyen, je peux supprimer mon compte afin d'exercer mon droit à l'effacement, sans que cela fausse les statistiques déjà agrégées.
- En tant que citoyen, je reçois un retour visuel joyeux (confettis, message de la mascotte) après un vote ou une réponse d'enquête, afin de me sentir valorisé dans ma participation.
- En tant que citoyen, je peux ouvrir le widget guide-citoyen pour poser des questions courantes (protection des données, fonctionnement du vote) afin de participer en confiance.
- En tant que citoyen, je peux publier un argument pour ou contre une proposition afin d'enrichir le débat (Lot 2).
- En tant que citoyen, je peux soumettre ma propre proposition afin de porter un sujet qui me tient à cœur (Lot 2).
- En tant que citoyen, je peux me désinscrire des notifications en un clic afin de garder le contrôle de ma boîte mail (Lot 2).

**Administrateur**
- En tant qu'admin, je peux rédiger et publier une proposition géolocalisée afin de lancer une concertation.
- En tant qu'admin, je peux construire un questionnaire sur mesure (types de questions variés, audience ciblée) sans modification du code afin de lancer de nouvelles enquêtes rapidement.
- En tant qu'admin, je peux consulter les résultats agrégés afin de produire un argumentaire chiffré à présenter à la mairie.
- En tant qu'admin, je peux modérer a priori commentaires et propositions citoyennes afin qu'aucun contenu inapproprié ne soit jamais publié (Lot 2).

---

## 11. Analyse des risques

| Risque | Probabilité | Impact | Mesures de prévention |
|--------|:-----------:|:------:|----------------------|
| Dérive de périmètre (« encore une petite fonctionnalité ») | Élevée | Élevé | Arbitrage tracé du 12/06/2026 (MVP élargi puis re-gelé en deux lots) ; toute idée nouvelle va dans « Évolutions » |
| Charge de modération continue (Lot 2) | Élevée | Moyen | Modération a priori (rien ne se publie sans validation) ; volume limité à l'échelle d'une ville ; possibilité de suspendre temporairement les commentaires |
| Délivrabilité des emails (dossier spam) | Moyenne | Moyen | Lot 1 limité au transactionnel ; SPF/DKIM configurés avant tout broadcast (Lot 2) ; lien de désinscription systématique |
| Cycle de décision public long / Lot 3 incertain | Élevée | Faible sur le MVP | Le Lot 3 est hors MVP : les Lots 1 et 2 ont leur valeur propre même sans client public ; anticipations limitées à ce qui est gratuit |
| Complexité du moteur d'enquêtes générique (requêtes statistiques) | Moyenne | Moyen | Schéma validé en amont ; agrégats via `groupBy` Prisma ; tests sur jeu d'essai |
| Courbe d'apprentissage Leaflet / GeoJSON | Moyenne | Moyen | Spike technique en début de projet (proto carte isolé avant intégration) |
| Faille de sécurité (données personnelles en jeu) | Faible | Critique | Validation Zod systématique, Argon2, rate limiting auth, Helmet, revue sécurité avant mise en ligne, tests de sécurité API (réutilisés de Cinés-Délices) |
| Non-conformité RGPD | Moyenne | Élevé | Minimisation des données, pseudonymisation des réponses, pages légales, registre des traitements simplifié |
| Faible participation (échantillon non significatif) | Élevée | Élevé | Hors-code : QR codes commerçants, relais associations / réseaux sociaux locaux ; afficher le nombre de répondants avec les résultats (honnêteté statistique) ; **design joyeux avec mascotte guide pour abaisser la barrière d'entrée** |
| Développement solo (maladie, indisponibilité) | Moyenne | Moyen | Documentation continue, commits atomiques, CI, ce cahier des charges |
| Biais de l'enquête (formulation orientée des questions) | Moyenne | Élevé | Questions neutres relues par un tiers ; options exhaustives incluant « Autre » |
| Surcharge visuelle de la mascotte / animations | Faible | Moyen | `prefers-reduced-motion` respecté (les animations se désactivent) ; mascotte non bloquante (widget fermable, bulles escamotables) ; audit accessibilité incluant les animations |

---

## 12. Rôles

Projet réalisé en **solo** par Nathalie Leduc, qui endosse successivement les casquettes suivantes (séparées volontairement pour structurer le travail) :

| Casquette | Responsabilités |
|-----------|----------------|
| Product Owner | Périmètre, priorisation MVP, rédaction des user stories |
| Concepteur | Cahier des charges, ERD, dictionnaire de données, diagrammes, wireframes |
| Designer | Direction artistique « la pierre, la rivière et le cerf », mascotte SVG, charte graphique, maquettes |
| Développeuse back | API Express, schéma Prisma, sécurité, tests API |
| Développeuse front | Composants React, intégration Leaflet, mascotte animée, accessibilité, tests unitaires |
| DevOps | Docker, CI GitHub Actions, déploiement Railway |
| Assistance | IA (Claude) en appui : revue de conception, pair programming, relecture — les décisions et le code final restent sous responsabilité humaine |

---

## Annexes — état des documents de conception

| Document | Statut |
|----------|--------|
| Diagramme ERD | ✅ Réalisé et mis à jour v1.1 (`schema.prisma` : + Comment, AuthToken, statuts de modération) |
| Dictionnaire de données | ✅ Réalisé (dérivé du schéma Prisma) |
| Diagramme de séquence (fonctionnalité complexe) | ✅ Réalisé — soumission d'une réponse d'enquête (transaction, unicité) |
| Use cases | ✅ Réalisé (5 cas détaillés) |
| Diagramme d'architecture | ✅ Réalisé |
| Diagramme d'activité | ✅ Réalisé — cycle de vie d'une proposition |
| Merise MCD/MLD/MPD | ✅ Réalisé |
| Charte graphique | ✅ Réalisé et mis à jour v1.1 — « la pierre, la rivière et le cerf » + mascotte + micro-interactions |
| Wireframes | ✅ Réalisé et mis à jour — intégrant la mascotte et les sections colorées |
| Maquettes | ✅ Réalisé et mis à jour — version joyeuse non interactive |
| Diagrammes UML (use cases, packages) | ✅ Réalisé |
| Sitemap | ✅ Réalisé |
| Kanban, sprints, backlog | ✅ Réalisé et mis à jour — issues mascotte intégrées aux sprints existants |
