# Procédure — voix naturelles de la lecture au survol (Azure AI Speech)

> **S5R2-07** (18/11/2026). Pour l'administratrice du site, et pour quiconque reprendra le projet : créer la ressource Azure gratuite, brancher sa clé sur l'API, vérifier que tout fonctionne, surveiller le quota.
>
> 🎓 **Analogie** : la lecture au survol a deux sources de son. La **radio** (les voix de l'appareil du visiteur) marche toujours, sans rien configurer. Le **lecteur CD** (les voix naturelles d'Azure) donne un bien meilleur son, mais il faut d'abord l'acheter… ici gratuitement, et le brancher. Si le lecteur CD tombe en panne, le site rebascule tout seul sur la radio.

---

## 1. Ce qui se passe quand un visiteur survole un texte

```
Navigateur ──(texte public)──▶ API Senlis  POST /api/v1/tts ──(clé secrète)──▶ Azure, région France Central
           ◀──(MP3)──────────                                ◀──(MP3)────────
```

| Garde-fou | Où | Pourquoi |
|---|---|---|
| La clé Azure reste sur le serveur | `api/.env` (`AZURE_SPEECH_KEY`) | Dans le code du site, n'importe qui pourrait la lire et épuiser le quota |
| Cache : un même texte n'est demandé qu'une fois | API (mémoire, ~300 textes) et navigateur | Titres, menus et boutons reviennent sans cesse : le quota dure bien plus longtemps |
| 40 lectures par minute et par adresse IP | `api/src/routes/tts.js` | Un script ne peut pas vider le quota |
| 600 caractères au plus par lecture | API et client | Un paragraphe, pas une page entière |
| Garde-fou mensuel : 450 000 caractères | `AZURE_SPEECH_MONTHLY_CHARS` | Repasser sur les voix de l'appareil avant qu'Azure ne refuse |
| Repli automatique | `client/src/services/speechReader.js` | Azure en panne, quota atteint, clé absente : la voix de l'appareil prend le relais, avec un message, et Azure n'est plus sollicité pendant 5 minutes |
| Jamais de données personnelles | `client/src/utils/hoverReading.js` | Aucune lecture sur `/mon-compte`, `/admin…`, `/reset-password`, `/verification-email` ; jamais un champ de saisie ; jamais le pseudo de l'en-tête (`data-no-read`) |

**RGPD** : Microsoft est **sous-traitant** (registre, document 22 ; politique de confidentialité). Le texte part sans le jeton de connexion : Microsoft ne sait pas qui fait lire quoi. Selon Microsoft, pour les voix neuronales prédéfinies, ni le texte ni l'audio produit ne sont conservés dans ses journaux. Le visiteur qui ne veut rien envoyer choisit une « voix de cet appareil ».

---

## 2. Créer la ressource Azure (une fois, 15 minutes)

> L'interface du portail Azure change souvent de libellés : si un nom ci-dessous diffère, cherchez l'équivalent. Les points qui comptent sont en **gras**.

1. Créer un compte sur **https://portal.azure.com** (gratuit). Une carte bancaire est demandée pour vérifier l'identité ; l'offre **F0** n'est jamais facturée.
2. **Créer une ressource** → rechercher **« Speech »** → choisir le service **Speech** (Azure AI services). Si le portail propose plutôt une ressource « Foundry » à plusieurs services, préférez la ressource **Speech** seule : une clé limitée à la voix, c'est moins de dégâts si elle fuit.
3. Remplir :
   - **Abonnement** : le vôtre ;
   - **Groupe de ressources** : en créer un, par exemple `senlis-participatif` ;
   - **Région** : **France Central** ;
   - **Nom** : par exemple `senlis-participatif-voix` ;
   - **Niveau tarifaire** : **Free F0** (0,5 million de caractères de voix neuronale par mois). **Jamais S0**, qui est payant. Une seule ressource F0 par abonnement est permise.
4. **Vérifier + créer**, puis **Créer**. Attendre « Déploiement terminé » → **Accéder à la ressource**.
5. Menu **Clés et point de terminaison** : noter la **Clé 1** et la **Région** (`francecentral`).

⚠️ La clé est un **secret** : ne jamais la coller dans le chat, un ticket, un commit, un document partagé. Elle va seulement dans `api/.env` (développement) et dans les variables d'environnement de Clever Cloud (production).

---

## 3. Brancher la clé sur l'API

**En développement** (`api/.env`) :

```bash
AZURE_SPEECH_KEY=la-cle-1-copiee-du-portail
AZURE_SPEECH_REGION=francecentral
# AZURE_SPEECH_MONTHLY_CHARS=450000   (facultatif)
```

Puis redémarrer l'API (`npm run dev`).

**En production** (Clever Cloud, S5-22) : application API → **Variables d'environnement** → ajouter `AZURE_SPEECH_KEY` et `AZURE_SPEECH_REGION` → redémarrer.

Sans clé, rien ne casse : le module propose seulement les voix de l'appareil.

---

## 4. Vérifier que ça marche

1. **L'API voit la clé** : ouvrir `http://localhost:3000/api/v1/tts` → `"enabled": true` et deux voix (Denise, Henri). La clé n'apparaît jamais dans cette réponse.
2. **Sur le site** : bouton ♿ → cocher « Lire le texte survolé par la souris » → la liste **Voix** commence par « Voix naturelles » ; survoler un titre.
3. **Le cache** : outils de développement (F12) → onglet **Réseau** → requête `tts` → en-tête `X-TTS-Cache` : `MISS` la première fois, `HIT` ensuite (même texte, même voix, même vitesse).
4. **Le repli** : mettre une fausse clé dans `.env`, redémarrer, survoler un texte → la voix de l'appareil lit, avec le message « Voix naturelle indisponible pour le moment ». Remettre la vraie clé.

---

## 5. Surveiller le quota (une fois par mois)

Portail Azure → la ressource → **Métriques** → métrique **Caractères synthétisés** (ou équivalent), période « 30 derniers jours ».

| Constat | Que faire |
|---|---|
| Loin des 500 000 | Rien |
| Proche de 450 000 chaque mois | Le garde-fou bascule sur les voix de l'appareil en fin de mois : c'est voulu. Pour plus, passer en S0 **payant** : décision de la mairie (document 30) |
| Pics soudains | Regarder les journaux de l'API (`[tts] Azure a répondu …`) : un abus est freiné par la limite de 40 lectures par minute |

Le compteur du garde-fou vit dans la mémoire de l'API : il repart de zéro à chaque redémarrage. Ce n'est pas grave : en F0, Azure **refuse** au-delà du quota, il ne facture pas.

---

## 6. Changer la clé (fuite, départ d'une personne, une fois par an)

Portail → **Clés et point de terminaison** → **Régénérer la clé 2** → la mettre dans `.env` / Clever Cloud → redémarrer → **Régénérer la clé 1** (l'ancienne ne marche plus). Deux clés, pour changer sans coupure.

## 7. Arrêter les voix naturelles

Retirer `AZURE_SPEECH_KEY` (ou la laisser vide) et redémarrer : le site n'utilise plus que les voix de l'appareil. Supprimer ensuite la ressource dans le portail, puis mettre à jour le registre (document 22) et la politique de confidentialité (`client/src/constants/legal.js`).

---

## Sources

- [Tarifs Azure AI Speech — offre gratuite F0](https://azure.microsoft.com/fr-fr/pricing/details/speech/)
- [Données, confidentialité et sécurité de la synthèse vocale (Microsoft Learn)](https://learn.microsoft.com/fr-fr/azure/ai-foundry/responsible-ai/speech-service/text-to-speech/data-privacy-security)
- [Entités Microsoft couvertes par le Data Privacy Framework](https://www.microsoft.com/fr-fr/privacy/microsoft-data-privacy-framework-covered-entities)
