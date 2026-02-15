Telemedicine Kiosk — LLM + RAG AI Agent (Ollama Llama 3.1 + Chroma)

A telemedicine kiosk where patients enter symptoms on a simple web UI and a Flask backend runs the full workflow.  
It uses RAG over 3 doctor-recommended reference books stored in Chroma, calls Llama 3.1 via Ollama, and returns structured JSON with patient details, likely condition, severity, and next-step guidance.

Latency (observed): ~53ms – 2s (depends on retrieval + generation)  
Validation (observed): 8/10 usefulness rated by an MBBS doctor (India) on sample cases  

What it does:-
● Patient enters symptoms (optionally age/sex/history).  
● Backend retrieves relevant passages from the reference books (RAG).  
● Llama generates a structured JSON response:  
  - patient summary  
  - likely condition(s)  
  - severity level  
  - suggested next steps / medications (non-prescriptive guidance)  
● Severity-based routing:  
  - Low/Moderate: issue a queue token  
  - High/Critical: email/alert a specialist  

Tech Stack:-
● LLM Serving: Ollama + Llama 3.1  
● RAG: Embeddings + Chroma Vector DB (persisted locally)  
● Backend: Python, Flask  
● Frontend: HTML, CSS, JavaScript  
● Alerts: Email (SMTP / provider config)  

What I learned:-
● RAG isn’t plug-and-play — chunk size, overlap, and top-k choices decide if the model stays grounded or starts guessing.  
● Structured output is essential — iterating until the LLM returned consistent JSON made routing + UI much simpler.  
● Speed and quality trade off — tuning retrieval depth and context length helped keep responses fast without losing usefulness.  
● The LLM is only one piece — severity rules, guardrails, and escalation logic are what make it an “agent.”  
● Full-stack work changes priorities — Flask + UI integration forced handling messy inputs, failures, and timeouts.  
● Quick evaluation matters — doctor feedback showed what was actually helpful and what needed tighter prompting.  

How to Use:-
Install Ollama  
Run the model: `ollama run llama3.1`  
Exit the model and pull the embedding model: `ollama pull nomic-embed-text`  
Once the download completes, rerun: `ollama run llama3.1`  

Navigate to the HAI folder: `cd ~/PATH/HAI-main`  
Install all the requirements from requirements.txt: `pip install -r requirements.txt`  
Run application using following command: `python app.py`  
Once server starts open `index.html` from the directory on web browser.  
Select any prompt from `prompt.txt` or any similar to context of provided prompts.  
Wait for model to work and you get all the information for disease a patient has.  

Project Structure (typical):-
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
