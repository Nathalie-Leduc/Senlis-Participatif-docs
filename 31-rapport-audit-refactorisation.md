# 31 — Rapport d'audit et de refactorisation avant la mise en ligne (S5R2-08)

> **État : en cours.** Parties 1 et 2 livrées (23–24/11/2026) ; la partie 3 (code mort, doublons, sécurité des routes récentes, Lighthouse) complétera ce rapport.
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
| Partie 3 *(à venir)* | Recherche de code mort, relecture des routes, Lighthouse | Fichiers inutilisés, doublons, failles, lenteurs |

**Chiffres des tests** : API **402 → 410**, client **345 → 370** (au 24/11/2026).

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

## 5. Partie 3 — *à venir*

- Code mort et doublons (fichiers, fonctions, styles inutilisés).
- Relecture de sécurité des routes récentes : lecture vocale (`/tts`), parkings, authentification (limites par compte).
- Performance : Lighthouse (accueil, propositions, carte, enquête) et poids du bundle.

---

## 6. Points à surveiller

| Point | Pourquoi | Quand le revoir |
|---|---|---|
| `overrides` de `effect` | Correctif imposé à Prisma | À chaque mise à jour de Prisma : le retirer si `npm audit` reste à 0 sans lui |
| Un échec isolé de `surveys.test.js` (« option qui n'appartient pas à la question ») | Survenu une fois juste après un redémarrage de PostgreSQL, **non reproduit** (15 essais sur 15 réussis) | S'il revient : relever le code HTTP reçu |
| Double appel des effets en développement | Normal avec `StrictMode` (React monte, démonte, remonte) | — |
| Mode `vmThreads` | Un test qui échouerait **seulement** dans ce mode (souvent un `instanceof` entre deux bacs à sable) | Comparer avec `npx vitest run --pool=threads` |

---

## 7. Leçons

1. **Des tests verts ne prouvent pas que le texte est juste** : il faut des tests qui lisent ce que voit le citoyen (messages, libellés).
2. **Une montée de version majeure se lit dans le guide de migration**, pas seulement dans le résultat des tests.
3. **Les parcours « aller-retour »** (revenir en arrière, revenir sur une page déjà vue) sont ceux qui cachent le plus de défauts : la recette humaine reste irremplaçable.
