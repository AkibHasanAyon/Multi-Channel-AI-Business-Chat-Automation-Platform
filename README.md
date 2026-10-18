# 🌐 Multi-Channel AI Business Chat Automation Platform

![Django](https://img.shields.io/badge/Django-5.2-092E20.svg?style=for-the-badge&logo=django&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.12-3776AB.svg?style=for-the-badge&logo=python&logoColor=white)
![Celery](https://img.shields.io/badge/Celery-5.5-37814A.svg?style=for-the-badge&logo=celery&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-7.0-DC382D.svg?style=for-the-badge&logo=redis&logoColor=white)
![Qdrant](https://img.shields.io/badge/Qdrant-Vector%20DB-DC2626.svg?style=for-the-badge)
![Docker](https://img.shields.io/badge/Docker-Enabled-2496ED.svg?style=for-the-badge&logo=docker&logoColor=white)

An enterprise-grade omnichannel customer engagement, business operations, and automated AI assistance platform. Centralizes communications across **Facebook Messenger**, **Instagram DM**, and **WhatsApp** with retrieval-augmented generation (RAG), Stripe marketplace billing, appointment scheduling, and automated ticketing.

---

## ⚡ Architecture & Subsystems

```
                                 ┌──────────────────────────────────────────────────────────┐
                                 │                OMNICHANNEL WEBHOOKS                      │
                                 │       Facebook  •  Instagram  •  WhatsApp               │
                                 └────────────────────────────┬─────────────────────────────┘
                                                              │
                                                              ▼
                                 ┌──────────────────────────────────────────────────────────┐
                                 │              CENTRAL ROUTER & CHANNELS                   │
                                 │             Django 5.2 + Django Channels                 │
                                 └──────────────┬────────────────────────────┬──────────────┘
                                                │                            │
                        ┌───────────────────────┴──────┐      ┌──────────────┴──────────────┐
                        ▼                              ▼      ▼                             ▼
         ┌─────────────────────────────┐ ┌───────────────────────────┐ ┌────────────────────────────┐
         │          AI CORE            │ │       FINANCE SUITE       │ │      OPERATIONS & SYNC     │
         │  • LangChain + OpenAI       │ │  • Stripe Subscriptions   │ │  • Google Calendar Sync    │
         │  • Qdrant Vector DB (RAG)   │ │  • Stripe Connect Vendors │ │  • Service Bookings        │
         │  • Doc Ingestion (PDF/Docx) │ │  • Automated Invoicing    │ │  • RBAC & Support Tickets  │
         └─────────────────────────────┘ └───────────────────────────┘ └────────────────────────────┘
```

---

## 🚀 Key Modules & Capabilities

### 1. 🤖 AI & RAG Engine (`Ai/`)
* **Vector Search with Qdrant**: Real-time semantic document retrieval providing context-aware responses without hallucination.
* **Knowledge Ingestion**: Upload business PDFs, catalogs, or policy documents via `/api/ai-training-files/` to automatically chunk, embed, and synchronize with Qdrant.
* **Analytics**: Question leaderboard identifying the most frequent customer inquiries for business intelligence.

### 2. 💬 Omnichannel Messaging (`Socials/`)
* **Unified Webhook Router**: `/webhook/{platform}/` normalizes inbound payloads from Meta platforms (Facebook, Instagram, WhatsApp).
* **Real-Time WebSockets**: Powered by Django Channels and Redis for instant back-office agent-to-customer live chat.
* **Customer Profile Tracking**: Automatically captures customer metadata, past interactions, and channel identities.

### 3. 💳 Financial Suite (`Finance/`)
* **Stripe Subscriptions**: Tiered subscription plans with automated checkout sessions and recurring lifecycle handling.
* **Stripe Connect**: Marketplace onboarding allowing tenant companies to accept customer payments directly.
* **Webhook Listeners**: Cryptographically verified Stripe event processing for renewals, invoices, and charge failures.

### 4. 🏢 Company Operations & Scheduling (`Accounts/`, `Others/`)
* **Google Calendar Synchronization**: Sync client bookings and working hours directly with Google Calendar via OAuth2.
* **Role-Based Access Control (RBAC)**: Manage employee roles, departmental permissions, and audit logs.
* **Support Ticket Desk**: Lifecycle management for customer support tickets from creation to resolution.

---

## 🔌 API Endpoints Quick Reference

| Domain | Base Path | Key Endpoints | Description |
|---|---|---|---|
| **Authentication** | `/api/auth/` | `login/`, `get-otp/`, `verify-otp/`, `google/login/`, `sessions/` | OTP auth, Social login, active session controls |
| **Messaging** | `/api/chat/` | `chat-profile-list/`, `question-leaderboard/`, `old-message/` | Chat history, customer profiles, AI analytics |
| **Finance** | `/api/finance/` | `plans/`, `create-checkout/`, `create-subscriptions/`, `connect/onboard/` | Stripe checkout, subscription tiers, Stripe Connect |
| **Operations** | `/api/` | `tickets/`, `knowledge-base/`, `booking/`, `google/calendar/connect/` | Bookings, ticketing, calendar integration |
| **Admin Console** | `/api/admin/` | `dashboard/`, `users/`, `companies/`, `approve-channel/` | Tenant approvals, plan requests, platform metrics |
| **Webhooks** | `/` | `webhook/{platform}/`, `{platform}/callback/`, `payments/stripe-webhook/` | External provider ingress points |

---

## 🛠️ Technology Stack

* **Core Framework**: Django 5.2, Django REST Framework
* **Real-Time Layer**: Django Channels, ASGI, Redis 7.0
* **Async Workers**: Celery 5.5, Celery Beat
* **AI & NLP**: LangChain, OpenAI API, Qdrant Vector Database
* **Database**: PostgreSQL (production) / SQLite3 (dev)
* **API Documentation**: Drf-Spectacular (OpenAPI 3.0 schema)
* **Containerization**: Docker & Docker Compose

---

## ⚙️ Installation & Deployment

### Option A: Running with Docker Compose (Recommended)

1. **Clone the repository and enter directory**:
   ```bash
   cd Multi-Channel-AI-Business-Chat-Automation-Platform
   ```

2. **Configure environment variables**:
   ```bash
   cp .env.example .env
   # Fill in DB credentials, OpenAI keys, Stripe secrets, and Meta App IDs
   ```

3. **Launch all containers**:
   ```bash
   docker-compose up --build -d
   ```
   *This starts: Django Web container, Celery Worker, Celery Beat Scheduler, and Redis.*

4. **Verify container health**:
   ```bash
   docker-compose ps
   ```

### Option B: Local Python Development Setup

1. **Create and activate a virtual environment**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: .\venv\Scripts\activate
   pip install -r requirements.txt
   ```

2. **Run Redis**:
   Ensure Redis is running locally on port `6379`.

3. **Apply migrations and start the server**:
   ```bash
   python manage.py migrate
   python manage.py runserver
   ```

4. **Run Celery worker in a separate terminal**:
   ```bash
   celery -A Verseai worker -l info
   ```

---

## 🛡️ Critical Environment Variables

| Variable | Description |
|---|---|
| `SECRET_KEY` | Django cryptographic signing key |
| `OPENAI_API_KEY` | OpenAI API key for embeddings and completions |
| `QDRANT_HOST` / `QDRANT_PORT` | Qdrant vector database connection endpoint |
| `STRIPE_SECRET_KEY` / `STRIPE_WEBHOOK_SECRET` | Stripe API credentials |
| `FB_APP_ID` / `FB_APP_SECRET` | Meta Developer Platform credentials |
| `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` | Google Calendar and OAuth credentials |
| `CELERY_BROKER_URL` | Redis broker URL (e.g., `redis://localhost:6379/0`) |

