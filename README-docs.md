# 📚 senlis-participatif-docs

> Documentation de conception du projet [Senlis Participatif](https://github.com/Nathalie-Leduc/senlis-participatif) — de l'idée au plan d'exécution. Les diagrammes sont en Mermaid : GitHub les affiche nativement.
>
> **Dernière revue complète : 23/09/2026** — tous les documents ont été confrontés au code de la branche `dev` (le code fait foi) et un audit sécurité / RGPD / accessibilité a été ajouté (document 21).

## Sommaire

| # | Document | Contenu |
|---|---|---|
| 01 | [Cahier des charges v1.4](01-cahier-des-charges-v1.4.md) | Besoins, objectifs, MVP en 2 lots, Lot 3 collectivités, technos, routes front et API réelles, user stories, risques — *avec journal des décisions* |
| 02 | [Diagramme ERD](02-diagramme-erd.md) | Base de données v1.2 + contraintes d'unicité + règles RGPD de suppression |
| 03 | [Dictionnaire de données](03-dictionnaire-de-donnees.md) | Les 10 entités et 12 énumérations, attribut par attribut + classement RGPD |
| 04 | [Use cases](04-use-cases.md) | 7 cas détaillés (scénarios nominaux + exceptions), dont 2FA admin et publication des résultats |
| 05 | [Diagramme de séquence](05-diagramme-sequence.md) | Soumission d'une réponse d'enquête (branchement, transaction, 409, synchro profil) |
| 06 | [Diagramme d'architecture](06-diagramme-architecture.md) | Client / API / BDD / services externes — hébergement Clever Cloud |
| 07 | [Diagramme d'activité](07-diagramme-activite.md) | Cycle de vie d'une proposition |
| 08 | [Merise : MCD · MLD · MPD](08-merise-mcd-mld-mpd.md) | Du conceptuel français au SQL réellement généré par Prisma |
| 09 | [Charte graphique](09-charte-graphique.md) | **« La pierre, la rivière et le cerf »** : palette (contrastes recalculés), mascotte, composants, accessibilité |
| 10 | [Wireframes](10-wireframes.html) | 6 écrans basse fidélité annotés |
| 11 | [Maquettes](11-maquettes.html) | Haute fidélité non interactive (à ouvrir dans un navigateur) |
| 12 | [Diagramme des cas d'utilisation](12-diagramme-use-cases.md) | Acteurs, héritage, include/extend |
| 13 | [Diagramme de packages](13-diagramme-package.md) | Organisation réelle du monorepo et dépendances |
| 14 | [Sitemap](14-sitemap.md) | Arborescence de navigation par niveau d'accès |
| 15 | [Kanban, sprints & Git](15-kanban-sprints-git.md) | Méthode : colonnes, labels, branches (`dev`), Definition of Done, livraison en zip |
| 16 | [Backlog complet des issues](16-kanban-issues-complet.md) | v1.10 — 78 issues sur 20 semaines, avec état ✅ / 🔶 et le bloc « Sprint 5 audit » |
| 17 | [Prototype joyeux](17-senlis-participatif-joyeux.html) | Prototype interactif de référence pour la direction artistique |
| 18 | [Logo](18-logo-senlis-participatif.svg) | Logo vectoriel |
| 19 | [schema.prisma](19-schema.prisma) | Copie de référence du schéma — la source vivante est `api/prisma/schema.prisma` du dépôt de code |
| 20 | Centre historique — parkings (`.mmd` / `.svg`) | Schéma des parkings de report |
| 21 | [Audit sécurité, RGPD, accessibilité](21-audit-securite-rgpd-accessibilite.md) | OWASP, ANSSI, CNIL, RGAA : constats, gravité, correctifs, issues associées |

Annexes Word : workflow GitHub, installation de Prisma 7, résumé technique.

## Comment lire cette documentation

Parcours conseillé : **01** (le pourquoi et le quoi) → **02-03** (les données) → **04-07** (les comportements) → **09-11** (le visuel) → **15-16** (le comment et le quand) → **21** (ce qui reste à sécuriser avant la mise en ligne). Le document 08 (Merise) reprend 02-03 sous l'angle académique MCD/MLD/MPD.

## Conventions

- Tout changement de périmètre passe par le **journal des décisions** du cahier des charges (document 01) — la documentation garde la mémoire de ses choix
- Les documents évoluent par PR, comme le code : une décision = un commit tracé
- **Le code fait foi** : quand un document et le code divergent, on corrige le document (ou on ouvre une issue si c'est le code qui est faux)
- Les images PNG des diagrammes sont régénérées depuis le Mermaid après chaque modification (voir ci-dessous)

## Régénérer les PNG des diagrammes

```bash
# Une fois : l'outil officiel Mermaid en ligne de commande
npm install -g @mermaid-js/mermaid-cli

# Puis, pour chaque document : extraire le bloc mermaid, puis l'exporter
awk '/^```mermaid/{f=1;next} /^```/{f=0} f' 02-diagramme-erd.md > /tmp/02.mmd
mmdc -i /tmp/02.mmd -o 02-Diagramme-erd.png -b white -w 2000
```

Alternative sans installation : coller le bloc dans [mermaid.live](https://mermaid.live) puis « Export PNG ».
