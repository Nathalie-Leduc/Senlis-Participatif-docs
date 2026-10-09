# Retours de recette du 08/10/2026 — triage

> Deuxième recette complète, après le Sprint 5 recette (S5R-01 → S5R-12, document `23`). Ce document reprend chaque retour, propose une réponse, la chiffre et la range : **avant la mise en ligne** (nouveau bloc **Sprint 5 recette 2**, issues S5R2-xx) ou **offre d'évolutions à la mairie** (document `30`).
>
> Numérotation : les documents 26 à 28 sont réservés aux guides de référencement et d'approche de la mairie déjà rédigés (guide de référencement Google, guide territorial API citoyenne / SEO / réseaux sociaux, approche de la mairie).

---

## 1. Synthèse

| # | Retour | Décision | Issue | Estim. |
|:--:|---|---|---|:--:|
| 1 | L'admin teste une enquête sans que sa réponse soit enregistrée, autant de fois qu'il veut | ✅ Avant la mise en ligne | S5R2-01 | 1 j |
| 2 | « Bus régional » proposé seulement aux résidents d'une autre ville | ✅ Avant — nouvelle capacité du moteur : **options conditionnelles** | S5R2-02 | 1,5 j |
| 3 | Activités en 6 familles + « Autre activité » → « Laquelle ? » facultative ; « voitures ou motos » ; « EDPM » | ✅ Avant — contenu de l'enquête (v3.1) | S5R2-03 | 0,5 j |
| 4 | Résultats avec la bibliothèque Chart.js | ✅ Avant | S5R2-04 | 1 j |
| 5 | Parkings : le plan officiel du centre (2023) plutôt qu'OpenStreetMap | ✅ Avant | S5R2-05 | 2 j |
| 6 | Quartiers : le plan municipal (2018) plutôt que les IRIS | ✅ Avant, **en 2 étapes** (noms maintenant, découpage fin du centre ensuite) | S5R2-06 | 1 j |
| 7 | Lecture au survol : lit le vide, voix robotique, « Arrêter » inefficace | ✅ Avant | S5R2-07 | 1 j |
| 8 | Référencement (SEO) prévu pour la mise en ligne ? | ⚠️ **Non, pas assez** — ajouté au Sprint 5ter | S5-26, S5-27 | 2 j |
| 9 | Audit complet et refactorisation | ✅ Avant la mise en ligne (inclut BACKLOG-03) | S5R2-08 | 3 j |
| 10 | Sprints 6, 7, backlogs, Lot 3 = évolutions à proposer à la mairie : regrouper, chiffrer | ✅ Document `30` ; le kanban s'arrête désormais à la mise en ligne | — | — |

**Total avant mise en ligne** : 11,5 jours de Sprint 5 recette 2, + S5R-13 (0,5 j, reportée), + 2 jours de SEO dans le Sprint 5ter.

---

## 2. Administration

### 2.1 « Voir » = « Tester » (S5R2-01)

> *« Un admin ne devrait répondre qu'en test, et sa réponse ne devrait pas être enregistrée. Il doit pouvoir tester autant de fois qu'il le souhaite. »*

**Réponse** — Un **mode test** pour l'administration :

- sur la page d'une enquête, l'admin voit **« Tester l'enquête »** (et non « Répondre ») ; la page de réponse affiche un bandeau « Mode test — rien n'est enregistré » ;
- à l'envoi, l'API **vérifie tout comme pour une vraie réponse** (parcours, questions obligatoires, bornes, limites de cases) mais **n'écrit rien** : ni réponse, ni mise à jour du profil. On teste donc vraiment le moteur, pas une maquette ;
- l'écran de fin récapitule le **parcours suivi** (questions vues) et propose « Recommencer le test » ;
- une **vraie** réponse d'un compte admin est **refusée** (403), pour ne jamais fausser les résultats.

Analogie : la répétition générale d'un spectacle — tout se passe comme le soir de la première, mais sans public et sans billetterie.

Le bouton « Voir » de la liste admin (S5R-12) mène à cette page.

---

## 3. Enquête stationnement

### 3.1 « Bus régional » seulement pour les résidents d'une autre ville (S5R2-02)

Le moteur sait afficher ou masquer une **question** selon des réponses précédentes, pas une **option**. Trois solutions ont été étudiées :

| Solution | Avantage | Limite |
|---|---|---|
| Dupliquer les questions « Comment venez-vous… ? » par lieu de résidence | Aucun développement | Impossible pour le bloc « travail » : il faudrait « travaille au centre **ET** réside ailleurs » — le moteur ne connaît que le **OU** |
| Renommer en « Bus régional (depuis une autre ville) » | Immédiat | L'option reste proposée à tous |
| **Options conditionnelles** : une option peut, comme une question, n'apparaître que si l'une de certaines réponses a été choisie | Réglé proprement, et réutilisable dans toutes les enquêtes | 1,5 j (base de données, moteur API et navigateur, constructeur) |

**Recommandation** : options conditionnelles. Même règle (OU) et même garde-fou que les questions : une option ne dépend que d'une question **précédente** ; l'API refuse une option qui n'était pas proposée à la personne.

### 3.2 Contenu de l'enquête, version 3.1 (S5R2-03)

- **EDPM** (« engins de déplacement personnel motorisés ») à la place de « Trottinette » — déjà modifié par Nath dans `stationnement-v3.js` : repris tel quel.
- **Activités** en 6 familles, avec leurs exemples en aide sous la question :
  1. Commerces et boutiques
  2. Services de proximité, agences et santé
  3. Cafés, hôtels et restaurants
  4. Administrations et services publics
  5. Enseignement, culture et social
  6. Bureaux et secteur tertiaire
  7. Autre activité → « Laquelle ? » **facultative**
- **Question 7** : « véhicules motorisés » → « **voitures ou motos** » (les EDPM ne sont pas concernés).
- **Mise à jour d'une base existante** : l'enquête de développement a des réponses de test ; une option `--remplacer` du seed (refusée en production si l'enquête a de vraies réponses) recrée l'enquête proprement.

### 3.3 Résultats avec Chart.js (S5R2-04)

Oui. Chart.js (via `react-chartjs-2`) pour des diagrammes en barres et en secteurs, **chargé seulement sur la page des résultats** (aucun poids ajouté au reste du site).

Point d'accessibilité : un graphique Chart.js est un dessin (`<canvas>`), illisible pour un lecteur d'écran. Chaque graphique garde donc **à côté** ses nombres et pourcentages en texte (comme aujourd'hui), et le `<canvas>` reçoit une description. Les exports CSV / JSON ne changent pas.

---

## 4. Cartes

### 4.1 Parkings : le plan officiel du centre (S5R2-05)

> *« À la place de la liste des parkings OpenStreetMap, pour laquelle il y a beaucoup trop de modifications à faire, utiliser le plan des parkings du centre-ville (2023-09). »*

Le plan de la Ville apporte exactement ce qui manquait à OpenStreetMap : le **nombre de places** et le **régime** de chaque parking (une quarantaine d'emplacements, dont 16 nommés : Tribunal, Saint-Pierre, Saint-Rieul, Cerf, Stade, Sous-Préfecture, Arènes, Heaume, Square des États-Unis, Bordeaux, Poste, Brunehaut, Ordener, Saint-Lazare, Hôpital, Lycées), plus les règles de stationnement.

| Régime (plan 2023-09) | Règle résumée |
|---|---|
| Parking public gratuit | — |
| Zone verte (payant) | 15 min gratuites par jour, puis 1,20 €/h, 4 h 15 max |
| Zone rouge (payant) | 15 min gratuites par jour, puis 1,50 €/h, 2 h 15 max ; aussi en voirie |
| Parking « Brunehaut » (ÉcoQuartier) | 1re heure gratuite, tarif dégressif, abonnements |
| Durée limitée (disque) | Thomas Couture 2 h ; avenue Foch 30 min |
| Bornes de recharge électrique | Signalées par parking |

**Réalisation** :

- un fichier de données **saisi d'après le plan** (`parkings-centre-2023.json`) : nom, places, régime, recharge, coordonnées ;
- une **légende** des régimes et tarifs sous la carte, avec la mention « tarifs indicatifs (plan de la Ville, septembre 2023) — seuls les tarifs affichés sur place font foi », comme sur le plan ;
- les coordonnées ne figurent pas sur un plan imprimé : un petit **outil admin « placer un point »** (clic sur la carte → coordonnées à copier) permet de les relever en quelques minutes ;
- l'extraction OpenStreetMap et ses compléments (S5R-11) sont **retirés** de la carte (le script reste dans le dépôt, s'il resservait un jour).

À prévoir avec la mairie : la date de mise à jour du plan et l'accord pour reprendre ses informations (données publiques, source citée).

### 4.2 Quartiers : le plan municipal de 2018 (S5R2-06)

Le plan municipal compte **8 quartiers**, l'INSEE **7 IRIS**. Ils se recouvrent presque exactement, à une différence près : le plan municipal coupe le centre en deux.

| Plan municipal 2018 | IRIS INSEE actuel |
|---|---|
| Villevert | Villevert |
| Bon Secours | Bon Secours |
| Zone d'activité | Zone industrielle |
| Val d'Aunette – Gâtelière | Val d'Aunette – La Gâtelière |
| Brichebay | Brichebay |
| Fours-à-Chaux – Bigüe – Villemétrie | Jardiniers |
| Centre-Sud **+** Centre-Est – Saint-Vincent | Centre Ville (un seul IRIS) |

**Recommandation en 2 étapes** :

1. **Maintenant (S5R2-06, 1 j)** — les **noms du plan municipal** partout (inscription, profil, public visé, zone d'une proposition, carte) et les couleurs du plan sur la carte ; contours IRIS conservés ; « Centre-Sud » et « Centre-Est – Saint-Vincent » regroupés sous « Centre historique », qui reste la notion clé de l'enquête stationnement. Aucune donnée existante à convertir (on garde les mêmes codes internes, seuls les libellés changent).
2. **Ensuite (offre mairie, document `30`)** — le découpage fin du centre en deux quartiers, à partir des **contours officiels** que le service SIG de la mairie peut fournir (fichier GeoJSON ou Shapefile). Les dessiner à la main d'après un plan imprimé introduirait des erreurs de frontière, gênantes pour un outil de démocratie participative.

À vérifier visuellement pendant S5R2-06 : la superposition des contours IRIS et du plan 2018 pour chaque quartier.

---

## 5. Accessibilité — lecture au survol (S5R2-07)

| Constat | Correction |
|---|---|
| Lit même les zones sans texte | Ne lit que les éléments qui portent **leur propre texte** (paragraphes, titres, boutons, liens, cellules, images avec texte alternatif) — jamais un conteneur vide ou décoratif |
| Voix très robotique | Choix de la **voix** dans le module (les voix « naturelles » gratuites des navigateurs sont proposées en premier, ex. « Microsoft Denise Online (Natural) » dans Edge, « Google français » dans Chrome) et réglage de la **vitesse** |
| « Arrêter la lecture » n'arrête pas : la lecture reprend au survol suivant | Trois commandes distinctes : **Pause**, **Reprendre**, **Arrêter** — « Arrêter » coupe la lecture **et** désactive la lecture au survol (on la réactive dans le module) |

**Un outil plus performant et gratuit ?** Les voix vraiment naturelles (« neuronales ») gratuites existent hors du navigateur, mais elles imposent soit un service externe (envoi du texte lu à un tiers : RGPD, coût à terme), soit un modèle de 60 Mo à télécharger dans la page (trop lourd pour un usage occasionnel). Les voix naturelles **déjà présentes** dans Edge, Chrome et Safari sont le meilleur compromis : gratuites, locales, sans envoi de données. Les voix neuronales restent la proposition L3-03 de l'offre mairie.

---

## 6. Mise en ligne

### 6.1 Référencement (SEO) — S5-26 et S5-27

Les guides 26–28 décrivent ce qu'il faut ; le Sprint 5ter ne le prévoyait **pas** (seulement domaine, hébergement, sauvegardes, chiffrement, communication). Deux issues ajoutées :

- **S5-26 — SEO technique (1,5 j)** : `robots.txt` ; `sitemap.xml` **généré par l'API** (pages fixes + propositions publiées + enquêtes ouvertes, toujours à jour) ; un `<title>` et une **meta description** par page (React 19 sait les placer dans le `<head>` depuis les composants) ; balises **Open Graph** (aperçu lors d'un partage sur les réseaux sociaux, avec l'image de la proposition) ; données structurées **Schema.org** (organisation, propositions) ; vérification du score Lighthouse « SEO » ≥ 90.
- **S5-27 — Visibilité (0,5 j)** : Google Search Console (validation par enregistrement DNS chez OVH, envoi du sitemap), Bing Webmaster Tools (couvre aussi Ecosia et DuckDuckGo), lien depuis le site de la mairie et les annuaires locaux.

Limite à connaître : le site est une application React rendue dans le navigateur. Google l'indexe correctement, mais certains robots d'aperçu (réseaux sociaux) ne lisent que le HTML initial : S5-26 prévoit donc des balises par défaut dans `index.html`, et, pour les propositions, une petite route de l'API qui sert les bonnes balises aux robots de partage.

### 6.2 Audit complet et refactorisation (S5R2-08)

Avant la mise en ligne, une relecture d'ensemble du code (3 j) :

- **BACKLOG-03 inclus** : un hook commun de chargement des données (les 18 avertissements `set-state-in-effect` disparaissent, la règle repasse en « error ») ;
- code mort, doublons, constantes dispersées (quartiers, libellés), cohérence des noms ;
- dépendances : versions, failles connues (`npm audit`), poids du site (Lighthouse « Performance ») ;
- relecture sécurité ciblée des routes ajoutées depuis l'audit S5A (enquêtes v2, public visé, zones, statistiques filtrées, mode test) ;
- un rapport (document `31`) : constats, corrections faites, points laissés pour plus tard.

---

## 7. Sprints 6, 7, backlogs et Lot 3 → offre d'évolutions à la mairie

Tout ce qui suit la mise en ligne devient une **offre** à présenter et facturer : regroupée par thème et chiffrée en jours dans le document **`30-offre-evolutions-mairie.md`**. Le kanban s'arrête donc à la mise en ligne (Sprint 5ter) ; les tableaux des sprints 6 et 7 y sont conservés **sans jours de calendrier**.

**Faut-il en intégrer avant la mise en ligne ?** Trois éléments sont proposés à l'intégration, car peu coûteux et utiles dès le lancement — à valider :

| Élément | Pourquoi maintenant | Estim. |
|---|---|:--:|
| BACKLOG-03 (chargement des données) | Qualité du code avant qu'il soit exploité ; déjà inclus dans S5R2-08 | (inclus) |
| L3-07 Second email avant suppression d'un compte inactif | Bonne pratique RGPD, évite de supprimer un compte dont le premier email a été manqué | 0,5 j |
| Mascotte de la page 404 (extrait de S7-05) | La page d'erreur est vue dès le premier lien cassé ; cohérence de l'identité | 0,5 j |

Le reste (débat et modération, propositions citoyennes, notifications, multilingue, FranceConnect, rôles, tableau de bord…) suppose un budget ou un choix de la future responsable de traitement : il attend la décision de la mairie.
