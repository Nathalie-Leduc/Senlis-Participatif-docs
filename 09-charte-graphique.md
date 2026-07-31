# Charte graphique — Senlis Participatif

> *Version 1.1 — 14 juin 2026*
>
> La charte est un contrat de cohérence : elle décide une fois pour que chaque écran n'ait plus à décider. Direction artistique : **« la pierre, la rivière et le cerf »** — l'identité visuelle est tirée de la matière même de Senlis (le calcaire des façades, l'ardoise des toits, la Nonette qui traverse la ville) et portée par un cerf mascotte joyeux, symbole de la forêt de Senlis. Le ton est **civique-chaleureux-ludique** : crédible sans être froid, accessible et joyeux sans être enfantin, engageant sans être intrusif.

---

## 1. Logo

Fichier : `logo-senlis-participatif.svg`

**Le signe raconte le projet en trois couches** : une *bulle de dialogue* (la parole citoyenne) contenant un *histogramme* (les données d'enquête) dont la barre la plus haute se termine en *flèche* (le clocher de la cathédrale Notre-Dame, silhouette emblématique de Senlis). La pointe brique sur fond bleu est le seul accent chaud du signe : elle attire l'œil sans crier.

**Règles d'usage**
- Zone de protection : un demi-module de bulle tout autour, rien ne s'en approche
- Taille minimale : 32 px de haut pour le signe seul, 48 px avec le mot
- Fonds autorisés : blanc, pierre (`#F6F1E7`), dégradé hero Nonette ; sur fond ardoise, passer le trait de la bulle en blanc
- Interdits : déformer, changer les couleurs, ajouter une ombre portée, incliner

---

## 2. Palette

### Palette principale (identité de Senlis)

| Nom | Hex | Rôle | Contraste (vs fond d'usage) |
|---|---|---|---|
| **Ardoise** | `#26333A` | Texte principal, titres, trait du logo | 12,6:1 sur Pierre ✅ AAA |
| **Pierre** | `#FDF9F0` | Fond de page (calcaire de Senlis), légèrement réchauffé | — |
| **Blanc** | `#FFFFFF` | Cartes, formulaires, surfaces, hero backgrounds internes | — |
| **Bleu Nonette** | `#1E5F7C` | Actions, liens, focus, barres du logo, section hero | 6,3:1 avec texte blanc ✅ AA |
| **Vert tilleul** | `#3A7A4D` | Vote / arguments POUR, succès, section carte | 4,9:1 avec texte blanc ✅ AA |
| **Brique** | `#A8442F` | Vote / arguments CONTRE, erreurs, pointe du logo | 6,0:1 avec texte blanc ✅ AA |
| **Doré** | `#D4A84A` | Mascotte, section enquêtes, badges « en modération », accents chaleureux | 3,1:1 (utilisé en décoratif ou avec texte blanc sur fond large) |
| **Gris pierre** | `#6B6257` | Texte secondaire, légendes | 5,1:1 sur Pierre ✅ AA |

### Palette étendue « Joy Layer » (les déclinaisons pastel des sections)

| Nom | Hex | Rôle |
|---|---|---|
| Nonette light | `#E3F0F6` | Fond de la section Propositions |
| Tilleul light | `#E0F2E5` | Fond de la section Carte |
| Doré light | `#FFF4DB` | Fond de la section Enquêtes |
| Brique light | `#FCEAE6` | Fond de la section alertes / erreurs |
| Doré bright | `#F0C45A` | Bouton CTA principal hero, accents solaires |
| Tilleul bright | `#4CAF65` | Barre de progression, récompenses |
| Nonette glow | `#2A8AB4` | Dégradé hero (point haut du gradient) |

**Règles d'usage de la couleur**
- Le Bleu Nonette reste la couleur d'action : *tout ce qui est cliquable est bleu, tout ce qui est bleu est cliquable*
- Vert et Brique sont réservés au sens POUR/CONTRE et aux états (succès/erreur) — jamais décoratifs
- Le Doré est la couleur de la **mascotte et de l'enquête** : il apporte la chaleur sans empiéter sur les couleurs d'action
- La couleur ne porte **jamais** seule l'information (daltonisme) : toujours doublée d'un libellé ou d'une icône (✓ POUR / ✗ CONTRE / ◯ NEUTRE)
- Chaque section de la page a sa **couleur dominante pastel** (Nonette light, Doré light, Tilleul light), reliées par des séparateurs ondulés SVG — comme la Nonette serpentant dans la ville
- Le dégradé hero (`#1a4f6b` → `#1E5F7C` → `#2A8AB4`) est le seul dégradé autorisé dans la palette ; pas de dégradés décoratifs ailleurs

---

## 3. Typographie

| Rôle | Police | Usage |
|---|---|---|
| Display (titres) | **Fraunces** (Google Fonts, libre) — graisses 500, 700, 800 | H1-H2, chiffres-clés, bulles de la mascotte — un sérif à caractère, qui évoque l'imprimé civique sans poussière |
| Texte courant | **Public Sans** (Google Fonts, libre) | Paragraphes, formulaires, navigation, widget guide — lisibilité maximale, conçue pour le service public (US Web Design System) |
| Données | Public Sans (chiffres tabulaires) | Résultats, compteurs animés, tableaux, statistiques dans les pill badges |

**Échelle typographique** (base élargie pour le public senior — 25,6 % de 60 ans et plus à Senlis) :

| Niveau | Taille / interligne | Graisse |
|---|---|---|
| H1 (hero) | clamp(30px, 5.5vw, 48px) / 1.15 | Fraunces 800 |
| H1 (pages) | 32 px / 1.2 | Fraunces 700 |
| H2 | 28-32 px / 1.25 | Fraunces 700 |
| H3 | 20-22 px / 1.3 | Fraunces 700 |
| Corps | **18 px** / 1.6 | Public Sans 400 |
| Petit texte | 15-16 px / 1.5 | Public Sans 400 — *taille plancher : rien en dessous, sauf badges (13-14 px en 600/700)* |

---

## 4. La mascotte : le cerf de Senlis

### 4.1 Identité du personnage

Le **cerf de Senlis** est la mascotte-guide de la plateforme. Il incarne l'identité de Senlis (la forêt, la nature en ville) et porte les valeurs du projet (participation, bienveillance, transparence). Son écharpe Bleu Nonette le rattache à la rivière et à la couleur d'action du site.

**Traits de caractère** : bienveillant, encourageant, légèrement espiègle (jeu de mots « cerf-tifié utile »), jamais condescendant. Il vouvoie, utilise des mots simples, et contextualise ses interventions.

**Règles d'usage**
- Le cerf est dessiné en **SVG inline** (zéro requête réseau, stylable en CSS, animable sans JavaScript)
- Il existe en **4 tailles** : hero (220 px), section (80-90 px), inline (48 px), widget (40-42 px)
- Palette du cerf : corps `#D4A84A` (Doré), ventre `#F6F1E7` (Pierre), visage `#E8BD6A`, nez/yeux `#26333A` (Ardoise), écharpe `#1E5F7C` (Nonette), bois `#8B6914`, joues `#E8A090` à 30 % d'opacité
- Le cerf ne recouvre **jamais** un contenu fonctionnel (bouton, formulaire, texte essentiel)
- Les étoiles décoratives (`✦`) qui l'entourent sont en Doré ou Nonette, opacity 0.5-0.7

### 4.2 Poses et contextes d'apparition

| Pose | Contexte | Animation |
|---|---|---|
| **Accueil** (hero) | Page d'accueil, grande taille, bulle « Bienvenue à Senlis ! » | Clignement des yeux, oreilles qui remuent, queue qui bouge |
| **Guide** (clipboard) | Section enquêtes, écharpe + clipboard à la main | Clignement des yeux |
| **Encouragement** | CTA de bas de page, taille moyenne | Flottement doux (bob vertical) |
| **Widget** (tête seule) | Bouton flottant en bas à droite, toujours visible | Pulse de notification si message non lu |
| **Mini-inline** | Titre de section, à gauche du H2 | Rebond doux vertical |

### 4.3 Animations CSS de la mascotte

Toutes les animations sont en **CSS pur** (aucune bibliothèque d'animation externe).

| Animation | Propriété | Durée | Boucle |
|---|---|---|---|
| Clignement des yeux | `scaleY` sur les groupes `.eye-blink` | 4 s | Infinie |
| Oreilles qui remuent | `rotate` sur `.ear-wiggle`, origin en bas | 5 s | Infinie |
| Queue qui bouge | `rotate` sur `.tail-wag`, origin à gauche | 2 s | Infinie |
| Bulle de parole | `scale` + `opacity` avec `cubic-bezier(.34,1.56,.64,1)` | 0.4 s | Une fois (re-jouée au changement de texte) |
| Flottement doux | `translateY` de 0 à -8 px | 3 s | Infinie |
| Rebond mini | `translateY` de 0 à -6 px | 2 s | Infinie |

**Règle critique : `@media (prefers-reduced-motion: reduce)`** — toutes les animations sont neutralisées (`animation-duration: 0.01ms; animation-iteration-count: 1`). La mascotte reste visible en image statique ; ses bulles de texte restent lisibles. Le site est 100 % fonctionnel sans animation.

---

## 5. Composants récurrents

### Boutons
Coins arrondis **16 px** (plus généreux que les 8 px initiaux, cohérent avec le ton joyeux), padding 16×32 px, cible tactile ≥ 48 px (doigts et tremblements). Transition au survol : `translateY(-3px)` + ombre portée renforcée (effet de soulèvement). Transition au clic : `scale(.98)` (effet d'enfoncement).

Quatre variantes :
- **Gold** (CTA hero) : fond Doré bright `#F0C45A`, texte Ardoise, ombre dorée — utilisé une seule fois par page (le CTA principal)
- **Primaire** : fond Bleu Nonette, texte blanc — actions courantes
- **Tilleul** : fond Vert tilleul, texte blanc — CTA enquête
- **Ghost** : contour Nonette, fond semi-transparent — actions secondaires

Les trois boutons de vote affichent icône + libellé + couleur (jamais la couleur seule). Au clic, le bouton voté passe en fond plein de sa couleur avec texte blanc et un léger `scale(1.05)`.

### Cartes (propositions, enquêtes)
Fond blanc, rayon **32 px** (arrondi généreux), ombre très douce en repos (`0 2px 8px rgba(38,51,58,.06)`), ombre renforcée au survol (`0 16px 40px rgba(38,51,58,.12)`) avec `translateY(-8px)`. L'illustration de la proposition (emoji en gros, opacity 15 %) flotte en haut à droite de la carte, jamais cliquable.

### Badge « En concertation »
Texte Vert tilleul en `700 13px` uppercase + point vert pulsant (`animation: pulse 2s infinite`). Le point vert est doublé du libellé « En concertation » → la couleur ne porte pas seule l'info.

### La jauge de vote *(élément signature des écrans)*
Barre horizontale de 18 px de haut, rayon 999 px. Trois segments : Vert tilleul (gradient `#3A7A4D` → `#4CAF65`) / Gris `#B9B2A4` / Brique (gradient `#C25539` → `#A8442F`). **Animation au scroll** : les segments partent de `width: 0` et s'étendent à leur valeur réelle en 1.2 s (déclenchée par `IntersectionObserver`). Toujours accompagnée des comptages en clair (« 184 pour · 36 neutres · 92 contre ») : la donnée se *voit* et se *lit*.

### Le débat en colonnes (Lot 2)
Deux colonnes POUR (liseré vert) / CONTRE (liseré brique), arguments neutres en pleine largeur dessous : la structure de la page *est* la structure du débat.

### Séparateurs ondulés
Les transitions entre sections colorées utilisent des SVG `viewBox` ondulés (`<path>` avec courbes Bézier), rendus en `preserveAspectRatio="none"` pour s'adapter à la largeur de l'écran. Ils remplacent les simples traits horizontaux pour un effet de fluidité — comme la Nonette qui serpente.

### Sections colorées
Chaque grande section de la page a sa propre couleur de fond pastel et un petit dégradé interne pour éviter la platitude :
- Hero : dégradé Nonette sombre → Nonette → Nonette glow, avec blobs flottants semi-transparents
- Propositions : Nonette light → bleu ciel doux
- Enquêtes : Doré light → jaune chaud
- Carte : Tilleul light → vert doux
- CTA final : Ardoise → Nonette sombre

### Statistiques en capsules (stat pills)
Affichées dans le hero : fond semi-transparent blanc (`rgba(255,255,255,.15)`) avec `backdrop-filter: blur(4px)`, rayon 999 px. Chiffre en Doré bright, libellé en blanc. Compteurs animés au chargement (incrémentation progressive).

### Widget guide-citoyen
Bouton flottant circulaire (68 px), bord Doré, fond blanc, ombre dorée. Badge de notification (cercle Brique 22 px avec compteur). Au clic : panneau de 330 px de large, rayon 24 px, header en dégradé Doré, messages en bulles Pierre arrondies, boutons d'action rapide en pills Doré. Animation d'entrée : `translateY(20px) scale(.9)` → position finale en 0.35 s.

### Confettis
Lancés au vote et à la publication de résultats. 40-50 rectangles et cercles aux couleurs de la charte (Tilleul, Nonette, Doré, Brique, `#E8BD6A`), positions aléatoires, chute en 2-4 s avec rotation 720°. Conteneur en `pointer-events: none`, `position: fixed`, suppression du DOM après 4 s.

### Toast de confirmation
Fond Tilleul, texte blanc, rayon 16 px, ombre verte, apparition en slide-up avec rebond (`cubic-bezier(.34,1.56,.64,1)`), disparition après 3 s.

---

## 6. Accessibilité (rappels intégrés à la charte)

- Contrastes : tout couple texte/fond fonctionnel de cette charte est ≥ 4,5:1 (AA) — vérifié ci-dessus
- Focus clavier : anneau Bleu Nonette de 3 px, jamais supprimé (`outline: 3px solid #1E5F7C; outline-offset: 2px`)
- Skip link « Aller au contenu » en premier élément focusable de chaque page
- **`prefers-reduced-motion` respecté** : aucune animation indispensable à la compréhension — la mascotte reste visible mais figée, les jauges sont à leur valeur finale immédiatement, les confettis ne se lancent pas, le widget s'ouvre sans animation
- Formulaires : labels toujours visibles (jamais de placeholder-comme-label), erreurs explicites en texte
- La mascotte et ses bulles sont décoratives du point de vue de l'information : tout ce que le cerf « dit » est redondant avec le contenu déjà présent sur la page (titre, sous-titre, CTA). Attributs `aria-hidden="true"` sur les éléments décoratifs, `role="img"` + `aria-label` sur la mascotte hero
- Le widget guide-citoyen est navigable au clavier et ses boutons d'action ont des labels explicites

## 7. Ton éditorial

Vouvoiement, mots du quotidien (« donner mon avis » plutôt que « soumettre une contribution »), phrases courtes, voix active. Les boutons disent ce qu'ils font : « Publier mon argument », pas « Envoyer ». Les chiffres sont toujours contextualisés : « 312 participants sur ~6 900 ménages » — l'honnêteté statistique fait partie de la marque.

**Ton de la mascotte** : bienveillant et légèrement espiègle. Exemples de bulles contextuelles :
- Accueil : « Bienvenue à Senlis ! 🏛️ », « Votre avis compte ici ! »
- Après un vote : « Merci ! Le cerf approuve 🦌 » (pour) / « Vote enregistré ! Chaque opinion compte 💪 » (contre)
- Enquête : « 3 minutes et vos données aident vraiment le débat »
- Widget : « Cerf-tifié utile ! 🦌 — Votre guide citoyen »
- Erreur : le cerf a l'air perplexe (pas de message culpabilisant)
- 404 : « Oups, je me suis perdu dans la forêt… Retournons à l'accueil ! »
