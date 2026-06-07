# Persona — AI News Aggregator

Persona is a personalisable AI news aggregator with no UI. Users interact with it via a chatbot, provide their email address, topics of interest and preferred schedule, and receive a personalised newsletter automatically.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Usage](#usage)
- [Features](#features)
- [Security & RGPD](#security--rgpd)
- [Workflow JSON](#workflow-json)

---

## Overview

Persona is built entirely on **N8N** (no custom UI, no backend code). The entry point is a single **Chat trigger** node. From there, the workflow:

1. Collects user info (name, email, topics of interest) via conversation
2. Validates and stores the profile
3. Fetches relevant news from multiple RSS sources
4. Filters and summarises stories using an AI agent
5. Sends a formatted HTML newsletter to the user's email at their chosen time

---

## Architecture

```
[Chat Trigger]
      │
      ▼
[AI Agent — Conversation]   ←── Simple Memory (session)
      │
      ▼
[Information Extractor]     ←── LLM (OpenAI)
      │  (nom, email, interets, est_valide)
      ▼
[If — est_valide?]
   │ TRUE                        │ FALSE
   ▼                             ▼
[RSS Read × N sources]      [Edit Fields — error reply]
   │
   ▼
[Limit]
   │
   ▼
[Aggregate]
   │
   ▼
[AI Agent — Newsletter]     ←── LLM (OpenAI)
      │
      ▼
[Gmail — Send HTML email]
```

---

## Prerequisites

- [N8N](https://n8n.io/) running locally (`localhost:5678`)
- A Google Cloud project with:
  - **Gmail API** enabled
  - An **OAuth 2.0 Client ID** (Web application type)
  - Redirect URI set to: `http://localhost:5678/rest/oauth2-credential/callback`
- An OpenAI API key (or any compatible LLM)

---

## Setup

### 1. Clone / Import the workflow

In N8N: **Workflows → Import from file** → select `persona.json`

### 2. Configure credentials

| Credential | Where to create |
|---|---|
| OpenAI API Key | N8N → Credentials → OpenAI |
| Gmail OAuth2 | N8N → Credentials → Gmail OAuth2 API |

For Gmail OAuth2:
- Go to [Google Cloud Console](https://console.cloud.google.com)
- APIs & Services → Credentials → Create Credentials → **OAuth client ID**
- Application type: **Web application**
- Authorized redirect URI: `http://localhost:5678/rest/oauth2-credential/callback`
- Copy Client ID + Client Secret into N8N

> **Note:** While your OAuth app is in test mode, add your Gmail address as a **Test User** in APIs & Services → OAuth consent screen.

### 3. Activate the workflow

Toggle the workflow to **Active** in N8N. The chat trigger will be available at the URL shown in the Chat Trigger node settings.

---

## Usage

Open the chatbot URL and start a conversation. The AI agent will guide you through the registration:

```
User  > Bonjour, je veux m'abonner à la newsletter
Agent > Bien sûr ! Quel est votre nom ?
User  > Omar Joudi
Agent > Votre adresse email ?
User  > omar@example.com
Agent > Quels sont vos centres d'intérêt ? (ex: technologie, sport, économie)
User  > technologie et IA
Agent > À quelle heure souhaitez-vous recevoir votre newsletter ?
User  > 8h le mercredi
Agent > Parfait, votre inscription est enregistrée !
```

A personalised newsletter will then be sent to the provided email address at the configured time.

---

## Features

### Mandatory (implemented)

- Chatbot trigger as unique entry point
- User information extraction via LLM (name, email, interests)
- Input validation (`est_valide` field)
- News fetching from multiple RSS sources
- AI-powered filtering and summarisation per user profile
- HTML email newsletter sent via Gmail

### Additional (implemented)

- HTML email format instead of plaintext
- Session memory for multi-turn conversation

### Planned / Bonus

- Scheduled sending at user-defined time (N8N Schedule trigger)
- User authentication to prevent unauthorised access to other users' data
- Unsubscribe mechanism (RGPD compliance)
- Fact-checking against secondary sources
- Multi-format output (blog post, Discord, social media)

---

## Security & RGPD

### Current measures

- User input is validated before any processing
- No hardcoded credentials in the workflow JSON (anonymised before export)
- HTTP Request nodes do not expose authentication headers

### RGPD considerations

- Email addresses are collected with explicit user consent via the chatbot
- Users can request deletion of their data by contacting the administrator
- A future unsubscribe flow should be implemented directly in the chatbot
- Data is stored locally in N8N (no third-party data broker involved)

> The workflow is designed for a European audience and attempts to follow basic RGPD guidelines. A full compliance audit is a planned bonus feature.

### Against malicious users

- The AI agent is instructed to reject prompt injection attempts
- Input validation via `est_valide` field blocks malformed or incomplete registrations
- Future: user authentication layer to isolate session data per user

---

## Workflow JSON

The exported workflow file is `persona.json`.

> **Before sharing:** credential names and IDs are included in N8N exports. This file has been anonymised — all credential names have been replaced with generic placeholders, and no API keys or authentication headers are present.

To import: N8N → Workflows → **Import from file** → `persona.json`

---

## Author

**Omar Joudi** — Epitech Lyon, Expert en ingénierie logicielle (RNCP Niveau 7)
