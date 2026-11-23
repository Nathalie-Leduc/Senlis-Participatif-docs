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
| Données | Pseudo, email, empreinte Argon2 du mot de passe, rôle, email vérifié (oui/non), préférences de notification, dates de création, de modification, de dernière connexion et des deux avertissements d'inactivité |
| Destinataires | Éditrice ; sous-traitants : Clever Cloud (hébergement), Brevo (emails) |
| Transferts hors UE | Aucun |
| Durée | Jusqu'à la suppression du compte, ou 3 ans sans connexion : **deux** emails d'avertissement, 30 jours puis 7 jours avant (« dernier rappel », S5R2-09), puis suppression (`npm run purge`, quotidien — jamais sans les deux avertissements envoyés, jamais moins de 7 jours après le dernier, jamais un compte admin ni Admin-test). Jetons email : effacés 24 h après expiration |
| Sécurité | HTTPS, Argon2id, jetons email à usage unique (empreinte SHA-256, 1 h), rate limiting, rôle relu en base à chaque requête |

## Fiche 2 — Profil déclaré et ciblage des enquêtes

| Rubrique | Contenu |
|---|---|
| Finalité | Proposer à chacun les enquêtes qui le concernent ; produire des résultats agrégés par type de public |
| Base légale | Exécution du service (art. 6.1.b) ; statistiques agrégées : intérêt légitime (art. 6.1.f) — mise en balance : données auto-déclaratives, peu intrusives, jamais publiées individuellement, groupes < 5 masqués ; droit d'opposition ouvert |
| Données | Situation de résidence, quartier de résidence, travaille à Senlis (oui / non / non renseigné), quartier de travail, rôle (commerçant·e / salarié·e) |
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
| Mesures spécifiques | Réponses commencées gardées **uniquement dans le navigateur** de la personne (S5R2-13) jusqu'à l'envoi, la déconnexion ou 7 jours — rien n'est transmis au serveur avant l'envoi |

## Fiche 4 — Sécurité et journalisation

| Rubrique | Contenu |
|---|---|
| Finalité | Protéger les comptes et le service (force brute, abus) |
| Base légale | Intérêt légitime (art. 6.1.f) |
| Données | Adresse IP (rate limiting, journaux techniques de l'hébergeur — adresses email masquées dans les journaux applicatifs), code de connexion admin (empreinte, 10 min, 5 essais), jeton « appareil de confiance » (1 h), numéro de version des sessions, nombre d'essais de connexion manqués par compte (empreinte HMAC de l'adresse, jamais l'adresse en clair — S5R-13b), date du dernier changement de mot de passe (rappel annuel, équipe seulement) |
| Destinataires | Éditrice, Clever Cloud |
| Durée | Codes et jetons : jusqu'à expiration ; compteurs d'essais de connexion : 1 heure, effacés chaque nuit ; journaux : 1 an au plus |
| Journal d'administration | Actions des comptes admin et Admin-test (connexions, rôles, propositions, enquêtes) — pseudo, date, objet visé ; conservé 6 mois (S5A-06) |

## Fiche 5 — Lecture au survol avec voix naturelles (S5R2-07)

| Rubrique | Contenu |
|---|---|
| Finalité | Lire à voix haute le texte survolé, avec une voix naturelle, pour les personnes qui lisent difficilement (dyslexie, illettrisme, fatigue visuelle, grand âge, francophones débutants) |
| Base légale | Exécution du service demandé (art. 6.1.b) — fonction activée par la personne elle-même dans le module d'accessibilité, et voix naturelle choisie par elle (une voix de l'appareil n'envoie rien) |
| Personnes concernées | Visiteurs, connectés ou non, qui activent la lecture au survol |
| Données | Le **texte public** survolé (titre, paragraphe, bouton), la voix et la vitesse choisies ; l'adresse IP (limite de 40 lectures par minute). **Jamais** : les pages « Mon compte » et d'administration, la réinitialisation du mot de passe, le contenu d'un champ de saisie, le pseudo de l'en-tête, le jeton de connexion (la demande est anonyme) |
| Destinataires | Éditrice ; sous-traitants : Clever Cloud (relais), **Microsoft (Azure AI Speech, région France Central)** |
| Transferts hors UE | Traitement en France ; Microsoft étant un groupe américain, un accès depuis les États-Unis ne peut être exclu — encadré par le Data Privacy Framework UE–États-Unis (Microsoft certifié) et l'accord de traitement des données de Microsoft |
| Durée | Aucune conservation côté Senlis Participatif hors cache audio en mémoire (effacé au redémarrage) ; selon Microsoft, ni le texte ni l'audio ne sont conservés dans ses journaux ; voix et vitesse : `localStorage` du navigateur (`senlis:lecture`) |
| Sécurité | Clé Azure côté serveur uniquement, texte jamais écrit dans les journaux de l'API, longueur limitée (600 caractères), limite par IP, garde-fou mensuel, repli sur les voix de l'appareil |

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
| Microsoft Ireland Operations Ltd (Azure AI Speech), région France Central | Voix naturelles de la lecture au survol (fiche 5) — offre gratuite F0 | Accord de traitement des données Microsoft (DPA, *Products and Services Data Protection Addendum*) ; Microsoft Corporation certifiée Data Privacy Framework UE–États-Unis — procédure : document 33 |

## Historique

| Date | Modification |
|---|---|
| 25/09/2026 | Création du registre (S5A-04) |
| 26/09/2026 | Fiche 1 : purge automatique effective, dates de connexion (S5A-05) ; droit d'accès et de portabilité en libre-service |
| 27/09/2026 | Fiche 4 : journal d'administration (6 mois), révocation des sessions, limite d'essais 2FA (S5A-06) |
| 09/10/2026 | Rôle « Admin-test » (S5R2-11) : comptes de préparation (mairie, partenaire) sans accès aux données des citoyen·nes — ni comptes, ni réponses, ni résultats détaillés ; 2FA et journalisation comme un admin ; jamais purgés automatiquement |
| 12/10/2026 | Fiche 3 : sauvegarde automatique des saisies en cours dans le navigateur (S5R2-13) — clé `senlis:draft:*`, propre à chaque compte, effacée à l'envoi, à la déconnexion ou après 7 jours ; politique de confidentialité mise à jour |
| 05/11/2026 | Fiche 4 : limite de 10 essais de connexion par heure et par compte, avec email d'alerte au titulaire (S5R-13b, CNIL 2022-100 § 43) — compteurs sans adresse en clair, effacés après une heure ; date du dernier changement de mot de passe pour le rappel annuel des comptes de l'équipe ; politique de confidentialité mise à jour |
| 18/11/2026 | Fiche 5 : lecture au survol avec voix naturelles (S5R2-07) — Microsoft (Azure AI Speech, France Central) ajouté aux sous-traitants ; texte public seulement, jamais les pages de compte ni les champs saisis ; politique de confidentialité mise à jour (destinataires, `senlis:lecture`) |
| 23/11/2026 | Fiche 1 : second email d'avertissement (« dernier rappel ») 7 jours avant la suppression d'un compte inactif (S5R2-09) ; la suppression exige les deux emails envoyés ; politique de confidentialité mise à jour |
