# RAG AI Agent

A sophisticated AI Agent application that answers user queries by intelligently leveraging both a private knowledge base (using Retrieval-Augmented Generation - RAG) and real-time web search, with a transparent agent trace workflow.

---

## Key Features

- **Hybrid AI & Intelligent Routing**: Combines internal RAG knowledge with real-time web search, dynamically selecting the best information source for each query.
- **User-Controlled Web Access**: UI toggle to enable or disable web search.
- **Transparent AI Workflow (Agent Trace)**: Step-by-step trace of the agent's internal thought process, including routing decisions, RAG sufficiency verdicts, and information retrieval summaries.
- **Contextual RAG Sufficiency Judgment**: An LLM assesses if retrieved RAG content is sufficient to answer a query, prompting further search if needed.
- **Dynamic Knowledge Ingestion (PDF Upload)**: Upload PDF documents directly, which are processed, embedded, and added to the Pinecone knowledge base.
- **Session Isolation**: Each session has its own namespace in Pinecone, keeping documents isolated between users.
- **Persistent Conversation Memory**: LangGraph's checkpointing maintains conversation context across turns.

---

## Architecture

```
RAG_AI_Agent/
├── frontend/
│   └── index.html          # HTML/CSS/JS frontend with Tailwind CSS
├── backend/
│   ├── main.py             # FastAPI entry point
│   ├── agent.py            # LangGraph AI agent workflow
│   ├── vectorstore.py      # Pinecone RAG logic
│   ├── config.py           # API keys and env vars
│   ├── requirements.txt    # Backend dependencies
│   └── Dockerfile
├── requirements.txt        # Root dependencies
├── data/
│   └── DIABETES.pdf        # Sample PDF
├── .env                    # API keys (not committed)
└── README.md
```

### Layers

- **User Interface (UI)**: Static HTML frontend (Tailwind CSS, Lucide icons, Marked.js).
- **API Layer**: FastAPI backend handling document upload and chat endpoints.
- **Agent Core**: LangGraph-powered workflow with router, RAG lookup, web search, and answer nodes.
- **Knowledge Base**: Pinecone vector DB with Jina AI embeddings.
- **External Tools**: Groq LLM (GPT-OSS models), Tavily Search API, Jina Embeddings API.

---

## Technology Stack

- **Language**: Python 3.12
- **Frontend**: HTML, CSS (Tailwind), JavaScript
- **Backend**: FastAPI, Uvicorn
- **Agent Orchestration**: LangGraph
- **LLMs**: Groq (GPT-OSS models)
- **Embeddings**: Jina AI Embeddings API (`jina-embeddings-v3`)
- **Vector Store**: Pinecone (1024 dimensions, cosine metric)
- **PDF Processing**: PyPDF + RecursiveCharacterTextSplitter
- **Search Engine**: Tavily API

---

## Setup and Installation

### Prerequisites

- Python 3.12+
- API Keys:
  - `GROQ_API_KEY`
  - `PINECONE_API_KEY`
  - `TAVILY_API_KEY`
  - `JINA_API_KEY`

### Installation

```bash
git clone https://github.com/shumail05/RAG_AI_Agent.git
cd RAG_AI_Agent

uv venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

uv pip install -r requirements.txt
```

Create a `.env` file at project root:

```dotenv
GROQ_API_KEY="your_groq_api_key_here"
PINECONE_API_KEY="your_pinecone_api_key_here"
PINECONE_ENVIRONMENT="us-east-1"
PINECONE_INDEX_NAME="rag-index"
TAVILY_API_KEY="your_tavily_api_key_here"
JINA_API_KEY="your_jina_api_key_here"
```

---

## Running the Application

### 1. Start the Backend (FastAPI)

```bash
cd backend
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 2. Serve the Frontend

Open `frontend/index.html` in a browser, or serve it locally:

```bash
cd ../frontend
uv run --with http.server python -m http.server 3000
```

Or with any static server:

```bash
npx serve frontend
```

---

## API Testing

### `/upload-document/` (POST)

- **URL**: `http://localhost:8000/upload-document/`
- **Body**: `form-data`, keys: `file` (PDF), `session_id` (string)
- **Response**:

```json
{
  "filename": "doc.pdf",
  "status": "success",
  "message": "Document added to session knowledge base."
}
```

### `/chat/` (POST)

- **URL**: `http://localhost:8000/chat/`
- **Body** (JSON):

```json
{
  "session_id": "test-session-001",
  "query": "What are the treatments of diabetes?",
  "enable_web_search": true
}
```

- **Response**:

```json
{
  "response": "Your agent's answer here...",
  "trace_events": [
    {
      "step": 1,
      "node_name": "router",
      "description": "Routed query to: RAG",
      "details": { "route": "rag" }
    }
  ]
}
```

---

## Docker

The backend includes a Dockerfile:

```bash
cd backend
docker build -t rag-agent-backend .
docker run -p 8000:8000 --env-file ../.env rag-agent-backend
```

---

## Future Improvements

- Integrate tools: calculator, calendar, code interpreter
- Stream LLM output token-by-token
- Advanced RAG techniques: reranking, multi-query
- Long-term memory database for chat history
- User authentication & profiles
- Enhanced UI: dark mode, animations, custom themes

---

Built with LangGraph, LangChain, Groq, Pinecone, and Jina AI.
