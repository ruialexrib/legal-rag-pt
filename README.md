# Legal RAG PT

An experimental *Retrieval-Augmented Generation* (RAG) pipeline for semantic search over Portuguese legal documents.

The project uses the **Porto Municipal Regulatory Code (CRMP)** as a case study and covers the entire process from PDF extraction to retrieval evaluation. The text is extracted, cleaned, structured into articles, split into chunks, converted into embeddings with `bge-m3`, and indexed in Qdrant.

> This repository currently focuses on the retrieval component. It does not yet include answer generation with an LLM.

## Companion n8n application

The companion [**`legal-rag-pt-n8n`**](https://github.com/ruialexrib/legal-rag-pt-n8n) repository provides the conversational application layer for this project. Its n8n workflow uses the `crmp_bge_m3` collection created here to retrieve relevant legal context, generate grounded answers with a local language model through Ollama, and return CRMP article and page references.

Run this repository's notebooks through `06_qdrant_setup.ipynb` before using the n8n application, so that Qdrant contains the required vectors and payload metadata.

## Pipeline

```text
PDF
 └─ Text extraction
     └─ Cleaning and normalization
         └─ Article parsing
             └─ Overlapping chunks
                 └─ Embeddings (bge-m3 via Ollama)
                     └─ Qdrant indexing
                         └─ Vector search and evaluation
```

## Technologies

- Python and Jupyter Notebooks
- [PyMuPDF](https://pymupdf.readthedocs.io/) for PDF text extraction
- [Ollama](https://ollama.com/) for running the `bge-m3` embedding model locally
- [Qdrant](https://qdrant.tech/) as the vector database
- Docker Compose for running Qdrant
- NumPy, pandas, and Requests for processing and evaluation

## Repository structure

```text
legal-rag-pt/
├── data/
│   ├── raw/             # Original PDF document
│   ├── processed/       # Extracted text, cleaned text, articles, and chunks
│   ├── embeddings/      # Generated embeddings
│   └── evaluation/      # Evaluation results and summary
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

## Prerequisites

- Python 3.10 or later
- JupyterLab or Jupyter Notebook
- Docker with Docker Compose
- Ollama

## Installation

1. Clone the repository and enter the project directory:

   ```bash
   git clone <REPOSITORY-URL>
   cd legal-rag-pt
   ```

2. Create and activate a virtual environment:

   ```bash
   python -m venv .venv
   ```

   On Windows (PowerShell):

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

   On Linux or macOS:

   ```bash
   source .venv/bin/activate
   ```

3. Install the Python dependencies:

   ```bash
   pip install jupyter pymupdf numpy pandas requests qdrant-client
   ```

4. Download the embedding model through Ollama:

   ```bash
   ollama pull bge-m3
   ```

5. Start Qdrant:

   ```bash
   docker compose up -d
   ```

Qdrant will be available at `http://localhost:6333`. Its web dashboard can be opened at `http://localhost:6333/dashboard`.

## Usage

Start Jupyter from the `notebooks` directory:

```bash
cd notebooks
jupyter lab
```

Run the notebooks in order, from `01` to `08`:

Each notebook includes step-by-step Markdown explanations and concise inline comments in English, documenting both the workflow and the reasoning behind key implementation decisions.

| Notebook | Purpose |
|---|---|
| `01_pdf_extraction.ipynb` | Extracts text and metadata from each PDF page. |
| `02_text_preprocessing.ipynb` | Cleans and normalizes the extracted text. |
| `03_document_parsing.ipynb` | Identifies articles and their legal hierarchy. |
| `04_document_chunking.ipynb` | Splits articles into chunks of up to 350 words with a 50-word overlap. |
| `05_embedding_generation.ipynb` | Generates local embeddings with `bge-m3` through Ollama. |
| `06_qdrant_setup.ipynb` | Creates the `crmp_bge_m3` collection and uploads the vectors to Qdrant. |
| `07_vector_search.ipynb` | Runs semantic queries and returns the most relevant articles. |
| `08_retrieval_evaluation.ipynb` | Calculates retrieval metrics and exports the results. |

The notebooks assume that `notebooks/` is the current working directory because project paths are built from `Path.cwd().parent`.

## Current dataset

The artifacts included in the repository were produced from the Porto Municipal Regulatory Code:

| Artifact | Count |
|---|---:|
| Extracted pages | 662 |
| Identified articles | 1,440 |
| Generated chunks | 1,617 |
| Generated embeddings | 1,617 |
| Embedding dimensions | 1,024 |

## Evaluation

The current evaluation uses five queries with manually defined relevant articles. It calculates `Hit@K`, `Recall@K`, Mean Reciprocal Rank (MRR), and query latency.

In the results stored under `data/evaluation/`, the relevant article ranks first for all five queries (`Hit@1`, `Recall@1`, and `MRR` are all `1.0`). The recorded mean latency is approximately `0.91 s` per query.

These figures should be interpreted as a **preliminary pipeline validation**. A set of five queries is too small to support general conclusions about system quality. Future evaluations should include more questions, different areas of the code, and broader relevance judgments.

## Current configuration

| Component | Value |
|---|---|
| Embedding model | `bge-m3` |
| Embedding service | Ollama at `http://localhost:11434` |
| Vector database | Qdrant at `http://localhost:6333` |
| Collection | `crmp_bge_m3` |
| Vector distance | Cosine |
| Maximum chunk size | 350 words |
| Chunk overlap | 50 words |

## Limitations

- The parser is tailored to the structure of the CRMP and may require changes for other legal documents.
- Search is exclusively vector-based; hybrid search, reranking, and answer generation are not yet implemented.
- Local service URLs and notebook paths cannot currently be configured through environment variables.
- The evaluation set is small and intended only as an initial validation.

## Roadmap

- Move the pipeline from notebooks into reusable Python modules.
- Add a version-pinned dependency file.
- Make service URLs, models, and collection names configurable.
- Expand the evaluation dataset.
- Experiment with hybrid search and reranking.
- Integrate an LLM to generate answers grounded in the retrieved articles.
- Add automated tests and continuous integration.

## Disclaimer

This project is intended for experimental and educational purposes. Its results do not constitute legal advice and should be verified against the applicable official sources.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
