# Vectorless_RAG

A lightweight, vectorless Retrieval-Augmented Generation (RAG) prototype that uses PageIndex to build a document tree and an LLM to select relevant sections and generate grounded answers. The project demonstrates how to perform retrieval over a document tree without an external vector store.
# Vectorless_RAG — Vectorless Retrieval-Augmented Generation (RAG)

One-line pitch: a compact, production-minded prototype that performs retrieval over a document Table-of-Contents (PageIndex) and produces grounded, cited answers without using a vector store.

Why this project stands out
- Focuses on precision: returns section-level citations (title + page) to make answers auditable.
- Practical engineering: minimizes API calls by compressing summaries, demonstrates asynchronous indexing with PageIndex, and shows how to swap LLM providers for comparison.
- Recruiter signal: demonstrates system design (retrieval, prompting, grounding), debugging of prompting/formatting issues, and reproducible experiments — strong evidence of applied ML engineering.

Highlights
- Document-tree (PageIndex) ingestion and cached index management
- LLM-driven tree selection (reasoning over TOC to pick relevant sections)
- Context assembly and grounded answer generation with citations
- Notebook-first, reproducible workflow suitable for demos and interviews

Tech stack
- Python 3.11+
- pageindex (PageIndex client)
- groq (Groq LLM client; swap to OpenAI if desired)
- Jupyter Notebook for guided demo and exploration

Quick start (minutes)

```bash
# 1) create and activate a virtualenv
python -m venv .venv
# Windows: .venv\\Scripts\\activate
source .venv/Scripts/activate

# 2) install dependencies
pip install -r requirements.txt

# 3) add API keys to a .env file
echo "PAGE_INDEX_API_key=..." >> .env
echo "GROQ_API_Key=..." >> .env

# 4) open the notebook and run cells in order
jupyter lab PageIndex_VectorLess_RAG.ipynb
```

Primary usage
- Upload a PDF: the notebook uploads the PDF via `pi_client.submit_document(...)` and waits for asynchronous index build.
- Inspect the tree: `pageindex_tree = pi_client.get_tree(doc_id, node_summary=True)["result"]`.
- Run retrieval + answer: `vectorless_rag(query, pageindex_tree)` returns a concise, cited answer.

Example (Python REPL)

```python
from pathlib import Path
import json

# load cached tree (optional)
pageindex_tree = json.loads(Path("pageindex_tree.json").read_text())

answer = vectorless_rag("What is wealth preservation?", pageindex_tree)
print(answer)
```

Persisting the tree

```python
import json
with open("pageindex_tree.json", "w", encoding="utf-8") as f:
    json.dump(pageindex_tree, f, indent=2, ensure_ascii=False)
```

Design notes (good talking points for interviews)
- Why vectorless? For many document Q&A use-cases, hierarchical structure + LLM selection is simpler and cheaper than dense vector stores.
- Prompt hygiene: the project demonstrates why correct separators and prompt-scoping matter (fixed a bug where `/n` was used instead of real newlines).
- Provider comparisons: the code isolates model calls so you can swap Groq <-> OpenAI for A/B testing.

How to evaluate and extend
- Benchmark: run a set of curated questions and measure citation accuracy (section title + page) and answer precision.
- Add caching: persist node text to reduce API calls during development.
- Add unit tests for tree traversal and context assembly.

Contributing & contact
- Open issues or PRs. If you’d like help benchmarking Groq vs OpenAI on this repo, open an issue describing your evaluation questions.

License
- See the `LICENSE` file at the project root.
