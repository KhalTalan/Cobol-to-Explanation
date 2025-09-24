# COBOL-Explain-Replication

This repository reproduces and extends the work from **["Explaining COBOL Programs with Large Language Models" (ASE 2025)](https://arxiv.org/pdf/2507.02182)**.  
It provides a clean, modular, and modern setup to parse COBOL projects, run explanation pipelines with different LLMs, and evaluate outputs automatically or manually.

Everything is orchestrated via `main.py` for reproducibility.

---

## 📂 Project Structure
```

cobol-to-explanation/
│
├── ASE2025                    # Reproducing this work
│
├── src/                       # Core modular Python code
│   ├── extractors/            # Artifact extraction modules
│   │   ├── cobol_parser.py    # Parses COBOL programs into functions/files/projects (regex/NetworkX/tree-sitter)
│   │   └── copybook_resolver.py # Resolves COPY statements by inlining external copybooks
│   │
│   ├── agents/                # LLM agent wrappers
│   │   ├── code_agent.py      # Code Processing Agent (Granite / LLaMA variant)
│   │   ├── text_agent.py      # Text Processing Agent (GPT-4o-mini / Claude)
│   │   └── judge_agent.py     # LLM-as-Judge for evaluation alignment
│   │
│   ├── pipelines/             # Level-specific workflows
│   │   ├── function_pipeline.py # Function-level explanation pipeline
│   │   ├── file_pipeline.py     # File-level pipeline (handles short/long files)
│   │   └── project_pipeline.py  # Project-level merging pipeline
│   │
│   └── utils/                 # Helper utilities
│       ├── config.py          # Lightweight config manager
│       ├── metrics.py         # Automatic evaluation metrics (METEOR, chrF, SBERT)
│       ├── token_utils.py     # Token counting and short/long classification
│       └── io.py              # File read/write helpers (CSV, JSON, graphs)
│
├── script/                    # Sequential notebooks
│   └── extract_artifact.ipynb # Extraction notebook outputting artifacts to data/processed/
│
├── data/                       # Data storage
│   ├── projects/              # Raw COBOL projects (from paper's 14 benchmarks or custom)
│   ├── processed/             # Extracted artifacts (functions, files, graphs in CSV/JSON)
│   └── references/            # Benchmark CSVs (function/file/project ground-truth)
│
├── outputs/                    # Generated explanations
│   └── run_metadata.json      # Lightweight provenance info (model, prompt version, seed)
│
├── evaluations/                # Evaluation tools and results
│   ├── manual_evaluation/     # Human-annotated CSVs for evaluation
│   ├── llm_judge/             # Scripts and outputs for LLM-as-Judge
│   ├── eval_notebook.ipynb    # Notebook to compute metrics & visual checks
│   └── demo_pipeline.ipynb    # Example end-to-end pipeline run
│
├── samples/                    # Tiny COBOL programs for smoke tests
│
├── tests/                      # Lightweight tests
│   └── test_smoke.py           # Verify extraction and pipelines on samples
│
├── main.py                     # CLI entry point orchestrating extract → pipeline → evaluation
├── requirements.txt            # Python dependencies (LangChain, LangGraph, networkx, sentence-transformers, etc.)
└── README.md                   # This file: setup, structure, replication guide
```


### `src/` – Core Source Code
- **`extractors/`**
  - `cobol_parser.py` → Parses COBOL code into functions, files, and project-level graphs. Can use regex + `networkx`, or tree-sitter if installed.
  - `copybook_resolver.py` → Expands `COPY` statements by inlining external copybooks. Ensures completeness of function/file graphs.

- **`agents/`**
  - `code_agent.py` → Agent interface for code-focused LLMs (e.g., Granite, LLaMA). Handles tokenization, chunking, and prompt formatting.
  - `text_agent.py` → Agent interface for general-purpose text LLMs (e.g., GPT-4o-mini, Claude). Useful for higher-level explanations.
  - `judge_agent.py` → LLM-as-a-Judge implementation. Evaluates generated explanations for correctness and coherence.

- **`pipelines/`**
  - `function_pipeline.py` → End-to-end flow for generating function-level summaries.
  - `file_pipeline.py` → Handles file-level summaries, including “short vs long” token-based branching.
  - `project_pipeline.py` → Combines multiple file-level summaries into project-level explanations.

- **`utils/`**
  - `config.py` → Lightweight configuration manager (dicts/env vars, no YAML bloat).
  - `metrics.py` → Automatic evaluation metrics (METEOR, chrF, Sentence-BERT similarity).
  - `token_utils.py` → Helpers for token counting and classifying artifacts as short/long.
  - `io.py` → Read/write helpers for CSV, JSON, and graphs.

---

### `script/`
- **`extract_artifact.ipynb`** → Jupyter notebook for parsing COBOL projects into structured artifacts. Outputs CSV/JSON into `data/processed/`.  
  Mirrors the original repo’s extraction workflow.

---

### `data/`
- **`projects/`** → Raw COBOL projects (the 14 benchmark repos from the paper, or your own).  
- **`processed/`** → Extracted artifacts (functions, files, dependency graphs).  
- **`references/`** → Ground-truth benchmark CSVs for evaluation (function/file/project summaries from the original dataset).

---

### `outputs/`
- Stores generated explanations from pipelines.  
- Each run may also create a `run_metadata.json` with minimal provenance (model name, date, prompt version, seed).

---

### `evaluations/`
- **`manual_evaluation/`** → Human-annotated evaluation CSVs (like in the paper).  
- **`llm_judge/`** → Scripts and results for LLM-as-a-Judge evaluations.  
- **`eval_notebook.ipynb`** → Notebook to run metrics, compare with references, and visualize results.  
- **`demo_pipeline.ipynb`** → (Optional) Example of running an end-to-end pipeline for demo/testing.

---

### `samples/`
- Tiny COBOL program(s) for quick smoke tests of the extraction + pipeline stages.

---

### `tests/`
- **`test_smoke.py`** → Minimal tests to verify environment and pipelines run on `samples/`.

---

### Root Files
- **`main.py`** → CLI entry point. Orchestrates:
  1. Extract COBOL artifacts
  2. Run selected pipeline (function/file/project)
  3. Save outputs and evaluations
- **`requirements.txt`** → Dependencies (LangChain, LangGraph, networkx, sentence-transformers, etc.).
- **`README.md`** → You’re reading it.

---

## 🚀 Submodules:
git submodule add https://github.com/anonymous-987654321/ASE2025.git **ASE2025**