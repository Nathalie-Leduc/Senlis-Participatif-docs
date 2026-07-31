# Diagramme des cas d'utilisation — Senlis Participatif

> Vue UML d'ensemble : *qui* peut faire *quoi*. Les acteurs héritent les uns des autres (un Citoyen peut tout ce que peut un Visiteur ; l'Administratrice peut tout ce que peut un Citoyen). Les ovales pointillés = Lot 2. `«include»` = passage obligé ; `«extend»` = comportement optionnel.

```mermaid
flowchart LR
    V(["🧍 Visiteur"])
    C(["🧑‍💼 Citoyen<br/>(email vérifié)"])
    A(["👑 Administratrice"])
    M(["📧 Service<br/>d'emails"])

    subgraph SYS ["Système Senlis Participatif"]
        direction TB
        subgraph L1 ["Lot 1 — Socle"]
            UC1(["Consulter les propositions<br/>et leurs résultats"])
            UC2(["Visualiser la carte<br/>(périmètres, IRIS, parkings)"])
            UC3(["S'inscrire et<br/>vérifier son email"])
            UC4(["S'authentifier"])
            UC5(["Voter POUR / CONTRE / NEUTRE"])
            UC6(["Répondre à une enquête"])
            UC7(["Gérer son compte<br/>(dont effacement RGPD)"])
            UC8(["Gérer les propositions<br/>(créer, publier, clore)"])
            UC9(["Construire et piloter<br/>une enquête"])
            UC10(["Consulter les<br/>résultats agrégés"])
        end
        subgraph L2 ["Lot 2 — Participation"]
            UC11(["Publier un argument<br/>pour / contre / neutre"]):::lot2
            UC12(["Soumettre une<br/>proposition citoyenne"]):::lot2
            UC13(["Modérer contenus<br/>(approuver / rejeter + motif)"]):::lot2
            UC14(["Gérer ses préférences<br/>de notification"]):::lot2
        end
    end

    V --- UC1 & UC2 & UC3
    C --- UC5 & UC6 & UC7 & UC11 & UC12 & UC14
    A --- UC8 & UC9 & UC10 & UC13

    UC5 -.->|«include»| UC4
    UC6 -.->|«include»| UC4
    UC7 -.->|«include»| UC4
    UC11 -.->|«include»| UC4
    UC12 -.->|«include»| UC4
    UC12 -.->|«extend»| UC13
    UC11 -.->|«extend»| UC13
    UC3 -.-> M
    UC13 -.->|notification du motif| M

    V ==>|hérite| C ==>|hérite| A

    classDef lot2 stroke-dasharray:5 5
```

**Lectures utiles**
- L'« include » systématique vers **S'authentifier** matérialise la règle : aucune participation sans compte vérifié — c'est le socle de la crédibilité statistique.
- Les deux « extend » vers **Modérer** disent la modération *a priori* : publier un argument ou soumettre une proposition déclenche potentiellement une modération avant toute visibilité.
- Le **Service d'emails** est un acteur secondaire (le système l'appelle, il ne décide rien) : vérification d'inscription, motif de rejet… En dev : Mailtrap ; en production : Brevo.
