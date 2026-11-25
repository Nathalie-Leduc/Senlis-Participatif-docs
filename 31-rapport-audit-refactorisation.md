# 31 — Rapport d'audit et de refactorisation avant la mise en ligne (S5R2-08)

> **État : terminé** (23–24/11/2026). Partie 1 : chargement des données ; partie 2 : dépendances ; partie 3 : code mort et doublons, sécurité (grille **OWASP Top 10:2025**), performance (Lighthouse).
>
> **Pourquoi cet audit ?** Avant d'ouvrir le site au public, on relit tout le code d'un œil neuf, comme on fait le tour d'une maison avant de rendre les clés : ce qui fuit, ce qui est usé, ce qui a été bricolé en cours de chantier. Chaque constat ci-dessous a reçu un correctif **et** un test qui empêche la rechute.

---

## 1. Méthode

| Étape | Outil | Ce qu'on cherche |
|---|---|---|
| Lecture du code | ESLint 10 + règles React (`react-hooks`) | Mauvaises pratiques détectables automatiquement |
| Dépendances | `npm outdated`, `npm audit` | Versions dépassées, failles connues |
| Comportement | Vitest (API et client), Playwright (captures) | Ce qui marche « par chance » et casserait au premier changement |
| Recette | Essais de Nath sur le téléporteur | Ce que seul un humain remarque (libellés, parcours, retours en arrière) |
| Code mort et doublons | `knip` (fichiers, exports, dépendances), `jscpd` (copier-coller) | Code inutile, copies qui divergent |
| Sécurité | Grille **OWASP Top 10:2025**, relecture des routes, essais d'attaque dans les tests | Failles et mauvaises configurations |
| Performance | **Lighthouse 12** (profil mobile), poids du build | Lenteurs, décalages de mise en page, accessibilité, SEO |

**Chiffres des tests** : API **402 → 417**, client **345 → 372** (au 25/11/2026).

---

## 2. Partie 1 — Chargement des données (BACKLOG-03) ✅ *23/11/2026*

### Constat

Quatorze pages et composants répétaient chacun à leur manière : « je passe en chargement, j'appelle l'API, je range la réponse ». ESLint 9 signalait **18 avertissements** `react-hooks/set-state-in-effect` (un état modifié en plein effet : rendu en trop, risque de boucle). Plus grave, certaines pages **n'ignoraient pas une réponse arrivée trop tard** : en changeant vite de filtre, l'ancienne liste pouvait écraser la nouvelle.

### Correctif

Un hook commun, `client/src/hooks/useApiQuery.js` :

- `useApiQuery(path)` → `{ data, error, loading, reload, setData }` ;
- `usePagedApiQuery(makePath)` → listes avec « Voir plus » (les pages ajoutées sont rattachées à la première adresse : un changement de filtre les oublie d'elles-mêmes).

L'état « chargement » n'est plus stocké mais **déduit** : si la réponse posée sur le bureau ne correspond pas à la question du moment, c'est qu'on cherche encore. La règle `set-state-in-effect` est repassée en **erreur** : la CI refuse toute rechute.

### Défauts trouvés en route

| Défaut | Effet pour le citoyen | Correctif |
|---|---|---|
| Erreur de vote jamais affichée (ex. votes clos entre-temps) | Le clic semblait sans effet | Message sous les boutons de vote |
| Réponse tardive non ignorée dans le questionnaire | Une ancienne enquête pouvait s'afficher | Garde `cancelled` |
| Clic sur une ville suggérée (erreur de la conversion, attrapée par le lint avant livraison) | La page aurait planté | Corrigé + test |
| Page d'une proposition **sans aucun test** | — | Tests : chargement, introuvable, vote, erreur, vote rejoué après connexion |

---

## 3. Partie 2 — Dépendances ✅ *23–24/11/2026*

### Résultat : `npm audit` → **0 faille** (4 « hautes » avant)

| Paquet | Avant → après | Raison |
|---|---|---|
| `zod` | 3 → **4.6.7** | Faille de consommation mémoire (GHSA-mrhx-c7xc-m3w5), signalée au document 21 § 7 |
| `effect` (via la CLI Prisma) | 3.20 → **3.22.5** | ReDoS (GHSA-7589-6fvf-x4pw) ; Prisma l'épingle même en 7.10 → `overrides` dans le `package.json` racine, **à retirer quand Prisma suivra** |
| `eslint`, `@eslint/js` | 9 → **10** | Version maintenue ; `eslint-plugin-react` **retiré** (incompatible, et ESLint 10 comprend le JSX seul) |
| `express-rate-limit` | 7 → **8** | Les adresses IPv6 d'une même box comptent ensemble ; option `max` renommée `limit` |
| `vitest` / `jsdom` / `@testing-library/jest-dom` | 4 / 26 / 6 → **5 / 31 / 8** | Outils de test à jour |
| `dotenv`, `globals` | 17 / 16 → **18 / 17** | — |
| vite, sass, react-router, nodemailer, argon2, supertest | correctifs | Sans rupture |
| `prisma` | **7.10.1 conservé** | Prisma 8 n'existe qu'en version candidate (rc) |

### Trois pièges évités

1. **Zod 4 ignore en silence les options de Zod 3** (`required_error`, `invalid_type_error`, `errorMap`). Les 402 tests restaient verts alors que des messages étaient devenus **anglais** (« Invalid input: expected string, received undefined » au lieu de « Le mot de passe est requis »). Aucun test ne lisait le texte des messages. → Options converties (`error`), réglage commun `api/src/lib/zod.js` (messages par défaut en français : « Ce champ est obligatoire », « Date invalide », puis `z.locales.fr`), unions « null ou X » remplacées par `.nullable()` (Zod 4 n'en rapportait plus que « Entrée invalide »), et nouveau test `validation-messages.test.js` qui **lit** les messages.
2. **dotenv 18 apporte sa propre commande `dotenv`**, qui masquait celle de `dotenv-cli` : `npm run migrate:test` n'affichait plus que l'aide. → Le script appelle `dotenv-cli` par son nom complet.
3. **Un fichier verrou mal nettoyé** : mon `package-lock.json` gardait 31 paquets orphelins (l'ancien jsdom 26) ; le `npm i` de Nath les a retirés à juste titre. → Désormais, un zip ne contient le fichier verrou que si une dépendance change, nettoyé avant.

### Bonus : tests du client 4 fois plus rapides

Vitest 5 signalait que le navigateur simulé (jsdom) était reconstruit pour **chacun** des 67 fichiers, soit la moitié du temps. Avec `pool: 'vmThreads'` (`client/vite.config.js`), chaque fichier reste isolé mais jsdom n'est préparé qu'une fois par fil : **≈ 50 s → 12 s** dans le bac à sable (≈ 80 s → une vingtaine de secondes attendues sur le téléporteur).

### Bonus : tests de l'API 4 fois plus rapides *(recette du 25/11)*

La suite de l'API prenait **186 s** sur le téléporteur. Cause principale : **Argon2**, volontairement lent (≈ 170 ms par inscription et connexion), recalculé pour chacune des centaines de comptes créés par les tests. Réglage léger (1 Mio, 1 passe : ≈ 2 ms) **uniquement quand `NODE_ENV === 'test'`**, dans un seul fichier (`api/src/lib/argon2Options.js`). Le développement et la production gardent les paramètres forts (argon2id, 64 Mio, 3 passes), et un test le vérifie. La vérification n'a besoin d'aucun réglage, car les paramètres sont écrits dans l'empreinte. Mesure dans le bac à sable : **114 s → 33 s**.

Au passage, l'avertissement « Both esbuild and oxc options were set » des tests du client a disparu : le réglage `esbuild` de `vite.config.js` ne servait plus qu'à Vitest 4.

### Version de Node

jsdom 31 exige **Node 22.22.2+ ou 24.15+**. La CI (Node 22, dernière version) et la production (jsdom n'y est pas installé) ne sont pas concernées ; le téléporteur (24.11) a été mis à jour avec `nvm install 24`.

---

## 4. Défauts remontés par la recette pendant l'audit ✅ *23–24/11/2026*

| Constat de Nath | Cause | Correctif |
|---|---|---|
| Résultats détaillés d'une enquête : le panneau des profils reste ouvert une fois le filtre appliqué | Aucun repli prévu | Panneau replié, rappel « Filtre appliqué : … » et bouton « Modifier le filtre » |
| Revenir sur une proposition déjà votée puis cliquer un autre choix change le vote sans prévenir | Le vote est un « upsert » (jamais deux votes, mais aucun message) | Rappel « Vous avez voté « Pour » » et confirmation « Voulez-vous changer votre vote ? » (Oui / Non), aussi pour un vote rejoué après connexion ; règle « un vote par proposition » confirmée |
| « 4,7 voitures » accepté, puis « Jusqu'à 4.7 réponses » | Rien n'imposait un entier | Réponses « Nombre » **entières** : message sous le champ, `step="1"`, clavier numérique ; l'API refuse aussi (validateur + moteur) ; bornes de l'admin entières |
| Situation changée dans un questionnaire, mais pas dans « Mon compte » | Le profil n'est mis à jour qu'**à l'envoi**, et le site gardait l'ancien profil en mémoire | Profil rafraîchi après l'envoi ; message « sera mis à jour quand vous enverrez le questionnaire » ; écran de fin « Votre profil a aussi été mis à jour » |
| En revenant sur la question, l'ancienne valeur réapparaît | La lecture seule montrait le **profil**, pas la réponse | Lecture seule seulement si la réponse est toujours celle du profil |

Piège évité au passage : rafraîchir le profil rechargeait l'enquête (elle dépendait de tout l'objet `user`) et affichait « Vous avez déjà répondu » à la place du merci. Le chargement ne dépend plus que de la **personne** (son id).

---

## 5. Partie 3 — Code mort, sécurité, performance ✅ *24/11/2026*

### 5.1 Code mort et doublons

`knip` ne trouve **aucun fichier inutilisé**. Sur les 3 dépendances et 30 exports signalés :

| Signalé | Verdict |
|---|---|
| `@prisma/client` | Fausse alerte : exigé par `prisma generate` (générateur `prisma-client-js`) |
| `@fontsource-variable/*` | Fausse alerte : utilisées par `_fonts.scss`, que l'outil ne lit pas |
| 28 exports (constantes, fonctions) | Utilisés **dans leur propre fichier** : le mot `export` est superflu, pas le code. Laissés tels quels |
| `resetIrisCache()` | **Supprimée** : annoncée « pour les tests », aucun test ne l'utilisait |
| `loadParkings()` d'`osmParkings.js` | **Supprimée** : lisait des fichiers OpenStreetMap retirés depuis S5R2-05 (le reste du fichier est gardé, décision S5R2-05) |

`jscpd` mesure **0,79 % de code dupliqué** (5 copies) :

| Copie | Verdict |
|---|---|
| Moteur de parcours des enquêtes (API ↔ client) | **Voulue** : les deux côtés doivent appliquer les mêmes règles ; `surveyFlow.test.js` vérifie que le bloc est identique |
| Fiche publique du compte, recopiée champ par champ (connexion, double authentification, `/auth/me`) | **Corrigée** : les copies avaient déjà divergé (3 champs de plus dans `/auth/me`). Une liste blanche unique, `toPublicUser()` (voir A01) |
| Filtres et listes des pages d'administration, des pages Propositions et Enquêtes | Tolérées : présentation proche, logique différente ; un composant commun coûterait plus qu'il ne rapporte avant la mise en ligne |

### 5.2 Sécurité — grille OWASP Top 10:2025

> L'OWASP Top 10 est la liste de référence des risques des applications web, établie par la fondation OWASP à partir de données réelles. L'édition **2025** remplace celle de 2021 : deux catégories nouvelles (A03 chaîne d'approvisionnement, A10 conditions exceptionnelles), la SSRF fusionnée dans A01.

| Risque | Ce qui est en place | Trouvé et corrigé en partie 3 | Reste / décision |
|---|---|---|---|
| **A01 Contrôle d'accès défaillant** (SSRF comprise) | `auth`, `isStaff`, `isAdmin`, `canEditDrafts` sur chaque route d'écriture ; brouillons invisibles (404, sans révéler leur existence) ; un vote, une réponse par personne garantis par la base ; on ne peut pas se retirer son propre rôle d'admin ; aucune adresse fournie par l'utilisateur n'est appelée par le serveur (pas de SSRF) | Fiche du compte envoyée au navigateur par **liste blanche** (`toPublicUser`), testée : aucun champ interne (`passwordHash`, `tokenVersion`…) ne peut sortir par oubli | — |
| **A02 Mauvaise configuration** | Helmet, CORS limité au site, corps JSON limités (10 ko / 100 ko), erreurs masquées en production, `.env` hors de Git, pas de source maps en production, configuration vérifiée au démarrage | Adresse d'API inconnue → **404 en JSON** (au lieu de la page HTML d'Express) | En-têtes CSP et HSTS du site statique : **S5-24**, avec l'hébergeur |
| **A03 Chaîne d'approvisionnement logicielle** | `npm audit` **à 0** (partie 2), fichier verrou + `npm ci`, Dependabot (npm et actions GitHub) | Audit de la CI **bloquant dès « élevé »** (avant : « critique » seulement) | `overrides` de `effect` à retirer quand Prisma suivra |
| **A04 Défaillances cryptographiques** | Argon2id, JWT HS256 avec algorithme imposé à la vérification, jetons d'email et de 2FA stockés en empreinte, adresses des limites par compte en HMAC, HTTPS chez l'hébergeur | En production, **`JWT_SECRET` de moins de 32 caractères refusé au démarrage** (un secret court se devine hors ligne et permet de fabriquer un jeton d'admin) | Changer tous les secrets avant la mise en ligne : **S5-22** |
| **A05 Injection** | Prisma (requêtes paramétrées ; les 2 requêtes SQL brutes sont des gabarits paramétrés) ; Zod sur toutes les entrées ; React échappe tout le texte (aucun `dangerouslySetInnerHTML`) ; infobulles de la carte en React, icônes faites de constantes ; SSML échappé ; aucun texte saisi par un utilisateur dans les emails | — | — |
| **A06 Conception non sécurisée** | Limites de requêtes (globale, authentification, 10 essais par heure et par compte, voix) ; email vérifié avant de participer ; quota mensuel des voix | — | Pas de CAPTCHA à l'inscription : la vérification de l'email et les limites suffisent au lancement |
| **A07 Défaillances d'authentification** | Politique CNIL + NIST (S5R-13b), 2FA de l'équipe, révocation des sessions (`tokenVersion`), réponses identiques à la connexion et au « mot de passe oublié », hachage factice pour égaliser les temps | — | Énumération à l'**inscription** (« adresse déjà utilisée ») : **risque accepté** pour l'ergonomie (document 21) |
| **A08 Intégrité des logiciels et des données** | Jetons signés et vérifiés, images ré-encodées par Sharp (un fichier piégé n'est jamais servi tel quel), migrations versionnées | CI en **moindre privilège** (`permissions: contents: read`) : une dépendance piégée exécutée pendant `npm ci` ne peut rien pousser | Actions GitHub épinglées par version majeure (`@v4`), suivies par Dependabot |
| **A09 Journalisation et alertes** | Journal d'administration (qui, quoi, quand), alerte email au titulaire après des échecs de connexion, adresses masquées dans les journaux, texte lu jamais journalisé | Perte de connexion à PostgreSQL **écrite dans les journaux** (`onPoolError`) au lieu d'être avalée en silence | **Alertes d'exploitation** (site injoignable, pic d'erreurs 5xx) à régler chez l'hébergeur : **S5-24** |
| **A10 Conditions exceptionnelles** | Gestionnaire d'erreurs central, délai d'attente sur Azure avec repli, mise à jour du profil sans faire échouer un questionnaire | **Faux PNG** (type annoncé falsifié) → **400 `INVALID_IMAGE`** au lieu d'une 500 avec le message technique de Sharp ; **arrêt propre** du serveur (les requêtes en cours se terminent lors d'un déploiement) | — |

Tests ajoutés : `public-user.test.js` (liste blanche, 404 JSON), `validate-env.test.js` (secret court), `error-handling.test.js` (faux PNG). Arrêt propre vérifié à la main (signal `SIGTERM` sur un serveur lancé).

### 5.3 Performance — Lighthouse (profil mobile, build de production)

| Page | Performance | Accessibilité | Bonnes pratiques | SEO | Décalages (CLS) | Temps de blocage |
|---|---|---|---|---|---|---|
| Accueil | 76 → **92** | 100 | 96 → 100 | 91 | 0,001 | 490 → **60 ms** |
| Propositions | 82 → **94** | 98 → **100** | 100 | 91 | 0,265 → **0** | 50 → 80 ms |
| Une proposition | 89 → 91 | 100 | 96 | 91 | 0 | 130 → 80 ms |
| Une enquête | 94 → 95 | 100 | 100 | 91 | 0,014 → 0 | 30 → 40 ms |
| Carte | 87 → 85 | 98 → **100** | 96 | 91 | 0,175 → **0** | 50 → 300 ms |

Correctifs :

- **La carte de l'accueil n'est chargée qu'à l'approche** (`LazyMapView`, `IntersectionObserver`, 300 px d'avance) : Leaflet ne concurrence plus le haut de la page.
- **Le pied de page ne saute plus** : zone principale d'au moins un écran de haut (`#main { min-height: 100vh }`).
- **Page Carte** : la place de la carte est réservée pendant le chargement (une simple ligne de texte tenait la place d'une carte haute d'un écran).
- **Ordre des titres** (RGAA 9.1) : cartes de propositions en `h2` ; panneau « Par quartier » en `h2` sur la page Carte, `h3` sur l'accueil, et ses listes un niveau en dessous.

À savoir pour lire ces chiffres :

- Sur la page Carte, le temps de blocage vient maintenant de Leaflet, chargé d'emblée puisque la carte **est** le contenu. Les mesures varient de quelques points d'un passage à l'autre.
- « Bonnes pratiques 96 » : erreurs de console dues aux **tuiles de carte bloquées par le bac à sable** de mesure, pas au site.
- **SEO 91** partout : il manque `robots.txt`, prévu dans **S5-26** (référencement), dans l'ordre du kanban.
- Mesures faites avec `vite preview`, sans compression ni HTTP/2 : en production, le temps d'affichage (LCP, 2,4 à 3,1 s ici) devrait baisser.

## 6. Points à surveiller

| Point | Pourquoi | Quand le revoir |
|---|---|---|
| `overrides` de `effect` | Correctif imposé à Prisma | À chaque mise à jour de Prisma : le retirer si `npm audit` reste à 0 sans lui |
| Un échec isolé de `surveys.test.js` (« option qui n'appartient pas à la question ») | Survenu une fois juste après un redémarrage de PostgreSQL, **non reproduit** (15 essais sur 15 réussis) | S'il revient : relever le code HTTP reçu |
| Double appel des effets en développement | Normal avec `StrictMode` (React monte, démonte, remonte) | — |
| Secrets provisoires | `JWT_SECRET`, mots de passe de démonstration | **S5-22**, avant la mise en ligne |
| CSP, HSTS, alertes d'exploitation | Côté hébergeur | **S5-24** |
| `robots.txt` (SEO 91) | Prévu au référencement | **S5-26** |
| Mode `vmThreads` | Un test qui échouerait **seulement** dans ce mode (souvent un `instanceof` entre deux bacs à sable) | Comparer avec `npx vitest run --pool=threads` |

---

## 7. Leçons

1. **Des tests verts ne prouvent pas que le texte est juste** : il faut des tests qui lisent ce que voit le citoyen (messages, libellés).
2. **Une montée de version majeure se lit dans le guide de migration**, pas seulement dans le résultat des tests.
3. **Les parcours « aller-retour »** (revenir en arrière, revenir sur une page déjà vue) sont ceux qui cachent le plus de défauts : la recette humaine reste irremplaçable.
4. **Une copie qui « ne fait que recopier » finit toujours par diverger** : la fiche du compte avait déjà trois versions. Pour ce qui sort du serveur, une liste blanche unique.
5. **Mesurer avant de corriger** : Lighthouse a désigné le pied de page comme coupable des décalages, alors que la vraie cause était la carte, au-dessus.
