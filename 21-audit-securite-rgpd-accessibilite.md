# Audit sécurité, RGPD et accessibilité — Senlis Participatif

> Audit du **23/09/2026**, réalisé sur la branche `dev` du dépôt de code (dernier commit du 23/09, 09h55), **avant la mise en ligne réelle** (Sprint 5ter).
> Référentiels : **OWASP Top 10 (2021)** et ASVS 4.0 · **ANSSI** (recommandations pour la sécurisation des sites web, guide d'hygiène) · **CNIL** (RGPD, délibération 2020-091 sur les traceurs, 2022-100 sur les mots de passe, recommandation du 14/04/2026 sur les pixels de suivi) · **RGAA 4.1** (WCAG 2.1 AA).
> Chaque constat renvoie à une issue du kanban (`16-kanban-issues-complet.md`, v1.10).

**Gravité** : 🔴 bloquant avant mise en ligne · 🟠 important · 🟡 amélioration · ✅ conforme

> 🎓 **Analogie de lecture** : un audit, c'est la visite du contrôleur technique. Il ne dit pas « la voiture est mauvaise » ; il liste ce qui empêche de passer (🔴), ce qu'il faudra réparer vite (🟠) et les conseils (🟡). Ici, la carrosserie et le moteur sont sains ; ce sont surtout des freins et des feux à régler.

---

## 1. Synthèse

| Domaine | Verdict | Points 🔴 | Issues |
|---|---|:--:|---|
| Sécurité applicative (OWASP / ANSSI) | Socle solide — faille de contrôle d'accès corrigée (S5A-01) | 0 | S5A-01, S5A-02, S5A-06, S5A-08 |
| Données personnelles (RGPD / CNIL) | Bonne conception (pseudonymisation), Google Fonts retiré ; information des personnes incomplète | 1 | S5A-03, S5A-04, S5A-05, S5-21 |
| Cookies et consentement | ✅ Aucun bandeau nécessaire (Google Fonts retiré par S5A-03) | 0 | S5A-03 ✅ |
| Accessibilité (RGAA) | Bonne base (skip link, widget, reduced-motion), critères de structure manquants | 0 | S5A-07 |
| Mise en production | 3 points bloqueraient le site en ligne | 1 | S5A-08, S5-22, S5-24 |

---

## 2. Ce qui est déjà conforme ✅

- **Mots de passe** : Argon2id (paramètres par défaut de `argon2` : 64 Mio, 3 itérations — au-dessus du minimum OWASP), 12 caractères et 4 familles (CNIL 2022-100), refus d'un nouveau mot de passe identique.
- **Jetons email** : 32 octets aléatoires, seule l'empreinte SHA-256 est stockée, usage unique, expiration 60 min ; code 2FA tiré par `crypto.randomInt`, 10 min.
- **Anti-énumération** au login et au « mot de passe oublié » (même réponse que le compte existe ou non).
- **Validation Zod** systématique, corps JSON limité à 10 ko, requêtes Prisma paramétrées (pas d'injection SQL).
- **Helmet, CORS restreint, rate limiting** global (100/min) et auth (10/15 min).
- **Erreurs** : pas de stack trace renvoyée en production.
- **2FA par email** pour les admins, appareil de confiance limité à 1 h.
- **RGPD by design** : pseudo public / email privé, votes supprimés et réponses anonymisées (`SET NULL`) à la suppression du compte, emails vérifiés, aucun pixel de suivi.
- **Images** : ré-encodage Sharp en WebP → les métadonnées EXIF (dont la position GPS de la photo) sont supprimées.
- **Configuration** : `validateEnv.js` refuse de démarrer en production avec un secret d'exemple ou Mailtrap.
- **Accessibilité** : `lang="fr"`, skip link, widget d'accessibilité, `prefers-reduced-motion` respecté, boutons de vote ≥ 48 px avec `aria-pressed`, `alt` sur les images, contrastes ≥ 4,5:1 pour les couples texte/fond de la palette relevés dans le code (vérification outillée page par page à refaire avec axe DevTools lors de S5A-07).

---

## 3. Sécurité applicative (OWASP / ANSSI)

### ✅ 3.1 Rôle administrateur lu dans le JWT — S5A-01 (OWASP A01 Contrôle d'accès) — *corrigé le 24/09/2026*

**Constat** : `middlewares/auth.js > isAdmin` vérifie `req.user.role`, qui vient du JWT signé à la connexion et valable **7 h**. Depuis S5-19 (rétrograder un admin), un admin rétrogradé garde tous ses droits jusqu'à l'expiration de son jeton. De même, un compte supprimé garde un jeton valide.

**Analogie** : retirer le badge de quelqu'un dans le registre sans désactiver la puce du badge — les portes s'ouvrent encore.

**Correctif appliqué** : `auth` et `optionalAuth` relisent le compte en base (une lecture par clé primaire, qui remplace celle que faisait déjà `requireVerifiedEmail` — même nombre de requêtes qu'avant sur les routes de participation) ; le rôle et `emailVerified` viennent de la base, jamais du jeton ; un compte supprimé → 401 (ou visiteur anonyme sur une route publique) ; signature et vérification épinglées en HS256. **Tests** (`api/tests/access-control.test.js`, 11 tests) : 7 d'entre eux échouent sur l'ancien code — la faille est démontrée, puis corrigée.

### ✅ 3.2 Erreurs de base de données renvoyées en 500 — S5A-02 — *corrigé le 25/09/2026*

- Pseudo déjà pris à l'inscription ou dans « Mon compte » → violation d'unicité Prisma `P2002` non traduite → **500** au lieu de 409.
- Image > 5 Mo ou mauvais format → erreur Multer sans `status` → **500** au lieu de 400/413.

**Correctif appliqué** : traduction centralisée dans `errorHandler.js` (`P2002` → 409 `EMAIL_TAKEN` / `PSEUDO_TAKEN` / `CONFLICT`, `P2025` → 404, `MulterError` → 400/413, JSON mal formé → 400, trop gros → 413) ; une 500 ne renvoie plus de code interne Prisma. Même issue : le profil travail est désormais enregistré à l'inscription, et `users.tests.js` (jamais exécuté) est renommé, avec un test garde-fou sur le nommage.

### 🟠 3.3 Durcissement de l'authentification — S5A-06 (OWASP A07, ASVS V2/V3, ANSSI)

| Constat | Risque | Correctif proposé |
|---|---|---|
| Code 2FA sans compteur d'essais par code (seul le rate limit IP protège) | Force brute distribuée sur 10⁶ codes | Invalider le code après 5 essais ratés |
| Changement d'email sans ressaisie du mot de passe | Session volée → prise de contrôle du compte (le lien de reset part à la nouvelle adresse) | `currentPassword` exigé |
| Suppression de compte sans ressaisie du mot de passe | Session volée → effacement | `currentPassword` exigé |
| Aucune révocation des sessions après reset/changement de mot de passe | Un JWT volé reste valide 7 h | Champ `tokenVersion` sur `User`, inclus dans le JWT et comparé par `auth` (migration Prisma) |
| Aucune trace des actions admin (changement de rôle, suppression, publication) | Pas d'imputabilité (ANSSI, CNIL 2021-122 sur la journalisation) | Journal applicatif structuré, sans donnée superflue, conservé 6 mois |
| Inscription : « email déjà utilisé » (409) et login plus rapide pour un email inconnu | Énumération des comptes | Accepté pour l'UX à l'inscription (documenté) ; hash factice au login pour égaliser les temps |

### 🟡 3.4 Divers

- `verify-email` et `reset-password` sans rate limit dédié (jetons de 256 bits : risque faible) → ajouter `authLimiter` (S5A-06).
- `nodemailer` présent dans les dépendances **du client** : inutile, surface d'attaque supply-chain (S5A-03).
- Pas d'`npm audit` ni de Dependabot en CI (OWASP A06 Composants vulnérables) → S5A-08.
- JWT en `localStorage` : exposé en cas de XSS. Compromis acceptable pour une API consommable par mobile, **à condition** d'avoir une CSP stricte sur le site statique (S5-24).
- Logs `📧 Email envoyé à <adresse>` : donnée personnelle dans les journaux → masquer (`n***@domaine.fr`) (S5A-06).

---

## 4. Données personnelles (RGPD / CNIL)

### ✅ 4.1 Google Fonts chargé depuis les serveurs de Google — S5A-03 — *corrigé le 25/09/2026*

**Constat** : `client/index.html` charge Fraunces et Public Sans depuis `fonts.googleapis.com`. Chaque visiteur transmet son **adresse IP à Google (États-Unis)** sans base légale ni information — pratique sanctionnée en Europe (tribunal de Munich, 2022) et régulièrement épinglée par les autorités de protection des données.

**Correctif appliqué** : polices auto-hébergées via `@fontsource-variable/fraunces` et `@fontsource-variable/public-sans` (licence OFL), déclarées dans `client/src/styles/_fonts.scss` sous leurs noms historiques (aucune des 64 références existantes à modifier). Polices variables (un fichier pour toutes les graisses), sous-ensembles latin + latin-ext. Un test (`src/styles/fonts.test.js`) échoue si un serveur de polices tiers réapparaît dans `index.html` ou `src/`. Pour S5-24 : la future CSP pourra se limiter à `font-src 'self'`.

### 🔴 4.2 Politique de confidentialité inexacte et incomplète — S5A-04 (RGPD art. 12-14)

| Constat | Correction |
|---|---|
| « Intérêt légitime de la collectivité (art. 6.1.e) » : 6.1.e = **mission d'intérêt public**, réservé à une autorité publique ; l'intérêt légitime est le 6.1.f | Base : exécution du service demandé (6.1.b) pour le compte et la participation ; 6.1.e seulement si la mairie devient responsable de traitement (Lot 3) |
| Profil déclaré (résidence, quartier, lieu et type de travail) **non mentionné** | L'ajouter, avec sa finalité (segmenter les résultats) |
| « Horodatage de votre dernière connexion » : **n'existe pas** dans le schéma | Retirer (on ne décrit que ce qu'on fait) |
| Parle de « cookies » : l'application n'en pose aucun, elle utilise `localStorage` | Décrire les 3 clés (`token`, `trustedDeviceToken`, préférences d'accessibilité) et `sessionStorage` (vote en attente) |
| Destinataires absents | Hébergeur (Clever Cloud, France), Brevo (emails, France), OpenStreetMap (tuiles, IP transmise — Royaume-Uni, décision d'adéquation), `geo.api.gouv.fr` (DINUM) |
| Droits incomplets | Ajouter opposition, limitation, portabilité, **réclamation auprès de la CNIL** (mention obligatoire art. 13.2.d) |
| Durées en placeholder | Compte : jusqu'à suppression ou 3 ans d'inactivité ; jetons : purgés à expiration ; journaux : 6 mois |
| Mentions légales en placeholders (LCEN art. 6-III) | Éditeur, directrice de publication, hébergeur. Un éditeur **non professionnel** peut ne publier que les coordonnées de l'hébergeur, à condition d'avoir communiqué son identité à celui-ci |
| Pas de registre des traitements (art. 30) | Un registre simple (1 fiche : « participation citoyenne ») — document à ajouter au dépôt docs |
| Case « consentement » à l'inscription | La reformuler en « J'ai lu la politique de confidentialité » : la base légale est le contrat, pas le consentement (sinon le retrait du consentement devrait supprimer le compte) |

### 🟠 4.3 Droits d'accès, de portabilité, et durées de conservation — S5A-05

- Aucun export des données (art. 15 et 20) → `GET /auth/me/export` (JSON : profil, votes, réponses) + bouton dans « Mon compte ».
- Durée de conservation annoncée mais **aucune purge** → script `npm run purge` (comptes inactifs depuis 3 ans après un email d'avertissement, `AuthToken` expirés depuis > 24 h), lancé par un cron Clever Cloud.

### 🟠 4.4 Ré-identification par les résultats segmentés — S5-21 (volet restant)

Segmenter par profil (ex. « salarié·e·s de la Zone industrielle ») peut produire un segment d'**une seule personne** : ses réponses deviennent nominatives de fait. **Correctif** : masquer tout segment de moins de 5 répondants à l'écran et dans l'export (principe du secret statistique de l'INSEE). Les réponses `TEXTE_LIBRE` ne doivent pas figurer telles quelles dans un document remis à la mairie.

### 🟡 4.5 Points à trancher avant le Lot 3

- Votes et arguments sur des projets municipaux : opinions sur la vie locale, pas nécessairement des « opinions politiques » au sens de l'art. 9 — à faire valider par le DPO de la mairie si elle devient responsable de traitement (une AIPD pourra être demandée).
- Préférences de notification à `true` par défaut → passer à `false` (art. 25, protection des données par défaut) : noté dans S7-04.

---

## 5. Cookies et consentement ✅

| Stockage | Contenu | Exempté de consentement ? |
|---|---|---|
| `localStorage.token` | Session (JWT) | ✅ Authentification (CNIL 2020-091, art. 82 LIL) |
| `localStorage.trustedDeviceToken` | Appareil de confiance 2FA (1 h) | ✅ Sécurité de l'authentification |
| `localStorage` préférences d'accessibilité | Réglages choisis par l'utilisateur | ✅ Personnalisation de l'interface demandée par l'utilisateur |
| `sessionStorage` vote en attente | Vote rejoué après connexion | ✅ Service expressément demandé |
| ~~Google Fonts~~ | ~~IP transmise à un tiers~~ | ✅ supprimé (S5A-03) |
| Tuiles OSM | IP transmise, pas de cookie | Pas un traceur ; à mentionner dans la politique |

**Conclusion** : aucun bandeau de consentement n'est nécessaire. Ne **pas** en ajouter un « par précaution » : un bandeau inutile habitue les gens à cliquer sans lire.

---

## 6. Accessibilité (RGAA 4.1) — S5A-07

| Critère | Constat | Correctif |
|---|---|---|
| 8.6 Titre de page pertinent | Toutes les pages s'appellent « Senlis Participatif » | `<title>` par page (React 19 le gère nativement dans les composants) : « Propositions — Senlis Participatif » |
| 11.10 / 11.11 Contrôle de saisie | Aucun `aria-invalid` ni `aria-describedby` : les erreurs ne sont pas reliées aux champs | Lier chaque message d'erreur à son champ, focus sur le premier champ en erreur |
| 7.5 / 12.x Messages de statut, navigation SPA | Changer de page n'est pas annoncé aux lecteurs d'écran | Déplacer le focus sur le `<h1>` (ou une région `aria-live`) à chaque changement de route |
| 12.1 Deux systèmes de navigation | Menu seul | Page « Plan du site » liée en pied de page |
| Obligation de déclaration (loi 2005-102, art. 47) | Aucune déclaration ni mention en pied de page | Page « Accessibilité : partiellement conforme » + lien en pied de page — obligatoire dès que la mairie devient éditrice |
| 3.2 Contrastes | Code conforme ; **la charte** annonçait Doré + texte blanc à 3,1:1 (réel : 2,2:1) | Charte corrigée (document 09) |
| 1.x Carte Leaflet | Alternative textuelle existante (liste des propositions) | ✅ |

---

## 7. Mise en production — S5A-08 (et S5-22, S5-24)

| Constat | Effet en ligne | Correctif |
|---|---|---|
| Pas de `app.set('trust proxy', 1)` | Derrière le proxy Clever Cloud, **tous les visiteurs partagent la même IP** : 100 requêtes/min pour tout le site, puis 429 général | `trust proxy` en production |
| Images en `src="/uploads/…"` relatif | Sur `senlis-participatif.fr` (site statique), l'image est cherchée au mauvais endroit → 404 (ça ne marche en dev que grâce au proxy Vite) | Préfixer par l'origine de l'API (variable `VITE_API_ORIGIN`) |
| Helmet : `Cross-Origin-Resource-Policy: same-origin` | Même avec la bonne URL, le navigateur bloque une image venant de `api.senlis-participatif.fr` | `crossOriginResourcePolicy: { policy: 'same-site' }` |
| Tuiles `{s}.tile.openstreetmap.org` | Sous-domaines dépréciés par OSM | `https://tile.openstreetmap.org/{z}/{x}/{y}.png` |
| `docker-compose.yml` : API sans `JWT_SECRET` ; PostgreSQL exposé sur toutes les interfaces | L'API plante au démarrage (`validateEnv`) ; base joignable depuis le réseau local | Ajouter les variables ; `127.0.0.1:5432:5432` |
| `Dockerfile` : `npm ci --omit=dev` alors que `postinstall` appelle `prisma` (devDependency) ; conteneur en root | Build cassé ; principe du moindre privilège non respecté (ANSSI) | Build multi-étapes + `USER node` (Docker = dev uniquement, Clever Cloud déploie sans Docker) |
| Pas d'en-têtes de sécurité sur le site statique (CSP, HSTS…) | Helmet ne protège que l'API | Configuré avec l'hébergeur en S5-24 |

---

## 8. Documentation

- Swagger est annoncé (`README-api`, cahier des charges) mais **n'est pas implémenté** : cahier des charges v1.4 corrigé (« prévu »), README-api à corriger dans la PR de S5A-08.
- README racine : `develop` → `dev`, Railway → Clever Cloud (même PR).
