# 📰 Persona — Votre Agrégateur d'Actualités IA sur-mesure

Salut ! Bienvenue sur le dépôt de **Persona**. 

L'idée derrière ce projet est simple : nous passons trop de temps à scroller pour trouver l'information qui nous intéresse vraiment. Persona règle ce problème en étant un agrégateur d'actualités 100 % personnalisé et automatisé, sans aucune interface lourde. Tout se passe via une simple discussion avec un chatbot, et la magie opère en coulisses pour vous livrer une newsletter sur-mesure, à l'heure que vous voulez.

L'intégralité de ce projet a été pensée et construite avec **n8n** (sans code backend traditionnel ni interface utilisateur frontend).

---

## 🛠️ Sous le capot (Architecture)

Comment ça marche concrètement ? Le workflow se divise en deux grandes parties :

1. **L'Onboarding (Le Chatbot) :** Un agent IA conversationnel accueille l'utilisateur, discute avec lui pour comprendre ses besoins (nom, email, centres d'intérêt, heure de réception souhaitée) et extrait ces données de façon structurée pour les stocker en base de données.
2. **L'Usine à News (L'Automatisation) :** À l'heure prévue, le système se réveille. Il va lire plusieurs flux RSS (Google News, France 24, etc.) en fonction des intérêts de l'utilisateur, demande à une IA de jouer au rédacteur en chef pour trier et résumer les meilleurs articles, et envoie le tout via un bel e-mail HTML.

---

## ✨ Ce qui est en place (Fonctionnalités)

Nous avons respecté à la lettre le cahier des charges (les "Musts"), et même un peu plus :

- **Point d'entrée unique :** Tout se gère depuis l'interface de chat.
- **Profilage IA :** Extraction intelligente des données utilisateurs en langage naturel.
- **Multi-sources :** Récupération d'articles depuis plusieurs flux RSS combinés.
- **Curation sur-mesure :** Filtrage et résumé des actualités par l'IA selon le profil.
- **Mise en page HTML :** Fini le texte brut, la newsletter est formatée proprement.
- **Mémoire de session :** Le chatbot se souvient du contexte de la discussion.

*Dans les cartons (À venir) :* L'ajout d'une régie publicitaire ciblée, la vérification des faits (fact-checking) via des outils externes de l'agent, et la diffusion multicanal (Discord/Telegram).

---

## 🔒 Sécurité & RGPD (Parce qu'on ne rigole pas avec les données)

Ce projet a été pensé pour le marché européen, la gestion des données est donc prise très au sérieux :

- **Contre les acteurs malveillants :** Le prompt de l'IA inclut des directives strictes contre la "Prompt Injection". L'architecture prévoit également une vérification (mot de passe/hash) pour éviter qu'un utilisateur ne modifie les données d'un autre.
- **RGPD :** Les données (email, intérêts) ne sont collectées qu'après validation explicite de l'utilisateur. Un flux de désabonnement permet la suppression totale des données de l'utilisateur en base s'il le demande au chatbot.
- **Anonymisation :** Le fichier d'export du workflow (`persona.json`) a été purgé de tous les identifiants, tokens d'API et clés secrètes avant d'être partagé.

---

## 🚀 Envie de tester ? (Installation)

Pour faire tourner Persona sur votre machine, voici la marche à suivre :

### Prérequis
- Une instance **n8n** qui tourne en local (sur `localhost:5678`).
- Une clé API **OpenAI** (ou un autre modèle LLM compatible).
- Un projet Google Cloud avec l'API **Gmail** activée et des identifiants OAuth 2.0 (Type : Application Web).

### Configuration rapide
1. **Importez la logique :** Dans n8n, allez dans *Workflows → Import from file* et sélectionnez le fichier `persona.json`.
2. **Branchez les tuyaux :** Allez dans l'onglet *Credentials* de n8n et configurez vos clés pour OpenAI et Gmail OAuth2. *(Pensez à bien mettre `http://localhost:5678/rest/oauth2-credential/callback` dans les URI de redirection sur Google Cloud).*
3. **Activez le tout :** Passez le workflow en "Active" en haut à droite. Ouvrez l'URL du Chat Trigger et commencez à discuter avec l'agent !

---

### 👨‍💻 Auteur
**Omar Joudi** — Epitech Lyon