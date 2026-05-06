# Bridging the Clinical Gap: NLP-Driven Discharge Summary Simplification

**Course project (Group 14)** — Personalized, readability-aware simplification of MIMIC-III discharge summaries for older adults, combining clinical entity extraction with demographic- and persona-aware large language model generation.

## Authors

| Name | Student ID |
|------|------------|
| Kirthik Raja Kesavan | s4570170 |
| Ilakiya Sulur Ravi Sumathi | s4666062 |
| Ekansh Khanulia | s4336089 |
| Likhitha Priyanka | s5004179 |

## Motivation

Hospital discharge summaries are written in dense medical language that often exceeds patients’ health literacy. Older adults (65+), who often manage several chronic conditions, are at higher risk of misunderstanding medications and follow-up care, which can worsen outcomes and increase readmissions. This project uses NLP to make discharge information **clearer and more actionable** while keeping **clinical facts grounded**, as part of an **NLP for social good** perspective on inclusion and wellbeing.

## Research question

**To what extent can demographic-aware prompting with LLMs make discharge summaries easier to understand while remaining accurate and useful for elderly patients?**

## Goals

- Perform **semantic translation**, not only word-level simplification: turn technical discharge text into a **structured, three-part** home-care style plan:
  1. Plain-language **diagnosis** explanation  
  2. Simplified **medication** schedule  
  3. **Red flag** symptoms that need urgent attention  
- Ground generation with **patient metadata** (e.g. age, health-literacy-oriented instructions) via **persona-based** system prompts that favor clarity and a caregiving tone.  
- Compare **model-agnostic** setups (e.g. local privacy-preserving models vs. cloud APIs) where applicable.  
- **Expected outcomes:** lower **Flesch–Kincaid** grade (target ≤ 8th grade where appropriate), stronger **actionability** and **lexical simplicity** vs. generic simplification baselines, plus a small **qualitative audit** to watch for **fidelity bias** (oversimplification or loss of critical facts).

## Methodology (summary)

| Stage | Description |
|--------|-------------|
| **Data** | MIMIC-III: **NOTEEVENTS** (discharge narratives) and **PATIENTS** (demographics). Filter **discharge summaries** for patients **aged 65+**. Preprocess to strip headers/artifacts; sample on the order of **500** diverse summaries (e.g. high-burden conditions). |
| **Silver-standard references** | High-quality simplified targets from a strong model on a subset; map technical entities (medications, doses, red-flag symptoms) to simplified counterparts for scalable evaluation. |
| **Pipeline** | **Two-stage hybrid:** (1) **MedSpaCy NER** to extract diagnoses and dose-related entities so critical facts stay anchored; (2) **in-context / persona-based LLM generation** (“semantic translation”), with caregiver framing, **no Latin abbreviations** in output, and tuning (e.g. repetition penalty) to reduce mimicry of jargon. Encoder–decoder models (e.g. Flan-T5) vs. decoder-only (e.g. Llama-3, Gemini) may be compared. |
| **Evaluation** | **Flesch–Kincaid** (readability), **ROUGE-L** between extracted entities and simplified output (fidelity of retained facts), plus **manual review of 20 random samples** for omissions/hallucinations. |

## Repository layout

```text
NLPA3/
├── README.md
├── data/
│   ├── PATIENTS_sorted.csv      # MIMIC-style patient demographics (SUBJECT_ID, DOB, …)
│   ├── ADMISSIONS_sorted.csv    # Hospital stays (SUBJECT_ID, HADM_ID, times, short DIAGNOSIS field, …)
│   └── NOTEEVENTS_sorted.csv    # Add when available — discharge TEXT lives here (not in ADMISSIONS)
└── notebooks/
    └── main_pipeline.ipynb      # Prototype: MedSpaCy/sciSpaCy extraction + LLM simplification + metrics
```

**Note:** The core narrative for simplification comes from **NOTEEVENTS** (`CATEGORY = Discharge summary`, column **`TEXT`**). **PATIENTS** supports joins and age filtering. **ADMISSIONS** describes stays (`HADM_ID`, times, short diagnosis line) but is **not** a substitute for full discharge-note text. Obtain **NOTEEVENTS** through your **MIMIC-III / PhysioNet** access and place it under `data/` if you are running the full corpus path from the proposal.

## Quick start

### Prerequisites

- Python **3.10+** (notebook metadata references 3.11).  
- [Jupyter](https://jupyter.org/) or VS Code / Cursor with Jupyter support.  
- Optional: **[Ollama](https://ollama.com/)** (or similar) for local LLM inference via an OpenAI-compatible API.  
- Optional: API keys if you use cloud models (e.g. OpenAI, Gemini) as in your experiments.

### Run the prototype

1. Clone the repository and open `notebooks/main_pipeline.ipynb`.  
2. Run the install cells (they pull **MedSpaCy**, **spaCy**, **sciSpaCy** `en_ner_bc5cdr_md`, **textstat**, **rouge-score**, etc.). Restart the kernel if prompted.  
3. Point CSV paths in the data-loading cell to **your** `data/` directory (this repo may not ship `NOTEEVENTS` due to data-use agreements).  
4. For a quick demo without NOTEEVENTS, the notebook can use a **synthetic discharge snippet** to exercise extraction and generation.

First-time setup can take several minutes while models download.

## Ethics and deployment stance

- **Data:** MIMIC-III is de-identified; still follow **PhysioNet responsible use** and **never** try to re-identify patients.  
- **Privacy:** Prefer **local** inference for real notes when possible so clinical text does not leave your environment.  
- **Safety:** Outputs can **omit or distort** doses or red flags (**fidelity loss / hallucinations**). This work is framed as **clinical decision support**: a **qualified clinician should review** any simplified summary before it is used in care.  
- **Bias:** Models may reflect poor coverage for some language or literacy backgrounds; prompts aim for **clear, respectful, action-focused** language rather than a patronizing tone—limitations should be stated in any write-up.

## AI use (proposal reflection)

AI tools were used **minimally** for structural guidance and polishing prose in the **written proposal**; the research question, hybrid design, and core methodology were developed by the group.

## References

1. Chen, Wang & Miller. Human-centered evaluation of language technologies: A systematic review. *ACL*, 2020.  
2. Devaraj et al. Paragraph-level medical text simplification. *ACL*, 2021.  
3. Kincaid et al. Derivation of new readability formulas (Flesch–Kincaid). Naval Technical Training Command, 1975.  
4. Lee & Smith. The fidelity bias: Evaluating medical accuracy in automated text simplification. *EMNLP*, 2020.  
5. Shihab et al. Patient-friendly clinical notes: Towards a new text simplification dataset. *TSAR*, 2022.  
6. Smith, Johnson & Brown. Medical text simplification using reinforcement learning (TESLEA). *Journal of Biomedical Informatics*, 2020.

