# Sitemap — Senlis Participatif

> Arborescence de navigation, par niveau d'accès : 🔓 public · 🔐 citoyen connecté · 👑 admin. Les nœuds pointillés = prévu (Lot 2 ou audit S5A-07).
>
> **Mise à jour 23/09/2026** : aligné sur `client/src/App.jsx` (branche `dev`). Il n'y a pas de page `/admin` « tableau de bord » : l'admin arrive directement sur ses trois rubriques depuis l'en-tête.

```mermaid
flowchart TD
    HOME["🔓 / <br/>Accueil<br/>hero, compteurs, carte"]

    HOME --> PROPS["🔓 /propositions<br/>liste, filtres, tri"]
    PROPS --> PROP["🔓 /propositions/:slug<br/>argumentaire, image, carte,<br/>votes 🔐"]
    PROP -.-> PROPOSE["🔐 /proposer<br/>(Lot 2)"]:::lot2

    HOME --> SURVEYS["🔓 /enquetes<br/>ouvertes / closes"]
    SURVEYS --> SURVEY["🔓 /enquetes/:slug<br/>présentation"]
    SURVEY --> ANSWER["🔐 /enquetes/:slug/repondre<br/>1 question par écran,<br/>une seule réponse"]
    SURVEY --> RESULTS["🔓 /enquetes/:slug/resultats<br/>si publiés par l'admin"]

    HOME --> AUTH["🔓 /inscription · /connexion<br/>/verification-email<br/>/mot-de-passe-oublie · /reset-password"]
    AUTH --> ACCOUNT["🔐 /mon-compte<br/>profil, situation, mot de passe,<br/>suppression RGPD"]

    HOME --> ADMP["👑 /admin/propositions<br/>liste"]
    ADMP --> ADMPF["👑 /admin/propositions/nouvelle<br/>/admin/propositions/:slug/modifier"]
    HOME --> ADME["👑 /admin/enquetes<br/>liste"]
    ADME --> ADMEF["👑 /admin/enquetes/nouvelle<br/>/admin/enquetes/:slug/modifier<br/>constructeur + branchement"]
    ADME --> ADMS["👑 /admin/enquetes/:id/stats<br/>résultats détaillés, segmentation,<br/>impression / PDF"]
    HOME --> ADMC["👑 /admin/comptes<br/>promouvoir / rétrograder"]
    HOME -.-> ADMM["👑 /admin/moderation<br/>(Lot 2)"]:::lot2

    HOME --> LEGAL["🔓 /mentions-legales<br/>/confidentialite"]
    HOME -.-> A11Y["🔓 /accessibilite · /plan-du-site<br/>(S5A-07)"]:::lot2
    HOME --> NF["🔓 /* — page 404"]

    classDef lot2 stroke-dasharray:5 5
```

**Principes de navigation**
- **Profondeur maximale : 3 clics** depuis l'accueil pour toute action citoyenne (voter, répondre) — parcours courts, exigence du public senior.
- **Tout le public est consultable sans compte** : on ne demande l'inscription qu'au moment de *participer* (voter, répondre), jamais pour *s'informer*. Un vote tenté sans être connecté est mémorisé (`sessionStorage`) puis rejoué après la connexion.
- **La zone admin est un sous-arbre étanche** : préfixe `/admin`, garde de route côté front (`ProtectedRoute adminOnly`) doublée par le middleware `isAdmin` côté API — la vraie protection est toujours côté serveur.
- Le footer porte les pages légales depuis chaque écran ; il portera aussi « Accessibilité » et « Plan du site » (RGAA 12.1 : deux systèmes de navigation).
