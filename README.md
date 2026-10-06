# StockAI Terminal

Local-first stock research for Indian and U.S. equities. StockAI Terminal is an educational research tool: it has no brokerage connection, trading, order execution, or portfolio management. It does not invent missing market values.

## Working features

- Streamlit terminal UI with five primary workspaces: Terminal, AI Research, Documents, Watchlist, and Settings. Terminal contains Overview, Stock Research, and Compare tabs; bounded Web Research lives inside AI Research.
- FastAPI service, local SQLite persistence, diagnostics, local preferences, and Ollama model discovery/switching.
- On-demand quotes, history, company profile/fundamentals, and news through one yfinance adapter. Provider and timestamps are exposed; data can be delayed or end-of-day and is subject to provider terms.
- Calculated SMA, EMA, RSI, MACD, Bollinger Bands, ATR, and volume from fetched price bars.
- Multi-symbol comparison, persisted watchlist, and searchable conversations.
- Ollama chat with selectable installed models, adjustable generation settings, recent conversation context, optional document RAG, relevance-triggered bounded web research, and saved citations.
- PDF, TXT, DOCX, CSV, and XLSX extraction. PDFs use PyMuPDF; OCR is attempted when extracted text is poor and requires Tesseract installed separately.
- EmbeddingGemma embeddings and CPU FAISS retrieval with optional per-document scoping. Extracted text previews and indexing status are available in Documents.
- Terminal news stream from provider-fetched headlines, with symbol context, publisher/date, source links, AI handoff, and save/index actions.
- Optional SEC EDGAR filing lists for U.S. companies, enabled after you enter a descriptive contact User-Agent in Settings. Filing documents open at SEC; the filing text is not automatically ingested into RAG.
- User-triggered web research fetches up to five public text pages per query. Local/private addresses, redirects, non-text responses, and oversized pages are rejected.

Market-wide scans across every listed company, guaranteed real-time quotes, SEC filing text ingestion, and broad historical source coverage are not included. Provider outages and missing fields are shown as unavailable; they are never replaced with guessed values.

## Requirements

- Windows 10/11
- Python 3.10+ (3.12+ recommended)
- Ollama for local AI, embeddings, and document RAG
- Tesseract OCR only for scanned PDFs

## Install and start

From PowerShell in the project directory:

```powershell
py -3 -m venv .venv
.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

Install and start Ollama separately, then download models:

```powershell
ollama pull qwen3:4b
ollama pull embeddinggemma
```

Start the app with `run.bat`, or run the services in separate terminals:

```powershell
.venv\Scripts\python.exe -m uvicorn app.api.main:app --host 127.0.0.1 --port 8000
.venv\Scripts\python.exe -m streamlit run app\ui.py --server.address 127.0.0.1 --server.port 8501
```

Open `http://127.0.0.1:8501`. API health and OpenAPI docs are at `http://127.0.0.1:8000/api/health` and `http://127.0.0.1:8000/docs`.

For development checks, install `requirements-dev.txt` and run `pytest`.

## Configuration and local data

`.env.example` documents the runtime, Ollama models, cache, and upload settings. Copy it to `.env`; local environment files are ignored. User preferences are saved in SQLite. The database is created at `data/stockai.db`, uploads at `storage/documents/`, and vectors/chunk metadata remain local. Removing a document or a saved web source also removes its extracted chunks.

Embedding calls release the embedding model after use to reduce memory pressure on an 8 GB machine. The app is a modular monolith: Streamlit → FastAPI → Python services → SQLite/files, providers, Ollama, and FAISS. It has no Node/React, PostgreSQL, Redis, Celery, Kubernetes, trading, or brokerage integrations.
# MyStock
