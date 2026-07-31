# Use cases — Senlis Participatif

> Les use cases étoffent les user stories du cahier des charges : acteurs, préconditions, scénario nominal, alternatives et exceptions. Cinq cas couvrent les mécaniques clés du système.

---

## UC-01 — S'inscrire et vérifier son email (Lot 1)

| | |
|---|---|
| **Acteur principal** | Visiteur |
| **Acteur secondaire** | Système d'envoi d'emails |
| **Préconditions** | Aucune |
| **Déclencheur** | Le visiteur clique sur « Créer un compte » |
| **Postconditions** | Compte créé, email vérifié, citoyen apte à participer |

**Scénario nominal**
1. Le visiteur saisit email, pseudo et mot de passe
2. Le système valide les données (format email, force du mot de passe, pseudo disponible)
3. Le système crée le compte (`emailVerified = false`) avec le mot de passe haché en Argon2
4. Le système génère un jeton `VERIFY_EMAIL`, en stocke l'empreinte (hash) et envoie le lien par email
5. Le visiteur clique sur le lien dans l'heure
6. Le système vérifie le jeton (empreinte connue, non expiré, non utilisé), marque `emailVerified = true` et consomme le jeton (`usedAt`)
7. Le citoyen est invité à se connecter

**Alternatives / exceptions**
- 2a. Email ou pseudo déjà pris → message explicite, retour au formulaire
- 5a. Jeton expiré → proposition de renvoyer un email de vérification (rate limité)
- 5b. Jeton déjà consommé → message « lien déjà utilisé », redirection connexion
- \* Tentatives répétées d'inscription depuis une même IP → rate limiting (anti-spam de comptes)

---

## UC-02 — Voter sur une proposition (Lot 1)

| | |
|---|---|
| **Acteur principal** | Citoyen (connecté, email vérifié) |
| **Préconditions** | Proposition au statut `PUBLISHED`, date `closesAt` non dépassée |
| **Déclencheur** | Clic sur POUR, CONTRE ou NEUTRE |
| **Postconditions** | Exactement un vote enregistré pour ce citoyen sur cette proposition |

**Scénario nominal**
1. Le citoyen consulte une proposition et clique sur un des trois boutons de vote
2. Le système authentifie la requête (JWT) et vérifie `emailVerified`
3. Le système valide la valeur (`POUR`/`CONTRE`/`NEUTRE`) et le statut de la proposition
4. Le système enregistre le vote en **upsert** : création s'il n'existe pas, mise à jour sinon (changement d'avis)
5. Le système renvoie le nouvel agrégat (totaux par camp) ; l'interface se met à jour

**Alternatives / exceptions**
- 2a. Non connecté → invitation à se connecter (le vote n'est pas perdu : rejoué après connexion)
- 2b. Email non vérifié → message expliquant pourquoi (intégrité des résultats) + lien de renvoi
- 3a. Proposition `CLOSED` → 403, votes clos
- 4a. Deux votes simultanés du même citoyen (double clic, deux onglets) → la contrainte `UNIQUE(userId, proposalId)` ne laisse passer qu'une ligne ; l'upsert absorbe le conflit

---

## UC-03 — Répondre à une enquête (Lot 1) ⭐ *fonctionnalité la plus complexe*

| | |
|---|---|
| **Acteur principal** | Citoyen (connecté, email vérifié) |
| **Préconditions** | Enquête au statut `OPEN`, citoyen n'ayant pas encore répondu |
| **Déclencheur** | Soumission du questionnaire complété |
| **Postconditions** | Un bulletin (`SurveyResponse`) et toutes ses réponses (`Answer`) enregistrés **atomiquement** |

**Scénario nominal**
1. Le citoyen ouvre l'enquête ; le système renvoie questions et options dans l'ordre
2. Le citoyen complète le questionnaire et soumet
3. Le système authentifie (JWT + `emailVerified`)
4. Le système valide le payload (Zod) : toutes les questions `required` couvertes, cohérence type/valeur (une option pour un choix unique, un nombre pour `NOMBRE`…), options appartenant bien à leurs questions
5. Le système vérifie que l'enquête est `OPEN` (et dans sa fenêtre `opensAt`/`closesAt`)
6. Le système ouvre une **transaction** : insertion du `SurveyResponse` puis de toutes les `Answer`
7. La transaction est validée (COMMIT) ; réponse `201 Created`
8. L'interface remercie et affiche, le cas échéant, le nombre de participants

**Alternatives / exceptions**
- 4a. Payload invalide → `400` avec le détail par question, rien n'est écrit
- 5a. Enquête close entre l'ouverture du formulaire et la soumission → `403` avec message clair
- 6a. Le citoyen a déjà répondu (contrainte `UNIQUE(userId, surveyId)`) → la transaction échoue entièrement, `409 Conflict` — aucune réponse partielle ne peut exister
- 6b. Erreur en cours d'insertion des `Answer` → ROLLBACK total : c'est tout le bulletin ou rien (intégrité statistique)

---

## UC-04 — Soumettre une proposition citoyenne (Lot 2)

| | |
|---|---|
| **Acteur principal** | Citoyen (connecté, email vérifié) |
| **Acteur secondaire** | Administratrice (modération) |
| **Préconditions** | — |
| **Déclencheur** | Le citoyen complète le formulaire « Proposer » |
| **Postconditions** | Proposition en file de modération, jamais visible publiquement avant validation |

**Scénario nominal**
1. Le citoyen rédige titre, résumé, argumentaire, et localise sa proposition (point et/ou périmètre sur la carte)
2. Le système valide (Zod) et crée la proposition au statut `PENDING_REVIEW`
3. L'administratrice consulte la file de modération
4. Elle approuve : statut `PUBLISHED`, la proposition devient visible et votable
5. (Lot 2 notifications) Les citoyens abonnés sont notifiés par email

**Alternatives / exceptions**
- 4a. Elle rejette avec un motif (`moderationNote`) : statut `REJECTED`, le motif est visible par l'auteur seul
- 4b. L'auteur corrige et soumet à nouveau → retour à l'étape 2
- \* L'auteur supprime son compte après publication → la proposition survit, anonymisée (`authorId = NULL`)

---

## UC-05 — Modérer un commentaire (Lot 2)

| | |
|---|---|
| **Acteur principal** | Administratrice |
| **Préconditions** | Des commentaires au statut `PENDING` existent |
| **Déclencheur** | Consultation de la file de modération |
| **Postconditions** | Chaque commentaire traité est `APPROVED` (visible) ou `REJECTED` (jamais affiché) |

**Scénario nominal**
1. L'administratrice ouvre `/admin/moderation`
2. Le système liste les commentaires `PENDING` (plus anciens d'abord), avec leur proposition et leur position (pour/contre/neutre)
3. Elle approuve un commentaire → `APPROVED`, il apparaît dans la colonne correspondant à sa position
4. Elle répète jusqu'à vider la file

**Alternatives / exceptions**
- 3a. Contenu inapproprié → `REJECTED` : le commentaire n'est **jamais** apparu publiquement (modération a priori)
- \* Afflux inhabituel de commentaires → possibilité de suspendre temporairement les commentaires sur une proposition (mesure du registre des risques)
