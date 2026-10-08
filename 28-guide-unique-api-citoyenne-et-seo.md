# Guide Ultime : Déploiement d'une API Citoyenne & Référencement Google

Ce document unique regroupe l'intégralité des guides techniques, juridiques et stratégiques pour votre projet d'application de participation citoyenne et son optimisation.

---

# PARTIE 1 : Guide Juridique, Technique et Commercial pour l'API Mairie

## 1. Phase de Lancement (Bénévole & Gratuit) : Les Obligations Juridiques

Même si le projet est gratuit et indépendant, le **RGPD (Règlement Général sur la Protection des Données)** s'applique dès la collecte de la première donnée personnelle.

### Déclaration CNIL
* **Non, il n'y a plus de déclaration à faire.** Depuis 2018, les déclarations préalables de fichiers sont supprimées. 
* Elles sont remplacées par une obligation d'auto-contrôle. Vous devez tenir en interne un **Registre des activités de traitement** (un simple tableau listant la nature des données collectées, leur finalité, leur durée de conservation et les accès).

### Documents et Outils obligatoires sur le site
* **Les Mentions Légales :** Obligatoires en France. Elles doivent mentionner l'identité de l'éditeur (nom, prénom, adresse, email) et les coordonnées complètes de l'hébergeur.
* **La Politique de Confidentialité :** Un texte accessible expliquant clairement l'usage des données, la durée de conservation (gérée via vos scripts de nettoyage de comptes) et la méthode pour demander la suppression de ses données.
* **Gestion des Cookies :** Si vous utilisez des outils de suivi tiers (comme des outils d'analyse d'audience), l'intégration d'un gestionnaire de consentement (comme *Tarteaucitron*) est obligatoire. Pour les cookies strictement techniques (maintien de la session utilisateur), le consentement n'est pas requis.

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
* **Le rôle RGPD :** Lors de la signature du contrat, la mairie devient réglementairement le *Responsable du traitement* et vous devenez son *Sous-traitant*. Le contrat doit include une clause de sous-traitance RGPD claire.

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

# PARTIE 2 : Guide Complet pour le Référencement Gratuit (Google et autres)

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
  * Renommez vos fichiers de manière explicite (ex: `recette-tarte-citron.webp` au lieu de `IMG_4829.jpg`).
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

Bien que Google possède plus de 90% des parts de marché en France, optimiser sa présence sur d'autres canaux est une stratégie payante.

* **Bing (Microsoft) :** Utilisez l'outil gratuit **Bing Webmaster Tools** pour importer votre configuration Google Search Console en un seul clic. Cela vous référence sur Bing, mais également sur **Ecosia** et **DuckDuckGo**.
* **Moteurs IA (AIO - AI Optimization) :** Pour que votre application ou documentation soit citée par **ChatGPT (Search)**, **Perplexity** ou **Claude**, produisez un contenu hautement structuré et utilisez des données de balisage (*Schema.org*) clairs.
* **Référencement Local (Indispensable pour votre projet municipal) :** Créez une fiche gratuite **Google Business Profile**. Elle vous propulse directement en haut des résultats sur **Google Maps** pour toutes les requêtes locales liées à votre secteur d'activité, une visibilité cruciale avant même le référencement classique d'un site web.
