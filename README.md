# Guideline Creative

**A RAG-powered study companion for AI annotation project guidelines.**

Annotators burn hours re-reading dense guideline docs and pinging SMEs with the same recurring questions. Guideline Creative fixes that: point it at any guideline document and it answers questions with citations back to the exact source passages — with quiz and coaching modes built in to help annotators prep for certification assessments.

Born out of a pain point lived firsthand for 5+ years working QA/SME roles on AI training-data projects.

---

## ✨ What it does

| Mode | Purpose |
|------|---------|
| **Ask** | Question-answering over your guideline docs, grounded with retrieved-context citations |
| **Quiz** | Auto-generates practice questions straight from the guidelines |
| **Coach** | Conversational walkthroughs of tricky edge cases and judgment calls |

Under the hood:
- **Smart model routing** — reasoning-heavy questions go to Groq's `gpt-oss-120b`, fast lookups go to `llama-4-scout`
- **Fully local embeddings** — `nomic-embed-text` via Ollama indexes your documents on-device; nothing leaves your machine for embedding
- **Rolling memory** — keeps the last 8 turns of context so follow-up questions stay coherent

---

## 🏗️ How it fits together

```
                    ┌─────────────────────────────┐
                    │        Gradio Web UI         │
                    │    Ask │ Quiz │ Coach        │
                    └──────────────┬──────────────┘
                                   │
                    ┌──────────────▼──────────────┐
                    │        Mode Router           │
                    │  (task type → model choice)  │
                    └──────┬──────────────┬───────┘
                           │              │
              ┌────────────▼───┐   ┌──────▼─────────────┐
              │  Retrieval      │   │  Groq API           │
              │  ChromaDB       │   │  gpt-oss-120b /     │
              │  (top-k chunks) │   │  llama-4-scout      │
              └────────┬───────┘   └────────────────────┘
                       │
              ┌────────▼───────┐
              │  Embeddings     │
              │  nomic-embed-   │
              │  text (Ollama,  │
              │  fully local)   │
              └────────────────┘
```

**Flow:** guideline docs get chunked → embedded locally → stored in ChromaDB. A question comes in, the top-k relevant chunks get retrieved, the router picks the right Groq model for the mode, and the answer comes back grounded in the exact passages it drew from.

---

## 🚀 Getting started

### You'll need

- Python 3.10+
- [Ollama](https://ollama.com/) running locally, with the embedding model pulled:
  ```
  ollama pull nomic-embed-text
  ```
- A free [Groq API key](https://console.groq.com/)

### Setup

```bash
# Clone
git clone https://github.com/Aurelia56-design/Creative.git
cd Creative

# Install
pip install -r requirements.txt

# Configure
cp .env.example .env
# → open .env and paste your Groq API key

# Run
python copilot_app2.py
```

Then open the local Gradio URL (usually `http://127.0.0.1:7860`), load a guideline document, and start asking questions.

---

## 🔐 Security

API keys live in a git-ignored local `.env` file — nothing hardcoded, nothing committed. Document embedding runs entirely on-device via Ollama.

---

## 🗺️ What's next

- [ ] FastAPI REST layer (upload / ask / list endpoints)
- [ ] LangGraph agent layer with low-confidence escalation to a human SME
- [ ] Docker image + GitHub Actions CI
- [ ] Per-project guideline workspaces

---

## 👤 Maintainer

[Aurelia56-design](https://github.com/Aurelia56-design)
