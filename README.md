<div align="center">

# Legal RAG PT

### Local semantic retrieval pipeline for Portuguese legal documents

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebooks-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![Ollama](https://img.shields.io/badge/Ollama-bge--m3-black)](https://ollama.com/)
[![Qdrant](https://img.shields.io/badge/Qdrant-Vector%20Database-DC244C)](https://qdrant.tech/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

**Legal NLP · Embeddings · Vector Search · Retrieval Evaluation**

</div>

---

## About

**Legal RAG PT** is an experimental Retrieval-Augmented Generation retrieval pipeline for semantic search over Portuguese legal documents. The **Porto Municipal Regulatory Code (CRMP)** is used as a case study.

The project covers the complete retrieval process: PDF extraction, preprocessing, legal structure parsing, article-aware chunking, local embedding generation with `bge-m3`, Qdrant indexing, semantic vector search, and retrieval evaluation.

> This repository focuses on the retrieval component. The conversational generation layer is provided by the companion [`legal-rag-pt-n8n`](https://github.com/ruialexrib/legal-rag-pt-n8n) project.

---

## Project Ecosystem

| Repository | Purpose |
| --- | --- |
| [`legal-rag-pt`](https://github.com/ruialexrib/legal-rag-pt) | Corpus processing, embeddings, indexing, retrieval, and evaluation |
| [`legal-rag-pt-n8n`](https://github.com/ruialexrib/legal-rag-pt-n8n) | Conversational RAG application and workflow orchestration |
| [`legal-rag-pt-doc`](https://github.com/ruialexrib/legal-rag-pt-doc) | Complete technical documentation and report |

---

## Pipeline

```text
CRMP PDF
   │
   ▼
Text Extraction
   │
   ▼
Cleaning & Normalisation
   │
   ▼
Legal Article Parsing
   │
   ▼
Overlapping Chunks
   │
   ▼
bge-m3 Embeddings
   │
   ▼
Qdrant Indexing
   │
   ▼
Vector Search
   │
   ▼
Retrieval Evaluation
```

---

## Technology Stack

| Technology | Purpose |
| --- | --- |
| **Python** | Data processing and evaluation |
| **Jupyter** | Reproducible experimental pipeline |
| **PyMuPDF** | PDF text extraction |
| **bge-m3** | Multilingual embeddings |
| **Ollama** | Local embedding model execution |
| **Qdrant** | Vector database and similarity search |
| **Docker Compose** | Local Qdrant infrastructure |
| **NumPy / pandas** | Processing and evaluation |

---

## Repository Structure

```text
legal-rag-pt/
├── data/
│   ├── raw/             # Original PDF document
│   ├── processed/       # Extracted and structured legal content
│   ├── embeddings/      # Generated embeddings
│   └── evaluation/      # Evaluation results
├── notebooks/
│   ├── 01_pdf_extraction.ipynb
│   ├── 02_text_preprocessing.ipynb
│   ├── 03_document_parsing.ipynb
│   ├── 04_document_chunking.ipynb
│   ├── 05_embedding_generation.ipynb
│   ├── 06_qdrant_setup.ipynb
│   ├── 07_vector_search.ipynb
│   └── 08_retrieval_evaluation.ipynb
└── docker-compose.yml
```

---

## Dataset

| Artifact | Count |
| --- | ---: |
| Extracted pages | 662 |
| Identified articles | 1,440 |
| Generated chunks | 1,617 |
| Generated embeddings | 1,617 |
| Embedding dimensions | 1,024 |

---

## Getting Started

### Requirements

- Python 3.10+
- JupyterLab or Jupyter Notebook
- Docker with Docker Compose
- Ollama

### Setup

```bash
git clone https://github.com/ruialexrib/legal-rag-pt.git
cd legal-rag-pt
python -m venv .venv
pip install jupyter pymupdf numpy pandas requests qdrant-client
ollama pull bge-m3
docker compose up -d
```

Start Jupyter from the notebooks directory:

```bash
cd notebooks
jupyter lab
```

Run notebooks `01` through `08` sequentially.

---

## Notebook Workflow

| Notebook | Purpose |
| --- | --- |
| `01_pdf_extraction.ipynb` | Extract page text and metadata |
| `02_text_preprocessing.ipynb` | Clean and normalise extracted text |
| `03_document_parsing.ipynb` | Identify articles and legal hierarchy |
| `04_document_chunking.ipynb` | Create chunks up to 350 words with 50-word overlap |
| `05_embedding_generation.ipynb` | Generate `bge-m3` embeddings locally |
| `06_qdrant_setup.ipynb` | Create and populate the Qdrant collection |
| `07_vector_search.ipynb` | Execute semantic retrieval queries |
| `08_retrieval_evaluation.ipynb` | Calculate retrieval metrics and export results |

---

## Evaluation

The current evaluation uses five manually annotated queries and calculates **Hit@K**, **Recall@K**, **Mean Reciprocal Rank (MRR)**, and query latency.

For these preliminary examples, the relevant article ranked first in all five queries, producing `Hit@1`, `Recall@1`, and `MRR` values of `1.0`, with mean latency of approximately `0.91 s`.

These results are an initial pipeline validation only. Five queries are insufficient to support general conclusions about retrieval quality.

---

## Current Configuration

| Component | Value |
| --- | --- |
| Embedding model | `bge-m3` |
| Embedding service | Ollama — `localhost:11434` |
| Vector database | Qdrant — `localhost:6333` |
| Collection | `crmp_bge_m3` |
| Distance | Cosine |
| Maximum chunk size | 350 words |
| Chunk overlap | 50 words |

---

## Limitations & Future Work

The current parser is tailored to the CRMP, retrieval is vector-only, service configuration is largely local, and the evaluation dataset is intentionally small.

Future work includes reusable Python modules, version-pinned dependencies, configurable services, a larger evaluation dataset, hybrid search, reranking, automated tests, and tighter integration with grounded answer generation.

---

## Disclaimer

This project is intended for experimental and educational purposes. Its results do not constitute legal advice and should be verified against the applicable official sources.

---

## License

This project is licensed under the [MIT License](LICENSE).
