# Offre d'évolutions à présenter à la mairie

> Tout ce qui vient **après la mise en ligne** du Lot 1 : anciens Sprints 6 et 7, backlogs, propositions Lot 3 (recette du 30/09) et suites de la recette du 08/10. Regroupé par **thème**, chiffré en **jours de développement**, pour construire une proposition commerciale. Les maquettes, wireframes et prototypes de présentation viendront ensuite.
>
> Source des éléments : kanban `16` (sprints 6–7, BACKLOG-01 à 03), `23` §5 (Lot 3), `29` (recette du 08/10).

---

## 1. Comment lire les chiffrages

- **1 jour = une journée pleine de développement**, tests, documentation et livraison compris (même règle que le kanban).
- Une **fourchette** (ex. 6–10 j) signale une évolution qui demande d'abord une courte conception avec la mairie ; le haut de la fourchette couvre les surprises.
- Les **coûts de services tiers** (traduction, voix, envoi d'emails) sont indiqués à part : ils dépendent du volume et sont payés directement par la mairie, ou refacturés.
- **Montant** = jours × taux journalier (TJM), à fixer. Exemple de calcul seulement : à 350 € HT/jour, 10 jours = 3 500 € HT. Le TJM d'un·e développeur·se freelance junior en France se situe couramment entre 250 et 400 € HT — à affiner selon le statut retenu (voir guide `27`).

---

## 2. Les évolutions, par thème

### A. Participation citoyenne — le cœur du Lot 2

| Réf. | Évolution | Estim. |
|---|---|:--:|
| A1 (ex-S6-01 à S6-06) | **Débat structuré** : commentaires pour / contre / neutre, colonnes de débat, file de modération a priori (rien ne se publie sans validation, motif de refus obligatoire) | 10 j |
| A2 (ex-S7-01, S7-02) | **Propositions citoyennes** : un citoyen soumet une proposition (formulaire + zone sur la carte), modérée avant publication | 4 j |
| | **Sous-total** | **14 j** |

### B. Communication et notifications

| Réf. | Évolution | Estim. | Coût tiers |
|---|---|:--:|---|
| B1 (ex-S7-03, S7-04) | **Notifications par email** : nouvelle proposition, nouvelle enquête — avec préférences dans « Mon compte », **désactivées par défaut** (RGPD), lien de désinscription | 3 j | Brevo : gratuit jusqu'à 300 emails/jour, puis abonnement |
| B2 (ex-L3-09) | **Notifications ciblées** selon le profil (ex. « habitants du centre ») et **annonce des résultats** aux participants | 2 j | idem |
| | **Sous-total** | **5 j** | |

### C. Accessibilité et inclusion

| Réf. | Évolution | Estim. | Coût tiers |
|---|---|:--:|---|
| C1 (ex-L3-01) | **Site multilingue** (anglais et langues des communautés locales), traduction assistée par IA puis relue | 4–6 j | API de traduction (ex. Mistral, DeepL) : quelques euros/mois au volume d'une ville |
| C2 (ex-L3-02) | **Aide à la rédaction** (orthographe, grammaire) en suggestion, pour les propositions et commentaires | 2 j | API IA, au volume |
| C3 (ex-L3-03) | **Voix naturelles** (« neuronales ») pour la lecture à voix haute | 2–3 j | Service de synthèse vocale, au volume |
| C4 (ex-L3-04) | Le **cerf comme curseur** de la lecture au survol | 0,5 j | — |
| | **Sous-total** | **8,5–11,5 j** | |

### D. Cartographie et territoire

| Réf. | Évolution | Estim. | Prérequis |
|---|---|:--:|---|
| D1 (recette 08/10) | **Découpage fin du centre** : Centre-Sud et Centre-Est – Saint-Vincent comme deux quartiers distincts (étape 2 de S5R2-06) | 1,5 j | Contours officiels fournis par le service SIG de la mairie |
| D2 (ex-L3-05) | **Trouver son quartier depuis son adresse** (adresse non conservée), via l'API Adresse de l'État | 1,5 j | — (API gratuite) |
| D3 (ex-L3-06) | **Zonage libre** : l'administration dessine sur la carte la zone exacte d'une proposition (rue, place) | 3 j | — |
| D4 (recette 08/10) | **Stationnement vélo, moto, covoiturage, autopartage** sur la carte | 1 j | Données OpenStreetMap ou inventaire de la Ville |
| D5 (recette 08/10) | **Mise à jour annuelle** du plan des parkings et de ses tarifs (saisie, vérification) | 0,5 j/an | Plan à jour fourni par la Ville |
| | **Sous-total** | **7 j** (+ 0,5 j/an) | |

### E. Enquêtes avancées

| Réf. | Évolution | Estim. |
|---|---|:--:|
| E1 (ex-BACKLOG-01) | **Questions répétées par véhicule** (ou par enfant, par commerce…) : le détail de stationnement de chaque véhicule déclaré | 6–8 j |
| E2 (recette 08/10, à étudier) | **Conditions combinées (ET)** dans le moteur d'enquête, en plus du « OU » actuel | 2–3 j |
| | **Sous-total** | **8–11 j** |

### F. Administration et gouvernance

| Réf. | Évolution | Estim. |
|---|---|:--:|
| F1 (ex-BACKLOG-02) | **Rôles complets** : admin mairie, agents municipaux, maisons de quartier (admins et délégués), circuit de validation avant publication — *une première brique, le rôle « Admin-test » limité aux brouillons, est livrée avec la mise en ligne (S5R2-11) : elle réduit d'autant ce chantier* | 5–9 j |
| F2 (ex-L3-10) | **Tableau de bord** : participation, inscriptions, emails envoyés, enquêtes en cours | 2–3 j |
| F3 (ex-L3-07) | **Second email** avant la suppression d'un compte inactif *(intégré à la mise en ligne, voir § 3)* | 0,5 j |
| | **Sous-total** | **8,5–13,5 j** |

### G. Connexion simplifiée

| Réf. | Évolution | Estim. | Prérequis |
|---|---|:--:|---|
| G1 (ex-L3-08) | **FranceConnect** | 3–5 j | Habilitation de la commune auprès de FranceConnect (démarche administrative, plusieurs semaines) |
| G2 (ex-L3-08) | **Connexion par QR code** (affiches en ville, réunions publiques) | 1 j | — |
| | **Sous-total** | **4–6 j** | |

### H. Identité et plaisir d'usage

| Réf. | Évolution | Estim. |
|---|---|:--:|
| H1 (ex-S7-05) | **Palier 3** : cerf contextuel sur chaque page, confettis à la publication des résultats *(la mascotte de la page 404 est proposée pour la mise en ligne, voir § 3)* | 0,5 j |

### I. Qualité, recette et maintenance

| Réf. | Évolution | Estim. |
|---|---|:--:|
| I1 (ex-S7-06, S7-07) | **Tests et recette** de chaque lot livré, mise en production versionnée | 2 j par lot |
| I2 | **Maintenance** : mises à jour de sécurité des dépendances (Dependabot), sauvegardes vérifiées, petites corrections | 0,5–1 j/mois |
| I3 | **Hébergement** (Clever Cloud, France) : application + base de données + sauvegardes | Coût mensuel de l'hébergeur, à chiffrer à la mise en ligne |

---

## 3. Intégré avant la mise en ligne (validé le 09/10)

Peu coûteux, utiles dès le premier jour — **validés par Nath le 09/10** :

| Élément | Estim. | Où |
|---|:--:|---|
| BACKLOG-03 — chargement des données (qualité du code) | (inclus) | S5R2-08 |
| F3 — second email avant suppression d'un compte inactif | 0,5 j | S5R2-09 |
| Mascotte de la page 404 (extrait de H1) | 0,5 j | S5R2-10 |
| Rôle « Admin-test » limité aux brouillons (première brique de F1) | 1,5 j | S5R2-11 |

---

## 4. Récapitulatif

| Thème | Jours |
|---|:--:|
| A. Participation citoyenne | 14 |
| B. Communication et notifications | 5 |
| C. Accessibilité et inclusion | 8,5–11,5 |
| D. Cartographie et territoire | 7 (+ 0,5/an) |
| E. Enquêtes avancées | 8–11 |
| F. Administration et gouvernance (hors F3) | 7–12 |
| G. Connexion simplifiée | 4–6 |
| H. Identité (hors 404) | 0,5 |
| I. Recette par lot | 2 par lot |
| **Total des évolutions** | **≈ 54 à 67 jours**, hors recettes, maintenance et coûts tiers |

**Découpage commercial suggéré** (à affiner en présentant les maquettes) :

1. **Lot 2 « Participation »** — A + B1 + I1 : ≈ 19 j. C'était le MVP complet prévu initialement ; le plus attendu par les citoyens.
2. **Lot 3 « Accessibilité et territoire »** — C + D : ≈ 15,5–18,5 j. Fort intérêt pour une commune (inclusion, carte).
3. **Lot 4 « Administration »** — F1, F2, G, B2 : ≈ 13–20 j. Utile quand plusieurs services de la mairie utilisent l'outil.
4. **Options** — E (enquêtes avancées), H, et la maintenance (I2) en contrat annuel.
