# Retours de recette du 30/09/2026 — triage et plan d'action

> Source : document « 20260930-améliorations-corrections » (tests manuels de Nath, avant la mise en ligne réelle).
> Chaque retour est classé dans l'une de trois catégories, puis rattaché à une issue du kanban (`16-kanban-issues-complet.md`, v1.11).
>
> | Catégorie | Signification | Où ça va |
> |---|---|---|
> | 🔧 **Recette** | Bug ou écart avec ce qui était prévu : à corriger **avant** la mise en ligne | Nouveau bloc **Sprint 5 recette** (S5R-01 → S5R-13), placé avant le Sprint 5ter |
> | 🏛️ **Lot 3** | Évolution de fond, à proposer à la mairie (budget, choix politique ou service tiers) | Section « Propositions Lot 3 » du backlog |
> | 💬 **Réponse** | Question posée pendant les tests : réponse argumentée ci-dessous, éventuellement suivie d'une issue | Ce document, §4 |
>
> 🎓 **Analogie** : c'est la visite de pré-réception d'une maison. On liste les réserves (la porte qui frotte, la prise mal posée) qui doivent être levées avant la remise des clés, et on met de côté les envies d'agrandissement pour un futur chantier.

---

## 1. Diagnostic des deux problèmes les plus graves

### 1.1 « Jeton invalide ou déjà utilisé » à la vérification de l'email (S5R-01)

**Ce qui se passe.** En développement, React active le mode `StrictMode` (`client/src/main.jsx`), qui **exécute chaque `useEffect` deux fois** pour aider à détecter les effets mal écrits. La page `VerificationEmail.jsx` envoie donc le jeton **deux fois** :

1. le 1er appel consomme le jeton → l'email est **bien vérifié en base** ;
2. le 2e appel trouve un jeton déjà utilisé → erreur ;
3. la réponse d'erreur arrive en dernier et **remplace l'écran de succès** par « Oups… ».

La personne croit alors que la vérification a échoué, clique « Réessayer l'inscription », retombe sur un formulaire vide, et reçoit « Cette adresse email est déjà utilisée » : tout concorde avec les captures d'écran. Le problème réseau du moment a pu aggraver la confusion, mais la cause est structurelle — et en production, un double clic sur le lien de l'email produirait le même écran.

**Analogie** : un ticket de vestiaire poinçonné deux fois de suite par le même guichetier ; au second coup de poinçon, il annonce « ticket déjà utilisé »… alors que le manteau a bien été rendu.

**Correctifs (S5R-01)** : un seul appel par jeton côté client ; côté API, un jeton déjà consommé **par un compte déjà vérifié** répond « Votre adresse est déjà vérifiée » (succès) au lieu d'une erreur ; ajout d'un bouton « Renvoyer l'email de vérification » (il n'existait pas : un lien expiré obligeait à tout recommencer) ; conservation de la saisie du formulaire d'inscription (sauf les mots de passe) en cas d'erreur.

### 1.2 La question « Travaillez-vous à Senlis ? » ne suit pas le profil (S5R-05)

**Ce qui se passe.** Le profil ne connaît le travail que par `travailleQuartier`. Or `null` y veut dire **deux choses** : « ne travaille pas à Senlis » **ou** « on ne lui a jamais demandé » (ambiguïté assumée dans le dictionnaire de données, doc 03). Le moteur d'enquête ne peut donc pas préremplir « Non », et une réponse « Non » ne peut rien écrire dans le profil (il n'y a pas de case pour « non »).

**Correctif (S5R-05)** : un vrai champ `travailleASenlis` (oui / non / inconnu) dans le profil, alimenté par l'inscription, « Mon compte » et l'enquête.

---

## 2. Tableau de triage complet

### Accessibilité

| Retour | Catégorie | Issue |
|---|:--:|---|
| Bouton « X » du module d'accessibilité inatteignable en mode Malvoyance | 🔧 | S5R-03 — le panneau dépasse de l'écran quand le texte est agrandi : il deviendra défilant, avec un en-tête « Fermer » toujours visible |
| Interligne : une seule augmentation possible | 🔧 | S5R-03 — 3 niveaux + espacements des lettres, mots et paragraphes (voir §4.2) |
| Autres langues (IA type Mistral) | 🏛️ | L3-01 |
| Correction orthographe et grammaire des textes (IA) | 🏛️ | L3-02 |
| Voix de lecture plus naturelle que SpeechSynthesis | 🏛️ | L3-03 |
| Le cerf comme curseur de lecture au survol | 🏛️ | L3-04 |

### Page d'accueil

| Retour | Catégorie | Issue |
|---|:--:|---|
| Pourquoi 2 cerfs ? / On est loin de la maquette (le cerf guide) | 🔧 | S5R-04 — voir §4.1 |

### Connexion et inscription

| Retour | Catégorie | Issue |
|---|:--:|---|
| Jeton de vérification invalide, email jamais validé, champs à ressaisir | 🔧 **prioritaire** | S5R-01 (voir §1.1) |
| Œil pour afficher le mot de passe à la connexion | 🔧 | S5R-02 (le composant existe déjà à l'inscription) |
| Vérification de l'adresse email (@ obligatoire, domaine…) | 🔧 | S5R-02 (format + suggestion) — voir §4.3 ; **S5R-02b** (vérification DNS du domaine, ajoutée le 02/10) |
| Messages d'erreur à côté du champ concerné, pas en haut ; dès la saisie de l'email | 🔧 | S5R-02 — message sous chaque champ, vérifié dès qu'on quitte le champ |
| « Je travaille à Senlis » coché → quartier et rôle obligatoires, avec message | 🔧 | S5R-02 |
| Aide pour trouver son quartier (lien vers la carte, adresse non conservée) | 🏛️ | L3-05 (voir §4.4 : peut remonter en Lot 1 si souhaité) |

### Enquête stationnement (tests 1, 2, 3)

| Retour | Catégorie | Issue |
|---|:--:|---|
| Nombre total de questions affiché trop élevé, décourageant | 🔧 | S5R-05 — le total ne compte que les questions que CETTE personne verra, et se met à jour selon ses réponses |
| Q2 « Travaillez-vous à Senlis ? » non adaptée au profil, profil non mis à jour | 🔧 | S5R-05 (voir §1.2) |
| 0 véhicule → l'enquête doit s'arrêter (« Merci d'avoir participé ») | 🔧 | S5R-05 (fin anticipée) + S5R-06 |
| « Optionnel » à retirer sur les questions clés (où sont garés, difficultés, lesquelles, fréquence, autre frein…) | 🔧 | S5R-06 |
| Centre historique : remplacer « Utilisez-vous une voiture… » par « Vous arrive-t-il… » + « Pourquoi ? » (arrêt minute ≤ 5 min / arrêt long / autre → lequel) | 🔧 | S5R-06 |
| « Où sont garés vos véhicules » : pas plus de cases cochées que de véhicules ; retirer « je n'ai pas de véhicule » | 🔧 | S5R-05 (nombre de choix max lié à une réponse) + S5R-06 |
| Véhicules du foyer : préciser « hors véhicules professionnels » pour les actifs | 🔧 | S5R-06 |
| « Type d'activité : Autre » → « Laquelle ? » | 🔧 | S5R-06 (déjà possible avec le branchement actuel) |
| Véhicules professionnels « Oui » → 0 impossible au nombre | 🔧 | S5R-05 (bornes min/max d'un nombre) + S5R-06 |
| Questions foyer / pro identiques : une seule question adaptée ? | 💬 | §4.5 |
| Actifs hors centre venant en voiture : ne pas poser les questions de stationnement du centre ; demander s'ils viennent en centre-ville, et comment | 🔧 | S5R-05 (conditions multiples) + S5R-06 |

### Administration — enquêtes

| Retour | Catégorie | Issue |
|---|:--:|---|
| Résultats illisibles ; le sélecteur « ne change rien » | 🔧 | S5R-08 — voir §4.6 |
| Filtrer par public, puis par question ; graphiques | 🔧 | S5R-08 |
| Export des résultats en données (Excel, JSON…) | 🔧 | S5R-08 (CSV lisible par Excel + JSON ; toujours soumis au secret statistique) |
| Public visé par profil (tous, habitants, quartier, commerçants, gérants, salariés, hors Senlis…) | 🔧 | S5R-07 |
| Qu'est-ce qui est prévu dans l'aide ? Des exemples ? Simplifier la création | 🔧 | S5R-09 |

### Administration — propositions et carte

| Retour | Catégorie | Issue |
|---|:--:|---|
| Comment remplir le formulaire ? | 🔧 | S5R-10 (aides sous chaque champ) |
| Zone : toute la ville ou sélection de quartiers | 🔧 | S5R-10 |
| Latitude/longitude et GeoJSON masqués sous un lien « Avancé » | 🔧 | S5R-10 |
| « Explorer la carte » mène à la proposition, pas aux enquêtes ; voir tout par quartier | 🔧 | S5R-11 |
| Vrais emplacements des parkings | 🔧 | S5R-11 (source des données : voir §4.8) |
| Sélection plus fine à l'intérieur d'un quartier (ex. le seul centre historique) | 🏛️ | L3-06 |
| Et si les contours IRIS sont mis à jour ? | 💬 | §4.7 → procédure documentée en S5R-13 |

### Administration — navigation

| Retour | Catégorie | Issue |
|---|:--:|---|
| Pour l'admin : retirer « Propositions » et « Enquêtes » du menu, bouton « Voir » dans les listes admin | 🔧 | S5R-12 |
| Tableau de bord (connexions, emails envoyés) | 🏛️ | L3-10 |

### Sécurité, RGPD, authentification, notifications

| Retour | Catégorie | Issue |
|---|:--:|---|
| Audit aussi avec le NIST | 🔧 | S5R-13 — voir §4.9 |
| 2 emails avant suppression (dont un 1 mois avant) | 🏛️ | L3-07 (voir §4.10 : la purge et le 1er email existent déjà) |
| Connexion par QR code, Google… | 🏛️ | L3-08 (recommandation : FranceConnect plutôt que Google) |
| Notifications ciblées (nouvelle proposition / enquête) et résultats | 🏛️ | L3-09 (le Lot 2 prévoit déjà des notifications simples : S7-03/S7-04) |

---

## 3. Ordre de traitement proposé

Les issues sont numérotées dans l'ordre où elles seront traitées :

1. **Ce qui bloque un citoyen dès la première visite** : S5R-01 (vérification d'email), S5R-02 (formulaires), S5R-03 (module d'accessibilité), puis S5R-02b (vérification DNS du domaine de l'email, ajoutée le 02/10).
2. **Ce qui se voit en premier** : S5R-04 (accueil et cerf guide).
3. **L'enquête, cœur du projet** : S5R-05 (moteur), puis S5R-06 (contenu), qui en dépend.
4. **Les outils de l'administratrice** : S5R-07 (public visé), S5R-08 (résultats), S5R-09 (création), S5R-10 (propositions).
5. **La carte, la navigation admin et la documentation** : S5R-11, S5R-12, S5R-13.

Total estimé : **14,5 jours** (S5R-02b comprise). La mise en ligne réelle (Sprint 5ter) est décalée d'autant — c'est le prix d'une première impression réussie auprès des Senlisiens et de la mairie.

---

## 4. Réponses aux questions posées

### 4.1 Pourquoi deux cerfs sur l'accueil ?

Il y a deux composants différents : `Mascot` (le grand cerf illustratif du hero, avec sa bulle « Bienvenue à Senlis ! ») et `MascotWidget` (le petit cerf flottant « Découvrez le site ! », présent sur toutes les pages). Sur l'accueil, les deux s'affichent en même temps et racontent la même chose. Dans la maquette (prototype `17`), le cerf flottant est un vrai **guide** : un panneau « Cerf-tifié utile ! » avec un message d'accueil et des questions fréquentes (« Comment voter ? », « Mes données ? », « Pourquoi participer ? »). S5R-04 rétablit ce guide et garde un seul cerf visible à la fois sur l'accueil.

### 4.2 L'interligne actuel répond-il au RGAA ?

Le critère RGAA 10.12 (WCAG 1.4.12) n'exige pas que le site **propose** un réglage d'espacement : il exige que le contenu **reste lisible** quand la personne applique elle-même, avec son navigateur ou une extension, un interlignage de 1,5, un espacement de 2× la taille du texte entre paragraphes, de 0,12× entre les lettres et de 0,16× entre les mots. Le module d'accessibilité est donc un plus, pas une obligation. S5R-03 lui donnera 3 niveaux (1,5 · 1,8 · 2) et appliquera aussi les espacements de lettres et de mots, en vérifiant qu'aucun texte n'est coupé.

### 4.3 Peut-on vérifier le domaine de l'adresse email (gmail.com, free.fr…) ?

On vérifie le **format** (un « @ », un domaine avec un point, pas d'espace). En revanche, une liste de domaines « autorisés » bloquerait des adresses légitimes (orange.fr, laposte.net, domaines professionnels, adresses de la mairie…) : il en existe des milliers. La bonne pratique est double : **suggérer une correction** pour les fautes de frappe courantes (« gmial.com → vouliez-vous dire gmail.com ? ») et laisser l'**email de vérification** faire la vraie preuve — une adresse qui ne reçoit pas le lien ne sera jamais vérifiée.

**Complément (02/10/2026, S5R-02b)** : une vérification DNS côté API refusera en plus les domaines **certainement** incapables de recevoir du courrier (domaine inexistant, ou « null MX »). Elle teste les MX puis l'adresse principale du domaine (« MX implicite », RFC 5321), s'arrête au bout de 2 secondes et **accepte** l'adresse en cas de panne DNS : on ne bloque jamais quelqu'un de bonne foi parce que le réseau hésite. Elle ne remplace ni la suggestion de faute de frappe (beaucoup de domaines-pièges ont un serveur de messagerie), ni l'email de vérification.

### 4.4 Trouver son quartier à partir de son adresse

C'est techniquement simple et gratuit : l'**API Adresse** de l'État (api-adresse.data.gouv.fr, BAN) renvoie les coordonnées d'une adresse, et le quartier IRIS se déduit des contours déjà présents dans le projet. L'adresse n'a pas besoin d'être conservée (calcul à la volée, seul le quartier est enregistré). Classé en Lot 3 comme demandé ; il pourrait toutefois remonter au Lot 1 s'il s'avère que beaucoup d'inscrits se trompent de quartier.

### 4.5 Une seule question « véhicules » qui s'adapte (foyer / professionnel) ?

Recommandation : **garder deux questions distinctes**, mais avec la même formulation de base. Deux raisons : les résultats restent directement comparables (« véhicules du foyer » et « véhicules professionnels » sont deux chiffres que la mairie voudra voir séparément), et le moteur reste simple (une question = une donnée). Pour simplifier la vie de l'administratrice, S5R-09 ajoutera un bouton « Dupliquer la question » dans le constructeur.

### 4.6 Pourquoi le sélecteur de segmentation « ne change rien » ?

Il fonctionne, mais il **ajoute** les résultats par segment **sous chaque question**, au lieu de **filtrer** : la page s'allonge sans que la vue d'ensemble change. S5R-08 inverse la logique : on choisit d'abord un **public** (tous, résidents du centre, actifs à Senlis…), puis une **question** (ou toutes), et on obtient des graphiques ; l'export reprend le même filtre.

### 4.7 Et si les contours IRIS sont redécoupés ?

Les contours IRIS sont publiés par l'IGN et l'INSEE chaque année, mais les **redécoupages** sont rares (ils suivent surtout les évolutions de population, en lien avec le recensement). Rien n'est automatique dans le projet, et c'est voulu : le fichier `iris-senlis.geojson` est figé, et les quartiers sont une liste fixe en base (`enum Quartier`). En cas de redécoupage, il faudra : remplacer le fichier GeoJSON, faire une migration qui ajoute les nouveaux quartiers et **fait correspondre** les anciens aux nouveaux, et décider pour chaque enquête en cours si elle garde l'ancien découpage (recommandé : oui, jusqu'à sa clôture, pour ne pas mélanger deux découpages dans les mêmes résultats). Cette procédure sera écrite en S5R-13.

### 4.8 Les vrais emplacements de parkings

Deux sources ouvertes possibles : **OpenStreetMap** (parkings cartographiés par des contributeurs, extraction ponctuelle en GeoJSON) et la **Base nationale des lieux de stationnement** publiée sur data.gouv.fr (alimentée par les collectivités — à vérifier si Senlis y figure). Dans les deux cas, on fera une **extraction figée** dans un fichier du projet, pas un appel en direct : aucune adresse IP de visiteur n'est transmise, et la carte ne dépend pas d'un service extérieur.

### 4.9 Audit NIST

Le NIST est l'organisme de normalisation américain. Deux documents sont pertinents : le **SP 800-63B** (authentification) et le **Cybersecurity Framework 2.0**. Un point mérite attention : le NIST déconseille les règles de composition (« une majuscule, un chiffre… ») et recommande plutôt une longueur minimale et la vérification que le mot de passe ne figure pas dans une liste de mots de passe déjà divulgués ; la CNIL, elle, accepte les deux approches. Le projet suit la CNIL (autorité française, et c'est elle qui contrôlerait la mairie) ; S5R-13 ajoutera à l'audit (doc 21) une section NIST et évaluera l'ajout d'un contrôle des mots de passe divulgués.

### 4.10 RGPD : emails et purge automatique

La purge automatique **existe déjà** (S5A-05) : comptes inactifs depuis 3 ans supprimés 30 jours après **un** email d'avertissement, jetons effacés, journal d'administration purgé après 6 mois. Il reste à la **brancher** sur une tâche planifiée à la mise en ligne (S5-22). La demande nouvelle est un **second** email (un mois avant, puis quelques jours avant) : classée en Lot 3 (L3-07), car elle relève d'un choix de la future responsable de traitement.

---

## 5. Propositions Lot 3 (à présenter à la mairie)

| ID | Proposition | Remarques |
|---|---|---|
| L3-01 | Site multilingue (traduction assistée par IA, ex. Mistral) | Mistral est français, hébergement européen possible — à cadrer (contrat, RGPD) |
| L3-02 | Aide à la rédaction : orthographe et grammaire des propositions et des textes d'administration (IA) | Suggestions, jamais de correction imposée |
| L3-03 | Lecture à voix haute plus naturelle | Voix de synthèse neuronales : services payants, ou modèle hébergé |
| L3-04 | Le cerf comme curseur de la lecture au survol | Uniquement si la lecture au survol est activée |
| L3-05 | Trouver son quartier depuis son adresse (non conservée) | API Adresse de l'État, gratuite (voir §4.4) |
| L3-06 | Zonage plus fin qu'un quartier IRIS (ex. le seul centre historique) | Dessin de zones personnalisées par l'administratrice |
| L3-07 | Second email avant suppression d'un compte inactif | Voir §4.10 |
| L3-08 | Connexion simplifiée : FranceConnect, QR code | FranceConnect plutôt que Google : identité vérifiée par l'État, pas de transfert vers un acteur américain |
| L3-09 | Notifications ciblées selon le profil, et annonce des résultats | Consentement et désinscription en un clic (CNIL) ; base : S7-03/S7-04 |
| L3-10 | Tableau de bord d'administration (connexions, emails envoyés…) | Statistiques agrégées, sans suivi individuel |
