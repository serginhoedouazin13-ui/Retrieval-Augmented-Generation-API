![Alt](https://repobeats.axiom.co/api/embed/4c0147b36badffe103e67cdee4420db2d72dbb27.svg "Repobeats analytics image")
# Retrieval-Augmented-Generation API

Production-ready Retrieval-Augmented Generation (RAG) API built with FastAPI, ChromaDB and Ollama. The service embeds documents, performs semantic search over those embeddings, retrieves relevant context, and generates grounded answers using a local LLM - including the Docker workflow contributed by the repository maintainer.

Badges:  
[![Python](https://img.shields.io/badge/python-3.12+-blue)]() [![License: MIT](https://img.shields.io/badge/license-MIT-green)]()

Quick highlights
- FastAPI backend with automatic Swagger (OpenAPI) docs
- Vector search with ChromaDB
- Local LLM inference via Ollama (e.g., TinyLlama)
- Document-based QA: send a question, receive a grounded answer
- Development hot-reload and simple project layout

Table of contents
- Features
- Tech stack
- Requirements
- Installation & setup
- Usage (endpoints + examples)
- Project structure
- Development & testing
- Roadmap
- Contributing
- License
- Contact

Features
- Add and index documents for semantic retrieval
- Query the indexed documents and get answers grounded in retrieved context
- Local inference: no cloud LLM required (runs with Ollama)
- Simple REST API, documented with Swagger UI
-  Modular backend suitable for production use
- Dockerfile provided for containerizing the service

Tech stack
- Python 3.12+
- FastAPI (web framework)
- Uvicorn (ASGI server)
- ChromaDB (vector store)
- Ollama (local LLM runtime)
- Requests (HTTP client)
- Docker (engine & CLI)
- Docker Hub account (if pushing images)

Requirements
- Python 3.12+
- Ollama installed locally (https://ollama.com)
- A compatible local model (e.g., tinyllama) pulled into Ollama
- (Optional) virtualenv / recommended to isolate dependencies

Installation & setup

1) Clone the repo
```bash
git clone https://github.com/serginhoedouazin13-ui/Retrieval-Augmented-Generation-API.git
cd Retrieval-Augmented-Generation-API
```

2) Create and activate a virtual environment
- macOS / Linux:
```bash
python -m venv venv
source venv/bin/activate
```
- Windows (PowerShell):
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

3) Install Python dependencies
If a requirements file exists:
```bash
pip install -r requirements.txt
```
Or install core deps:
```bash
pip install fastapi uvicorn chromadb requests python-dotenv
```

4) Install and run Ollama
- Download / install from: https://ollama.com
- Pull a model (example):
```bash
ollama pull tinyllama
```
- Start Ollama (runs on port 11434 by default). Refer to Ollama docs for platform-specific instructions.

Configuration
The app reads a few runtime variables (you can set them as environment variables or use a .env file):
- OLLAMA_HOST (default: http://127.0.0.1:11434)
- MODEL_NAME (default: tinyllama)
- CHROMA_PERSIST_DIR (optional, for persistent vectors)
- PORT (default: 8000)

Example .env
```env
OLLAMA_HOST=http://127.0.0.1:11434
MODEL_NAME=tinyllama
CHROMA_PERSIST_DIR=./chroma
PORT=8000
```

Run the API
```bash
# development (hot reload)
uvicorn app:app --reload --host 0.0.0.0 --port 8000
```
Open:
- Swagger UI: http://127.0.0.1:8000/docs
- ReDoc: http://127.0.0.1:8000/redoc

API Endpoints (overview)
- GET /health — service health check
- POST /add — add documents to the index
- POST /query — ask a question; returns answer grounded on retrieved context

Example usage

1) Add documents (example payload)
```bash
curl -X POST http://127.0.0.1:8000/add \
  -H "Content-Type: application/json" \
  -d '{
        "documents": [
          {"id": "doc1", "text": "Ollama provides a local LLM runtime."},
          {"id": "doc2", "text": "ChromaDB is a vector database for semantic search."}
        ]
      }'
```

2) Query the RAG system
```bash
curl -X POST http://127.0.0.1:8000/query \
  -H "Content-Type: application/json" \
  -d '{"question": "What is Ollama used for?"}'
```

Expected (example) response
```json
{
  "answer": "Ollama runs local LLMs for inference; it can be used to generate text from retrieved context.",
  "sources": [
    {"id": "doc1", "score": 0.92, "text": "Ollama provides a local LLM runtime."}
  ]
}
```

Project structure (example)
```
Retrieval-Augmented-Generation-API/
├─ app.py                # FastAPI app entrypoint
├─ requirements.txt
├─ README.md
├─ docs/                 # optional screenshots / examples
└─ ...                   # other modules (indexing, vector store, LLM client)
```

Development & testing
- Use `uvicorn` with `--reload` for quick dev iterations.
- Add tests and use a test ChromaDB instance or in-memory config for CI.
- Linting/formatting: consider black / ruff / isort.

Notes & best practices
- Keep your vector store persisted (CHROMA_PERSIST_DIR) for production so embeddings aren't rebuilt on every restart.
- When adding documents, deduplicate or assign stable IDs to avoid index bloat.
- Monitor memory & disk usage of ChromaDB and the model runtime (Ollama).
- If you deploy to a server, ensure Ollama is installed and appropriate model files are present.

Roadmap (ideas)
- PDF & other file upload ingestion
- JWT authentication / API keys
- Streaming / partial answers while model generates
- Frontend UI for demos
- Cloud deployment scripts & Helm charts
- Multi-model routing & model selection

Contributing
Contributions are welcome. Suggested workflow:
1. Fork the repo
2. Create a branch: git checkout -b feat/my-change
3. Implement and add tests
4. Open a PR with a clear description



## Environment variables (PART 2)

Set these in your environment, a `.env` file, or pass them to Docker with `-e` flags:

- `OLLAMA_HOST` (default: `localhost`)
- `OLLAMA_PORT` (default: `11434`)
- `OLLAMA_MODEL` (e.g. `llama2` or your local model name)
- `CHROMA_DB_DIR` (path for ChromaDB persistence, optional)
- `RAG_API_PORT` (default: `8000`)

Adjust variable names above if the repository uses different config keys.

## Build and run with Docker (local)

1. Build the Docker image locally (replace or keep the tag as needed):

```bash
docker build -t serginhoedouazin13-ui/rag-api:local .
```

2. Run the container exposing port 8000 and passing env vars:

```bash
docker run --rm -p 8000:8000 \
  -e OLLAMA_HOST=host.docker.internal \
  -e OLLAMA_PORT=11434 \
  -e OLLAMA_MODEL=your-model-name \
  -e CHROMA_DB_DIR=/data/chroma \
  serginhoedouazin13-ui/rag-api:local
```

Notes:
- On macOS/Windows `host.docker.internal` lets the container access services running on the host (like a local Ollama instance). On Linux, configure networking accordingly.
- If ChromaDB persistence is used, mount a volume: `-v $(pwd)/chroma:/data/chroma`.

## Push to Docker Hub

Tag the image and push to Docker Hub (assumes you're logged in with `docker login`):

```bash
docker tag serginhoedouazin13-ui/rag-api:local serginhoedouazin13-ui/rag-api:latest
docker push serginhoedouazin13-ui/rag-api:latest
```

Pull and run from Docker Hub:

```bash
docker pull serginhoedouazin13-ui/rag-api:latest
docker run --rm -p 8000:8000 \
  -e OLLAMA_HOST=... -e OLLAMA_PORT=... -e OLLAMA_MODEL=... \
  serginhoedouazin13-ui/rag-api:latest
```

## API Endpoints

- Swagger / OpenAPI UI: http://localhost:8000/docs
- Health check (example): http://localhost:8000/health or `/ping` (depends on implementation)
- RAG query endpoint (example): `POST http://localhost:8000/query`

Example curl to test the query endpoint (adjust JSON body to match the repo's schema):

```bash
curl -X POST "http://localhost:8000/query" \
  -H "Content-Type: application/json" \
  -d '{"question":"What is retrieval augmented generation?","top_k":5}'
```

## Development (without Docker)

1. Create a virtual environment and install dependencies:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

2. Start the FastAPI server (example):

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

(Adjust `app.main:app` to your actual module path if different.)

## Troubleshooting

- Docker cannot reach Ollama: ensure Ollama is running and reachable. Use `host.docker.internal` on macOS/Windows. On Linux, consider running Ollama in a container on the same network or expose it to the host network.
- ChromaDB persistence issues: ensure the persistence directory is mounted and writable by the container.
- Port conflicts: ensure port 8000 is free or change `RAG_API_PORT` and the Docker port mapping.

## Notes from contributor

This repository was extended to include a custom Dockerfile, locally-built images, containerized runtime tests, and a public Docker Hub image pushed for sharing. Examples in this README use the Docker Hub username `serginhoedouazin13-ui`. Update those examples if you publish under a different account.


License
This project is licensed under the MIT License. See LICENSE for details.

Acknowledgements
- Ollama — local LLM runtime
- ChromaDB — vector database for semantic search
- FastAPI — web framework

Contact
Maintainer: serginhoedouazin13-ui  
Repo: https://github.com/serginhoedouazin13-ui/Retrieval-Augmented-Generation-API
