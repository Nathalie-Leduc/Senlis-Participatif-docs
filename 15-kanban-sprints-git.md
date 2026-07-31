# Plan de développement — Kanban, sprints & workflow Git

> Organisation pour un développement **solo, en lots, livrable à chaque étape**. Outil : GitHub Projects (kanban) adossé au dépôt `senlis-participatif`. Devise du plan : *déployer dès le sprint 0, livrer à chaque sprint*.
>
> **Mise à jour v1.1** : les issues de la direction artistique joyeuse (mascotte, micro-interactions, sections colorées) sont **intégrées aux sprints existants en trois paliers**, sans lot supplémentaire. Le palier 1 (fondations visuelles) démarre au Sprint 0 ; le palier 2 (mascotte animée, widget) se déploie en fin de Lot 1 ; le palier 3 (expérience immersive) arrive avec le Lot 2.

---

## 1. Le kanban (GitHub Projects)

**Colonnes** :

| Colonne | Règle d'entrée |
|---|---|
| 📋 Backlog | Toute idée validée par le cahier des charges (sinon → section Évolutions) |
| 🎯 Sprint en cours | Choisi au début du sprint — jamais plus de ce qu'un sprint peut tenir |
| 🔨 En cours | **1 seule carte à la fois** (limite WIP : le multitâche solo est un mythe) |
| 👀 Revue | PR ouverte, CI verte, auto-relecture à tête reposée (le lendemain si possible) |
| ✅ Fait | Mergé sur `develop` + critères de la Definition of Done cochés |

**Definition of Done** (à coller dans le README du projet) : code mergé · tests écrits et verts · validation Zod en place si entrée utilisateur · accessible au clavier · responsive vérifié mobile · `prefers-reduced-motion` respecté si animation · documenté si nécessaire (Swagger / README).

**Labels GitHub** :

| Famille | Labels | Usage |
|---|---|---|
| Lot | `lot:1` `lot:2` `lot:3` | Rattachement au périmètre |
| Type | `type:feature` `type:bug` `type:test` `type:docs` `type:infra` `type:design` | Nature du travail |
| Zone | `zone:api` `zone:client` `zone:bdd` `zone:ci` | Où ça se passe |
| Priorité | `prio:haute` `prio:normale` | Le critique d'abord |
| État | `bloqué` `spike` | `spike` = exploration technique en temps boxé |
| Joy | `joy:palier-1` `joy:palier-2` `joy:palier-3` | Palier de la direction artistique joyeuse |

---

## 2. Workflow Git

**Branches** :

```
main      ── production (protégée : merge uniquement par PR, CI verte obligatoire)
  └─ develop ── intégration (l'état "prochain déploiement")
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
- **PR même en solo** : la PR `feat/... → develop` force la CI et l'auto-relecture du diff — 80 % des étourderies s'attrapent là
- Release : merge `develop → main` en fin de sprint + tag `v0.1.0`, `v0.2.0`… → déploiement automatique

---

## 3. Les sprints

> Durée indicative : 1 à 2 semaines selon ta disponibilité. Chaque sprint se termine par un **incrément déployé** — le site fonctionne toujours. Les issues `joy:palier-*` sont intégrées aux sprints existants.

### 🏗️ Sprint 0 — Fondations *(1 sem)* `lot:1` + `joy:palier-1`
- [ ] Monorepo `client/` + `api/` initialisé, ESLint/Prettier, `.env.example`
- [ ] `docker-compose.yml` (api + PostgreSQL) — `docker compose up` et ça tourne
- [ ] `schema.prisma` v1.1 intégré + première migration + `GET /api/v1/health`
- [ ] CI GitHub Actions minimale (lint + tests vides qui passent)
- [ ] **Déploiement Railway du squelette** — l'URL existe dès le jour 5 🚀
- [ ] `spike` : proto Leaflet isolé (afficher Senlis, un marqueur, un polygone GeoJSON, la couche IRIS) — on dérisque la carte *avant* de s'engager
- [ ] READMEs initiaux (racine + api + client)
- [ ] 🦌 **Palier 1** : `_variables.scss` avec palette étendue Joy Layer + `_joy-layer.scss` (sections colorées, séparateurs ondulés SVG, stat pills) + composant `Mascot.jsx` SVG statique 4 tailles

### 🔐 Sprint 1 — Auth & emails transactionnels *(2 sem)* `lot:1` + `joy:palier-1`
- [ ] `POST /auth/register` : Zod + Argon2 + jeton `VERIFY_EMAIL`
- [ ] Envoi d'email via **Mailtrap** (`services/email.js`, config 100 % par variables d'env)
- [ ] `POST /auth/verify-email` + `login` (JWT) + middleware `auth` + `rate limit` sur les routes auth
- [ ] Mot de passe oublié (jeton `RESET_PASSWORD`, même mécanique)
- [ ] Front : pages inscription / connexion / vérification, contexte `useAuth`
- [ ] Tests d'intégration : register → verify → login → `me` (le parcours complet)
- [ ] 🦌 **Palier 1** : page d'accueil hero avec mascotte statique + bulles de texte, ton éditorial chaleureux sur toutes les pages auth (messages d'erreur bienveillants, textes d'aide)

### 🗳️ Sprint 2 — Propositions & votes *(2 sem)* `lot:1`
- [ ] CRUD admin des propositions (+ statuts) · liste et détail publics
- [ ] `PUT /proposals/:id/vote` en **upsert** + agrégats par camp
- [ ] Front : liste, détail, jauge de vote (la maquette devient code), boutons accessibles
- [ ] Tests : unicité du vote (409 simulé), changement d'avis, agrégats

### 🗺️ Sprint 3 — La carte *(1-2 sem)* `lot:1`
- [ ] Intégration react-leaflet : marqueurs des propositions, périmètre GeoJSON
- [ ] Couche IRIS (GeoJSON statique INSEE/IGN) + parkings de report
- [ ] Admin : saisie lat/lng + dessin/import du périmètre
- [ ] Lazy loading de la carte (elle ne se charge que si visible — éco-conception)

### 📊 Sprint 4 — Moteur d'enquêtes *(2 sem)* `lot:1`
- [ ] Admin : constructeur de questionnaire (questions typées + options + audience)
- [ ] `POST /surveys/:id/responses` : validation Zod par type + **transaction** + 409
- [ ] Front : parcours une-question-par-écran avec progression
- [ ] Agrégats (`groupBy` Prisma) + page résultats
- [ ] Tests : la transaction tout-ou-rien, la double réponse, les agrégats sur jeu d'essai

### 🚀 Sprint 5 — RGPD, a11y, mise en ligne Lot 1 *(1 sem)* `lot:1` + `joy:palier-2`
- [ ] Suppression de compte (cascade votes / SetNull réponses) + pages légales + consentement
- [ ] Audit accessibilité (axe DevTools + navigation clavier complète) et Lighthouse (perf/éco)
- [ ] **Bascule email Mailtrap → Brevo** (changer le `.env` de prod, vérifier SPF/DKIM du domaine)
- [ ] `seed.js` final : la proposition piétonnisation + l'enquête stationnement réelles
- [ ] 🦌 **Palier 2** : `_animations.scss` (keyframes mascotte), `_mascot.scss`, `MascotWidget.jsx` (panneau guide-citoyen), confettis au vote (composant `Confetti.jsx`), compteur animé sur les enquêtes, `useScrollReveal` hook, jauges animées au scroll, toast de confirmation
- [ ] 🦌 Audit `prefers-reduced-motion` : vérifier que toutes les animations sont neutralisées
- [ ] Release `v1.0.0` → **Lot 1 en ligne, communication QR codes / réseaux locaux** 🚀

### 💬 Sprint 6 — Commentaires & modération *(2 sem)* `lot:2`
- [ ] CRUD commentaires avec `stance` + statut `PENDING`
- [ ] File de modération admin (approuver / rejeter + motif)
- [ ] Front : débat en colonnes pour/contre · Tests modération

### 📣 Sprint 7 — Propositions citoyennes & notifications *(2 sem)* `lot:2` + `joy:palier-3`
- [ ] `POST /proposals/submit` → `PENDING_REVIEW` + formulaire `/proposer`
- [ ] Notifications broadcast Brevo (nouvelle proposition, clôture d'enquête) + préférences + **lien de désinscription** dans chaque email
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
- **API (prioritaire)** : tests d'intégration Vitest + Supertest sur une BDD de test jetable — cibles n°1 : les invariants métier (unicité du vote, transaction d'enquête, droits admin, effacement RGPD)
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
- CI verte = condition de merge (protection de branche sur `main` et `develop`)
- CD : Railway déploie automatiquement `main` — le merge *est* la mise en production

### Seed (`prisma/seed.js`)
- 1 admin + 3 citoyens de test (mots de passe en clair **uniquement** dans le seed de dev)
- La proposition « Piétonnisation du centre historique le samedi » avec son vrai périmètre GeoJSON
- L'enquête stationnement complète (2 variantes : résidents / commerçants) — le seed **est** ton questionnaire validé
- Une poignée de votes et réponses factices pour voir les agrégats vivre en dev (et les jauges s'animer !)

### Emails : Mailtrap (dev) → Brevo (prod)
- **Mailtrap** = boîte aux lettres de test : tout email « envoyé » y est capturé, rien ne part jamais vers de vrais destinataires — on teste le rendu, les liens, le spam-score, sans risque
- **Brevo** (français 🇫🇷, RGPD-friendly, ~300 emails/jour en gratuit) pour la production : SMTP fourni, SPF/DKIM à configurer sur le domaine — obligatoire *avant* tout broadcast Lot 2 (délivrabilité)
- Le code ne change **jamais** : `services/email.js` lit `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` — la bascule dev → prod est une affaire de `.env`

### Déploiement (Railway)
- Dès le Sprint 0 : API + PostgreSQL managé + front (build statique) — déployer tôt, c'est découvrir les problèmes d'environnement quand ils sont petits
- Variables d'env de prod ≠ dev (secrets JWT, SMTP Brevo, `DATABASE_URL`) — jamais de secret dans Git, `.env` dans le `.gitignore` dès le premier commit
- Sauvegarde BDD avant chaque migration en prod (`pg_dump` — un réflexe, pas une option)
- Horizon Lot 3 : la conteneurisation rend la migration vers OVHcloud/Scaleway indolore
