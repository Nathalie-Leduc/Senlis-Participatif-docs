# Dépannage — terminal, Git, npm, Prisma, VS Code

> Fiche récapitulative des problèmes **réellement rencontrés** pendant le projet (septembre–octobre 2026), avec leur cause et la solution appliquée. À relire **avant** de chercher sur Internet : la plupart des blocages se répètent.
>
> Format de chaque fiche : **Symptôme** (ce qu'on voit) → **Cause** (pourquoi) → **Solution** (quoi taper) → **Prévention** (pour que ça ne revienne pas).
>
> 🎓 **Méthode générale** : face à une erreur, lire la **première** ligne d'erreur, pas la dernière. La dernière décrit souvent une conséquence (« 119 tests en échec ») ; la première, la cause (« Unknown argument `lastLoginAt` »).

---

## Sommaire

| # | Domaine | Problème |
|:--:|---|---|
| 1 | Git / SSH | `UNPROTECTED PRIVATE KEY FILE` — `Permission denied (publickey)` |
| 2 | Git | `object file … is empty` — `reference is not a tree` |
| 3 | Git | Fichier à supprimer qu'un zip ne peut pas supprimer |
| 4 | Git | Conflit sur `package-lock.json` |
| 5 | GitHub | PR ouverte vers la mauvaise branche |
| 6 | npm | Message « New version of npm available » |
| 7 | npm | Vulnérabilités que `npm audit fix` ne corrige pas |
| 8 | npm | `npm audit fix --force` propose de passer à Prisma 6 |
| 9 | npm | Avertissements `install-scripts … not covered by allowScripts` et `deprecated` |
| 10 | Prisma | 119 tests en échec : `Unknown argument` après une migration |
| 11 | PostgreSQL | Erreurs PostgreSQL en français → mauvais code d'erreur |
| 12 | PostgreSQL / Docker | `service "postgres" is not running` — mot de passe `psql` redemandé |
| 13 | Zip | Fichiers cachés (`.dockerignore`) introuvables après `unzip` |
| 14 | VS Code | Plus de couleurs (jaune / vert) sur les fichiers modifiés |
| 15 | VS Code / ESLint | Une erreur de syntaxe passe le lint |
| 16 | Navigateur | Avertissements CSS de Leaflet et message React DevTools |
| 17 | OVH | Où créer la redirection `contact@senlis-participatif.fr` |

---

## 1. Git / SSH — clé privée « trop ouverte »

**Symptôme**
```
WARNING: UNPROTECTED PRIVATE KEY FILE!
Permissions 0760 for '/home/student/.ssh/id_ed25519' are too open.
git@github.com: Permission denied (publickey).
```

**Cause** — Les droits `0760` laissaient le **groupe** lire la clé privée. SSH refuse alors de l'utiliser (sécurité), et GitHub ne reconnaît plus la machine. Analogie : un serrurier qui refuse une clé dont un double traîne dans le couloir.

**Solution**
```bash
chmod 700 ~/.ssh                  # le dossier : toi seule
chmod 600 ~/.ssh/id_ed25519       # la clé PRIVÉE : lecture/écriture pour toi seule
chmod 644 ~/.ssh/id_ed25519.pub   # la clé publique : lisible par tous, c'est normal
ssh -T git@github.com             # → « Hi Nathalie-Leduc! You've successfully authenticated… »
```

**Prévention** — Après une copie ou une restauration du dossier `~/.ssh`, revérifier avec `ls -l ~/.ssh`.

---

## 2. Git — objet corrompu (« object file … is empty »)

**Symptôme**
```
error: object file .git/objects/aa/8f3b3e… is empty
fatal: reference is not a tree: dev
error: … did not send all necessary objects
```

**Cause** — Un fichier interne de Git a été créé **vide** : écriture interrompue par un **disque plein** ou une **extinction brutale** pendant un commit ou un pull. La branche pointe vers ce fichier vide, Git ne peut plus rien faire. Le code n'est pas perdu : il est sur GitHub.

**Solution** (plan A, celui qui a fonctionné)
```bash
df -h /var/www/html                                  # 0. espace disque (Use% proche de 100 % = cause)
cp -a /var/www/html/Senlis-Participatif ~/Senlis-Participatif-sauvegarde-$(date +%Y%m%d)   # 1. sauvegarde
cd /var/www/html/Senlis-Participatif
find .git/objects -type f -empty -delete             # 2. supprimer les objets vides
git fetch --refetch origin                           # 3. tout redemander à GitHub
git checkout -f dev && git reset --hard origin/dev   # 4. dev = exactement GitHub
git fsck --full                                      # 5. vérifier (les « dangling » sont sans gravité)
```

**Plan B** (si le A échoue) : renommer le dossier, refaire un `git clone`, puis recopier ce que GitHub n'a pas — `api/.env`, `api/.env.test`, `client/.env`, `api/uploads/`.

**Prévention** — Surveiller l'espace disque (`df -h`) ; ne pas éteindre la machine virtuelle pendant une commande Git ; pousser régulièrement (`git push`), pour que GitHub ait toujours une copie à jour.

---

## 3. Git — supprimer un fichier qu'un zip ne peut pas supprimer

**Symptôme** — Après S5A-02, `users.tests.js` (ancien nom) restait à côté de `users.test.js` (nouveau nom).

**Cause** — Un zip **ajoute ou remplace** des fichiers ; il ne sait pas en **supprimer**.

**Solution**
```bash
git rm api/tests/users.tests.js     # supprime du disque ET de Git en une commande
```

**Prévention** — Quand une livraison renomme ou supprime un fichier, les commandes l'indiquent explicitement (`git rm` ou `git mv`).

---

## 4. Git — conflit sur `package-lock.json`

**Symptôme** — Deux branches ont modifié les dépendances (ex. S5R-01 et `chore/nodemailer-10`) : conflit au merge sur le lockfile.

**Cause** — Le lockfile est généré par npm : il n'a pas vocation à être fusionné à la main.

**Solution**
```bash
# Pendant le merge en conflit, depuis la racine du projet :
npm install                 # npm recalcule un lockfile cohérent avec les deux package.json
git add package-lock.json
git commit                  # termine le merge
```

**Prévention** — Merger les PR de dépendances **une par une**, et repartir de `dev` à jour (`git checkout dev && git pull origin dev`) avant chaque nouvelle branche.

---

## 5. GitHub — PR vers la mauvaise branche

**Symptôme** — La PR proposait de fusionner vers `main` au lieu de `dev`.

**Cause** — GitHub mémorise la **dernière** branche de base utilisée.

**Solution** — Sur la page de création de la PR, vérifier le menu **« base: dev »** avant de cliquer « Create pull request » ; sur une PR déjà ouverte : bouton **Edit** à côté du titre → changer la base.

**Prévention** — Réflexe à chaque PR : relire « base: dev ← compare: ma-branche ».

---

## 6. npm — « New version of npm available »

**Symptôme**
```
npm notice New minor version of npm available! 11.6.1 -> 11.20.0
```

**Cause** — Simple information : npm (l'outil d'installation) a une version plus récente. Aucun effet sur le site.

**Solution** — Mettre à jour **entre deux issues**, jamais au milieu :
```bash
which npm                      # chemin avec « .nvm » → pas de sudo ; /usr/... → sudo
sudo npm install -g npm@11.20.0
npm -v
npm ci && (cd api && npm test) && (cd client && npm test)
git status                     # le lockfile ne doit pas avoir changé
```

**Prévention** — Si une nouvelle version de npm modifie le lockfile sans ajout de paquet, en faire un **commit à part** (`chore: lockfile régénéré avec npm x.y`).

---

## 7. npm — vulnérabilités que `npm audit fix` ne corrige pas

**Symptôme** — `npm audit` annonce « fix available via `npm audit fix` », mais `npm audit fix` répond « up to date » (cas `nodemailer <= 10.0.8`).

**Cause** — La correction n'existe que dans une **version majeure** plus récente (10.x), alors que `package.json` autorisait `^9.0.0`. `npm audit fix` respecte cette règle et ne franchit pas une version majeure. Analogie : le contrat couvre le modèle 2024 ; la pièce corrigée n'existe que sur le modèle 2025.

**Solution** — Lire les notes de version (« BREAKING CHANGES »), puis installer explicitement, sur une branche à part :
```bash
git switch -c chore/nodemailer-10
npm install nodemailer@^10.0.13 -w api
cd api && npm test && cd ..
npm audit                      # la ligne nodemailer a disparu
```

**Prévention** — Dependabot (S5A-08) ouvre chaque lundi une PR pour ces mises à jour.

---

## 8. npm — `npm audit fix --force` propose Prisma 6

**Symptôme**
```
Will install prisma@6.19.3, which is a breaking change
```

**Cause** — Les failles signalées (`mysql2`, `deepmerge-ts`, `@hono/node-server`, `valibot`) sont dans l'**outillage de la CLI Prisma 7**, jamais exécuté par l'API en fonctionnement. La seule « correction » automatique serait de redescendre en Prisma 6, ce qui casserait tout le projet.

**Solution** — **Ne jamais lancer `--force`.** Risque analysé et documenté (audit, document 21, §7) ; la CI bloque seulement sur « critique ».

**Prévention** — Viser des alertes **connues, analysées et suivies**, pas « zéro alerte ».

---

## 9. npm — avertissements `install-scripts` et `deprecated`

**Symptôme**
```
npm warn install-scripts 4 packages have install scripts not yet covered by allowScripts
npm warn deprecated whatwg-encoding@3.1.1
```

**Cause** — `install-scripts` : nouveauté de npm 11.20, qui signale les paquets exécutant un script à l'installation (`argon2`, `prisma`, `@prisma/engines`, `@parcel/watcher` — tous légitimes). `deprecated` : une dépendance de `jsdom` (tests du client uniquement).

**Solution** — Rien à faire pour l'instant : simples avertissements, les scripts se sont exécutés (les tests passent). Consulter avec `npm install-scripts ls` si besoin.

---

## 10. Prisma 7 — « Unknown argument » après une migration

**Symptôme** — Après `npx prisma migrate dev` : 119 tests en échec, dont la première erreur est :
```
Unknown argument `lastLoginAt`. Available options are marked with ?.
```

**Cause** — Depuis **Prisma 7**, `migrate dev` met à jour la **base** mais ne régénère plus le **client JavaScript**. La base avait la nouvelle colonne, le code ne la connaissait pas. Les autres échecs n'étaient que des conséquences (plus aucune connexion possible → plus de jeton → 401 partout).

**Solution — la séquence complète après toute modification de `schema.prisma`**
```bash
cd api
npx prisma migrate dev       # 1. la base de dev
npx prisma generate          # 2. le client JavaScript (obligatoire depuis Prisma 7)
npm run migrate:test         # 3. la base de test
npm test
```

---

## 11. PostgreSQL en français — mauvais code d'erreur

**Symptôme** — Tests attendant `PSEUDO_TAKEN` ou `EMAIL_TAKEN`, recevant `CONFLICT`.

**Cause** — PostgreSQL local réglé en français (`SHOW lc_messages;` → `fr_FR.UTF-8`) : ses messages d'erreur sont traduits, et l'adaptateur Prisma cherchait le texte anglais « Key (pseudo)=… ».

**Solution** — Corrigé dans le code (S5A-02, `errorHandler.js`) : le champ est retrouvé grâce au **nom de l'index** (`User_pseudo_key`), qui ne se traduit jamais.

**Prévention** — Ne jamais faire dépendre le code du **texte** d'un message d'erreur : toujours d'un code ou d'un identifiant.

---

## 12. PostgreSQL local — Docker « not running » et mot de passe redemandé

**Symptôme** — `docker compose exec postgres …` → `service "postgres" is not running` ; `psql` demande le mot de passe à chaque fois.

**Cause** — La base de développement est un PostgreSQL **installé sur la machine**, pas celui de Docker.

**Solution** — Utiliser `psql` directement, et un trousseau de mots de passe :
```bash
echo "localhost:5432:*:senlis:TON_MOT_DE_PASSE" >> ~/.pgpass
chmod 600 ~/.pgpass            # sinon psql l'ignore (même règle que la clé SSH)
psql -h localhost -U senlis -d senlis_test -c "SHOW lc_messages;"
```

---

## 13. Zip — fichiers cachés « introuvables »

**Symptôme** — `.dockerignore` ou `.github/dependabot.yml` semblent absents après `unzip`.

**Cause** — Sous Linux, un nom qui commence par un point est **caché** : `ls` ne l'affiche pas.

**Solution**
```bash
ls -a                       # affiche aussi les fichiers cachés
unzip -l fichier.zip        # liste le contenu du zip AVANT de l'extraire
```

**Rappel** — Toujours extraire **depuis la racine du projet** avec `unzip -o` (`-o` = écraser sans demander), puis vérifier avec `git status`.

---

## 14. VS Code — plus de couleurs sur les fichiers modifiés

**Symptôme** — Les fichiers modifiés (jaune) et nouveaux (vert) ne sont plus colorés, alors qu'ils sont bien là.

**Cause** — L'extension Git de VS Code garde l'état qu'elle avait **pendant** la panne de Git et ne s'est pas rafraîchie. Autre cause possible sous Linux : trop de fichiers surveillés après un `npm ci` (`node_modules`).

**Solution**
```bash
git status                  # 1. la vérité, dans le terminal : si les fichiers y sont, Git va bien
```
2. Panneau Contrôle de code source (`Ctrl+Maj+G`) → **⟳ Actualiser**.
3. `Ctrl+Maj+P` → **Developer: Reload Window** (le plus efficace).
4. Vérifier le dossier ouvert (barre de titre) : pas la sauvegarde.
5. **Affichage → Sortie → Git** pour lire les erreurs de l'extension.
6. Si VS Code signale « unable to watch for file changes » :
```bash
echo "fs.inotify.max_user_watches=524288" | sudo tee /etc/sysctl.d/99-vscode-watch.conf
sudo sysctl --system
```

---

## 15. ESLint — une erreur de syntaxe passe le lint

**Symptôme** — `npm run lint` « vert », mais `npm run build` échoue sur une faute de syntaxe dans un `.jsx`.

**Cause** — La configuration ESLint du client n'avait pas de clé `files` : les fichiers `.jsx` étaient ignorés en silence.

**Solution** — Corrigé en S5A-08 (`client/eslint.config.js` + `eslint-plugin-react`).

**Prévention** — `npm run build` fait partie de la vérification de chaque issue : c'est le dernier filet. Pour le SCSS (qu'ESLint ne lit pas), le test `client/src/styles/styles.test.js` compile toutes les feuilles de style depuis S5R-03 : une accolade en trop fait échouer `npm test`.

---

## 16. Navigateur — avertissements dans la console

**Symptôme** — Firefox : « Propriété « behavior » inconnue », « -moz-transition », « progid »… ; et « Download the React DevTools ».

**Cause** — Lignes de `leaflet.css` écrites pour Internet Explorer et d'anciens Firefox : le navigateur les ignore une par une (règle d'or du CSS : une propriété inconnue ne casse jamais le reste). Le message React est une suggestion d'extension, en développement uniquement.

**Solution** — Rien à corriger. Filtre **CSS** de la console pour masquer ces avertissements ; extension React DevTools (utile pour inspecter les composants).

---

## 17. OVH — créer `contact@senlis-participatif.fr`

**Chemin** — Espace client OVH → **Web Cloud** → colonne de gauche **MX Plan** (offre gratuite « Redirect ») → `senlis-participatif.fr` → onglet **Emails** → **Gestion des redirections** → **Ajouter une redirection** (« ne pas conserver de copie »).

**Vérification**
```bash
dig MX senlis-participatif.fr +short     # → mx1.mail.ovh.net. (et mx2, mx3…)
```

**Prévention** — Au déploiement (S5-22), ne **jamais** modifier les lignes `MX` de la zone DNS : ce sont elles qui acheminent le courrier de `contact@`.

---

## Réflexes à garder

1. Avant chaque issue : `git checkout dev && git pull origin dev`, puis `git checkout -b <branche-de-l-issue>`.
2. Après chaque zip : `git status`, puis tests → lint → build.
3. Après chaque migration : `migrate dev` → `generate` → `migrate:test`.
4. Lire la **première** erreur, pas la dernière.
5. En cas de doute sur Git : **sauvegarde du dossier d'abord**, réparation ensuite.
