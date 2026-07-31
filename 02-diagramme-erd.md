# Diagramme ERD — Senlis Participatif

> Schéma de la base de données (v1.1) — source de vérité : `schema.prisma`.
> Légende : `||--o{` = un-à-plusieurs · `|o--o{` = la clé étrangère est **nullable** (pseudonymisation RGPD : le lien au compte peut être rompu sans perdre la donnée).

```mermaid
erDiagram
    USER ||--o{ PROPOSAL : "rédige"
    USER ||--o{ VOTE : "émet"
    USER ||--o{ AUTH_TOKEN : "possède"
    PROPOSAL ||--o{ VOTE : "reçoit"
    USER |o--o{ COMMENT : "argumente"
    PROPOSAL ||--o{ COMMENT : "débat sous"
    USER |o--o{ SURVEY_RESPONSE : "soumet"
    SURVEY ||--o{ SURVEY_RESPONSE : "collecte"
    SURVEY ||--o{ QUESTION : "contient"
    QUESTION ||--o{ QUESTION_OPTION : "propose"
    SURVEY_RESPONSE ||--o{ ANSWER : "regroupe"
    QUESTION ||--o{ ANSWER : "cible"
    QUESTION_OPTION |o--o{ ANSWER : "choisie par"

    USER {
        uuid id PK
        string email UK
        string pseudo UK
        string passwordHash
        enum role
        boolean emailVerified
        boolean notifyNewProposal
        boolean notifySurveyClosed
    }
    AUTH_TOKEN {
        uuid id PK
        uuid userId FK
        string tokenHash UK
        enum type
        datetime expiresAt
        datetime usedAt "nullable"
    }
    PROPOSAL {
        uuid id PK
        uuid authorId FK "nullable"
        string slug UK
        string title
        string summary
        text content
        enum status
        float lat "nullable"
        float lng "nullable"
        json geoJson "nullable"
        string moderationNote "nullable"
        datetime publishedAt "nullable"
        datetime closesAt "nullable"
    }
    VOTE {
        uuid id PK
        uuid userId FK
        uuid proposalId FK
        enum value
    }
    COMMENT {
        uuid id PK
        uuid authorId FK "nullable"
        uuid proposalId FK
        text content
        enum stance
        enum status
    }
    SURVEY {
        uuid id PK
        string slug UK
        string title
        text description
        enum audience
        enum status
        datetime opensAt "nullable"
        datetime closesAt "nullable"
    }
    QUESTION {
        uuid id PK
        uuid surveyId FK
        int order
        string label
        string helpText "nullable"
        enum type
        boolean required
    }
    QUESTION_OPTION {
        uuid id PK
        uuid questionId FK
        int order
        string label
    }
    SURVEY_RESPONSE {
        uuid id PK
        uuid surveyId FK
        uuid userId FK "nullable"
        datetime submittedAt
    }
    ANSWER {
        uuid id PK
        uuid responseId FK
        uuid questionId FK
        uuid optionId FK "nullable"
        string valueText "nullable"
        float valueNumber "nullable"
    }
```

## Contraintes d'unicité métier (l'« isoloir numérique »)

| Table | Contrainte | Garantie |
|---|---|---|
| VOTE | `UNIQUE(userId, proposalId)` | Un citoyen = un vote par proposition |
| SURVEY_RESPONSE | `UNIQUE(userId, surveyId)` | Un citoyen = une réponse par enquête |
| ANSWER | `UNIQUE(responseId, questionId, optionId)` | Pas de double coche d'une même option |
| QUESTION | `UNIQUE(surveyId, order)` | Ordre des questions sans doublon |
| QUESTION_OPTION | `UNIQUE(questionId, order)` | Ordre des options sans doublon |

## Règles de suppression (RGPD)

| Relation | Règle | Pourquoi |
|---|---|---|
| User → Vote | CASCADE | Le vote est un acte strictement personnel |
| User → AuthToken | CASCADE | Les jetons n'ont aucun sens sans le compte |
| User → Proposal | SET NULL | Une proposition publique survit, anonymisée |
| User → Comment | SET NULL | On n'ampute pas un débat public |
| User → SurveyResponse | SET NULL | Les statistiques agrégées survivent à la désinscription |
| Proposal → Vote / Comment | CASCADE | Sans la proposition, plus d'objet |
| Survey → Question → Option → Answer | CASCADE | Suppression en chaîne d'une enquête entière |
