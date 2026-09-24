# Diagramme des cas d'utilisation — Senlis Participatif

> Vue UML d'ensemble : *qui* peut faire *quoi*. Les acteurs héritent les uns des autres (un Citoyen peut tout ce que peut un Visiteur ; l'Administratrice peut tout ce que peut un Citoyen). Les ovales pointillés = Lot 2. `«include»` = passage obligé ; `«extend»` = comportement optionnel.
>
> **Mise à jour 23/09/2026** : ajout de la double authentification admin, du profil déclaré (résidence / travail), de la publication et de la segmentation des résultats, et de la gestion des comptes.

```mermaid
flowchart LR
    V(["🧍 Visiteur"])
    C(["🧑‍💼 Citoyen<br/>(email vérifié)"])
    A(["👑 Administratrice"])
    M(["📧 Service<br/>d'emails"])
    G(["🗺️ geo.api.gouv.fr"])

    subgraph SYS ["Système Senlis Participatif"]
        direction TB
        subgraph L1 ["Lot 1 — Socle"]
            UC1(["Consulter les propositions<br/>et leurs résultats"])
            UC2(["Visualiser la carte<br/>(périmètres, IRIS, parkings)"])
            UC3(["S'inscrire, déclarer sa situation<br/>et vérifier son email"])
            UC4(["S'authentifier"])
            UC4b(["Valider un code 2FA<br/>(comptes admin)"])
            UC5(["Voter POUR / CONTRE / NEUTRE"])
            UC6(["Répondre à une enquête<br/>(questions conditionnelles)"])
            UC7(["Gérer son compte : profil,<br/>mot de passe, effacement RGPD"])
            UC8(["Gérer les propositions<br/>(créer, image, carte, publier, clore)"])
            UC9(["Construire et piloter une enquête<br/>(branchement, synchro profil)"])
            UC10(["Consulter les résultats détaillés,<br/>segmentés, imprimer / PDF"])
            UC15(["Publier les résultats<br/>d'une enquête"])
            UC16(["Promouvoir / rétrograder<br/>un compte admin"])
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
    A --- UC8 & UC9 & UC10 & UC13 & UC15 & UC16

    UC5 -.->|«include»| UC4
    UC6 -.->|«include»| UC4
    UC7 -.->|«include»| UC4
    UC11 -.->|«include»| UC4
    UC12 -.->|«include»| UC4
    UC4b -.->|«extend» si rôle ADMIN<br/>et appareil non reconnu| UC4
    UC12 -.->|«extend»| UC13
    UC11 -.->|«extend»| UC13
    UC6 -.->|suggestions de ville| G
    UC3 -.-> M
    UC4b -.-> M
    UC13 -.->|notification du motif| M

    V ==>|hérite| C ==>|hérite| A

    classDef lot2 stroke-dasharray:5 5
```

**Lectures utiles**
- L'« include » systématique vers **S'authentifier** matérialise la règle : aucune participation sans compte vérifié — c'est le socle de la crédibilité statistique.
- **Valider un code 2FA** *étend* l'authentification : il ne s'ajoute que pour un compte admin, et seulement si ce navigateur n'a pas déjà réussi un code dans l'heure (appareil de confiance). Analogie : le coffre de la banque demande une seconde clé, pas la porte d'entrée.
- **Publier les résultats** est distinct de **clore l'enquête** : l'administratrice peut analyser des résultats clos avant de les rendre publics.
- Les deux « extend » vers **Modérer** disent la modération *a priori* (Lot 2).
- Le **Service d'emails** et **geo.api.gouv.fr** sont des acteurs secondaires : le système les appelle, ils ne décident rien. Emails : Mailtrap en dev, Brevo en production.
