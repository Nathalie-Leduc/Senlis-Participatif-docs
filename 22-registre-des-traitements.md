# Registre des activités de traitement — Senlis Participatif

> Tenu en application de l'**article 30 du RGPD**. L'exemption prévue pour les structures de moins de 250 personnes ne s'applique pas ici : le traitement n'est pas occasionnel (il fonctionne en continu). Modèle inspiré du registre simplifié proposé par la CNIL.
>
> **Créé le 25/09/2026 (S5A-04).** À mettre à jour à chaque évolution qui ajoute une donnée, une finalité ou un destinataire — même règle que `client/src/constants/legal.js`, dont ce registre est le pendant « côté responsable ». Le jour où la mairie reprend le site (Lot 3), c'est elle qui devient responsable de traitement : ce registre sera transmis à son DPO.

> 🎓 **Politique de confidentialité ou registre ?** La politique s'adresse aux **citoyens** (« voici ce que nous faisons de vos données ») ; le registre s'adresse au **responsable et à la CNIL** (« voici, traitement par traitement, ce que nous faisons, et la preuve que nous l'avons réfléchi »). Analogie : le menu du restaurant et le cahier de traçabilité de la cuisine — les deux doivent raconter la même chose.

## Responsable du traitement

| | |
|---|---|
| Responsable | L'éditrice du site, personne physique agissant à titre non professionnel |
| Contact | contact@senlis-participatif.fr |
| Délégué à la protection des données (DPO) | Non désigné (non obligatoire : pas d'autorité publique, pas de suivi à grande échelle ni de données sensibles) |

---

## Fiche 1 — Gestion des comptes citoyens

| Rubrique | Contenu |
|---|---|
| Finalité | Créer et sécuriser un compte, vérifier l'adresse email, permettre la réinitialisation du mot de passe, garantir « une personne = un compte » |
| Base légale | Exécution du service demandé (art. 6.1.b) |
| Personnes concernées | Habitants, actifs et visiteurs de Senlis qui s'inscrivent |
| Données | Pseudo, email, empreinte Argon2 du mot de passe, rôle, email vérifié (oui/non), préférences de notification, dates de création et de modification |
| Destinataires | Éditrice ; sous-traitants : Clever Cloud (hébergement), Brevo (emails) |
| Transferts hors UE | Aucun |
| Durée | Jusqu'à la suppression du compte, ou 3 ans sans connexion après un email d'avertissement (purge automatique : S5A-05) |
| Sécurité | HTTPS, Argon2id, jetons email à usage unique (empreinte SHA-256, 1 h), rate limiting, rôle relu en base à chaque requête |

## Fiche 2 — Profil déclaré et ciblage des enquêtes

| Rubrique | Contenu |
|---|---|
| Finalité | Proposer à chacun les enquêtes qui le concernent ; produire des résultats agrégés par type de public |
| Base légale | Exécution du service (art. 6.1.b) ; statistiques agrégées : intérêt légitime (art. 6.1.f) — mise en balance : données auto-déclaratives, peu intrusives, jamais publiées individuellement, groupes < 5 masqués ; droit d'opposition ouvert |
| Données | Situation de résidence, quartier de résidence, quartier de travail, rôle (commerçant·e / salarié·e) |
| Destinataires | Éditrice ; résultats **agrégés** : public et, le cas échéant, élus municipaux |
| Transferts hors UE | Aucun |
| Durée | Celle du compte |
| Mesures spécifiques | Secret statistique (`api/src/lib/privacy.js`) : aucun groupe de 1 à 4 personnes n'est détaillé, réponses libres jamais détaillées par segment ni imprimées |

## Fiche 3 — Participation (votes et enquêtes)

| Rubrique | Contenu |
|---|---|
| Finalité | Recueillir l'avis des citoyens sur des propositions d'aménagement et publier des résultats d'ensemble |
| Base légale | Exécution du service (art. 6.1.b) |
| Données | Votes (pour / contre / neutre, dates), réponses aux enquêtes (choix, nombres, textes libres) |
| Destinataires | Éditrice ; résultats agrégés : public |
| Transferts hors UE | Aucun |
| Durée | Votes : supprimés avec le compte. Réponses : détachées du compte à sa suppression (`SET NULL`), conservées anonymement |
| Point de vigilance | Les textes libres peuvent contenir des données identifiantes : jamais exportés tels quels, conseil de prudence affiché dans la politique. Question à trancher avec le DPO de la mairie au Lot 3 : un vote sur un projet municipal relève-t-il des « opinions politiques » (art. 9) ? |

## Fiche 4 — Sécurité et journalisation

| Rubrique | Contenu |
|---|---|
| Finalité | Protéger les comptes et le service (force brute, abus) |
| Base légale | Intérêt légitime (art. 6.1.f) |
| Données | Adresse IP (rate limiting, journaux techniques de l'hébergeur), code de connexion admin (empreinte, 10 min), jeton « appareil de confiance » (1 h) |
| Destinataires | Éditrice, Clever Cloud |
| Durée | Codes et jetons : jusqu'à expiration ; journaux : 1 an au plus |
| Évolution prévue | Journal des actions d'administration (S5A-06) |

## Services tiers appelés par le navigateur (hors sous-traitance)

| Service | Données vues | Localisation | Justification |
|---|---|---|---|
| Tuiles OpenStreetMap | IP, zones de carte consultées | Royaume-Uni (décision d'adéquation) | Affichage de la carte interactive — aucune alternative sans tiers à ce stade |
| geo.api.gouv.fr (DINUM) | IP, saisie du champ « ville » | France | Suggestions de communes (question `VILLE_FR`) |
| ~~Google Fonts~~ | — | — | Supprimé par S5A-03 (polices auto-hébergées) |

## Sous-traitants (art. 28)

| Sous-traitant | Prestation | Garantie contractuelle |
|---|---|---|
| Clever Cloud SAS, 4 rue Voltaire, 44000 Nantes | Hébergement (API, base PostgreSQL, site) | Accord de traitement des données (DPA) intégré aux CGU — ISO 27001, données en France |
| Sendinblue SAS (Brevo), 17 rue Salneuve, 75017 Paris | Emails transactionnels | DPA intégré aux conditions d'utilisation — suivi d'ouverture désactivé |

## Historique

| Date | Modification |
|---|---|
| 25/09/2026 | Création du registre (S5A-04) |
