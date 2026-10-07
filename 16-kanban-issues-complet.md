# Backlog complet — 92 issues planifiées sur 23 semaines

> Rythme : **5 jours / semaine**, 111 jours au total. Chaque issue = une carte du kanban GitHub Projects, un *milestone* = un sprint. Les jours (J1 → J111) sont indicatifs : ils supposent des journées pleines — si tu développes à mi-temps, double le calendrier, pas le contenu.
>
> **Mise à jour v1.1** : +6 issues `joy:palier-*` (mascotte, animations, sections colorées) intégrées aux sprints existants sans modifier le calendrier — les issues design sont courtes (0,5 à 1,5 j) et se glissent dans les marges de chaque sprint.
>
> **Mise à jour v1.2** : Sprint 5 renforcé sur trois points qui n'étaient pas prévus au départ — sécurité (mot de passe CNIL + 2FA email admin) et accessibilité (widget complet plutôt qu'un simple audit). +5 jours au calendrier total (65 → 70 j), répercutés sur les sprints 6 et 7.
>
> **Mise à jour v1.3** : la conclusion du Sprint 5 ("Lot 1 en ligne") passait sous silence tout ce qu'une VRAIE mise en ligne demande au-delà du code — nom de domaine, hébergement, sauvegardes, chiffrement, communication de lancement. +4 issues (S5-11 à S5-14), +4 jours au calendrier total (70 → 74 j), répercutés sur les sprints 6 et 7.
>
> **Mise à jour v1.4** : revue du cahier des charges avant déploiement — le moteur d'enquêtes (Sprint 4) ne répondait pas complètement au premier cas d'usage (enquête stationnement ciblée résidents/commerçants du centre) : rien ne vérifiait la situation géographique des répondants, les résultats étaient toujours publics, aucune vue admin détaillée, aucun moyen de scinder une enquête par public. Nouveau sous-groupe **Sprint 5bis** (6 issues, S5-15 à S5-20). +7 jours au calendrier total (74 → 81 j), répercutés sur les sprints 6 et 7. Réordonné ensuite : Sprint 5bis (revue enquêtes) passe avant Sprint 5ter (mise en ligne réelle, ex-S5-11 à S5-14) — corriger le fond avant de déployer et communiquer.
>
> **Mise à jour v1.5** : les citoyens d'"un autre quartier de Senlis" n'avaient aucun moyen de préciser lequel — risque de sentiment d'exclusion, et aucune donnée exploitable pour cibler de futures enquêtes/propositions par quartier. +1 issue (S5-13, menu en cascade vers les 6 quartiers IRIS INSEE hors centre historique — Brichebay, Bon Secours, Val d'Aunette-Gâtelière, Zone Industrielle, Villevert, Jardiniers). +1 jour au calendrier total (81 → 82 j), répercuté sur la suite du Sprint 5bis, le Sprint 5ter, et les sprints 6 et 7.
>
> **Mise à jour v1.6** : l'enquête stationnement (S5-17) a été affinée avec la mairie sur le fond — distinguer 4 profils (habitant, patron/gérant, salarié, visiteur occasionnel), les véhicules professionnels séparément des personnels, les difficultés de stationnement rencontrées, les freins d'accès sans voiture pour les non-résidents du centre. Une vraie limite du moteur assumée au passage : pas de détail "par véhicule" (plusieurs voitures = choix multiple des emplacements utilisés, sans répartition fine), et la sélection de ville reste en texte libre plutôt qu'un vrai annuaire INSEE avec autocomplétion (fonctionnalité à part entière, pas encore chiffrée). Au passage : le compteur "participants" de l'accueil affichait un 0 fixe, remplacé par un vrai chiffre (citoyens ayant voté ou répondu à une enquête), et ajout du lien "Voir les enquêtes". +1 jour au calendrier total (82 → 83 j), répercuté sur le Sprint 5ter et les sprints 6 et 7.
>
> **Mise à jour v1.7** : +1 issue (S5-18, champ ville avec suggestions via l'API officielle geo.api.gouv.fr — DINUM, gratuite et sans clé — plutôt qu'un vrai nouveau type de question : un simple indicateur sur une question TEXTE_LIBRE existante). +2 jours au calendrier total (83 → 85 j), répercutés sur le Sprint 5ter et les sprints 6 et 7.
>
> **Mise à jour v1.8** : ajout du détail de stationnement PAR VÉHICULE au backlog non planifié (BACKLOG-01) — noté pour ne pas être oublié, mais volontairement sans jour ni estimation tant qu'une vraie conception n'a pas eu lieu. Aucun impact sur le calendrier des 85 jours.
>
> **Mise à jour v1.9** : +3 issues (S5-19 gestion simple des comptes — promouvoir/rétrograder un admin, sans la hiérarchie complète ; S5-20 mémoriser l'appareil de confiance 1h après un 2FA réussi ; S5-21 résultats segmentés par profil de répondant + export impression/PDF, propositions et enquêtes). +3 jours au calendrier total (85 → 88 j), répercutés sur le Sprint 5ter et les sprints 6 et 7. La hiérarchie complète des rôles (admin mairie, salariés, maisons de quartier, délégués + circuit de validation) est notée en backlog non planifié (BACKLOG-02) — un chantier plus vaste que tout le moteur de branchement réuni, à concevoir en détail avant de le chiffrer.
>
> **Mise à jour v1.10** (23/09/2026) : audit sécurité / RGPD / accessibilité avant la mise en ligne (voir `21-audit-securite-rgpd-accessibilite.md`). Nouveau bloc **Sprint 5 audit** (8 issues, S5A-01 à S5A-08), placé **après S5-21 et avant la mise en ligne réelle (Sprint 5ter)** : on ne met pas en ligne un site qui laisse ses droits d'admin à un compte rétrogradé ou qui envoie l'IP de ses visiteurs à Google. +7 jours de travail (88 → 96 j au calendrier, S5-21 et S5A se chevauchant d'un jour), répercutés sur les sprints 5ter, 6 et 7. Au passage : cases ✅ ajoutées aux sprints 0 à 4 (tous livrés, tag `v0.3.0` pour la carte), S5-16 coché (livré mais oublié), S5-21 complété le 24/09 (volet propositions + seuil de confidentialité de 5 personnes, ajouté à ses critères par l'audit), `develop` → `dev` dans les règles, calendrier semaines/jours réaligné. Pour le Lot 2 : les préférences de notification devront passer à `false` par défaut (RGPD art. 25, protection des données par défaut) — noté dans S7-04.
>
> **Ajout v1.10 (28/09/2026, pendant S5A-07)** : deux constats versés dans S5A-08 plutôt que corrigés au passage — (1) la config ESLint du client n'a pas de clé `files` : les fichiers `.jsx` sont ignorés (« File ignored because no matching configuration »), donc aucune page n'est réellement lintée ; l'activer demande `eslint-plugin-react` (sinon 137 faux « unused-vars » sur les composants utilisés en JSX) ; (2) `Connexion.jsx` accepte un `?redirect=` commençant par `//` (URL « protocole-relative ») — pas de fuite, `pushState` lève une erreur, mais à refuser explicitement.
>
> **Mise à jour v1.11** (30/09/2026) : **retours de recette** de Nath avant la mise en ligne (document `23-retours-recette-2026-09-30.md`). Nouveau bloc **Sprint 5 recette** (13 issues, S5R-01 → S5R-13, 14 jours) inséré entre le Sprint 5 audit (✅ terminé) et la mise en ligne réelle (Sprint 5ter), qui glisse de 14 jours ; Sprints 6 et 7 décalés d'autant (96 → 110 jours). Bug majeur diagnostiqué : la page de vérification d'email envoyait le jeton **deux fois** (StrictMode de React en développement) et affichait « jeton invalide » alors que l'adresse était bien vérifiée. Dix propositions d'évolution classées **Lot 3** (à présenter à la mairie), listées en fin de backlog.
>
> **Mise à jour v1.12** (02/10/2026) : ajout de **S5R-02b** (vérification DNS du domaine de l'email côté API), née d'une proposition de Nath après S5R-02 ; placée juste après S5R-03. +0,5 jour, arrondi à 1 jour de décalage pour la suite (5ter, 6 et 7 : +1 jour ; 111 jours au total).
>
> **Légende** : ✅ livré (mergé sur `dev`) · 🔶 partiellement livré · sans marque = à faire.
>
> **Gabarit d'issue** (à enregistrer comme template GitHub) : *Contexte* (lien cahier des charges) · *Critères d'acceptation* (cases à cocher) · *Definition of Done rappelée*.

---

## 🏗️ Sprint 0 — Fondations *(Semaine 1 · J1–J5)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S0-01 | `chore(repo): initialiser le monorepo (ESLint, Prettier, .env.example) + READMEs` ✅ | `lot:1` `type:infra` `zone:ci` | `chore/init-monorepo` | 1 j | J1 |
| S0-02 | `chore(infra): docker-compose (api + PostgreSQL)` ✅ | `lot:1` `type:infra` `zone:bdd` | `chore/docker-compose` | 0,5 j | J2 |
| S0-03 | `feat(api): schéma Prisma v1.1 + migration initiale + GET /api/v1/health` ✅ | `lot:1` `type:feature` `zone:api` `zone:bdd` | `feat/prisma-init-health` | 1 j | J2–J3 |
| S0-04 | `chore(ci): pipeline GitHub Actions (lint+tests) + déploiement Railway` ✅ *(CI livrée ; déploiement Railway abandonné → remplacé par Clever Cloud en S5-22)* | `lot:1` `type:infra` `zone:ci` `prio:haute` | `chore/ci-cd-railway` | 1 j | J3–J4 |
| S0-05 | `spike(client): prototype Leaflet — marqueur, polygone GeoJSON, couche IRIS` ✅ | `lot:1` `spike` `zone:client` `prio:haute` | `spike/leaflet-proto` | 1 j | J4 |
| **S0-06** | **`feat(client): joy layer — palette étendue, sections colorées, séparateurs ondulés, Mascot SVG statique`** ✅ | **`lot:1` `type:design` `zone:client` `joy:palier-1`** | **`feat/joy-layer-base`** | **0,5 j** | **J5** |

🎯 *Fin de sprint : une URL publique répond, la carte est dérisquée, le joy layer est posé.*

## 🔐 Sprint 1 — Auth & emails transactionnels *(Semaines 2–3 · J6–J15)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S1-01 | `feat(api): inscription — Zod + Argon2 + jeton VERIFY_EMAIL` ✅ | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/auth-register` | 1,5 j | J6–J7 |
| S1-02 | `feat(api): service email Nodemailer (Mailtrap) + gabarits HTML` ✅ | `lot:1` `type:feature` `zone:api` | `feat/email-service` | 1 j | J7–J8 |
| S1-03 | `feat(api): vérification d'email — jeton hashé, usage unique, expiration` ✅ | `lot:1` `type:feature` `zone:api` | `feat/auth-verify-email` | 1 j | J8–J9 |
| S1-04 | `feat(api): connexion JWT + middleware auth + rate limiting des routes auth` ✅ | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/auth-login-jwt` | 1,5 j | J9–J10 |
| S1-05 | `feat(api): mot de passe oublié / réinitialisation (jeton RESET_PASSWORD)` ✅ | `lot:1` `type:feature` `zone:api` | `feat/auth-reset-password` | 1 j | J11 |
| S1-06 | `feat(api): profil /auth/me — consultation, édition, changement de mot de passe` ✅ | `lot:1` `type:feature` `zone:api` | `feat/auth-me` | 1 j | J12 |
| S1-07 | `feat(client): pages auth (inscription, connexion, vérification, reset) + useAuth` ✅ | `lot:1` `type:feature` `zone:client` | `feat/client-auth-pages` | 2 j | J13–J14 |
| S1-08 | `test(api): parcours d'intégration register → verify → login → me` ✅ | `lot:1` `type:test` `zone:api` | `test/auth-flow` | 0,5 j | J15 |
| **S1-09** | **`feat(client): page Accueil hero joyeux — mascotte statique + bulles, stat pills, CTA doré, ton éditorial chaleureux`** ✅ | **`lot:1` `type:design` `zone:client` `joy:palier-1`** | **`feat/hero-joyeux`** | **0,5 j** | **J15** |

🎯 *Fin de sprint : on peut créer un compte vérifié et se connecter, les emails arrivent dans Mailtrap. L'accueil est joyeux.*

## 🗳️ Sprint 2 — Propositions & votes *(Semaines 4–5 · J16–J25)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S2-01 | `feat(api): CRUD propositions admin + middleware isAdmin + liste/détail publics` ✅ | `lot:1` `type:feature` `zone:api` | `feat/proposals-crud` | 2,5 j | J16–J18 |
| S2-02 | `feat(api): vote — upsert, retrait, agrégats par camp` ✅ | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/proposal-vote` | 1,5 j | J18–J19 |
| S2-03 | `feat(client): liste des propositions (filtres statut, tri par votes)` ✅ | `lot:1` `type:feature` `zone:client` | `feat/client-proposals-list` | 1,5 j | J20–J21 |
| S2-04 | `feat(client): page détail — jauge de vote + boutons accessibles (48px, aria-pressed)` ✅ | `lot:1` `type:feature` `zone:client` | `feat/client-proposal-detail` | 2 j | J21–J23 |
| S2-05 | `feat(client): admin propositions (formulaires de création/édition/statut)` ✅ | `lot:1` `type:feature` `zone:client` | `feat/client-admin-proposals` | 1,5 j | J23–J24 |
| S2-06 | `test(api): invariants du vote — unicité, changement d'avis, 401/403` ✅ | `lot:1` `type:test` `zone:api` | `test/votes` | 1 j | J25 |

🎯 *Fin de sprint : on publie une proposition et on vote dessus, de bout en bout.*

## 🗺️ Sprint 3 — La carte *(Semaine 6 · J26–J30)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S3-01 | `feat(client): composant MapView (react-leaflet, import dynamique lazy)` ✅ | `lot:1` `type:feature` `zone:client` | `feat/map-view` | 1 j | J26 |
| S3-02 | `feat(client): marqueurs + périmètres GeoJSON (accueil, détail proposition)` ✅ | `lot:1` `type:feature` `zone:client` | `feat/map-proposals` | 1 j | J27 |
| S3-03 | `feat(client): couche IRIS (GeoJSON statique) + parkings de report` ✅ | `lot:1` `type:feature` `zone:client` | `feat/map-iris` | 1 j | J28 |
| S3-04 | `feat(api+client): saisie géo côté admin (lat/lng + import GeoJSON)` ✅ | `lot:1` `type:feature` `zone:api` `zone:client` | `feat/admin-geo-input` | 1,5 j | J29–J30 |
| S3-05 | `perf(client): lazy loading images + audit du poids de la carte` ✅ | `lot:1` `type:infra` `zone:client` | `perf/lazy-eco` | 0,5 j | J30 |

🎯 *Fin de sprint : la proposition piétonnisation s'affiche avec son périmètre sur la carte de Senlis.*

## 📊 Sprint 4 — Moteur d'enquêtes *(Semaines 7–8 · J31–J40)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S4-01 | `feat(api): CRUD enquêtes + questions/options imbriquées` ✅ | `lot:1` `type:feature` `zone:api` | `feat/surveys-crud` | 2 j | J31–J32 |
| S4-02 | `feat(api): soumission de réponse — validation Zod par type + transaction + 409` ✅ | `lot:1` `type:feature` `zone:api` `prio:haute` | `feat/survey-response` | 2 j | J33–J34 |
| S4-03 | `feat(api): agrégats (groupBy Prisma) + endpoint résultats` ✅ | `lot:1` `type:feature` `zone:api` | `feat/survey-results` | 1 j | J35 |
| S4-04 | `feat(client): constructeur de questionnaire (admin)` ✅ | `lot:1` `type:feature` `zone:client` | `feat/client-survey-builder` | 2 j | J36–J37 |
| S4-05 | `feat(client): parcours répondant (1 question/écran, progression) + page résultats` ✅ | `lot:1` `type:feature` `zone:client` | `feat/client-survey-form` | 2 j | J38–J39 |
| S4-06 | `test(api): moteur d'enquête — transaction tout-ou-rien, double réponse, agrégats` ✅ | `lot:1` `type:test` `zone:api` `prio:haute` | `test/survey-engine` | 1 j | J40 |

🎯 *Fin de sprint : l'enquête stationnement est constructible, remplissable, et ses résultats s'agrègent.*

## 🚀 Sprint 5 — RGPD, sécurité, accessibilité *(Semaines 9–10 · J41–J50)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S5-01 | `feat(api+client): effacement RGPD (cascade/SetNull) + pages légales + consentement + mention pixels de suivi (CNIL)` ✅ | `lot:1` `type:feature` `zone:api` `zone:client` `prio:haute` | `feat/rgpd-deletion-legal` | 1 j | J41 |
| **S5-02** | **`feat(api+client): mot de passe renforcé CNIL (12 car. min, maj/min/chiffre/spécial) + jauge de robustesse + resaisie`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/password-policy`** | **0,5 j** | **J41** |
| **S5-03** | **`feat(api+client): double authentification (2FA) par email pour les comptes admin`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/admin-2fa-email`** | **1,5 j** | **J42–J43** |
| **S5-04** | **`feat(client): widget d'accessibilité complet (profils, réglage lecteur d'écran, navigation clavier, grille de souris, contrastes, monochrome)`** ✅ | **`lot:1` `type:feature` `zone:client` `prio:haute`** | **`feat/a11y-widget`** | **2,5 j** | **J43–J45** |
| S5-05 | `chore(client): audit accessibilité (axe DevTools, parcours clavier) + correctifs — widget inclus` ✅ | `lot:1` `type:infra` `zone:client` `prio:haute` | `chore/a11y-audit` | 0,5 j | J46 |
| **S5-06** | **`feat(client): palier 2 — mascotte animée CSS (blink, ears, tail), MascotWidget guide-citoyen, confettis, toast, useScrollReveal, jauges animées, compteur enquête`** ✅ | **`lot:1` `type:design` `zone:client` `joy:palier-2` `prio:haute`** | **`feat/mascot-animations-widget`** | **1,5 j** | **J46–J47** |
| **S5-07** | **`chore(client): audit prefers-reduced-motion — vérifier neutralisation de toutes les animations`** ✅ | **`lot:1` `type:infra` `zone:client` `joy:palier-2`** | **`chore/reduced-motion-audit`** | **0,5 j** | **J47** |
| S5-08 | `chore: audit Lighthouse (perf / éco) + correctifs` ✅ | `lot:1` `type:infra` `zone:client` | `chore/lighthouse` | 0,5 j | J48 |
| S5-09 | `feat(api): seed de production — proposition piétonnisation + enquête stationnement réelles` ✅ | `lot:1` `type:feature` `zone:bdd` | `feat/seed-prod` | 0,5 j | J48 |
| S5-10 | `chore(infra): bascule SMTP Brevo (SPF/DKIM, pixel de suivi désactivé — conforme CNIL) + release v1.0.0 (tag) 🚀` ✅ | `lot:1` `type:infra` `zone:ci` `prio:haute` | `chore/brevo-release-v1` | 0,5 j | J49–J50 |

🎯 *Fin de sprint : le code est prêt et tagué v1.0.0 — pas encore réellement en ligne (voir Sprint 5ter).*

## 🔍 Sprint 5bis — Revue cahier des charges : ciblage et enquêtes *(Semaines 11–13 · J51–J64)*

> Trouvé en relisant le cahier des charges juste avant le déploiement (v1.4) : le moteur d'enquêtes livré au Sprint 4 ne vérifiait rien de la situation des répondants ni ne permettait de scinder une enquête par public — pourtant au cœur du premier cas d'usage (enquête stationnement, résidents/commerçants du centre historique). Traité **avant** la mise en ligne réelle (Sprint 5ter) : pas question de déployer et communiquer sur une enquête qui ne répond pas encore correctement à son propre cas d'usage. Découpé en tickets **indépendants et testables un par un**, précisément pour limiter le risque de régression sur un moteur déjà utilisé.

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| **S5-11** | **`feat(api): schéma — situation citoyen, publication des résultats, branchement de questions`** ✅ | **`lot:1` `type:feature` `zone:bdd` `prio:haute`** | **`feat/schema-situation-branching`** | **0,5 j** | **J51** |
| **S5-12** | **`feat(api+client): situation déclarée du citoyen (inscription + Mon compte)`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/citizen-situation`** | **1 j** | **J51–J52** |
| **S5-13** | **`feat(api+client): quartier précis en cascade pour "autre quartier" (6 quartiers IRIS)`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client`** | **`feat/quartier-cascade`** | **0,5 j** | **J53** |
| **S5-14** | **`feat(api+client): publication des résultats soumise à l'admin + vue détaillée`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/survey-results-publish-stats`** | **1,5 j** | **J53–J54** |
| **S5-15** | **`feat(api+client): branchement conditionnel de questions dans le moteur d'enquête`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/survey-branching-engine`** | **1,5 j** | **J55–J56** |
| **S5-16** | **`feat(client): interface de branchement dans le constructeur d'enquête`** ✅ | **`lot:1` `type:feature` `zone:client` `prio:haute`** | **`feat/survey-builder-branching-ui`** | **1,5 j** | **J56–J57** |
| **S5-17** | **`feat(api+client): enquête stationnement reconstruite en 4 parcours (habitant/patron/salarié/visiteur) + compteur "participants" réel + lien "Voir les enquêtes"`** ✅ | **`lot:1` `type:feature` `zone:bdd` `zone:api` `zone:client`** | **`feat/seed-prod-stationnement-v2`** | **1,5 j** | **J58–J59** |
| **S5-18** | **`feat(api+client): champ ville avec suggestions (API officielle geo.api.gouv.fr) pour les questions "quelle est cette ville ?"`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client`** | **`feat/survey-city-autocomplete`** | **1,5 j** | **J60–J61** |
| **S5-19** | **`feat(api+client): gestion simple des comptes — promouvoir/rétrograder un admin (sans la hiérarchie complète)`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/simple-user-management`** | **1 j** | **J61–J62** |
| **S5-20** | **`feat(api+client): 2FA admin — mémoriser l'appareil de confiance pendant 1h`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client`** | **`feat/2fa-trusted-device`** | **0,5 j** | **J62** |
| **S5-21** | **`feat(api+client): résultats segmentés par profil de répondant + export impression/PDF (propositions et enquêtes)`** ✅ *(+ secret statistique : groupes de moins de 5 personnes masqués, réponses libres jamais détaillées par segment ni imprimées)* | **`lot:1` `type:feature` `zone:api` `zone:client`** | **`feat/results-segmented-export`** | **2 j** | **J62–J64** |

🎯 *Fin de sprint : l'enquête stationnement cible vraiment son public, ses résultats ne sont visibles que si l'admin les publie, et l'admin dispose d'une vue détaillée pour vérifier qui a réellement répondu.*

## 🛡️ Sprint 5 audit — Sécurité, RGPD, accessibilité avant mise en ligne *(Semaines 13–15 · J65–J71)*

> Issu de l'audit du 23/09/2026 (`21-audit-securite-rgpd-accessibilite.md`). Chaque issue cite sa référence (OWASP Top 10 2021, CNIL, ANSSI, RGAA 4.1). Ordre = gravité : ce qui ouvre une faille ou fausse des données d'abord, le confort réglementaire ensuite.

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| **S5A-01** | **`fix(api): contrôle d'accès — rôle et existence du compte relus en base à chaque requête protégée + algorithme JWT épinglé (OWASP A01/A07)`** ✅ | **`lot:1` `type:bug` `zone:api` `prio:haute`** | **`fix/access-control-fresh-role`** | **0,5 j** | **J65** |
| **S5A-02** | **`fix(api): robustesse — P2002/P2025/Multer traduits en 409/404/400, profil travail enregistré à l'inscription, tests users réactivés (users.tests.js → users.test.js)`** ✅ | **`lot:1` `type:bug` `zone:api` `prio:haute`** | **`fix/api-error-mapping`** | **0,5 j** | **J65** |
| **S5A-03** | **`fix(client): polices auto-hébergées (fin des appels Google Fonts — CNIL) + retrait de nodemailer des dépendances client`** ✅ | **`lot:1` `type:bug` `zone:client` `prio:haute`** | **`fix/self-hosted-fonts`** | **0,5 j** | **J66** |
| **S5A-04** | **`docs(client): politique de confidentialité et mentions légales exactes (bases légales, destinataires, durées, droits, CNIL) + registre des traitements`** ✅ | **`lot:1` `type:docs` `zone:client` `prio:haute`** | **`docs/legal-pages-accuracy`** | **1 j** | **J66–J67** |
| **S5A-05** | **`feat(api+client): droits RGPD — export de mes données (accès/portabilité) + purge des comptes inactifs et des jetons expirés`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client`** | **`feat/gdpr-export-purge`** | **1 j** | **J67–J68** |
| **S5A-06** | **`feat(api+client): durcissement auth — essais 2FA limités, mot de passe exigé pour changer d'email ou supprimer son compte, révocation des sessions après changement de mot de passe, journal des actions admin (ANSSI/CNIL)`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `zone:bdd` `prio:haute`** | **`feat/auth-hardening`** | **1,5 j** | **J68–J69** |
| **S5A-07** | **`fix(client): RGAA — titre unique par page, erreurs reliées aux champs, annonce des changements de page, déclaration d'accessibilité + plan du site`** ✅ | **`lot:1` `type:bug` `zone:client` `prio:haute`** | **`fix/rgaa-conformity`** | **1,5 j** | **J70–J71** |
| **S5A-08** | **`chore(infra): préparation prod — trust proxy, URL absolue des images + CORP same-site, tuiles OSM, docker-compose/Dockerfile corrigés, npm audit en CI, ESLint client étendu aux .jsx, redirection après connexion refusant « // »`** ✅ | **`lot:1` `type:infra` `zone:ci` `prio:haute`** | **`chore/prod-readiness`** | **0,5 j** | **J71** |

🎯 *Fin de sprint : plus aucun point 🔴 de l'audit ouvert — le site peut être mis en ligne sans exposer ni ses visiteurs ni sa future relation avec la mairie.*

## 🧪 Sprint 5 recette — Retours de tests avant mise en ligne *(Semaines 15–18 · J72–J86)*

> Issu de la recette du 30/09/2026 (`23-retours-recette-2026-09-30.md`) : chaque issue reprend un ou plusieurs retours, le document donne le détail et les réponses aux questions posées. Ordre = ordre de traitement : d'abord ce qui bloque un citoyen, ensuite l'enquête, puis les outils d'administration.

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| **S5R-01** | **`fix(api+client): vérification d'email fiable — un seul envoi du jeton, jeton déjà consommé par un compte vérifié = succès, renvoi de l'email de vérification, saisie de l'inscription conservée`** ✅ | **`lot:1` `type:bug` `zone:api` `zone:client` `prio:haute`** | **`fix/email-verification-flow`** | **1 j** | **J72** |
| **S5R-02** | **`fix(client): formulaires d'authentification — erreur sous chaque champ dès la sortie du champ, format d'email + suggestion de faute de frappe, œil à la connexion, quartier et rôle obligatoires si « je travaille à Senlis »`** ✅ | **`lot:1` `type:bug` `zone:client` `prio:haute`** | **`fix/auth-forms-inline-errors`** | **1 j** | **J73** |
| **S5R-03** | **`fix(client): module d'accessibilité — panneau défilant et bouton Fermer toujours atteignable, interlignage sur 3 niveaux + espacements lettres/mots/paragraphes (WCAG 1.4.12)`** ✅ | **`lot:1` `type:bug` `zone:client` `prio:haute`** | **`fix/a11y-widget-spacing`** | **0,5 j** | **J74** |
| **S5R-02b** | **`feat(api): vérification DNS du domaine de l'email — MX puis A/AAAA (« MX implicite »), « null MX » refusé, délai 2 s, tolérance aux pannes DNS (dans le doute, on accepte) — à l'inscription, au changement d'email et au renvoi du lien`** ✅ | **`lot:1` `type:feature` `zone:api`** | **`feat/email-domain-dns-check`** | **0,5 j** | **J74** |
| **S5R-04** | **`fix(client): accueil fidèle à la maquette — un seul cerf, guide « Cerf-tifié utile » avec questions fréquentes`** ✅ | **`lot:1` `type:design` `zone:client`** | **`fix/home-mascot-guide`** | **1 j** | **J75** |
| **S5R-05** | **`feat(api+bdd+client): moteur d'enquête v2 — total de questions adapté au parcours, conditions multiples (OU), fin anticipée, bornes min/max d'un nombre, nombre de choix max lié à une réponse, profil « travaille à Senlis » explicite`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:bdd` `zone:client` `prio:haute`** | **`feat/survey-engine-v2`** | **2 j** | **J76–J77** |
| **S5R-05b** | **`fix(api+client): limiteur des routes d'authentification — réponse JSON lisible (et non « erreur réseau »), seuls les échecs comptent, limite large en développement`** ✅ *(constaté pendant la recette de S5R-05)* | **`lot:1` `type:bug` `zone:api` `zone:client`** | **`fix/auth-rate-limit-json`** | **—** | **J77** |
| **S5R-06** | **`feat(data+client): enquête stationnement v3 — parcours par profil, questions clés obligatoires, « Autre → laquelle ? », questions propres au centre historique, visiteurs venant en centre-ville ; page de présentation sans le « 34 questions » trompeur (nombre selon le profil)`** ✅ | **`lot:1` `type:feature` `zone:bdd`** | **`feat/parking-survey-v3`** | **1 j** | **J78** |
| **S5R-07** | **`feat(api+bdd+client): public visé d'une enquête par critères de profil (résidence, quartier, travail, rôle, hors Senlis)`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:bdd` `zone:client`** | **`feat/survey-audience-profiles`** | **1 j** | **J79** |
| **S5R-08** | **`feat(api+client): résultats d'enquête lisibles — filtre par public puis par question, graphiques, export CSV et JSON (secret statistique conservé)`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:client` `prio:haute`** | **`feat/survey-results-charts-export`** | **2 j** | **J80–J81** |
| **S5R-09** | **`feat(client): création d'enquête simplifiée — aides sous chaque champ, exemples, modèle prêt à l'emploi, dupliquer une question`** ✅ | **`lot:1` `type:feature` `zone:client`** | **`feat/survey-builder-help`** | **1 j** | **J82** |
| **S5R-10** | **`feat(api+bdd+client): zone d'une proposition — toute la ville ou quartiers IRIS ; coordonnées et GeoJSON sous « Avancé » ; aides de saisie`** ✅ | **`lot:1` `type:feature` `zone:api` `zone:bdd` `zone:client`** | **`feat/proposal-zone-quartiers`** | **1 j** | **J83** |
| **S5R-11** | **`feat(client): carte « Explorer » — propositions ET enquêtes ouvertes par quartier, emplacements réels des parkings (extraction figée)`** | **`lot:1` `type:feature` `zone:client`** | **`feat/explore-map-quartiers`** | **1,5 j** | **J84–J85** |
| **S5R-12** | **`fix(client): navigation admin — bouton « Voir » dans les listes admin, pages publiques Propositions/Enquêtes retirées du menu admin`** | **`lot:1` `type:bug` `zone:client`** | **`fix/admin-navigation`** | **0,5 j** | **J85** |
| **S5R-13** | **`docs: audit complété NIST (SP 800-63B, CSF 2.0) + procédure de mise à jour des contours IRIS`** | **`lot:1` `type:docs`** | **`docs/nist-iris-procedure`** | **0,5 j** | **J86** |

🎯 *Fin de sprint : un premier citoyen s'inscrit, vérifie son adresse et répond à l'enquête stationnement sans accroc ; l'administratrice lit et exporte des résultats clairs.*

## 🌍 Sprint 5ter — Mise en ligne réelle *(Semaines 18–19 · J87–J91)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| **S5-22** | **`chore(infra): nom de domaine + déploiement chez un hébergeur souverain (Clever Cloud, France) — OVH pour le domaine`** | **`lot:1` `type:infra` `zone:ci` `prio:haute`** | **`chore/domain-hosting`** | **1 j** | **J87–J88** |
| **S5-23** | **`chore(infra): sauvegarde automatisée quotidienne de PostgreSQL (cron), stockage isolé, rétention 30 jours`** | **`lot:1` `type:infra` `zone:bdd` `prio:haute`** | **`chore/db-backups`** | **1 j** | **J88–J89** |
| **S5-24** | **`chore(infra): chiffrement des flux (TLS partout, HSTS, CSP sur le site statique) + chiffrement des données sensibles au repos`** | **`lot:1` `type:infra` `zone:ci` `prio:haute`** | **`chore/encryption-transit-rest`** | **0,5 j** | **J89** |
| **S5-25** | **`chore: communication de lancement — QR codes commerçants, réseaux locaux, approche des élus (mairie)`** | **`lot:1` `type:doc` `zone:comm`** | **`chore/launch-comms`** | **1 j** | **J90–J91** |

🎯 *Fin de sprint : **Lot 1 réellement en ligne**, hébergé en France chez un prestataire souverain, sauvegardé quotidiennement et chiffré — avec une enquête stationnement qui répond enfin correctement à son cas d'usage — lancement accompagné de QR codes commerçants, réseaux locaux, et d'une prise de contact avec les élus municipaux.*

## 💬 Sprint 6 — Commentaires & modération *(Semaines 19–21 · J92–J101)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S6-01 | `feat(api): commentaires — stance pour/contre/neutre, statut PENDING` | `lot:2` `type:feature` `zone:api` | `feat/comments-api` | 1,5 j | J92–J93 |
| S6-02 | `feat(api): endpoints de modération (approbation / rejet + motif)` | `lot:2` `type:feature` `zone:api` `prio:haute` | `feat/moderation-api` | 1,5 j | J93–J94 |
| S6-03 | `feat(client): débat en colonnes pour/contre (empilées en mobile)` | `lot:2` `type:feature` `zone:client` | `feat/client-debate-columns` | 2 j | J95–J96 |
| S6-04 | `feat(client): formulaire « Publier mon argument » + état "en modération"` | `lot:2` `type:feature` `zone:client` | `feat/client-comment-form` | 1,5 j | J97–J98 |
| S6-05 | `feat(client): file de modération admin (contexte complet + motif obligatoire)` | `lot:2` `type:feature` `zone:client` | `feat/client-moderation` | 2,5 j | J98–J100 |
| S6-06 | `test(api): modération a priori — rien ne se publie sans validation` | `lot:2` `type:test` `zone:api` | `test/moderation` | 1 j | J101 |

🎯 *Fin de sprint : le débat structuré est ouvert, sous contrôle.*

## 📣 Sprint 7 — Propositions citoyennes & notifications *(Semaines 21–23 · J102–J111)*

| ID | Issue | Labels | Branche | Estim. | Jours |
|---|---|---|---|:--:|:--:|
| S7-01 | `feat(api): soumission citoyenne → PENDING_REVIEW + modération des propositions` | `lot:2` `type:feature` `zone:api` | `feat/citizen-proposals` | 2 j | J102–J103 |
| S7-02 | `feat(client): page /proposer — formulaire + localisation sur la carte` | `lot:2` `type:feature` `zone:client` | `feat/client-propose` | 2 j | J104–J105 |
| S7-03 | `feat(api): notifications broadcast Brevo + lien de désinscription (obligation légale)` | `lot:2` `type:feature` `zone:api` `prio:haute` | `feat/notifications-broadcast` | 2 j | J106–J107 |
| S7-04 | `feat(client): préférences de notification dans /mon-compte — désactivées par défaut (RGPD art. 25, migration notify* → false)` | `lot:2` `type:feature` `zone:client` `zone:bdd` | `feat/client-notif-prefs` | 1 j | J108 |
| **S7-05** | **`feat(client): palier 3 — widget cerf contextuel par page, confettis résultats, mascotte 404 « perdue dans la forêt »`** | **`lot:2` `type:design` `zone:client` `joy:palier-3`** | **`feat/joy-palier-3`** | **1 j** | **J109** |
| S7-06 | `test(api): parcours Lot 2 — soumission, notification, désinscription` | `lot:2` `type:test` `zone:api` | `test/lot2-flows` | 0,5 j | J109 |
| S7-07 | `chore: recette finale complète + release v2.0.0 — MVP complet 🎉` | `lot:2` `type:infra` `prio:haute` | `chore/release-v2` | 1,5 j | J110–J111 |

🎯 *Fin de sprint : **MVP complet en ligne**, dossier prêt pour la mairie.*

---

## 📦 Backlog non planifié — à chiffrer plus tard

> Identifié en revoyant le contenu de l'enquête stationnement (v1.6/v1.7), mais volontairement **hors calendrier** : l'ampleur réelle (comparable à l'ensemble S5-11→S5-17 réunis) mérite une conception à part avant d'y mettre une estimation ou des jours précis. Pas de branche ni d'ID dans la séquence J1→J111 tant que ce n'est pas chiffré.

| ID | Issue | Pourquoi ce n'est pas encore chiffré |
|---|---|---|
| **BACKLOG-01** | `feat(api+client): détail de stationnement PAR VÉHICULE dans le moteur d'enquête (répétition d'un bloc de questions selon une réponse NOMBRE antérieure)` | Touche le modèle de données (suivre à quelle répétition appartient chaque réponse), le parcours répondant (génération dynamique de blocs), l'agrégation des résultats (regrouper avant de calculer les pourcentages), le constructeur admin (nouvelle interface de configuration), et son interaction avec le branchement déjà en place — un chantier à concevoir en détail avant de pouvoir l'estimer sérieusement. |
| **BACKLOG-02** | `feat(api+client): hiérarchie complète des rôles — admin mairie, salariés mairie, maisons de quartier (admins + délégués), circuit de validation avant publication` | Remplace le modèle binaire CITOYEN/ADMIN actuel par de vraies permissions granulaires (créer ≠ publier), vérifiées dans chaque contrôleur admin. Ajoute une entité "maison de quartier" qui n'existe pas encore. Ajoute un état intermédiaire "en attente de validation mairie" avec notification réelle. Ajoute un système d'invitation/délégation de droits (personne ne peut s'auto-déclarer salarié mairie). Comparable en ampleur à tout le moteur de branchement des enquêtes réuni — à concevoir en détail (notamment : le contenu d'une maison de quartier reste-t-il cloisonné, ou tout le monde voit tout une fois publié ?) avant de pouvoir l'estimer sérieusement. Un premier palier simple (promouvoir/rétrograder un compte admin, sans la hiérarchie) est déjà livré en S5-19. |
| **BACKLOG-03** | `refactor(client): chargement des données sans setState synchrone dans useEffect (règle react-hooks/set-state-in-effect, eslint-plugin-react-hooks 7)` | 18 avertissements, surtout le motif « setLoading(true) puis appel à l'API » au chargement d'une page. Piste : un hook commun (ex. `useApiQuery`) qui part d'un état « chargement » et ne met à jour l'état que dans les callbacks de la requête, ou une bibliothèque dédiée (TanStack Query). À faire en une fois, avec les tests de chaque page concernée ; la règle repasse ensuite en « error ». Constaté le 04/10/2026 (Dependabot : react-hooks 5 → 7). |

## 🏛️ Propositions Lot 3 — issues de la recette du 30/09/2026 (à présenter à la mairie)

> Évolutions de fond qui dépendent d'un budget, d'un service tiers ou d'un choix de la future responsable de traitement. Pas de branche ni de jours tant que la mairie ne les a pas retenues. Détail et remarques : `23-retours-recette-2026-09-30.md`, §5.

| ID | Proposition |
|---|---|
| L3-01 | Site multilingue (traduction assistée par IA, ex. Mistral) |
| L3-02 | Aide à la rédaction : orthographe et grammaire (IA), en suggestion |
| L3-03 | Lecture à voix haute plus naturelle (voix neuronales) |
| L3-04 | Le cerf comme curseur de la lecture au survol |
| L3-05 | Trouver son quartier depuis son adresse, non conservée (API Adresse de l'État) |
| L3-06 | Zonage plus fin qu'un quartier IRIS (zones dessinées par l'administratrice) |
| L3-07 | Second email avant la suppression d'un compte inactif |
| L3-08 | Connexion simplifiée : FranceConnect, QR code |
| L3-09 | Notifications ciblées selon le profil, annonce des résultats |
| L3-10 | Tableau de bord d'administration (connexions, emails envoyés) |

> Lien avec **BACKLOG-01** : S5R-05 apporte une version simple de la demande « pas plus de places de stationnement cochées que de véhicules » (nombre de choix max lié à une réponse). Le détail **par véhicule** reste BACKLOG-01.

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
| 10 | S5 Sécurité/access. | S5-05 → S5-10 | Release v1.0.0 (tag) — mascotte animée |
| 11 | S5bis Revue enquêtes | S5-11 → S5-15 | Schéma, situation + quartier, publication des résultats, branchement |
| 12 | S5bis Revue enquêtes | S5-16 → S5-18 | Constructeur, enquête stationnement v2, ville avec suggestions |
| 13 | S5bis + S5 audit | S5-19 → S5-21, S5A-01 → S5A-02 | Comptes admin, appareil de confiance, résultats segmentés · **failles critiques corrigées** |
| 14 | S5 audit | S5A-03 → S5A-07 | Polices locales, pages légales exactes, droits RGPD, auth durcie, RGAA |
| 15 | S5 audit + S5 recette | S5A-08, S5R-01 → S5R-04 | Préparation prod · **vérification d'email fiable**, formulaires, accessibilité, DNS de l'email, accueil |
| 16 | S5 recette | S5R-05 → S5R-08 | Moteur d'enquête v2, enquête stationnement v3, public visé, résultats et export |
| 17 | S5 recette | S5R-08 → S5R-12 | Création d'enquête, zones de proposition, carte, navigation admin |
| 18 | S5 recette + S5ter | S5R-13, S5-22 → S5-25 | NIST · domaine, hébergement, sauvegardes, chiffrement |
| 19 | S5ter + S6 | S5-25, S6-01 → S6-03 | **🚀 Lot 1 réellement en ligne** · API commentaires, modération |
| 20 | S6 Commentaires | S6-03 → S6-05 | Débat en colonnes, formulaire, file de modération |
| 21 | S6 + S7 | S6-06, S7-01 → S7-02 | Modération testée · propositions citoyennes |
| 22 | S7 Participation | S7-03 → S7-07 | Notifications, préférences, palier 3 |
| 23 | S7 Participation | S7-07 | **🎉 Release v2.0.0 — MVP complet + palier 3** |

## Règles de fonctionnement (rappel)

- **1 issue = 1 branche = 1 PR** vers `dev` (vérifier « base: dev » avant de créer la PR) ; merge `dev → main` + tag en fin de sprint = déploiement
- Limite WIP : **une seule** issue « En cours » à la fois
- Une issue qui dépasse son estimation de ×2 → on la découpe, on ne s'enterre pas
- Tout imprévu hors périmètre → nouvelle issue au Backlog avec `lot:` adéquat, jamais « vite fait au passage »
- Les estimations supposent des journées pleines : à mi-temps, le calendrier double mais l'ordre ne change pas
- Les issues `joy:palier-*` sont des issues front pur — elles ne bloquent jamais une issue API ou BDD
- Livraison : un zip `senlis-<ID>-<résumé>.zip` par issue, arborescence exacte du dépôt, seulement les fichiers touchés ; tests vérifiés et mis à jour à chaque issue
