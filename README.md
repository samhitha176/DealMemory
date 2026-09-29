# DealMemory

> **An AI sales intelligence agent that remembers what worked, what failed, and uses past deal experience to prepare reps for the next conversation.**

DealMemory is an AI-powered sales intelligence application built around one idea: **sales agents should remember experience, not just conversations.**

Sales knowledge is often scattered across CRM records, interaction notes, stakeholder information, and individual memory. A traditional AI assistant can summarize a conversation, but a summary alone does not make the outcome reusable during the next sales interaction.

DealMemory separates structured CRM data from experiential memory. PostgreSQL stores the current state of accounts, deals, stakeholders, interactions, and follow-ups, while **Hindsight** stores and retrieves experiences about what happened, what approach was used, and what outcome followed. **Groq** then combines the current deal context with recalled experience to generate an evidence-grounded pre-call briefing.

---

## The Core Idea

**Most AI sales assistants remember the conversation.  
DealMemory remembers the experience.**

The system follows this loop:

```text
Sales Interaction
       ↓
PostgreSQL
Structured Deal Record
       ↓
Hindsight RETAIN
       ↓
Experiential Memory
       ↓
Hindsight RECALL
       ↓
Current Deal Context + Past Experience
       ↓
Groq
       ↓
Personalized Deal Brief
       ↓
New Sales Outcome
       ↓
Hindsight RETAIN

What DealMemory Does

DealMemory helps a sales representative:

Manage accounts and deals
Track stakeholders
Record customer interactions
Track follow-ups
Preserve successful and unsuccessful approaches
Store interaction experiences using Hindsight
Recall relevant past experiences during deal preparation
Generate AI-powered pre-call briefings
Ground recommendations in recorded evidence
Keep memories isolated by authenticated user and deal
How It Works
1. Record

A sales representative records an interaction with information such as:

Customer concern or objection
Approach or tactic used
Outcome
Notes
Stakeholder
Interaction type
Date

The structured interaction is stored in PostgreSQL.

2. RETAIN

The interaction is converted into a factual representation and sent to Hindsight RETAIN.

The retained experience captures the relationship between:

Concern → Approach → Outcome

This allows the system to preserve more than a simple conversation summary.

3. RECALL

When the representative prepares for a deal, the backend builds a contextual recall query using information such as:

Deal
Stage
Current objection
Next action

Hindsight RECALL retrieves relevant past experiences.

4. Generate

The backend combines:

Current PostgreSQL Data
+
Recalled Hindsight Experience

and sends the context to Groq.

The generated briefing includes information such as:

Key points
What worked
What failed
Stakeholders
Commitments
Recommendation
Evidence
5. Adapt

The new sales outcome can become another retained experience, allowing the memory layer to continue accumulating deal knowledge.

Architecture
<img width="2939" height="465" alt="mermaid-diagram" src="https://github.com/user-attachments/assets/2b3ff935-5dc1-47a3-a63d-eb2e9029fcc3" />

Architecture Responsibilities
Layer	Responsibility
React + Vite	User interface
FastAPI	API and application logic
PostgreSQL / Supabase	Structured CRM data
Hindsight	Experiential memory
Groq	AI briefing generation
JWT	Authentication and access control
The Important Distinction

PostgreSQL is the structured system of record.

It stores entities such as:

Accounts
Deals
Stakeholders
Interactions
Follow-ups

Hindsight is the experiential memory layer.

It stores and retrieves experiences about:

Concerns
Approaches
Outcomes
Relevant past deal experiences

Hindsight does not replace PostgreSQL.

PostgreSQL vs Hindsight
PostgreSQL	Hindsight
Structured system of record	Experiential memory
Accounts	Past experiences
Deals	Approaches
Stakeholders	Outcomes
Interactions	Relevant memories
Follow-ups	Semantic recall
Ownership and access control	Experience retrieval

The two systems have different responsibilities but work together during deal preparation.

Example: Learning From a Failed Approach

The Acme Corp example in the application demonstrates a pricing objection.

The recorded experience describes:

Customer concern:
Pricing objection

Approach:
Discount-first

Outcome:
Failed to move the conversation forward

A second approach focuses on:

ROI + Payback

which is represented as the better-engaging approach in the example.

When the deal is prepared again, the system can retrieve the relevant pricing experience through Hindsight.

The resulting briefing can distinguish between:

What failed
→ Discount-first approach

What worked better
→ ROI / payback framing

The recommendation is therefore grounded in the recorded experience rather than presented as an unsupported prediction.

Note: The Acme Corp information is demo/example data and verification data in the project. It should not be interpreted as measured results from a real customer engagement.

Hindsight Memory

Hindsight is the core experiential-memory component of DealMemory.

RETAIN

When an interaction is recorded, DealMemory creates a factual representation containing information such as:

Customer Concern / Objection
Approach / Tactic Used
Outcome
Notes
Deal Context
Stakeholder Context
Interaction Date

A simplified representation of the implementation is:

lines.append(
    f"Customer Concern/Objection: {concern.strip()}"
)

lines.append(
    f"Approach/Tactic Used: {approach.strip()}"
)

lines.append(
    f"Outcome: {outcome.strip() if outcome else 'Neutral'}"
)

The backend then sends this experience to Hindsight RETAIN.

RECALL

During deal preparation, the backend builds a contextual query using the current deal state.

The recalled memories are combined with current PostgreSQL information before the briefing is generated.

This creates the important flow:

Past Experience
      ↓
Hindsight RECALL
      ↓
Current Deal Context
      ↓
Groq
      ↓
Evidence-Grounded Briefing
AI Briefing

DealMemory uses Groq to generate the final pre-call briefing.

The model receives:

Current information
Deal stage
Deal health
Current objection
Account context
Stakeholders
Recent interactions
Next action
Recalled experience
Previous approaches
Previous outcomes
Relevant Hindsight memories

The briefing is structured around:

Key Points
What Worked
What Failed
Stakeholders
Commitments
Recommendation
Evidence

The generation process is designed to remain grounded in the information supplied to the model and to acknowledge when there is insufficient recorded evidence.

Key Features
Authentication
User signup
User login
JWT-based authentication
Authenticated user context
Protected API routes
Ownership-based authorization
Sales Management
Account management
Deal management
Stakeholder management
Interaction tracking
Follow-up tracking
Experiential Memory
Hindsight RETAIN
Hindsight RECALL
Deal-scoped memory
User-scoped memory
Memory synchronization status
AI Intelligence
Contextual deal preparation
Hindsight-powered experience retrieval
Groq-powered briefing generation
Evidence-grounded recommendations
What-worked / what-failed extraction
Security and Data Isolation

DealMemory applies authentication and ownership checks across the application.

The backend:

Uses JWT authentication
Protects authenticated API routes
Derives ownership from the authenticated user
Validates access to deals, accounts, and related resources
Prevents cross-user resource access
Keeps Hindsight memory scoped to the authenticated user and deal
Stores secrets through environment variables
Never returns password hashes through API responses

The client does not control which Hindsight memory bank is accessed.

Memory scope is derived on the server using authenticated user and deal information.

API

The backend is built with FastAPI.

Authentication
POST /api/auth/signup
POST /api/auth/login
GET  /api/auth/me
POST /api/auth/logout
Core Sales Data
Accounts
Deals
Stakeholders
Interactions
Follow-ups

The application provides protected CRUD operations for the core sales entities.

Deal Preparation
GET /api/deals/{deal_id}/prepare

The preparation flow combines:

PostgreSQL
    +
Hindsight RECALL
    +
Groq

to produce the pre-call briefing.

Memory

The application also exposes memory operations for retaining and recalling deal experiences.

FastAPI's interactive API documentation is available at:

http://localhost:8000/docs

when running locally.

Tech Stack
Frontend
React
Vite
TypeScript
Tailwind CSS
React Router
Lucide
Backend
Python
FastAPI
Pydantic
SQLAlchemy
Alembic
Database
PostgreSQL
Supabase
AI & Memory
Hindsight
hindsight-client
Groq
openai/gpt-oss-120b
Project Structure
DealMemory/
│
├── backend/
│   ├── app/
│   │   ├── core/
│   │   │   ├── config.py
│   │   │   ├── database.py
│   │   │   ├── deps.py
│   │   │   └── security.py
│   │   │
│   │   ├── models/
│   │   │   ├── account.py
│   │   │   ├── deal.py
│   │   │   ├── follow_up.py
│   │   │   ├── interaction.py
│   │   │   ├── stakeholder.py
│   │   │   └── user.py
│   │   │
│   │   ├── routers/
│   │   │   ├── accounts.py
│   │   │   ├── auth.py
│   │   │   ├── deals.py
│   │   │   ├── follow_ups.py
│   │   │   ├── health.py
│   │   │   ├── interactions.py
│   │   │   ├── memory.py
│   │   │   └── stakeholders.py
│   │   │
│   │   ├── schemas/
│   │   └── services/
│   │       ├── hindsight_service.py
│   │       └── groq_service.py
│   │
│   ├── alembic/
│   ├── scripts/
│   ├── requirements.txt
│   └── .env.example
│
├── frontend/
│   ├── src/
│   ├── package.json
│   └── .env.example
│
├── docker-compose.yml
└── README.md
Getting Started
Prerequisites

Make sure the following are installed:

Python
Node.js
PostgreSQL / Supabase
Hindsight account/API access
Groq API access
1. Clone the repository
git clone https://github.com/samhitha176/DealMemory.git
cd DealMemory
2. Backend Setup
cd backend

python -m venv .venv

.\.venv\Scripts\Activate.ps1

pip install -r requirements.txt

Configure the backend environment variables using:

backend/.env.example

Do not commit the actual .env file.

Run database migrations:

alembic upgrade head

Start the backend:

python -m uvicorn app.main:app --reload --port 8000

Backend:

http://localhost:8000

API documentation:

http://localhost:8000/docs
3. Frontend Setup

Open another terminal:

cd frontend

npm install

npm run dev

Frontend:

http://localhost:5173
Environment Variables

Use the provided .env.example files as the source of truth for required configuration.

Typical configuration includes:

DATABASE_URL=your_database_url

JWT_SECRET_KEY=your_secret

HINDSIGHT_BASE_URL=your_hindsight_url
HINDSIGHT_API_KEY=your_hindsight_key
HINDSIGHT_BANK_ID=your_bank_id

GROQ_API_KEY=your_groq_key

Never commit .env files, database credentials, API keys, JWT secrets, or other private credentials to GitHub.

Why Experiential Memory Matters

A conventional CRM interaction might tell a future sales representative:

Pricing was discussed.

DealMemory aims to preserve more useful experience:

Concern:
Pricing

Approach:
Discount-first

Outcome:
Did not advance the conversation

Alternative:
ROI / payback framing

That difference is the core of the system.

The objective is not simply to remember that a conversation happened.

It is to make the outcome of that experience available when the next conversation needs it.

Design Principles
Structured data stays structured

PostgreSQL remains the source of truth for current CRM state.

Experience is remembered separately

Hindsight provides a memory layer for past experiences and outcomes.

Recall should be contextual

Memory retrieval is shaped by the current deal rather than treated as a generic history search.

AI should stay evidence-grounded

Generated recommendations should be based on the current deal context and recalled experiences.

Memory should respect ownership

User and deal boundaries are enforced before memory operations are performed.

Current Limitations

DealMemory depends on the quality of the information recorded during sales interactions.

The usefulness of a recalled experience depends on:

What was recorded
Whether the experience was successfully retained
Whether relevant memories are retrieved
The quality of the context provided to the LLM

The generated briefing is an AI-assisted interpretation of recorded information. It is not a guarantee of future sales outcomes.

The Acme Corp scenario used in the demo represents application/example data and should not be interpreted as measured customer performance.

Future Improvements

Potential future work includes:

CRM integrations
Email and calendar integrations
Automated follow-up suggestions
Richer deal analytics
Team-level experiential insights
Larger-scale evaluation of briefing quality
More advanced memory-based sales workflows

These are future directions and are not represented as currently implemented functionality.

Hindsight Resources
Hindsight GitHub
Hindsight Documentation
Vectorize — Agent Memory
