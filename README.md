# Persona

Salut et bienvenue sur le dépôt de Persona. 

L'idée derrière ce projet est née d'un constat simple : nous passons trop de temps à trier l'information pour trouver ce qui nous intéresse vraiment. Persona est une solution automatisée qui règle ce problème en se débarrassant complètement de l'interface utilisateur classique. 

Tout le système repose sur n8n. L'interaction se fait uniquement via une discussion naturelle avec un chatbot. L'agent conversationnel comprend vos centres d'intérêt, enregistre vos préférences, et une automatisation se charge d'aller lire la presse, de faire trier les articles par une IA, et de vous envoyer une newsletter HTML sur-mesure dans votre boîte mail.

---

## Ce qui fonctionne actuellement

Ce prototype couvre les exigences principales du projet :
- Un point d'entrée unique via le chat.
- L'extraction intelligente des données utilisateurs (nom, email, intérêts) en langage naturel.
- La récupération d'actualités depuis de multiples flux RSS combinés.
- Une curation personnalisée par l'IA (tri des articles selon le profil et rédaction).
- Un envoi automatisé d'e-mails au format HTML.
- Le respect des directives RGPD de base avec un flux de désabonnement prévu dans la base de données.

---

## Guide d'installation et de configuration

Pour que ce workflow fonctionne sur votre machine, il a besoin de se connecter à trois éléments extérieurs : une mémoire (la base de données), un cerveau (OpenAI), et un facteur (Gmail). Le fichier d'export ayant été anonymisé pour des raisons de sécurité, vous devrez fournir vos propres clés.

Voici la marche à suivre pas à pas.

### 1. Importer la logique
Lancez votre instance n8n locale (généralement accessible sur localhost:5678). Allez dans la section des workflows, choisissez d'importer un projet depuis un fichier, et sélectionnez le fichier persona.json fourni dans ce dépôt.

### 2. Configurer la base de données (Supabase)
Le projet utilise des nœuds PostgreSQL pour stocker les profils. La méthode recommandée et la plus rapide est d'utiliser Supabase.

1. Créez un projet gratuit sur Supabase. Prenez soin de bien noter le mot de passe de la base de données que vous créez à cette étape.
2. Allez dans l'éditeur SQL de Supabase et exécutez la requête suivante pour préparer votre table :
   `CREATE TABLE users (id SERIAL PRIMARY KEY, email TEXT UNIQUE NOT NULL, interests TEXT, send_time TEXT, password TEXT);`
3. Allez dans les paramètres de base de données de Supabase pour récupérer vos identifiants de connexion (l'URL du Host et l'utilisateur). Privilégiez l'adresse de "Connection pooling" avec le port 6543.
4. Dans n8n, ouvrez un des nœuds Postgres, créez un nouveau "Credential" et collez vos informations. Point crucial : n'oubliez pas d'activer l'option "Ignore SSL Issues" tout en bas de la fenêtre de configuration n8n pour autoriser la connexion.

### 3. Connecter l'IA
1. Connectez-vous à votre espace développeur OpenAI et générez une nouvelle clé API (Secret Key).
2. Dans n8n, allez dans l'onglet Credentials, cherchez "OpenAI API" et collez votre clé. 
3. Retournez dans le workflow et assurez-vous que les deux nœuds "OpenAI Model" utilisent bien ce nouveau credential.

### 4. Configurer l'envoi d'e-mails (Gmail OAuth2)
Google demande un peu plus de configuration pour autoriser n8n à envoyer des e-mails en votre nom.

1. Allez sur la console Google Cloud et créez de nouveaux identifiants OAuth 2.0. Choisissez le type "Application Web".
2. Dans les URI de redirection autorisés, ajoutez l'adresse de callback de votre n8n (par défaut : http://localhost:5678/rest/oauth2-credential/callback).
3. Important : Votre application Google Cloud étant en mode test, allez dans l'écran de consentement OAuth et ajoutez votre propre adresse e-mail dans la liste des utilisateurs de test (Test users). Sans cela, Google bloquera la connexion.
4. Copiez le Client ID et le Client Secret, collez-les dans les credentials Gmail sur n8n, et cliquez sur le bouton de connexion pour lier votre compte.

### 5. Lancement
Une fois tous les identifiants au vert, activez le workflow en haut à droite de l'écran n8n. Ouvrez l'URL fournie par le nœud "Chatbot Trigger", dites bonjour à l'assistant, et laissez-vous guider !

---

## Auteur

**Omar Joudi**
