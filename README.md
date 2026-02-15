# Telemedicine Kiosk — LLM + RAG AI Agent (Ollama Llama 3.1 + Chroma)

A telemedicine kiosk where patients enter symptoms on a simple web UI and a **Flask** backend runs the full workflow.  
It uses **RAG over 3 doctor-recommended reference books** stored in **Chroma**, calls **Llama 3.1 via Ollama**, and returns **structured JSON** with patient details, likely condition, severity, and next-step guidance.

- **Latency (observed):** ~53ms – 2s (depends on retrieval + generation)
- **Validation (observed):** 8/10 usefulness rated by an MBBS doctor (India) on sample cases

---

## What it does
1. Patient enters symptoms (optionally age/sex/history).
2. Backend retrieves relevant passages from the reference books (RAG).
3. Llama generates a structured JSON response:
   - patient summary
   - likely condition(s)
   - severity level
   - suggested next steps / medications (non-prescriptive guidance)
4. Severity-based routing:
   - **Low/Moderate:** issue a queue token
   - **High/Critical:** email/alert a specialist

---

## Tech Stack
- **LLM Serving:** Ollama + Llama 3.1
- **RAG:** Embeddings + Chroma Vector DB (persisted locally)
- **Backend:** Python, Flask
- **Frontend:** HTML, CSS, JavaScript
- **Alerts:** Email (SMTP / provider config)

---

## What I learned
- RAG isn’t plug-and-play — chunk size, overlap, and top-k choices decide if the model stays grounded or starts guessing.
- Structured output is essential - iterating until the LLM returned consistent JSON made routing + UI much simpler.
- Speed and quality trade off — tuning retrieval depth and context length helped keep responses fast without losing usefulness.
- The LLM is only one piece — severity rules, guardrails, and escalation logic are what make it an “agent.”
- Full-stack work changes priorities — Flask + UI integration forced handling messy inputs, failures, and timeouts.
- Quick evaluation matters — doctor feedback showed what was actually helpful and what needed tighter prompting.

---

## Project Structure (typical)
```bash
HAI-main/
├─ app.py                         # Flask backend (API + orchestration)
├─ index.html                      # Frontend entry
├─ assets/                         # CSS/JS files
├─ images/                         # UI images/icons
├─ chroma_db_combined_textbooks/   # Persisted Chroma DB
├─ extract_content.ipynb           # Build/refresh embeddings + DB
├─ prompt.txt                      # Prompt template
├─ requirements.txt                # Python dependencies
└─ README.md
