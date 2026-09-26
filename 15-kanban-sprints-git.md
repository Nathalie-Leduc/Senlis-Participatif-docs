# Plan de développement — Kanban, sprints & workflow Git

> Organisation pour un développement **solo, en lots, livrable à chaque étape**. Outil : GitHub Projects (kanban) adossé au dépôt `senlis-participatif`. Devise du plan : *déployer dès le sprint 0, livrer à chaque sprint*.
>
> **Mise à jour v1.1** : les issues de la direction artistique joyeuse (mascotte, micro-interactions, sections colorées) sont **intégrées aux sprints existants en trois paliers**, sans lot supplémentaire. Le palier 1 (fondations visuelles) démarre au Sprint 0 ; le palier 2 (mascotte animée, widget) se déploie en fin de Lot 1 ; le palier 3 (expérience immersive) arrive avec le Lot 2.
>
> **Mise à jour v1.2 (23/09/2026)** : alignement sur la réalité du projet — branche d'intégration `dev` (et non `develop`), hébergement Clever Cloud (Railway abandonné), Sprint 5 découpé en **5 / 5bis / 5 audit / 5ter**, règle de livraison en zip, et Definition of Done enrichie (sécurité, RGPD, RGAA) après l'audit du document 21. Le détail issue par issue reste dans `16-kanban-issues-complet.md`.

---

## 1. Le kanban (GitHub Projects)

**Colonnes** :

| Colonne | Règle d'entrée |
|---|---|
| 📋 Backlog | Toute idée validée par le cahier des charges (sinon → section Évolutions) |
| 🎯 Sprint en cours | Choisi au début du sprint — jamais plus de ce qu'un sprint peut tenir |
| 🔨 En cours | **1 seule carte à la fois** (limite WIP : le multitâche solo est un mythe) |
| 👀 Revue | PR ouverte, CI verte, auto-relecture à tête reposée (le lendemain si possible) |
| ✅ Fait | Mergé sur `dev` + critères de la Definition of Done cochés |

**Definition of Done** (à coller dans le README du projet) : code mergé · tests écrits **et réellement exécutés** (fichier nommé `*.test.js`, visible dans la sortie de Vitest) et verts · migration appliquée aussi à la base de test (`npm run migrate:test`) · validation Zod en place si entrée utilisateur · route admin protégée côté API · aucune nouvelle donnée personnelle, nouveau service tiers ou nouvelle clé `localStorage` sans mise à jour de `client/src/constants/legal.js` (le test de concordance le rappelle) et du registre `22` · accessible au clavier, erreurs reliées aux champs · responsive vérifié mobile · `prefers-reduced-motion` respecté si animation · documenté si nécessaire (README, dépôt docs).

**Labels GitHub** :

| Famille | Labels | Usage |
|---|---|---|
| Lot | `lot:1` `lot:2` `lot:3` | Rattachement au périmètre |
| Type | `type:feature` `type:bug` `type:test` `type:docs` `type:infra` `type:design` | Nature du travail |
| Zone | `zone:api` `zone:client` `zone:bdd` `zone:ci` `zone:comm` | Où ça se passe |
| Priorité | `prio:haute` `prio:normale` | Le critique d'abord |
| État | `bloqué` `spike` | `spike` = exploration technique en temps boxé |
| Joy | `joy:palier-1` `joy:palier-2` `joy:palier-3` | Palier de la direction artistique joyeuse |

---

## 2. Workflow Git

**Branches** :

```
main      ── production (protégée : merge uniquement par PR, CI verte obligatoire)
  └─ dev     ── intégration (l'état "prochain déploiement")
       ├─ feat/auth-register        ── une fonctionnalité = une branche
       ├─ feat/proposal-vote
       ├─ feat/mascot-svg-base      ── composant mascotte SVG
       ├─ feat/joy-layer-sections   ── sections colorées + séparateurs ondulés
       ├─ fix/vote-bar-contrast
       └─ chore/ci-postgres-service
```

**Règles** :
- Nommage : `feat/…`, `fix/…`, `test/…`, `docs/…`, `chore/…` — en kebab-case, court et parlant
- **Commits conventionnels** : `feat(client): composant Mascot SVG inline 4 tailles` · `feat(client): joy layer — sections colorées + séparateurs ondulés` · `fix(client): contraste de la jauge` — le préfixe rend l'historique lisible et prépare un changelog automatique
- Commits **atomiques** : un commit = un changement cohérent qui compile (ton assurance-vie de dev solo : `git bisect` et les retours arrière deviennent triviaux)
- **PR même en solo** : la PR `feat/... → dev` force la CI (⚠️ vérifier « base: dev » : GitHub mémorise la dernière base utilisée) et l'auto-relecture du diff — 80 % des étourderies s'attrapent là
- Release : merge `dev → main` en fin de sprint + tag `v0.1.0`, `v0.2.0`… → déploiement automatique (Clever Cloud, à partir du Sprint 5ter)
- **Livraison des fichiers** : un zip `senlis-<ID>-<résumé>.zip` par issue, reprenant l'arborescence exacte du dépôt avec seulement les fichiers touchés (jamais de copier-coller manuel fichier par fichier)

---

## 3. Les sprints

> Durée indicative : 1 à 2 semaines selon ta disponibilité. Chaque sprint se termine par un **incrément déployé** — le site fonctionne toujours. Les issues `joy:palier-*` sont intégrées aux sprints existants.

### 🏗️ Sprint 0 — Fondations *(1 sem)* `lot:1` + `joy:palier-1`
- [x] Monorepo `client/` + `api/` initialisé, ESLint/Prettier, `.env.example`
- [x] `docker-compose.yml` (api + PostgreSQL) — `docker compose up` et ça tourne
- [x] `schema.prisma` v1.1 intégré + première migration + `GET /api/v1/health`
- [x] CI GitHub Actions minimale (lint + tests vides qui passent)
- [x] ~~Déploiement Railway du squelette~~ — abandonné : premier déploiement réel chez Clever Cloud au Sprint 5ter
- [x] `spike` : proto Leaflet isolé (afficher Senlis, un marqueur, un polygone GeoJSON, la couche IRIS) — on dérisque la carte *avant* de s'engager
- [x] READMEs initiaux (racine + api + client)
- [x] 🦌 **Palier 1** : `_variables.scss` avec palette étendue Joy Layer + `_joy-layer.scss` (sections colorées, séparateurs ondulés SVG, stat pills) + composant `Mascot.jsx` SVG statique 4 tailles

### 🔐 Sprint 1 — Auth & emails transactionnels *(2 sem)* `lot:1` + `joy:palier-1`
- [x] `POST /auth/register` : Zod + Argon2 + jeton `VERIFY_EMAIL`
- [x] Envoi d'email via **Mailtrap** (`services/email.js`, config 100 % par variables d'env)
- [x] `POST /auth/verify-email` + `login` (JWT) + middleware `auth` + `rate limit` sur les routes auth
- [x] Mot de passe oublié (jeton `RESET_PASSWORD`, même mécanique)
- [x] Front : pages inscription / connexion / vérification, contexte `useAuth`
- [x] Tests d'intégration : register → verify → login → `me` (le parcours complet)
- [x] 🦌 **Palier 1** : page d'accueil hero avec mascotte statique + bulles de texte, ton éditorial chaleureux sur toutes les pages auth (messages d'erreur bienveillants, textes d'aide)

### 🗳️ Sprint 2 — Propositions & votes *(2 sem)* `lot:1`
- [x] CRUD admin des propositions (+ statuts) · liste et détail publics
- [x] `PUT /proposals/:id/vote` en **upsert** + agrégats par camp
- [x] Front : liste, détail, jauge de vote (la maquette devient code), boutons accessibles
- [x] Tests : unicité du vote (409 simulé), changement d'avis, agrégats

### 🗺️ Sprint 3 — La carte *(1-2 sem)* `lot:1`
- [x] Intégration react-leaflet : marqueurs des propositions, périmètre GeoJSON
- [x] Couche IRIS (GeoJSON statique INSEE/IGN) + parkings de report
- [x] Admin : saisie lat/lng + dessin/import du périmètre
- [x] Lazy loading de la carte (elle ne se charge que si visible — éco-conception)

### 📊 Sprint 4 — Moteur d'enquêtes *(2 sem)* `lot:1`
- [x] Admin : constructeur de questionnaire (questions typées + options + audience)
- [x] `POST /surveys/:id/responses` : validation Zod par type + **transaction** + 409
- [x] Front : parcours une-question-par-écran avec progression
- [x] Agrégats (`groupBy` Prisma) + page résultats
- [x] Tests : la transaction tout-ou-rien, la double réponse, les agrégats sur jeu d'essai

### 🚀 Sprint 5 — RGPD, sécurité, accessibilité *(2 sem)* `lot:1` + `joy:palier-2` — ✅ livré
- [x] Suppression de compte (cascade votes / SetNull réponses) + pages légales + consentement
- [x] Mot de passe renforcé (CNIL) + 2FA email pour les admins
- [x] Widget d'accessibilité, audit clavier, audit Lighthouse
- [x] Bascule Mailtrap → Brevo préparée (`validateEnv`), seed de production, release `v1.0.0`
- [x] 🦌 **Palier 2** : mascotte animée, `MascotWidget`, confettis, toasts, `useScrollReveal`, jauges animées, audit `prefers-reduced-motion`

### 🔍 Sprint 5bis — Revue du cahier des charges : ciblage et enquêtes *(2-3 sem)* `lot:1`
- [x] Profil déclaré : résidence (situation + quartier IRIS) et travail (quartier + rôle)
- [x] Publication des résultats décidée par l'admin + vue détaillée
- [x] Branchement conditionnel de questions (moteur + constructeur)
- [x] Enquête stationnement reconstruite, ville avec suggestions (`geo.api.gouv.fr`), synchro réponse → profil
- [x] Gestion simple des comptes admin, appareil de confiance 2FA
- [ ] Résultats segmentés + export : volet propositions et seuil de confidentialité restants (S5-21)

### 🛡️ Sprint 5 audit — avant mise en ligne *(1,5 sem)* `lot:1`
- [ ] Les 8 issues S5A-01 → S5A-08 issues de l'audit `21-audit-securite-rgpd-accessibilite.md` : contrôle d'accès, erreurs API, polices auto-hébergées, pages légales exactes, droits RGPD, durcissement auth, RGAA, préparation prod

### 🌍 Sprint 5ter — Mise en ligne réelle *(1 sem)* `lot:1`
- [ ] Domaine OVH + Clever Cloud (API Node.js, PostgreSQL managé, site statique avec fallback SPA)
- [ ] Sauvegardes quotidiennes, TLS/HSTS/CSP, chiffrement au repos
- [ ] **Brevo en production** : domaine authentifié par **DKIM** (+ DMARC recommandé ; SPF inutile hors IP dédiée), suivi d'ouverture désactivé (CNIL)
- [ ] Communication de lancement : QR codes commerçants, réseaux locaux, élus 🚀

### 💬 Sprint 6 — Commentaires & modération *(2 sem)* `lot:2`
- [ ] CRUD commentaires avec `stance` + statut `PENDING`
- [ ] File de modération admin (approuver / rejeter + motif)
- [ ] Front : débat en colonnes pour/contre · Tests modération

### 📣 Sprint 7 — Propositions citoyennes & notifications *(2 sem)* `lot:2` + `joy:palier-3`
- [ ] `POST /proposals/submit` → `PENDING_REVIEW` + formulaire `/proposer`
- [ ] Notifications broadcast Brevo (nouvelle proposition, clôture d'enquête) + préférences **désactivées par défaut** (RGPD art. 25) + **lien de désinscription** dans chaque email
- [ ] 🦌 **Palier 3** : widget cerf enrichi (messages contextuels selon la page visitée), confettis à la publication des résultats d'enquête, transitions de page élaborées, mascotte « perdue » sur la page 404
- [ ] Release `v2.0.0` → **MVP complet en ligne** 🎉

---

## 4. Chapitres transverses

### READMEs
- **Racine** : pitch (3 lignes + capture), badges CI, stack, démarrage rapide (`docker compose up` + `npm run dev`), arborescence, lien vers le dépôt de docs
- **api/README** : variables d'env documentées une à une, scripts (`migrate`, `seed`, `test`), lien Swagger
- **client/README** : scripts, structure des composants (dont mascotte et joy layer), conventions Sass, check-list accessibilité (incluant `prefers-reduced-motion`)
- Règle : *le README se met à jour dans la PR qui change le comportement*, jamais « plus tard »

### Tests
- **API (prioritaire)** : tests d'intégration Vitest + Supertest sur une BDD de test dédiée (`.env.test`, migrée par `npm run migrate:test`) — cibles n°1 : les invariants métier (unicité du vote, transaction d'enquête, droits admin — y compris après rétrogradation —, effacement RGPD)
- ⚠️ Vitest ne lit que les fichiers `*.test.js` / `*.spec.js` : un fichier `users.tests.js` est **ignoré silencieusement** (cas réel, corrigé par S5A-02) — le test `tests/test-files-naming.test.js` échoue désormais si un fichier de `tests/` est mal nommé
- **Client** : Vitest + Testing Library sur les composants à logique (VoteBar, SurveyForm, **MascotWidget**) — tester le *comportement*, pas le pixel
- **Animations** : vérifier que `prefers-reduced-motion: reduce` neutralise bien toutes les animations (test d'accessibilité automatisé)
- Seuil pragmatique : tout invariant du cahier des charges a son test ; pas de course au % de couverture

### CI/CD (GitHub Actions)
```yaml
# .github/workflows/ci.yml — l'idée générale
on: [push, pull_request]
jobs:
  api:
    services:
      postgres: { image: postgres:16, env: {…}, ports: ["5432:5432"] }
    steps: install → prisma migrate deploy → lint → vitest
  client:
    steps: install → lint → vitest → vite build
```
- CI verte = condition de merge (protection de branche sur `main` et `dev`) ; la CI écoute `main` et `dev`
- CD (Sprint 5ter) : Clever Cloud déploie automatiquement `main` — le merge *est* la mise en production

### Seed (`prisma/seed.js`)
- 1 admin + 3 citoyens de test (mots de passe en clair **uniquement** dans le seed de dev)
- La proposition « Piétonnisation du centre historique le samedi » avec son vrai périmètre GeoJSON
- L'enquête stationnement complète — une seule enquête à branchements (résidence, travail, puis blocs résidents / actifs à Senlis / visiteurs), dans `seed-prod.js` : le seed **est** ton questionnaire validé
- `seed-prod.js` (production) ne crée **que** le compte admin réel (`ADMIN_EMAIL` / `ADMIN_PASSWORD`), la proposition et l'enquête — aucune donnée de démonstration
- Une poignée de votes et réponses factices pour voir les agrégats vivre en dev (et les jauges s'animer !)

### Emails : Mailtrap (dev) → Brevo (prod)
- **Mailtrap** = boîte aux lettres de test : tout email « envoyé » y est capturé, rien ne part jamais vers de vrais destinataires — on teste le rendu, les liens, le spam-score, sans risque
- **Brevo** (français 🇫🇷, RGPD-friendly, ~300 emails/jour en gratuit) pour la production : SMTP fourni, **DKIM** à configurer sur le domaine (+ DMARC recommandé), suivi d'ouverture désactivé — obligatoire *avant* tout broadcast Lot 2 (délivrabilité)
- Le code ne change **jamais** : `services/email.js` lit `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` — la bascule dev → prod est une affaire de `.env`

### Déploiement (Clever Cloud — Sprint 5ter)
- Hébergeur français (Paris), certifié ISO 27001, hors Cloud Act : API Node.js déployée par `git push` (sans Docker), PostgreSQL managé avec sauvegardes quotidiennes, client en site statique avec redirection SPA vers `index.html`
- Domaine `senlis-participatif.fr` chez OVH : `senlis-participatif.fr` → client, `api.senlis-participatif.fr` → API, certificats Let's Encrypt automatiques
- Variables d'env de prod ≠ dev (secrets JWT, SMTP Brevo, `CLIENT_URL`) — jamais de secret dans Git ; `validateEnv.js` refuse de démarrer si la config est incohérente
- Sauvegarde BDD avant chaque migration en prod (`pg_dump` — un réflexe, pas une option), puis `npx prisma migrate deploy`
- Docker reste l'outil de **développement** (PostgreSQL local) : le fichier `docker-compose.yml` n'est pas utilisé en production
