# Student Database Application System - Backend

A robust, modular backend application for a **Student Database Management System** built with **FastAPI**, **SQLAlchemy**, **ChromaDB Vector Store**, and an **AI Chatbot powered by LangGraph and Google Gemini API**.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [Technology Stack](#technology-stack)
- [Vector Database Technical Justification](#vector-database-technical-justification)
- [Modular Project Structure](#modular-project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Environment Configuration](#environment-configuration)
  - [Local Installation & Run](#local-installation--run)
- [Docker Service Deployment](#docker-service-deployment)
- [API Documentation (Swagger UI)](#api-documentation-swagger-ui)
- [API Endpoints Overview](#api-endpoints-overview)
- [AI Chatbot Integration (LangGraph + Gemini)](#ai-chatbot-integration-langgraph--gemini)
- [Testing](#testing)

---

## Project Overview

The Student Database Application Backend provides scalable RESTful APIs to manage student records, perform relational queries and natural language vector searches, and interact with an AI Chatbot using **LangGraph** workflows powered by **Google Gemini API**.

---

## Key Features

- 🏗️ **Modular Clean Architecture**: Organized into distinct layers (`core`, `models`, `schemas`, `crud`, `services`, `chatbot`, `api`).
- ⚡ **FastAPI REST Endpoints**: High-performance CRUD operations for student records.
- 🔍 **Vector DB Semantic Search**: Natural language similarity search across student profiles, bios, and course enrollments.
- 🤖 **LangGraph AI Chatbot**: Tool-calling agent powered by Google Gemini (`gemini-2.5-flash`) to answer database questions in natural language.
- 📖 **Automatic Swagger Docs**: Interactive OpenAPI documentation served directly at `/docs`.
- 🐳 **Docker Deployment**: Single-command container deployment using `Dockerfile` and `docker-compose.yml`.

---

## Technology Stack

| Layer | Technology |
| :--- | :--- |
| **Backend Framework** | Python 3.11 + FastAPI |
| **Web Server** | Uvicorn |
| **Database & ORM** | SQLite + SQLAlchemy ORM |
| **Vector Database** | ChromaDB (Embedded Persistent Vector Store) |
| **AI LLM API** | Google Gemini API (`gemini-2.5-flash`) |
| **AI Agent Framework** | LangGraph & LangChain |
| **Data Validation** | Pydantic v2 |
| **Containerization** | Docker & Docker Compose |
| **Testing** | Pytest + HTTPX |

---

## Vector Database Technical Justification

For the semantic search and retrieval component of student bios, interests, and courses, **ChromaDB** was selected as the project's vector database.

### Technical Justification & Evaluation Matrix

1. **Embedded & Zero-Config Architecture**:
   - Unlike server-based vector stores (such as Qdrant or Milvus) or cloud-only vector databases (such as Pinecone), **ChromaDB** runs directly in-process or as an embedded container service.
   - It requires no external background daemons or cloud API keys for vector indexing, making the application fully self-contained and reproducible.

2. **Native Python & LangChain Ecosystem Integration**:
   - ChromaDB offers first-class Python SDK support and seamless integration with LangChain and LangGraph tool wrappers (`langchain-community.vectorstores`).

3. **Persistent On-Disk Storage**:
   - Automatically serializes embeddings and metadata to local disk storage (`./chroma_db`), ensuring vector index persistence across application restarts.

4. **Data Privacy & Zero Cloud Cost**:
   - Student records and bios remain entirely on-premise within the backend deployment, satisfying privacy considerations while eliminating recurring SaaS subscription fees.

---

## Modular Project Structure

```
stu DB/
├── app/
│   ├── __init__.py
│   ├── main.py                     # FastAPI application entrypoint & middleware
│   ├── core/                       # App settings & database initialization
│   │   ├── config.py
│   │   └── database.py
│   ├── models/                     # SQLAlchemy database models
│   │   └── student.py
│   ├── schemas/                    # Pydantic validation & serialization models
│   │   └── student.py
│   ├── crud/                       # Database CRUD operations
│   │   └── student.py
│   ├── api/                        # REST API routing
│   │   ├── router.py
│   │   └── endpoints/
│   │       ├── student.py          # Student REST endpoints
│   │       └── chatbot.py          # AI Chatbot REST endpoint
│   ├── services/                   # Services (ChromaDB Vector Store)
│   │   └── vector_store.py
│   └── chatbot/                    # LangGraph workflow & Gemini agent
│       ├── agent.py
│       └── tools.py
├── tests/                          # Automated Pytest suite
│   ├── test_api.py
│   └── test_chatbot.py
├── .env.example                    # Environment variable template
├── .gitignore
├── Dockerfile                      # Container build definition
├── docker-compose.yml              # Service orchestration file
├── requirements.txt                # Dependencies list
└── README.md                       # Project documentation
```

---

## Getting Started

### Prerequisites
- Python 3.10+ (Python 3.11 recommended)
- Git
- Docker Desktop (optional, for containerized running)

### Environment Configuration

1. Clone or navigate to the project directory:
   ```bash
   cd "stu DB"
   ```

2. Copy `.env.example` to create your `.env` file:
   ```bash
   cp .env.example .env
   ```

3. Configure environment variables in `.env`:
   ```env
   APP_NAME=Student Database Management System
   DEBUG=True
   API_V1_PREFIX=/api/v1
   DATABASE_URL=sqlite:///./student_database.db
   CHROMA_PERSIST_DIR=./chroma_db
   GEMINI_API_KEY=your_google_gemini_api_key_here
   GEMINI_MODEL=gemini-2.5-flash
   ```

---

### Local Installation & Run

1. **Create and activate a virtual environment**:
   ```bash
   python -m venv venv
   # On Windows:
   venv\Scripts\activate
   # On macOS/Linux:
   source venv/bin/activate
   ```

2. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

3. **Run the FastAPI development server**:
   ```bash
   uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
   ```

4. Open your browser and navigate to:
   - **Service Health Check**: `http://localhost:8000/`
   - **Interactive Swagger Documentation**: `http://localhost:8000/docs`

---

## Docker Service Deployment

To containerize and deploy the application as a standalone service:

1. **Build and start the container using Docker Compose**:
   ```bash
   docker compose up --build -d
   ```

2. **Verify container health and logs**:
   ```bash
   docker compose logs -f student-backend
   ```

3. **Stop the service**:
   ```bash
   docker compose down
   ```

---

## API Documentation (Swagger UI)

FastAPI automatically generates interactive Swagger API documentation accessible at `http://localhost:8000/docs` or ReDoc at `http://localhost:8000/redoc`.

You can test all endpoints, inspect request schemas, and try out live API calls directly from the browser UI.

---

## API Endpoints Overview

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/` | Health check & service info |
| `POST` | `/api/v1/students/` | Create a new student record (indexes into Vector DB) |
| `GET` | `/api/v1/students/` | List students with pagination (`page`, `limit`) and filter (`major`, `min_gpa`) |
| `GET` | `/api/v1/students/{id}` | Get student details by ID |
| `PUT` | `/api/v1/students/{id}` | Update student details (syncs with Vector DB) |
| `DELETE` | `/api/v1/students/{id}` | Delete student record (deletes from Vector DB) |
| `POST` | `/api/v1/students/search/semantic` | Natural language vector similarity search |
| `POST` | `/api/v1/students/seed` | Seed database with sample student records |
| `POST` | `/api/v1/chat/` | Send query to LangGraph Gemini AI Chatbot |

---

## AI Chatbot Integration (LangGraph + Gemini)

The chatbot uses **LangGraph** to manage a stateful agent graph with tool binding to Google Gemini (`gemini-2.5-flash`).

### Agent Capabilities:
- **`query_students_db`**: Queries structured relational database for specific majors or GPA cutoffs.
- **`semantic_student_search`**: Searches ChromaDB vector store for matching student bios or interests.
- **`get_database_statistics`**: Computes totals, average GPA, and available majors.

### Example Chatbot Requests:
```json
POST /api/v1/chat/
{
  "message": "Find all Computer Science students with a GPA above 3.5"
}
```

---

## Testing

Run the automated test suite using `pytest`:

```bash
pytest -v
```
