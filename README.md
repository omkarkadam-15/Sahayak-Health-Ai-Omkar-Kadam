# Sahayak Health AI

**Educational prototype — not clinically validated. Do not use as a replacement for qualified medical judgment or professional medical assessment.**

Agentic decision-support prototype that maps free-text symptom descriptions to one care-seeking label: `WAIT`, `DOCTOR`, or `ER`. It is designed for experimentation and evaluation, not real patient care.

## Features

- Two-phase sequential agent workflow with an interactive human-in-the-loop follow-up pause.
- Symptom extraction, severity scoring, follow-up question generation, triage decision, response formatting, and safety evaluation.
- Four tools: hybrid retrieval over a SQLite clinical case database, FDA drug-label lookup with selected India-specific mappings, deterministic vital-sign parsing, and NEWS2-based scoring.
- Trust dashboard with benchmark cases, deterministic safety audit, confusion matrix, per-class recall, and custom-query testing.
- Evaluation script with held-out test split metrics and timestamped JSON artifacts.
- Four course notebooks covering the staged implementation.

## Repository layout

- `demo_app.py` — FastAPI demo and dashboard.
- `learner/` — agent instructions and tools imported by the app.
- `setup_db.py` — downloads the public `medical-triage-500` JSONL dataset and builds `cases.db`; optional embeddings use `all-MiniLM-L6-v2`.
- `eval_agent.py` — batch evaluation.
- `notebooks/` — Week 1–4 notebooks.
- `docs/final_capstone_report.pdf` — capstone report.

## Setup

Use Python 3.11 in a virtual environment. Install the project dependencies needed by the app and notebooks; pin tested versions before deployment.

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
````

Create a local `.env` file (never commit it):

```dotenv
OLLAMA_API_KEY=your_ollama_cloud_api_key
OLLAMA_API_BASE=https://ollama.com/v1
SAHAYAK_OLLAMA_MODEL=gpt-oss:120b
````

Build the local case database if it is not already available:

```bash
python setup_db.py
````

Run the app:

```bash
python demo_app.py
````

The code's local launch configuration documents `http://localhost:7860`. For hosted deployment, configure the host and port according to the platform (for example, Uvicorn on `0.0.0.0` and the platform-provided `$PORT`). Verify all imported modules and the database path in the deployed environment.

## Evaluation and limitations

The supplied final report records a locked 50-case evaluation: 70.0% exact accuracy, 81.2% ER recall, 6.0% under-triage, 24.0% over-triage, zero ER cases predicted as WAIT, 78.0% safety-evaluator pass rate, and 38.0% human-review rate. The report explicitly concludes that the prototype **fails its stated safety gate** because under-triage exceeds 5% and ER recall is below its 95% target. These results are from a small evaluation sample and do not establish clinical correctness.

## Safety and privacy

- Do not enter real identifiable patient data. Use synthetic scenarios only.
- Keep API keys in `.env` locally and in the host's secret/environment settings when deployed.
- The application is an educational prototype, not clinically validated and not intended for diagnosis, prescriptions, or independent clinical decisions.
- Consult qualified medical professionals for diagnosis and treatment.

## Acknowledgements and data

The setup script references the public `syntech-ai/medical-triage-500` dataset on Hugging Face. Review that dataset's license and terms before redistribution. The evaluation utilities reference `gretelai/symptom_to_diagnosis`; review its license and terms before use.
