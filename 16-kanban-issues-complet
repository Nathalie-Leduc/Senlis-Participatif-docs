# Backlog complet — 55 issues planifiées sur 14 semaines

> Rythme : **5 jours / semaine**, 70 jours au total. Chaque issue = une carte du kanban GitHub Projects, un *milestone* = un sprint. Les jours (J1 → J70) sont indicatifs : ils supposent des journées pleines — si tu développes à mi-temps, double le calendrier, pas le contenu.
>
> **Mise à jour v1.1** : +6 issues `joy:palier-*` (mascotte, animations, sections colorées) intégrées aux sprints existants sans modifier le calendrier — les issues design sont courtes (0,5 à 1,5 j) et se glissent dans les marges de chaque sprint.
>
> **Mise à jour v1.2** : Sprint 5 renforcé sur trois points qui n'étaient pas prévus au départ — sécurité (mot de passe CNIL + 2FA email admin) et accessibilité (widget complet plutôt qu'un simple audit). +5 jours au calendrier total (65 → 70 j), répercutés sur les sprints 6 et 7.
>
> **Gabarit d'issue** (à enregistrer comme template GitHub) : *Contexte* (lien cahier des charges) · *Critères d'acceptation* (cases à cocher) · *Definition of Done rappelée*.

---

## 🏗️ Sprint 0 — Fondations *(Semaine 1 · J1–J5)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S0-01 | `chore(repo): initialiser le monorepo (ESLint, Prettier, .env.example) + READMEs` | `lot:1` `type:infra` `zone:ci` | `chore/init-monorepo` | 1 j | J1 |
| S0-02 | `chore(infra): docker-compose (api + PostgreSQL)` | `lot:1` `type:infra` `zone:bdd` | `chore/docker-compose` | 0,5 j | J2 |
| S0-03 | `feat(api): schéma Prisma v1.1 + migration initiale + GET /api/v1/health` | `lot:1` `type:feature` `zone:api` `zone:bdd` | `feat/prisma-init-health` | 1 j | J2–J3 |
| S0-04 | `chore(ci): pipeline GitHub Actions (lint+tests) + déploiement Railway` | `lot:1` `type:infra` `zone:ci` `prio:haute` | `chore/ci-cd-railway` | 1 j | J3–J4 |
| S0-05 | `spike(client): prototype Leaflet — marqueur, polygone GeoJSON, couche IRIS` | `lot:1` `spike` `zone:client` `prio:haute` | `spike/leaflet-proto` | 1 j | J4 |
| **S0-06** | **`feat(client): joy layer — palette étendue, sections colorées, séparateurs ondulés, Mascot SVG statique`** | **`lot:1` `type:design` `zone:client` `joy:palier-1`** | **`feat/joy-layer-base`** | **0,5 j** | **J5** |

🎯 *Fin de sprint : une URL publique répond, la carte est dérisquée, le joy layer est posé.*

## 🔐 Sprint 1 — Auth & emails transactionnels *(Semaines 2–3 · J6–J15)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S1-01 | `feat(api): inscription — Zod + Argon2 + jeton VERIFY_EMAIL` | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/auth-register` | 1,5 j | J6–J7 |
| S1-02 | `feat(api): service email Nodemailer (Mailtrap) + gabarits HTML` | `lot:1` `type:feature` `zone:api` | `feat/email-service` | 1 j | J7–J8 |
| S1-03 | `feat(api): vérification d'email — jeton hashé, usage unique, expiration` | `lot:1` `type:feature` `zone:api` | `feat/auth-verify-email` | 1 j | J8–J9 |
| S1-04 | `feat(api): connexion JWT + middleware auth + rate limiting des routes auth` | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/auth-login-jwt` | 1,5 j | J9–J10 |
| S1-05 | `feat(api): mot de passe oublié / réinitialisation (jeton RESET_PASSWORD)` | `lot:1` `type:feature` `zone:api` | `feat/auth-reset-password` | 1 j | J11 |
| S1-06 | `feat(api): profil /auth/me — consultation, édition, changement de mot de passe` | `lot:1` `type:feature` `zone:api` | `feat/auth-me` | 1 j | J12 |
| S1-07 | `feat(client): pages auth (inscription, connexion, vérification, reset) + useAuth` | `lot:1` `type:feature` `zone:client` | `feat/client-auth-pages` | 2 j | J13–J14 |
| S1-08 | `test(api): parcours d'intégration register → verify → login → me` | `lot:1` `type:test` `zone:api` | `test/auth-flow` | 0,5 j | J15 |
| **S1-09** | **`feat(client): page Accueil hero joyeux — mascotte statique + bulles, stat pills, CTA doré, ton éditorial chaleureux`** | **`lot:1` `type:design` `zone:client` `joy:palier-1`** | **`feat/hero-joyeux`** | **0,5 j** | **J15** |

🎯 *Fin de sprint : on peut créer un compte vérifié et se connecter, les emails arrivent dans Mailtrap. L'accueil est joyeux.*

## 🗳️ Sprint 2 — Propositions & votes *(Semaines 4–5 · J16–J25)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S2-01 | `feat(api): CRUD propositions admin + middleware isAdmin + liste/détail publics` | `lot:1` `type:feature` `zone:api` | `feat/proposals-crud` | 2,5 j | J16–J18 |
| S2-02 | `feat(api): vote — upsert, retrait, agrégats par camp` | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/proposal-vote` | 1,5 j | J18–J19 |
| S2-03 | `feat(client): liste des propositions (filtres statut, tri par votes)` | `lot:1` `type:feature` `zone:client` | `feat/client-proposals-list` | 1,5 j | J20–J21 |
| S2-04 | `feat(client): page détail — jauge de vote + boutons accessibles (48px, aria-pressed)` | `lot:1` `type:feature` `zone:client` | `feat/client-proposal-detail` | 2 j | J21–J23 |
| S2-05 | `feat(client): admin propositions (formulaires de création/édition/statut)` | `lot:1` `type:feature` `zone:client` | `feat/client-admin-proposals` | 1,5 j | J23–J24 |
| S2-06 | `test(api): invariants du vote — unicité, changement d'avis, 401/403` | `lot:1` `type:test` `zone:api` | `test/votes` | 1 j | J25 |

🎯 *Fin de sprint : on publie une proposition et on vote dessus, de bout en bout.*

## 🗺️ Sprint 3 — La carte *(Semaine 6 · J26–J30)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S3-01 | `feat(client): composant MapView (react-leaflet, import dynamique lazy)` | `lot:1` `type:feature` `zone:client` | `feat/map-view` | 1 j | J26 |
| S3-02 | `feat(client): marqueurs + périmètres GeoJSON (accueil, détail proposition)` | `lot:1` `type:feature` `zone:client` | `feat/map-proposals` | 1 j | J27 |
| S3-03 | `feat(client): couche IRIS (GeoJSON statique) + parkings de report` | `lot:1` `type:feature` `zone:client` | `feat/map-iris` | 1 j | J28 |
| S3-04 | `feat(api+client): saisie géo côté admin (lat/lng + import GeoJSON)` | `lot:1` `type:feature` `zone:api` `zone:client` | `feat/admin-geo-input` | 1,5 j | J29–J30 |
| S3-05 | `perf(client): lazy loading images + audit du poids de la carte` | `lot:1` `type:infra` `zone:client` | `perf/lazy-eco` | 0,5 j | J30 |

🎯 *Fin de sprint : la proposition piétonnisation s'affiche avec son périmètre sur la carte de Senlis.*

## 📊 Sprint 4 — Moteur d'enquêtes *(Semaines 7–8 · J31–J40)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S4-01 | `feat(api): CRUD enquêtes + questions/options imbriquées` | `lot:1` `type:feature` `zone:api` | `feat/surveys-crud` | 2 j | J31–J32 |
| S4-02 | `feat(api): soumission de réponse — validation Zod par type + transaction + 409` | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/survey-response` | 2 j | J33–J34 |
| S4-03 | `feat(api): agrégats (groupBy Prisma) + endpoint résultats` | `lot:1` `type:feature` `zone:api` | `feat/survey-results` | 1 j | J35 |
| S4-04 | `feat(client): constructeur de questionnaire (admin)` | `lot:1` `type:feature` `zone:client` | `feat/client-survey-builder` | 2 j | J36–J37 |
| S4-05 | `feat(client): parcours répondant (1 question/écran, progression) + page résultats` | `lot:1` `type:feature` `zone:client` | `feat/client-survey-form` | 2 j | J38–J39 |
| S4-06 | `test(api): moteur d'enquête — transaction tout-ou-rien, double réponse, agrégats` | `lot:1` `type:test` `zone:api` `prio:haute` | `test/survey-engine` | 1 j | J40 |

🎯 *Fin de sprint : l'enquête stationnement est constructible, remplissable, et ses résultats s'agrègent.*

## 🚀 Sprint 5 — RGPD, sécurité, accessibilité, mise en ligne Lot 1 *(Semaines 9–10 · J41–J50)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S5-01 | `feat(api+client): effacement RGPD (cascade/SetNull) + pages légales + consentement + mention pixels de suivi (CNIL)` | `lot:1` `type:feature` `zone:api` `zone:client` `prio:haute` | `feat/rgpd-deletion-legal` | 1 j | J41 |
| **S5-02** | **`feat(api+client): mot de passe renforcé CNIL (12 car. min, maj/min/chiffre/spécial) + jauge de robustesse + resaisie`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/password-policy`** | **0,5 j** | **J41** |
| **S5-03** | **`feat(api+client): double authentification (2FA) par email pour les comptes admin`** | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/admin-2fa-email`** | **1,5 j** | **J42–J43** |
| **S5-04** | **`feat(client): widget d'accessibilité complet (profils, réglage lecteur d'écran, navigation clavier, grille de souris, contrastes, monochrome)`** | **`lot:1` `type:feature` `zone:client` `prio:haute`** | **`feat/a11y-widget`** | **2,5 j** | **J43–J45** |
| S5-05 | `chore(client): audit accessibilité (axe DevTools, parcours clavier) + correctifs — widget inclus` | `lot:1` `type:infra` `zone:client` `prio:haute` | `chore/a11y-audit` | 0,5 j | J46 |
| **S5-06** | **`feat(client): palier 2 — mascotte animée CSS (blink, ears, tail), MascotWidget guide-citoyen, confettis, toast, useScrollReveal, jauges animées, compteur enquête`** | **`lot:1` `type:design` `zone:client` `joy:palier-2` `prio:haute`** | **`feat/mascot-animations-widget`** | **1,5 j** | **J46–J47** |
| **S5-07** | **`chore(client): audit prefers-reduced-motion — vérifier neutralisation de toutes les animations`** | **`lot:1` `type:infra` `zone:client` `joy:palier-2`** | **`chore/reduced-motion-audit`** | **0,5 j** | **J47** |
| S5-08 | `chore: audit Lighthouse (perf / éco) + correctifs` | `lot:1` `type:infra` `zone:client` | `chore/lighthouse` | 0,5 j | J48 |
| S5-09 | `feat(api): seed de production — proposition piétonnisation + enquête stationnement réelles` | `lot:1` `type:feature` `zone:bdd` | `feat/seed-prod` | 0,5 j | J48 |
| S5-10 | `chore(infra): bascule SMTP Brevo (SPF/DKIM, pixel de suivi désactivé — conforme CNIL) + release v1.0.0 🚀` | `lot:1` `type:infra` `zone:ci` `prio:haute` | `chore/brevo-release-v1` | 0,5 j | J49–J50 |

🎯 *Fin de sprint : **Lot 1 en ligne** avec mascotte animée, mot de passe renforcé, 2FA admin par email, widget d'accessibilité complet — communication QR codes commerçants + réseaux locaux.*

## 💬 Sprint 6 — Commentaires & modération *(Semaines 11–12 · J51–J60)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S6-01 | `feat(api): commentaires — stance pour/contre/neutre, statut PENDING` | `lot:2` `type:feature` `zone:api` | `feat/comments-api` | 1,5 j | J51–J52 |
| S6-02 | `feat(api): endpoints de modération (approbation / rejet + motif)` | `lot:2` `type:feature` `zone:api` `prio:haute` | `feat/moderation-api` | 1,5 j | J52–J53 |
| S6-03 | `feat(client): débat en colonnes pour/contre (empilées en mobile)` | `lot:2` `type:feature` `zone:client` | `feat/client-debate-columns` | 2 j | J54–J55 |
| S6-04 | `feat(client): formulaire « Publier mon argument » + état "en modération"` | `lot:2` `type:feature` `zone:client` | `feat/client-comment-form` | 1,5 j | J56–J57 |
| S6-05 | `feat(client): file de modération admin (contexte complet + motif obligatoire)` | `lot:2` `type:feature` `zone:client` | `feat/client-moderation` | 2,5 j | J57–J59 |
| S6-06 | `test(api): modération a priori — rien ne se publie sans validation` | `lot:2` `type:test` `zone:api` | `test/moderation` | 1 j | J60 |

🎯 *Fin de sprint : le débat structuré est ouvert, sous contrôle.*

## 📣 Sprint 7 — Propositions citoyennes & notifications *(Semaines 13–14 · J61–J70)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S7-01 | `feat(api): soumission citoyenne → PENDING_REVIEW + modération des propositions` | `lot:2` `type:feature` `zone:api` | `feat/citizen-proposals` | 2 j | J61–J62 |
| S7-02 | `feat(client): page /proposer — formulaire + localisation sur la carte` | `lot:2` `type:feature` `zone:client` | `feat/client-propose` | 2 j | J63–J64 |
| S7-03 | `feat(api): notifications broadcast Brevo + lien de désinscription (obligation légale)` | `lot:2` `type:feature` `zone:api` `prio:haute` | `feat/notifications-broadcast` | 2 j | J65–J66 |
| S7-04 | `feat(client): préférences de notification dans /mon-compte` | `lot:2` `type:feature` `zone:client` | `feat/client-notif-prefs` | 1 j | J67 |
| **S7-05** | **`feat(client): palier 3 — widget cerf contextuel par page, confettis résultats, mascotte 404 « perdue dans la forêt »`** | **`lot:2` `type:design` `zone:client` `joy:palier-3`** | **`feat/joy-palier-3`** | **1 j** | **J68** |
| S7-06 | `test(api): parcours Lot 2 — soumission, notification, désinscription` | `lot:2` `type:test` `zone:api` | `test/lot2-flows` | 0,5 j | J69 |
| S7-07 | `chore: recette finale complète + release v2.0.0 — MVP complet 🎉` | `lot:2` `type:infra` `prio:haute` | `chore/release-v2` | 1,5 j | J69–J70 |

🎯 *Fin de sprint : **MVP complet en ligne**, dossier prêt pour la mairie.*

---

## 📅 Vue calendrier

| Semaine | Sprint | Issues | Jalon |
|:--:|---|---|---|
| 1 | S0 Fondations | S0-01 → S0-06 | URL publique + carte dérisquée + **joy layer posé** |
| 2 | S1 Auth | S1-01 → S1-04 | Inscription + connexion |
| 3 | S1 Auth | S1-05 → S1-09 | Parcours auth complet testé + **hero joyeux** |
| 4 | S2 Propositions | S2-01 → S2-03 | API propositions + votes |
| 5 | S2 Propositions | S2-04 → S2-06 | Vote de bout en bout |
| 6 | S3 Carte | S3-01 → S3-05 | Carte complète (IRIS, périmètres) |
| 7 | S4 Enquêtes | S4-01 → S4-03 | Moteur d'enquête côté API |
| 8 | S4 Enquêtes | S4-04 → S4-06 | Enquête remplissable + résultats |
| 9 | S5 Sécurité/access. | S5-01 → S5-04 | Mot de passe renforcé, 2FA admin, widget d'accessibilité |
| 10 | S5 Mise en ligne | S5-05 → S5-10 | **🚀 Release v1.0.0 — Lot 1 + mascotte animée** |
| 11 | S6 Commentaires | S6-01 → S6-03 | Débat en colonnes |
| 12 | S6 Commentaires | S6-04 → S6-06 | Modération opérationnelle |
| 13 | S7 Participation | S7-01 → S7-03 | Propositions citoyennes + notifications |
| 14 | S7 Participation | S7-04 → S7-07 | **🎉 Release v2.0.0 — MVP complet + palier 3** |

## Règles de fonctionnement (rappel)

- **1 issue = 1 branche = 1 PR** vers `develop` ; merge `develop → main` + tag en fin de sprint = déploiement
- Limite WIP : **une seule** issue « En cours » à la fois
- Une issue qui dépasse son estimation de ×2 → on la découpe, on ne s'enterre pas
- Tout imprévu hors périmètre → nouvelle issue au Backlog avec `lot:` adéquat, jamais « vite fait au passage »
- Les estimations supposent des journées pleines : à mi-temps, le calendrier double mais l'ordre ne change pas
- Les issues `joy:palier-*` sont des issues front pur — elles ne bloquent jamais une issue API ou BDD
