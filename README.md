# Clinical Discharge Summary Simplification

## Repo Link
https://github.com/Kirthikraja/NLPA3

**NLP for Social Good — Leiden University, Spring 2026**

A three-stage NLP pipeline that simplifies clinical discharge summaries from MIMIC-III into patient-facing text targeting a 6th–8th grade reading level, with NER-based fact anchoring to preserve clinical accuracy.

---

## Requirements

- **Python 3.11.9** (strictly required — other versions may cause dependency conflicts)
- **MIMIC-III dataset** — you must have the dataset loaded in your sytem

---

## Project Structure

```
NLPA3/
├── data/
│   ├── NOTEEVENTS_sorted.csv      # MIMIC-III clinical notes (discharge summaries)
│   └── PATIENTS_sorted.csv        # MIMIC-III patient metadata (DOB, demographics)
├── notebooks/
│   ├── main_pipeline.ipynb        # Full 3-stage pipeline (NER → generation → evaluation)
│   └── ablation_results.ipynb     # Ablation study across 5 conditions
└── README.md
```

---

## Installation

Run the following in order before executing any notebook cell:

```bash
pip install "numpy<2" medspacy spacy openai pandas textstat rouge-score
pip install https://s3-us-west-2.amazonaws.com/ai2-s2-scispacy/releases/v0.5.4/en_ner_bc5cdr_md-0.5.4.tar.gz
python -m spacy download en_core_web_sm
```

Or run the installation cell at the top of `main_pipeline.ipynb` directly — it handles all of the above.

---
> **Note:** Only move forward with the below step if the current API Key in the project stops working.
## API Key

This project uses the **Groq API** to run Llama 3.3 70b and Llama 3.1 8b.

1. Create a free account at [console.groq.com](https://console.groq.com)
2. Generate an API key
3. Paste it into the `GROQ_API_KEY` variable in the second cell of `main_pipeline.ipynb`:

```python
GROQ_API_KEY = "your_api_key"
```

---

## How to Run

### Main pipeline

Open `notebooks/main_pipeline.ipynb` and run all cells top to bottom. The notebook will:

1. Load and filter MIMIC-III notes for elderly patients (age ≥ 65)
2. Extract clinical entities (CHEMICAL, DISEASE) using MedSpaCy
3. Generate a simplified patient summary using the LLM
4. Print evaluation metrics (FK Grade, ROUGE-L, Fact Recall)

### Ablation study

Open `notebooks/ablation_results.ipynb` and run all cells. This runs 5 conditions across 5 patients (25 API calls total) and prints a mean ± std results table.

> **Note:** Allow ~2–3 minutes for the ablation to complete due to API call volume and the built-in rate-limit delay.

---

## Dependencies

| Package | Purpose |
|---|---|
| `medspacy` | Clinical NLP pipeline loader |
| `en_ner_bc5cdr_md` | BC5CDR-trained NER model (CHEMICAL, DISEASE) |
| `spacy` | Base NLP engine |
| `openai` | Groq API client (OpenAI-compatible) |
| `pandas` | Data loading and filtering |
| `textstat` | Flesch-Kincaid readability metrics |
| `rouge-score` | ROUGE-L evaluation |
| `numpy<2` | Required for MedSpaCy compatibility |

---

## Data

The `data/` folder contains two files derived from MIMIC-III:

- `NOTEEVENTS_sorted.csv` — clinical notes, filtered to discharge summary category
- `PATIENTS_sorted.csv` — patient records used for age calculation (DOB field)

---

## Reproducibility

All experiments use `random_state=42` for patient sampling and `temperature=0` for the main pipeline, ensuring identical results across runs. Python 3.11.9 is required to guarantee dependency resolution consistency.


