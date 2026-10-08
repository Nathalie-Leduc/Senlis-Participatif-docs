# Guide Territorial Complet : API Citoyenne, Cadre Juridique, Hébergement Souverain et Stratégie Réseaux Sociaux

Ce document unique et centralisé regroupe l'intégralité des guides techniques, juridiques, stratégiques et de communication nécessaires pour lancer, optimiser et faire connaître votre application de participation citoyenne (propositions, votes, enquêtes) auprès de la population locale et des municipalités en France.

---
---

# PARTIE 1 : Guide Juridique, Technique et Commercial pour l'API Mairie

## 1. Phase de Lancement (Bénévole & Gratuit) : Les Obligations Juridiques

Même si le projet est gratuit et indépendant, le **RGPD (Règlement Général sur la Protection des Données)** s'applique dès la collecte de la première donnée personnelle (comme une adresse email).

### Déclaration CNIL
* **Non, il n'y a plus de déclaration à faire.** Depuis l'entrée en vigueur du RGPD en 2018, les déclarations préalables de fichiers sont supprimées. 
* Elles sont remplacées par une obligation d'auto-contrôle. Vous devez tenir en interne un **Registre des activités de traitement** (un simple document listant la nature des données collectées, leur finalité, leur durée de conservation et les accès).

### Documents et Outils obligatoires sur le site
* **Les Mentions Légales :** Obligatoires en France. Elles doivent mentionner l'identité de l'éditeur (nom, prénom, adresse, email) et les coordonnées complètes de l'hébergeur.
* **La Politique de Confidentialité :** Un texte accessible expliquant clairement l'usage des données, la durée de conservation (gérée via vos scripts de nettoyage de comptes) et la méthode pour exercer le droit de suppression.
* **Gestion des Cookies :** Si vous utilisez des outils de suivi tiers (outils d'analyse d'audience, etc.), l'intégration d'un gestionnaire de consentement (comme *Tarteaucitron*) est obligatoire. Pour les cookies strictement techniques (maintien de la session de connexion), le consentement n'est pas requis.

### Sensibilité des Données
Les données de vote ou de participation peuvent révéler indirectement des opinions politiques, classées comme **données sensibles** par la CNIL. L'API doit être hautement sécurisée (mots de passe hachés avec des algorithmes robustes comme `bcrypt` ou `argon2`, connexions chiffrées en HTTPS et isolation/anonymisation des tables de vote).

---

## 2. Commercialisation auprès d'une Mairie : Structure & Règles Publics

Pour pouvoir facturer une mairie, vous devez obligatoirement adopter une structure juridique professionnelle.

### Quel statut juridique choisir ?
* **La Micro-entreprise (Auto-entrepreneur) :** C'est le statut idéal pour démarrer seul. La création est gratuite, rapide, et les charges ne sont payées que sur le chiffre d'affaires réellement encaissé.
* **Règle des Marchés Publics :** En dessous du seuil de **40 000 € HT**, une mairie peut signer un contrat de gré à gré (un simple devis signé) sans obligation de lancer un appel d'offres public. Au-delà, ou si le projet grandit à plusieurs, une bascule vers une société (SASU, EURL, SAS) sera nécessaire.

### Les exigences spécifiques des communes
* **L'Accessibilité (RGAA) :** Les outils numériques des collectivités doivent être accessibles aux personnes en situation de handicap (normes RGAA / WCAG). Veillez à la sémantique HTML et aux contrastes dès la conception de l'interface.
* **La facturation via Chorus Pro :** Les mairies ne paient pas sur facture classique. Vous devrez obligatoirement déposer vos factures au format électronique sur la plateforme de l'État **Chorus Pro** en utilisant votre numéro SIRET.
* **Le rôle RGPD :** Lors de la signature du contrat, la mairie devient réglementairement le *Responsable du traitement* et vous devenez son *Sous-traitant*. Le contrat doit inclure une clause de sous-traitance RGPD claire.

---

## 3. Infrastructure et Hébergement Souverain

En France, les données des citoyens gérées par une collectivité territoriale répondent à une exigence de souveraineté numérique.

### Le choix de l'hébergeur (Cloud de Confiance)
Il est indispensable de bannir les serveurs soumis au *Cloud Act* américain (AWS, Google Cloud, Microsoft Azure ou les configurations par défaut de Railway/Render situées aux États-Unis). Vous devez privilégier des hébergeurs dont les infrastructures sont situées en Europe, et idéalement en France :
* **Scaleway** (Infrastructure cloud française moderne)
* **OVHcloud** (Leader européen, infrastructures basées en France)
* **Clever Cloud** (Plateforme PaaS française facilitant le déploiement de code et de bases de données gérées)

### Stratégies de déploiement : SaaS vs On-Premise

```
┌────────────────────────────────────────────────────────┐
│               Votre infrastructure (SaaS)              │
│  ┌──────────────────┐           ┌──────────────────┐   │
│  │     Votre API    │◄──────────┤  Base de données │   │
│  └────────▲─────────┘           └──────────────────┘   │
└───────────┼────────────────────────────────────────────┘
            │ (Requêtes HTTPS sécurisées)
┌───────────┴────────────────────────────────────────────┐
│              Application Front-end Client              │
│  ┌──────────────────┐           ┌──────────────────┐   │
│  │   Site Mairie A  │           │   Site Mairie B  │   │
│  │ (Charte Ville A) │           │ (Charte Ville B) │   │
│  └──────────────────┘           └──────────────────┘   │
└────────────────────────────────────────────────────────┘
```

#### Option A : Le modèle SaaS (Recommandé)
L'API et la base de données PostgreSQL restent hébergées sur votre propre compte d'hébergement souverain. L'application est **multi-tenant** (chaque donnée est cloisonnée par une clé API ou un identifiant de commune).
* *Avantage :* Vous gardez la propriété intellectuelle exclusive de votre code via un contrat de licence d'utilisation non exclusif. Les mises à jour profitent à tous instantanément et vous facturez un abonnement récurrent.
* *Contrainte :* Vous devez inclure un contrat de niveau de service (SLA) garantissant la disponibilité de l'application et la gestion des pannes.

#### Option B : Le modèle On-Premise (Déploiement local)
La mairie exige que l'application tourne sur ses propres serveurs ou chez son hébergeur institutionnel.
* *Solution technique :* Vous devez conteneuriser votre API et votre base de données à l'aide de **Docker** et **Docker Compose** pour fournir un package standardisé installable en une ligne de commande par la DSI de la mairie. Vous facturez alors une licence fixe et un contrat de maintenance pour les mises à jour.

### Sauvegardes et Sécurité de production
* Mettre en place un processus automatisé de sauvegarde (*cron*) quotidienne de la base de données PostgreSQL, stocké de manière isolée avec un historique de 30 jours.
* Chiffrer l'ensemble des flux (transit) et des données sensibles au repos.

---
---

# PARTIE 2 : Guide Complet pour le Référencement Gratuit (SEO)

Pour être visible sur les moteurs de recherche sans débourser un centime dans la publicité (Google Ads), vous devez utiliser le **SEO (Search Engine Optimization)** ou référencement naturel.

## 1. Le signal de départ (Dire à Google que vous existez)

Par défaut, Google finira par trouver votre site, mais cela peut prendre des semaines. Vous pouvez accélérer gratuitement le processus en utilisant les outils officiels.

1. **Créer un compte Google Search Console :** C'est l'outil gratuit indispensable de Google pour suivre la santé de votre site. Connectez-vous et ajoutez l'adresse de votre site (votre "propriété").
2. **Valider la propriété de votre site :** Google va vous demander de prouver que ce site est bien le vôtre. Vous devrez ajouter une ligne de code (balise HTML) dans votre code source, ou ajouter un enregistrement DNS chez votre hébergeur.
3. **Soumettre votre plan de site (Sitemap) :** Générez un fichier `sitemap.xml` (la plupart des CMS ou des frameworks modernes comme SvelteKit ou Next.js le font automatiquement). Renseignez l'URL de ce fichier dans l'onglet "Sitemaps" de la Search Console. Cela donne la carte routière de votre site aux robots de Google (*Googlebots*).

## 2. L'optimisation "On-Page" (Aider Google à comprendre votre contenu)

Google analyse la structure sémantique de vos pages pour savoir si elles répondent à une **intention de recherche** précise (une question que se pose l'internaute).

* **La balise `<title>` :** C'est le titre bleu qui apparaît dans les résultats Google. Il doit faire entre 50 et 60 caractères, être accrocheur et contenir votre mot-clé principal dès le début.
* **La structure des titres (`Hn`) :** Structurez votre page de manière logique. Un seul titre `<h1>` (le titre principal de votre page), puis des sous-titres `<h2>` et `<h3>` pour vos sections. N'utilisez pas ces balises pour le design, mais bien pour hiérarchiser votre texte.
* **L'optimisation des images :** Les robots ne voient pas les images, ils lisent leur description. 
  * Renommez vos fichiers de manière explicite (ex: `projet-piste-cyclable-centre.webp` au lieu de `IMG_4829.jpg`).
  * Remplissez systématiquement l'attribut **`alt`** (le texte alternatif) avec une description claire de l'image.

## 3. La technique et l'expérience utilisateur

Google privilégie les sites agréables, sécurisés et fluides pour les internautes. Trois critères techniques gratuits sont indispensables :

* **Le Mobile-First :** Assurez-vous que votre site est parfaitement *responsive* (lisible et navigable sur smartphone). Plus de 60% des recherches se font sur mobile, et Google indexe désormais la version mobile en priorité.
* **La vitesse (Performance) :** Un internaute quitte généralement une page si elle met plus de 3 secondes à charger. Compressez vos images (le format **WebP** ou **AVIF** est vivement recommandé, idéalement moins de 300 Ko par image) et minifiez votre code.
* **La sécurité (HTTPS) :** Activez un certificat SSL gratuit (souvent fourni par défaut via *Let's Encrypt* chez la plupart des hébergeurs ou des plateformes de déploiement). Google pénalise les sites affichant la mention "Non sécurisé".

## 4. Développer la popularité (Le Netlinking)

Pour Google, si des sites de confiance parlent de vous, c'est que votre site a de la valeur. Vous devez obtenir des **backlinks** (des liens provenant d'autres sites web vers le vôtre).

* **Les articles invités :** Proposez à un blog ou un site partenaire de rédiger gratuitement un article de qualité pour eux, en échange d'un lien permanent vers votre site au milieu du texte.
* **Les réseaux et forums spécialisés :** Participez à des discussions techniques ou thématiques et intégrez votre lien *uniquement* si cela apporte une vraie réponse ou une forte valeur ajoutée à la discussion.

## 5. Le Référencement au-delà de Google

* **Bing (Microsoft) :** Utilisez l'outil gratuit **Bing Webmaster Tools** pour importer votre configuration Google Search Console en un seul clic. Cela vous référence sur Bing, mais également sur **Ecosia** et **DuckDuckGo**.
* **Moteurs IA (AIO - AI Optimization) :** Pour que votre application ou documentation soit citée par **ChatGPT (Search)**, **Perplexity** ou **Claude**, produisez un contenu hautement structuré et utilisez des données de balisage (*Schema.org*) clairs.

---
---

# PARTIE 3 : Stratégie de Visibilité sur les Réseaux Sociaux à l'Échelle Locale

Pour toucher spécifiquement les habitants de votre commune et des environs, la stratégie sur les réseaux sociaux doit être ultra-locale et participative. Le but est de transformer votre application en un sujet de conversation incontournable dans la vie quotidienne de la commune.

## 1. Investir les groupes locaux existants (Là où sont déjà les citoyens)

Avant de chercher à construire une communauté à partir de zéro, allez là où les habitants de votre ville se rassemblent déjà virtuellement pour s'échanger des infos de proximité.

* **Les groupes Facebook de la ville :** C'est souvent là que bat le cœur de la communauté locale. Cherchez des groupes du type *"Tu sais que tu viens de [Nom de votre ville] quand..."*, *"Habitants de [Nom de votre ville]"* ou *"Entraide [Nom de votre ville]"*.
* **La méthode d'approche :** Ne venez pas faire de la publicité agressive (votre publication serait supprimée par les modérateurs). Présentez-vous comme un habitant qui a développé bénévolement un outil gratuit pour améliorer la commune. Posez une question ouverte pour inciter au débat.
  > *Exemple de message : « Bonjour à tous ! Habitant de [Ville] depuis plusieurs années, j'ai développé un outil 100% gratuit et indépendant pour que nous puissions proposer et voter pour des projets dans notre commune (pistes cyclables, parcs, événements...). Que pensez-vous de la piétonnisation du centre-ville ou du manque de commerces ? Venez donner votre avis ici : [Lien]. J'attends vos retours avec impatience ! »*

## 2. Le choix des plateformes et formats percutants

Selon la cible que vous visez, adaptez vos contenus pour susciter l'engagement :

* **Facebook (Cible active, familles, commerçants) :** Créez une page officielle pour votre projet. C'est le meilleur endroit pour publier des sondages écrits, partager les résultats d'une enquête en cours ou relayer une proposition citoyenne qui fait débat.
* **Instagram & TikTok (Cible plus jeune / trentenaires) :** C'est le visuel qui prime. Faites de courtes vidéos (Reels/Shorts) au format vertical. 
  * Filmez-vous en marchant dans un quartier de la ville en montrant un problème précis (ex: un passage piéton dangereux, un manque de verdure).
  * Dites : *"Regardez ce carrefour... Un habitant a proposé d'y installer un feu ou une zone 30 sur notre application. Vous êtes pour ou contre ? Le lien est en bio pour voter !"*
* **LinkedIn (Cible professionnelle / Élus) :** Publiez des retours d'expérience sur les coulisses techniques et la démarche citoyenne de votre projet. C'est ici que les agents de la mairie, les élus locaux ou les DSI des communes voisines remarqueront votre expertise pour la future phase payante.

## 3. Les tactiques algorithmiques pour verrouiller la géolocalisation

Pour force les algorithmes (Instagram, Facebook, TikTok) à montrer vos publications aux personnes qui vivent près de chez vous :

* **Identifiez systématiquement le lieu :** Ajoutez la localisation géographique précise de votre ville sur *chaque* publication, Reel ou Story.
* **Utilisez des hashtags locaux :** Utilisez des hashtags de niche géographiques (ex: `#NomDeVotreVille`, `#SecteurDeVotreVille`, `#Commerces[NomDeVille]`, `#MaVille`). Évitez les hashtags trop génériques comme `#Politique` ou `#CivicTech` qui vous noieraient dans la masse mondiale.
* **Mentionnez les acteurs locaux :** Si une proposition citoyenne concerne un quartier proche d'un commerce connu ou d'une association locale, tagguez-les de manière bienveillante pour qu'ils partagent votre publication en Story.

## 4. Créer des rendez-vous réguliers (Le Storytelling municipal)

L'engagement repose sur la récurrence. Pour créer une habitude chez les citoyens, mettez en place des formats hebdomadaires :

* **« La proposition de la semaine » :** Chaque lundi, mettez en avant une idée soumise par un citoyen sur votre API (ex: *"Marc propose d'installer des composteurs partagés dans le quartier de la Gare"*). Créez un visuel simple et demandez aux internautes de débattre en commentaires avant d'aller voter sur l'application.
* **« Le point Enquête » :** Quand une enquête se termine, publiez une infographie claire des résultats (ex: *"82% des votants souhaitent plus de pistes cyclables"*). C'est extrêmement valorisant pour la population de voir que leur vote produit une donnée concrète.

## 5. Le hack "Phygital" gratuit (Le terrain au service du virtuel)

Pour donner le premier coup d'accélérateur à vos réseaux sociaux, n'hésitez pas à utiliser des ponts entre le monde réel et le numérique :

1. Générez un **QR Code** gratuit pointant vers le site ou les réseaux de votre application.
2. Imprimez quelques affichettes simples mettant en avant une question choc (ex: *"Une idée pour améliorer notre ville ? Proposez et votez !"*).
3. Demandez poliment aux commerçants du centre-ville (boulangeries, cafés, bureaux de tabac) s'il est possible de coller votre QR Code sur leur comptoir ou leur vitrine. C'est un flux de trafic local, gratuit et ultra-qualifié.
