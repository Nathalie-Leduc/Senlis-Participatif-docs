# Procédure — mettre à jour les quartiers et les parkings

> **S5R-13** (21/10/2026). Pour l'administratrice du site, et pour quiconque reprendra le projet : quoi vérifier, quand, et comment, pour que la carte reste juste.
>
> 🎓 **Analogie** : l'entretien d'un plan de ville affiché dans un office de tourisme. Le fond (les quartiers) change rarement, mais il faut vérifier une fois par an ; les informations pratiques (les parkings, les tarifs) changent plus souvent, et on les corrige au fil de l'eau.

---

## 1. Ce qui vit où

| Information | Où elle est stockée | Qui la modifie | Comment |
|---|---|---|---|
| Contours des 8 quartiers | Fichier `client/public/data/iris-senlis.geojson` (dans Git) | Développeuse | Script `update-iris.mjs` (§ 2) ou outil `/admin/quartiers` (§ 3), puis commit et déploiement |
| Parkings : nom, places, régime, recharge, emplacements | Table `Parking` de la base | Administration du site | Écran `/admin/parkings` (§ 4), effet immédiat |
| Tarifs et règles de stationnement (légende sous la carte) | `client/src/constants/parkings.js` (`TARIFS_2025`) | Développeuse | Modifier le fichier, commit et déploiement (§ 5) |
| Liste de départ des parkings | `api/prisma/data/parkings-initiaux.js` | — | Ne sert qu'une fois, base vide (nouvelle installation) |

---

## 2. Contours INSEE — la vérification annuelle

**Quand** : une fois par an (janvier), ou si la mairie signale un changement. L'INSEE publie les IRIS par millésime ; pour Senlis, ils changent très rarement.

**Source** : contours IRIS de l'IGN (Géoplateforme, flux WFS), **Licence Ouverte Etalab** — à citer dans les mentions légales (déjà fait). Adresse du flux, filtrée sur Senlis (code commune 60612) :

```
https://data.geopf.fr/wfs/ows?service=WFS&version=2.0.0&request=GetFeature&typeName=STATISTICALUNITS.IRIS:contours_iris&outputFormat=application/json&srsName=EPSG:4326&CQL_FILTER=code_insee='60612'
```

**Commandes** :

```bash
cd /var/www/html/Senlis-Participatif
git checkout dev
git pull origin dev
git checkout -b chore/iris-AAAA            # AAAA = l'année
cd client
node scripts/update-iris.mjs --essai       # 1. calcule et compare, n'écrit rien
node scripts/update-iris.mjs               # 2. si tout va bien : écrit le nouveau fichier
npm run dev                                 # 3. vérifier /carte et /admin/quartiers
cd ..
git add client/public/data/iris-senlis.geojson
git commit -m "chore(data): contours IRIS AAAA"
git push -u origin chore/iris-AAAA
# → Pull Request sur GitHub avec base: dev, puis merge
```

**Ce que fait le script** : il télécharge les 7 IRIS de Senlis, vérifie qu'ils sont bien 7 et tous de Senlis, puis **redécoupe le « Centre Ville » avec la forme de Saint-Vincent déjà en place** (rien à retracer). Il affiche ensuite l'écart de surface de chaque quartier avec le fichier actuel.

| Le script dit… | Ce que ça veut dire | Que faire |
|---|---|---|
| ✅ Changements minimes | L'INSEE n'a fait que des retouches (souvent aucune) | Publier |
| ⚠️ Un quartier change de plus d'un point | Une limite a vraiment bougé | Regarder la carte avant de publier ; en cas de doute, demander au service SIG de la mairie |
| ❌ 7 IRIS attendus… / « Centre Ville » absent | L'INSEE a **redécoupé** la commune | Ne rien publier ; voir § 2.1 |
| ❌ Impossible de joindre le service de l'IGN | Coupure réseau ou service indisponible | Ouvrir l'adresse ci-dessus dans le navigateur, enregistrer la page (`iris.geojson`), puis `node scripts/update-iris.mjs --source iris.geojson` |

### 2.1 Si l'INSEE redécoupe la commune (rare)

Un nouvel IRIS, ou un IRIS qui disparaît, touche toute l'application : la liste des quartiers vit à plusieurs endroits, qu'il faut faire évoluer **ensemble**, dans une branche dédiée :

- base : énumération `Quartier` (`api/prisma/schema.prisma`) + migration écrite à la main (comme `saint_vincent`), en décidant ce que deviennent les profils déjà enregistrés ;
- API : `api/src/lib/quartiers.js` ;
- client : `client/src/constants/zones.js` (libellés, `quartierOfFeature`) ;
- enquêtes : les options de quartier de l'enquête stationnement (`api/prisma/surveys/stationnement-v3.js`) ;
- les tests qui listent les quartiers.

Commencer par une estimation avec la mairie (document 30).

---

## 3. La limite Centre historique / Saint-Vincent

**Quand** : seulement si la mairie fournit une limite officielle différente du plan municipal de 2018 (aujourd'hui : rue de la République).

**Comment** : outil **`/admin/quartiers`** (compte ADMIN) — poser le plan en surimpression si besoin, tracer le contour de Saint-Vincent au clic (on peut déborder du centre), calculer, vérifier, **télécharger** le fichier, le placer dans `client/public/data/iris-senlis.geojson`, puis commit et Pull Request comme au § 2. Le script annuel reprendra ensuite automatiquement cette nouvelle forme.

> L'image du plan n'est jamais envoyée au serveur ; elle reste dans le navigateur le temps du tracé.

---

## 4. Les parkings

**Quand** : à chaque nouveau plan ou guide de stationnement de la Ville, après des travaux (parking créé, supprimé, agrandi), ou sur signalement d'un habitant.

**Sources** : le guide du stationnement de la Ville (Indigo, version de janvier 2025) et le plan des parkings du centre-ville (septembre 2023). En cas d'écart, le document **le plus récent** l'emporte ; noter l'écart dans la remarque du parking.

**Comment** : écran **`/admin/parkings`** (compte ADMIN ; l'Admin-test consulte seulement). Les modifications sont **immédiates** sur la carte et notées au journal d'administration.

| Je veux… | Dans l'écran |
|---|---|
| Ajouter un parking | « + Ajouter un parking » — le nom suffit, le reste peut attendre |
| Corriger places, régime, recharge, PMR, remarque | « Modifier » sur la ligne |
| Placer un parking sur la carte | « Placer », puis **📍 Ajouter des points** : un clic par aire de stationnement (le stade en a plusieurs) |
| Indiquer des places le long d'une rue (zone rouge, zone verte en voirie) | **〰️ Tracer un trait** : cliquer le long de la rue, du début à la fin des places, puis « Terminer le trait » |
| Retirer un parking de la carte sans le supprimer | « Modifier », « Tout retirer de la carte », Enregistrer |
| Supprimer un parking | « Supprimer » (confirmation demandée) |
| Voir ce qu'il reste à placer | Filtre « À placer » |

**Astuce** : zoomer au maximum avant de cliquer. Chaque emplacement se retire seul (« retirer ») s'il est mal placé.

---

## 5. Les tarifs et les règles (légende de la carte)

**Quand** : quand la Ville change ses tarifs ou ses règles (durées maximales, gratuité, abonnements).

**Comment** : modifier `client/src/constants/parkings.js`, objet `TARIFS_2025` (et son nom, s'il change d'année), puis Pull Request comme au § 2. Garder la mention « tarifs indicatifs — seuls les tarifs affichés sur place font foi » et mettre à jour la **source** (`PARKINGS_SOURCE`) et la date dans les mentions légales (`MentionsLegales.jsx`, `LAST_UPDATED` de `constants/legal.js`).

> Pourquoi dans le code et pas dans l'écran d'administration ? Les tarifs changent une fois par an au plus et se relisent mieux dans une Pull Request. S'ils devaient changer souvent, un écran d'édition irait dans l'offre à la mairie.

---

## 6. Nouvelle installation (autre serveur, autre commune)

```bash
cd api
npx prisma migrate deploy
npm run seed:prod      # crée l'admin, l'enquête, et les 19 parkings de départ si la table est vide
```

Les parkings arrivent **sans emplacement** : les placer avec l'écran du § 4. Pour une autre commune : remplacer le code commune (`60612`) dans `client/scripts/update-iris.mjs`, le centre (`CENTRE_IRIS`) dans `client/src/utils/quartierSplit.js`, et la liste `api/prisma/data/parkings-initiaux.js`.

---

## 7. Check-list après une mise à jour

- [ ] `/carte` : les quartiers se cochent, la liste « Par quartier » suit, les parkings et leurs traits s'affichent avec la bonne couleur.
- [ ] Une infobulle de parking au hasard : places, régime, recharge, remarque.
- [ ] La légende : tarifs et source à jour.
- [ ] `npm test --prefix client` et `npm test --prefix api` (si du code a changé).
- [ ] Mentions légales : sources et date.
