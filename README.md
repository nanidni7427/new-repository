# SentinelAI — AI-Driven Threat Detection & Simulation Engine
** | Cybersecurity Track | **

---

## What it does
SentinelAI is a real-time SOC (Security Operations Center) dashboard that:
- Ingests events from network, endpoint, and application layers
- Detects 4 threat types: brute force, C2 beaconing, lateral movement, data exfiltration
- Correctly identifies false positives (admin bulk transfers)
- Correlates alerts across layers → upgrades severity to Critical
- Maps every threat to MITRE ATT&CK technique IDs
- Generates dynamic AI-powered response playbooks via **Ollama (free, local AI)**
- Streams all alerts live to a React dashboard via WebSocket

---

## AI Engine — Ollama (Free, No API Key Needed)

This version uses **Ollama** — a free, local AI that runs entirely on your machine.
No cloud, no API key, no cost.

### Install Ollama
1. Download from https://ollama.com and install it
2. Open a terminal and pull a model:

```bash
# Pick ONE — llama3 is recommended
ollama pull llama3        # Best quality (~4.7GB)
ollama pull mistral       # Fast & capable (~4.1GB)
ollama pull gemma:2b      # Lightest option (~1.7GB)
ollama pull phi3:mini     # Very small (~2.3GB)
```

3. Ollama runs automatically in the background after install.

> **No Ollama?** The app still works — it falls back to built-in static playbooks automatically. You won't see AI-generated responses but everything else works perfectly.

---

## Project Structure
```
sentinelai/
├── backend/
│   ├── main.py          ← FastAPI server + WebSocket + /inject endpoint
│   ├── data_gen.py      ← Synthetic log generator (network/endpoint/app layers)
│   ├── ingestor.py      ← Normalizes all log formats to unified schema
│   ├── detector.py      ← Rule engine + IsolationForest anomaly detection
│   ├── correlator.py    ← Cross-layer correlation engine
│   ├── playbook.py      ← Ollama local AI playbook generator
│   ├── requirements.txt
│   └── .env             ← Ollama settings (model name, URL)
└── frontend/
    ├── src/
    │   ├── App.jsx               ← Main dashboard layout
    │   └── components/
    │       ├── StatsRow.jsx      ← 6 live metric counters
    │       ├── AlertFeed.jsx     ← Scrolling live alert list
    │       ├── PlaybookPanel.jsx ← AI playbook display
    │       └── ThreatChart.jsx   ← Bar + line charts
    ├── public/index.html
    └── package.json
```

---

## Setup & Run

### Step 1 — Install Ollama (one-time)
```bash
# Go to https://ollama.com, install, then:
ollama pull llama3
```

### Step 2 — Backend
```bash
cd backend

# Install dependencies (no anthropic package needed!)
pip install -r requirements.txt

# (Optional) Change model in .env — default is llama3
# OLLAMA_MODEL=mistral

# Start the server
uvicorn main:app --reload --port 8000
```

### Step 3 — Frontend
```bash
cd frontend

# Install dependencies
npm install

# Start the dashboard
npm start
```

Open http://localhost:3000

---

## Changing the AI Model
Edit `backend/.env`:
```
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=llama3        # Change to: mistral, gemma:2b, phi3:mini, etc.
```

---

## Demo Script (5 minutes)

1. Show live alerts streaming in with severity color codes
2. Click a **Critical** alert → show AI-generated playbook
3. Point out a **CORRELATED** badge → explain cross-layer detection
4. Point out a **FALSE POSITIVE** → show correct classification
5. Click **INJECT BRUTE FORCE** → new wave of alerts in 3 seconds
6. Show MITRE ID (T1110, T1071 etc.) on any alert card

---

## Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core backend language |
| FastAPI | Web server + WebSocket streaming |
| scikit-learn | IsolationForest anomaly detection |
| **Ollama (local LLM)** | **Dynamic playbook generation — free, no API key** |
| React | Live SOC dashboard UI |
| Recharts | Threat distribution + timeline charts |
| WebSocket | Real-time alert streaming |
| SQLite | Event storage |

---

## Threat Detection

| Threat | MITRE ID | Detection Method |
|---|---|---|
| Brute Force | T1110 | Rule: 15%+ failed auths from single IP |
| C2 Beaconing | T1071 | Rule: fixed-interval small packets to external IP |
| Lateral Movement | T1021 | Rule: PsExec/SMB east-west after auth anomaly |
| Data Exfiltration | T1041 | Rule: >50MB outbound to external IP |
| Anomaly | N/A | ML: IsolationForest on bytes_out + port features |
| False Positive | — | Rule: known admin + internal destination |
