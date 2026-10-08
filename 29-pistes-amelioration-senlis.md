# Pistes d'amélioration — Senlis Participatif

> Notes de réflexion — 16 juin 2026. Idées complémentaires aux Lots 1-2, classées par impact.

---

## 1. Ce qui ferait la différence pour la démo mairie

### Un « mode démo » avec des données réalistes

Quand tu présenteras le site à la mairie de Senlis, il faudra que ça vive : la proposition piétonnisation avec 180 votes, l'enquête stationnement avec 100 réponses crédibles, des pseudos réalistes ("Marie du centre", "Pierre_commerçant"), des résultats d'enquête qui racontent une histoire. Ton `seed.js` devrait pouvoir remplir la plateforme en un `npm run seed:demo` avec tout ça. C'est la différence entre montrer un prototype vide et montrer un produit qui tourne.

### Un QR code sur la proposition pilote

Imagine un A4 affiché chez les commerçants du centre : le cerf mascotte, "Donnez votre avis sur la piétonnisation du samedi", et un QR code qui mène directement à la page de vote. Tu peux générer le QR code gratuitement — l'important c'est de le prévoir dans ta communication. Le site ne sert à rien si personne ne sait qu'il existe.

### Les pages légales

En France, c'est obligatoire pour tout site public : mentions légales (hébergeur, responsable de publication) et politique de confidentialité (quelles données, pourquoi, combien de temps, droits de la personne). Elles sont dans le sitemap mais pas encore codées. Pour la démo mairie, leur absence serait remarquée — ça montre le sérieux du projet.

---

## 2. Ce qui améliorerait l'expérience utilisateur

### Le partage social (Open Graph)

Quand quelqu'un partage le lien de la proposition sur Facebook ou dans un groupe WhatsApp local, il faut que l'aperçu soit beau — une image, un titre, une description. Ce sont les balises Open Graph dans le `<head>`. Sans elles, le lien partagé affiche un rectangle gris triste. Avec elles, le cerf apparaît dans l'aperçu, le titre "Piétonnisation du centre le samedi — Votez !" donne envie de cliquer. Pour un projet civique qui se diffuse par le bouche-à-oreille local, c'est crucial.

### Une PWA légère

Ajouter un `manifest.json` et un service worker minimal permettrait aux gens d'« installer » le site sur leur écran d'accueil comme une app. Pas besoin d'aller sur le Play Store — un simple "Ajouter à l'écran d'accueil" et le cerf apparaît sur le téléphone. Pour le public senior visé, c'est un raccourci familier. Ça se fait en une demi-journée et Vite a un plugin (`vite-plugin-pwa`) qui automatise la majeure partie.

### Un Error Boundary React

En ce moment, si un composant plante (donnée inattendue de l'API, par exemple), tout le site affiche une page blanche. Un Error Boundary attrape l'erreur et affiche le cerf avec "Oups, quelque chose s'est mal passé — rechargez la page". C'est 20 lignes de code et ça évite de perdre un utilisateur.

---

## 3. Ce qui pourrait se discuter pour plus tard

### Le SEO

Le site est une SPA React — Google voit une page blanche au premier chargement. Pour un site civique local, ce n'est pas dramatique (les gens arrivent par QR code ou lien direct, pas par Google). Mais si tu veux que "participation citoyenne Senlis" remonte dans les résultats, il faudrait envisager le SSR (avec Next.js ou un pré-rendu statique). C'est un chantier lourd, plutôt Lot 3.

### Une stratégie de mesure sans tracking

Comment sauras-tu si ça marche ? Pas besoin de Google Analytics (RGPD hostile, et contradiction avec l'esprit du projet). Mais un simple compteur côté API — nombre de comptes créés, nombre de votes, nombre de réponses d'enquête par jour — te donnerait un tableau de bord basique pour prouver à la mairie que la plateforme est utilisée. Un endpoint `/api/v1/stats` public avec les chiffres agrégés, que le hero pourrait afficher en temps réel dans les stat pills.

### Un nom de domaine

`senlis-participatif.fr` est probablement disponible. Pour la démo mairie, présenter une URL propre plutôt qu'un `.railway.app` fait meilleure impression. Un `.fr` coûte environ 7 € par an.
