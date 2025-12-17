# Private Codebase Assistant (RAG System)

A multi-project **Retrieval-Augmented Generation (RAG)** system that lets teams **query their private repositories** through natural-language questions.
Built with **FastAPI**, **Streamlit**, **ChromaDB**, and **Groq LLM (LLaMA 3)** for blazing-fast and secure in-house code understanding.

---

## Features

 **Automatic Repository Ingestion**

- Clone or upload repositories.
- Parse source files into semantic chunks with metadata (path, file type, etc.).
- Store embeddings persistently in ChromaDB.

 **Multi-Project Management**

- Create, list, and delete projects.
- Re-embed or update code incrementally.

 **Intelligent Question Answering**

- Ask natural-language questions about your code.
- Retrieves top-K most relevant chunks using vector similarity.
- LLaMA 3 (Groq API) generates concise, code-aware answers with citations.

 **FastAPI + Streamlit Interface**

- **Backend**: `/projects`, `/ask`, `/search`, `/ingest`, `/browse` endpoints.
- **Frontend**: A clean Streamlit dashboard to:
  - Manage projects.
  - Ingest or upload code.
  - Ask questions with or without LLM.
  - Browse files, documents, and chunks interactively.

 **Persistent Local Vector Store**

- All embeddings are stored in `data/chroma_store/` using `chromadb.PersistentClient`.

---

## Architecture Overview

```
┌───────────────┐
│ ingest_repo.py│  ← Clone or read files
└──────┬────────┘
       ↓
┌───────────────┐
│ parse_chunk.py│  ← Split files → text chunks + metadata
└──────┬────────┘
       ↓
┌───────────────┐
│ embed_store.py│  ← Store / query embeddings in ChromaDB
└──────┬────────┘
       ↓
┌───────────────┐
│ retrieval.py  │  ← Query top-K relevant chunks
└──────┬────────┘
       ↓
┌───────────────┐
│ answer_generation.py │  ← LLM (Groq) answers using context
└──────────────────────┘

Frontend → **Streamlit app.py**  
Backend → **FastAPI api_server.py**
```

---

## Repository Structure

```
src/
 ├── pipeline/
 │   ├── ingest_repo.py       # Clone or walk folders, parse + upsert chunks
 │   ├── parse_chunk.py       # Chunk text & attach metadata
 │   ├── embed_store.py       # Persistent ChromaDB store & retrieval utils
 │   ├── retrieval.py         # Query + generate answers
 │   ├── answer_generation.py # Embeddings + LLM (Groq API)
 │
 ├── services/
 │   ├── api_server.py        # FastAPI backend (project CRUD + /ask)
 │   ├── app.py               # Streamlit frontend dashboard
 │
data/
 ├── chroma_store/            # Persistent vector database
 ├── projects.json            # Registered project metadata
 ├── uploads/                 # Uploaded files storage
 └── parsed_chunks.json       # Example output
```

---

## Installation

### 1. Clone the repo

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

### 2. Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate    # or venv\Scripts\activate on Windows
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Environment variables

Create a `.env` file in the root:

```
LLM_key=your_groq_api_key
API_BASE=http://localhost:8000
```

---

## Running the System

### Start the **FastAPI backend**

```bash
uvicorn src.services.api_server:app --reload --port 8000
```

### Start the **Streamlit frontend**

```bash
streamlit run src.services.app.py
```

Access it at  **http://localhost:8501**

---

## Example Usage

1. **Create a new project**
   - From the *Projects* tab → “Create New Project” → give it a name.
2. **Ingest your repo**
   - Either clone from GitHub or specify a local folder.
3. **Ask a question**
   - Go to *Ask* tab → select your project → ask:
     _“How are sentences split in our code?”_
4. **View matches & answer**
   - See retrieved code chunks and the AI’s summarized explanation.

---

## Tech Stack

| Layer       | Technology                                                   |
| ----------- | ------------------------------------------------------------ |
| Backend API | **FastAPI**                                            |
| Frontend UI | **Streamlit**                                          |
| Vector DB   | **ChromaDB (PersistentClient)**                        |
| Embeddings  | **SentenceTransformer – all-MiniLM-L6-v2**            |
| LLM Engine  | **Groq API (LLaMA 3 / Mixtral)**                       |
| Environment | **Python 3.10+**, `.env`, `requests`, `pydantic` |

---

## Example Data Paths

- `data/projects.json` — metadata for registered projects
- `data/chroma_store/` — vector database
- `data/parsed_chunks.json` — sample parsed output
- `data/uploads/` — temporary uploaded files

---

## Future Enhancements

- Fine-tuned code understanding for different languages.
- User authentication for team collaboration.
- Streamed LLM responses and syntax-highlighted answers.
- Hybrid retrieval (text + AST embeddings).
- Integration with enterprise Git services (GitLab, Bitbucket, etc.).

---

## License

This project is released under the **MIT License** — feel free to use, modify, and adapt it for your internal systems.

---

### Author

**Yousif Al Eshari** — Al-Hussein Technical University
