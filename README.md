# Agentic_RAG

An agentic RAG pipeline built with LangGraph. It loads a web page, embeds it with a
HuggingFace sentence-transformer (`all-MiniLM-L6-v2`, 384 dims), stores the chunks in
Pinecone, and answers questions with a Groq-hosted LLM (`openai/gpt-oss-120b`),
falling back to Tavily web search when the retrieved context is not enough.

All the code lives in [Agentic_RAG.ipynb](Agentic_RAG.ipynb).

## Prerequisites

- Python 3.13 (tested with 3.13.7)
- Free API keys from:
  - **Groq**: https://console.groq.com/keys
  - **Pinecone**: https://app.pinecone.io (API Keys section)
  - **Tavily**: https://app.tavily.com

## Setup

1. Clone the repo and enter it:

   ```bash
   git clone <repo-url>
   cd Agentic_RAG
   ```

2. Create and activate a virtual environment:

   ```bash
   python -m venv arag
   ```

   - Windows (PowerShell): `arag\Scripts\Activate.ps1`
   - macOS / Linux: `source arag/bin/activate`

3. Install dependencies (plus Jupyter if you don't already have it):

   ```bash
   pip install -r requirements.txt jupyter ipykernel
   ```

## Create the `.env` file

Create a file named `.env` in the project root (next to the notebook) with these keys:

```env
GROQ_API_KEY=your_groq_api_key
PINECONE_API_KEY=your_pinecone_api_key
TAVILY_API_KEY=your_tavily_api_key
```

| Variable           | Used for                                   |
|--------------------|--------------------------------------------|
| `GROQ_API_KEY`     | LLM calls (`ChatGroq`)                     |
| `PINECONE_API_KEY` | Vector store (index creation and retrieval) |
| `TAVILY_API_KEY`   | Web search fallback                        |

The notebook loads these with `load_dotenv()`. If a key is missing, it prompts for it
with `getpass` instead. `.env` is already in `.gitignore`. Never commit it.

## Run

```bash
jupyter notebook Agentic_RAG.ipynb
```

Select the `arag` environment as the kernel and run the cells top to bottom. On the
first run, the notebook creates the Pinecone index `industry-agentic-rag-kb`
(namespace `langgraph-agentic-rag`) if it doesn't exist and uploads the document
chunks. Later runs reuse the existing index.

## Notes

- If Groq no longer offers `openai/gpt-oss-120b`, change the model name in the
  notebook to one listed in your Groq dashboard.
- The first run downloads the embedding model from HuggingFace, which takes a minute.
