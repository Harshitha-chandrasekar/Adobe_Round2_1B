# Round 1B: Persona-Driven Document Intelligence
---
An end‑to‑end system that ingests a collection of PDFs, a persona definition, and a job‑to‑be‑done, then uses semantic embeddings to identify and rank the most relevant sections and refined paragraphs. Designed for CPU‑only execution, model size ≤ 1 GB, and no internet dependency during inference.

---

## 🚀 Features

- **Two‑stage pipeline**  
  1. **Heading Extraction** via a fine‑tuned TinyBERT (Round 1A).  
  2. **Semantic Ranking** using `all-MiniLM-L6-v2` embeddings.

- **Persona + Task Querying**  
  Ranks sections based on how well they satisfy a persona’s concrete job.

- **Snippet Drill‑down**  
  Selects the single most relevant paragraph from each top section.

- **Lightweight & Fast**  
  - Runs on CPU only  
  - Total model footprint < 1 GB  

----

##  Repository Structure

```

round1b/
├── data/                    # Input PDFs (e.g. Collection A)
├── model/                   # Fine‑tuned TinyBERT checkpoint
├── outputs/
│   ├── headings/            # JSON outputs from infer.py
│   └── result.json          # Final ranked output
├── src/
│   ├── infer.py             # Heading extractor (TinyBERT)
│   ├── ranker.py            # Section ranker (MiniLM)
│   └── utils.py             # PDF & JSON helpers
├── input.json               # Master spec: documents, persona, job
├── requirements.txt         # Python dependencies
                  

````

---

## Setup

1. **Clone & enter project**  
   ```bash
   git clone <repo-url> round1b
   cd round1b
``

2. **Install dependencies**

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```

3. **Populate folders**

   * **`data/`**: place your PDF collection (e.g. South of France).
   * **`model/`**: drop your TinyBERT model files (`config.json`, `vocab.txt`, `pytorch_model.bin`, etc.).
   * **`input.json`**: supply the challenge spec, e.g.:

     ```json
     {
       "challenge_info": { ... },
       "documents": [
         { "filename":"South of France - Cities.pdf", "title":"Cities" },
         …
       ],
       "persona": { "role":"Travel Planner" },
       "job_to_be_done": { "task":"Plan a trip of 4 days for a group of 10 college friends." }
     }
     ```

---

##  Usage


python src/infer.py                      # generates JSONs in outputs/headings/
python src/ranker.py                     # produces outputs/result.json
```

**Output**:

* **`outputs/headings/*.json`** – section outlines for each PDF
* **`outputs/result.json`** – final ranked sections & refined snippets

---

##  Output Format

```jsonc
{
  "metadata": {
    "input_documents": [ "Cities.pdf", … ],
    "persona":         "Travel Planner",
    "job_to_be_done":  "Plan a trip of 4 days…",
    "processing_timestamp": "2025-07-28T22:40:00.123456"
  },
  "extracted_sections": [
    {
      "document":        "Cities.pdf",
      "section_title":   "Major Cities Overview",
      "page_number":     2,
      "importance_rank": 1
    },
    …
  ],
  "subsection_analysis": [
    {
      "document":     "Cities.pdf",
      "refined_text": "Nice: Known for its seaside…",
      "page_number":  2
    },
    …
  ]
}
```

---

##  Customization & Troubleshooting

* **Adjust Top‑N** in `ranker.py` by changing `[:5]`.
* **Path errors?** Ensure `input.json`, `data/`, and `outputs/headings/` are correctly populated.
* **Model caching warnings** on Windows can be ignored or resolved via dev‑mode/administrator privileges.

---

##  License & Acknowledgments

* Implementation inspired by Adobe Hackathon Round 1B “Connect What Matters”.
* Uses [Hugging Face Transformers](https://github.com/huggingface/transformers) and [Sentence‑Transformers](https://github.com/UKPLab/sentence-transformers).
* PDF parsing via [PyMuPDF](https://github.com/pymupdf/PyMuPDF) & [PyPDF2](https://github.com/py-pdf/PyPDF2).

---
#
