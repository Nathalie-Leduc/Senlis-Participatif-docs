# Diagramme d'architecture — Senlis Participatif (bonus)

> Architecture découplée client / API / base de données, en monorepo (`client/` + `api/`). La règle d'or qui structure tout : **le front affiche, l'API décide, la base garantit**.
>
> **Mise à jour 23/09/2026** : hébergement souverain Clever Cloud dès le Lot 1 (Railway abandonné), domaine `senlis-participatif.fr` chez OVH, emails Brevo, API officielle `geo.api.gouv.fr` pour les suggestions de ville, images servies par l'API.

```mermaid
flowchart LR
    subgraph Clients
        direction TB
        R["React 19 SPA — senlis-participatif.fr<br/>(Vite + Sass + react-leaflet)<br/>site statique Clever Cloud"]
        M["Application mobile<br/>(évolution future)"]
    end

    subgraph API ["API Node.js 22 / Express 5 — api.senlis-participatif.fr/api/v1"]
        direction TB
        SEC["Chaîne de middlewares<br/>Helmet · CORS · rate limit<br/>JWT (+ 2FA admin) · validation Zod"]
        CTRL["Contrôleurs<br/>(logique métier)"]
        LIB["lib/ + services/<br/>jwt · token · email · Sharp"]
        PR["Prisma 7 ORM<br/>(adapter pg)"]
        UP[("/uploads<br/>images WebP")]
        SEC --> CTRL --> PR
        CTRL --> LIB
        LIB --> UP
    end

    DB[("PostgreSQL managé<br/>Clever Cloud (Paris)<br/>contraintes d'unicité + transactions")]

    subgraph Externes ["Services externes"]
        direction TB
        OSM["Tuiles OpenStreetMap<br/>(fonds de carte)"]
        IGN["Contours IRIS<br/>(GeoJSON statique, IGN — servi par le client)"]
        GEO["geo.api.gouv.fr (DINUM)<br/>suggestions de communes"]
        SMTP["SMTP : Mailtrap (dev)<br/>Brevo (prod, DKIM + DMARC)"]
    end

    R -- "JSON / HTTPS" --> SEC
    R -- "images /uploads" --> UP
    M -. "mêmes endpoints" .-> SEC
    R -- "tuiles raster" --> OSM
    R -- "couche carte" --> IGN
    R -- "autocomplétion ville" --> GEO
    LIB -- "Nodemailer" --> SMTP
    PR --> DB
```

## Lecture du diagramme

**Les clients sont interchangeables.** Le site React et la future application mobile frappent à la même porte (`/api/v1`) et reçoivent le même JSON. C'est la cuisine de restaurant : la salle (web) et la livraison (mobile) sont servies par la même cuisine, avec le même menu. (La documentation Swagger du « menu » est prévue, pas encore implémentée.)

**Toute requête traverse la chaîne de sécurité dans le même ordre.** Helmet durcit les en-têtes, CORS filtre les origines (sans gêner les clients mobiles, qui n'envoient pas d'en-tête `Origin`), le rate limiting protège l'authentification de la force brute, le JWT identifie, Zod assainit. Aucun contrôleur ne voit jamais une donnée brute. Un compte admin passe en plus par un code à 6 chiffres reçu par email.

**Deux domaines, une même « famille ».** Le site (`senlis-participatif.fr`) et l'API (`api.senlis-participatif.fr`) sont deux origines différentes mais le même *site* au sens des navigateurs : c'est ce qui permet d'autoriser les images de l'API avec `Cross-Origin-Resource-Policy: same-site` (voir audit 21, §7).

**La carte ne coûte rien au serveur.** Les tuiles OpenStreetMap, les contours IRIS et les suggestions de villes sont chargés directement par le navigateur : l'API n'est sollicitée que pour les données métier. Contrepartie RGPD à assumer : l'adresse IP du visiteur est vue par OSM et par la DINUM — c'est mentionné dans la politique de confidentialité.

**Environnements.** Développement : `docker compose` (PostgreSQL, et API en option) + `vite dev`, emails capturés par Mailtrap, deux bases distinctes (dev et test). Production : Clever Cloud (France, ISO 27001, hors Cloud Act) — l'API est déployée par `git push` sans Docker, le client comme site statique avec redirection SPA vers `index.html`.
