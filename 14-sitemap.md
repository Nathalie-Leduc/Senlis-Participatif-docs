# Sitemap — Senlis Participatif

> Arborescence de navigation, par niveau d'accès : 🔓 public · 🔐 citoyen connecté · 👑 admin. Les nœuds pointillés = Lot 2.

```mermaid
flowchart TD
    HOME["🔓 / <br/>Accueil<br/>carte + à la une"]

    HOME --> PROPS["🔓 /propositions<br/>liste, filtres, tri"]
    PROPS --> PROP["🔓 /propositions/:slug<br/>argumentaire, carte,<br/>votes 🔐, débat"]
    PROP -.-> PROPOSE["🔐 /proposer<br/>soumettre une proposition<br/>(Lot 2)"]:::lot2

    HOME --> SURVEYS["🔓 /enquetes<br/>ouvertes / closes"]
    SURVEYS --> SURVEY["🔐 /enquetes/:slug<br/>répondre (1 fois)"]
    SURVEYS --> RESULTS["🔓 /enquetes/:slug/resultats<br/>agrégats si publiés"]

    HOME --> AUTH["🔓 /inscription · /connexion<br/>+ vérification email,<br/>mot de passe oublié"]
    AUTH --> ACCOUNT["🔐 /mon-compte<br/>profil, suppression RGPD,<br/>notifications (Lot 2)"]

    HOME --> ADMIN["👑 /admin<br/>tableau de bord"]
    ADMIN --> ADMP["👑 /admin/propositions<br/>CRUD"]
    ADMIN --> ADME["👑 /admin/enquetes<br/>CRUD + constructeur"]
    ADME --> ADMS["👑 /admin/enquetes/:id/stats<br/>résultats détaillés"]
    ADMIN -.-> ADMM["👑 /admin/moderation<br/>commentaires + propositions<br/>(Lot 2)"]:::lot2

    HOME --> LEGAL["🔓 /mentions-legales<br/>/confidentialite"]
    HOME --> NF["🔓 /* — page 404"]

    classDef lot2 stroke-dasharray:5 5
```

**Principes de navigation**
- **Profondeur maximale : 3 clics** depuis l'accueil pour toute action citoyenne (voter, répondre) — parcours courts, exigence du public senior.
- **Tout le public est consultable sans compte** : on ne demande l'inscription qu'au moment de *participer* (voter, répondre), jamais pour *s'informer*. La barrière est au bon endroit.
- **La zone admin est un sous-arbre étanche** : préfixe `/admin`, garde de route côté front (redirection si non-ADMIN) doublée par le middleware côté API — la vraie protection est toujours côté serveur.
- Le footer porte les pages légales depuis chaque écran (obligation RGPD d'accessibilité permanente).
