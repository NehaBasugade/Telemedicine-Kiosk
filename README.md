# Telemedicine-Kiosk (HAI) — LLM + RAG AI Agent (Ollama Llama 3.1 + Chroma)

I built an LLM-powered telemedicine kiosk where patients enter symptoms and a **Flask** backend runs the full workflow end-to-end.  
The system uses **RAG over 3 doctor-recommended reference books** (embedded + stored in **Chroma**), serves **Llama 3.1 locally via Ollama**, and returns **structured JSON** with patient details, likely condition, severity, and suggested medications/next steps.

**Performance (observed):** ~**53ms – 2s** response time depending on retrieval + generation.  
**Quality (observed):** Results rated **8/10** by an MBBS doctor (India) on sample cases.

---

## What it does

### How it works
1. A patient enters symptoms on the web UI.
2. The backend pulls the most relevant snippets from the medical references (RAG).
3. Llama 3.1 generates a clean JSON response:
   - patient summary
   - likely condition(s)
   - severity level
   - recommended next steps / medications (non-prescriptive guidance)
4. Based on severity:
   - **Low/Moderate:** a queue token is generated
   - **High/Critical:** the case is escalated via email to a specialist

---

## What I learned

- **RAG isn’t “plug and play.”**  
  Small choices like chunk size, overlap, and top-k retrieval made a real difference in whether the model stayed grounded or started guessing.

- **Structured output matters in real apps.**  
  Getting the LLM to consistently return valid JSON took iteration. Once stable, it became much easier to automate routing and display results cleanly.

- **Speed and accuracy always fight each other.**  
  Tuning retrieval depth and context length taught me how to balance latency with answer quality instead of chasing max accuracy at any cost.

- **The model is only one piece of the system.**  
  The triage logic (token vs escalation) and guardrails are what make it feel like an actual agent not just a chat response.

- **Full-stack integration changes everything.**  
  Wiring Flask + frontend made me think about user experience, edge cases, and reliability (bad inputs, empty fields, timeouts, and safe fallbacks).

- **Evaluation is important even for small projects.**  
  Having a doctor review outputs was the fastest way to learn what was useful, what sounded risky, and what needed tighter prompting.

---

## Key features
- RAG pipeline over 3 reference books (chunking → embeddings → Chroma → retrieval)
- Ollama-hosted Llama 3.1 (local inference)
- Structured JSON output for downstream automation
- Severity-based routing (queue tokens + escalation email)
- Frontend (HTML/CSS/JS) + Flask backend

---

## Tech Stack
**LLM / Agent:** Ollama, Llama 3.1, prompt + JSON-structured generation  
**RAG:** embeddings, Chroma vector DB (persisted locally)  
**Backend:** Python, Flask, email notifications  
**Frontend:** HTML, CSS, JavaScript

---

## Project structure (high-level)
```bash
HAI-main/
├─ app.py                         # Flask backend (API + orchestration)
├─ index.html                      # Frontend entry
├─ assets/                         # CSS/JS/webfonts + styling
├─ images/                         # UI images/icons
├─ chroma_db_combined_textbooks/   # Chroma persisted vector DB
├─ extract_content.ipynb           # Build/refresh dataset + embeddings
├─ fetch_speak.py                  # (Optional) speech/TTS helper
├─ prompt.txt                      # Prompt template for the agent
├─ requirements.txt                # Python dependencies
└─ README.md
