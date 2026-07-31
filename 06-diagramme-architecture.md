# Diagramme d'architecture — Senlis Participatif (bonus)

> Architecture découplée client / API / base de données, en monorepo (`client/` + `api/`). La règle d'or qui structure tout : **le front affiche, l'API décide, la base garantit**.

```mermaid
flowchart LR
    subgraph Clients
        direction TB
        R["React 19 SPA<br/>(Vite + Sass + react-leaflet)"]
        M["Application mobile<br/>(évolution future)"]
    end

    subgraph API ["API Node.js 22 / Express 5 — /api/v1"]
        direction TB
        SEC["Chaîne de middlewares<br/>Helmet · CORS · rate limit<br/>JWT · validation Zod"]
        CTRL["Contrôleurs<br/>(logique métier)"]
        PR["Prisma ORM"]
        SEC --> CTRL --> PR
    end

    DB[("PostgreSQL<br/>contraintes d'unicité<br/>+ transactions")]

    subgraph Externes ["Services externes"]
        direction TB
        OSM["Tuiles OpenStreetMap<br/>(fonds de carte)"]
        IGN["Contours IRIS<br/>(GeoJSON statique, INSEE/IGN)"]
        SMTP["Serveur SMTP<br/>(emails transactionnels)"]
    end

    R -- "JSON / HTTPS" --> SEC
    M -. "mêmes endpoints" .-> SEC
    R -- "tuiles raster" --> OSM
    R -- "couche carte" --> IGN
    CTRL -- "Nodemailer" --> SMTP
    PR --> DB
```

## Lecture du diagramme

**Les clients sont interchangeables.** Le site React et la future application mobile frappent à la même porte (`/api/v1`) et reçoivent le même JSON. C'est la cuisine de restaurant : la salle (web) et la livraison (mobile) sont servies par la même cuisine, avec le même menu (le contrat Swagger).

**Toute requête traverse la chaîne de sécurité dans le même ordre.** Helmet durcit les en-têtes, CORS filtre les origines (sans gêner les clients mobiles, qui n'envoient pas d'en-tête `Origin`), le rate limiting protège l'authentification de la force brute, le JWT identifie, Zod assainit. Aucun contrôleur ne voit jamais une donnée brute.

**La carte ne coûte rien au serveur.** Les tuiles OpenStreetMap et les contours IRIS sont chargés directement par le navigateur : l'API n'est sollicitée que pour les données métier (propositions, votes, enquêtes). Sobriété et performance.

**Environnements.** En développement : `docker compose` (API + PostgreSQL), front en `vite dev`. En production (Lots 1-2) : Railway. Lot 3 : migration vers un hébergeur souverain (OVHcloud / Scaleway) — l'application étant conteneurisée, la portabilité est acquise par construction.
