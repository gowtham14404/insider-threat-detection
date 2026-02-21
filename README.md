# 🛡️ AI-Driven Insider Threat Detection
### A Biometric Behavioral Analytics System

> **Monitor. Learn. Detect. Protect.**  
> Real-time insider threat detection using keystroke dynamics, mouse biometrics, and AI — without ever storing what you type.

---

## 📸 System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    ENDPOINT AGENT                                │
│  ⌨️  Keystroke Timing  🖱️  Mouse Dynamics  💻 Process Monitor   │
│              ↓ Feature Vectors Only (Privacy Safe)              │
└──────────────────────────┬──────────────────────────────────────┘
                           │ HTTPS / localhost
┌──────────────────────────▼──────────────────────────────────────┐
│                    AI SERVER (FastAPI)                           │
│   🧠 One-Class SVM  +  🧠 LSTM Autoencoder (Dual Model)         │
│              ↓ Threat Score (0.0 → 1.0)                         │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│                  SOC DASHBOARD (Real-time)                       │
│   ✅ BASELINE  👀 WATCH  ⚠️ SUSPICIOUS  🚨 CRITICAL             │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🎯 What This Project Does

This system creates a **"Computational DNA"** profile for each user — learning *how* they use their computer, not *what* they do. It then uses two AI models to detect when someone's behavior suddenly changes, which can indicate:

- 🔑 **Account takeover** — someone else using stolen credentials
- 😤 **Disgruntled employee** — erratic behavior before a data leak
- 🕵️ **Lateral movement** — attacker moving through the network
- 📤 **Data exfiltration** — unusual file access + network activity

---

## 🏗️ Architecture

### Version 1 — Basic (Local Python)
```
agent/collector.py  →  server/main.py  →  dashboard/index.html
    pynput               FastAPI              Vanilla HTML/JS
    psutil               One-Class SVM        Canvas Charts
    numpy                SQLite               REST Polling
```

### Version 2 — Production (Docker + LSTM)
```
docker-compose up  →  Everything starts automatically
    threat-server        FastAPI + OCSVM + LSTM
    threat-simulator     3 demo users with different profiles
    Persistent volumes   Models survive restarts
    Health checks        Auto-restart on failure
```

---

## 🧠 AI Models Explained

### Model 1: One-Class SVM (Fast Scoring)
```python
OneClassSVM(kernel='rbf', nu=0.08, gamma='scale')
```
- **What it does:** Learns the boundary of "normal" behavior
- **Speed:** Scores in <1ms per sample
- **Best for:** Catching sudden, obvious anomalies
- **Limitation:** No memory — treats each snapshot independently

### Model 2: LSTM Autoencoder (Deep Scoring)
```
Input Sequence → Encoder LSTM → Bottleneck (8 units) → Decoder LSTM → Reconstructed Sequence
                                      ↓
                           If reconstruction error HIGH → ANOMALY
```
- **What it does:** Learns the *sequence* of normal behavior over time
- **Speed:** Scores in ~5ms per sequence
- **Best for:** Catching subtle, gradual behavioral drift
- **Advantage:** Understands that "normal" is a sequence, not a snapshot

### Ensemble Scoring
```
Final Score = OCSVM(45%) + LSTM(55%)

Per modality:
  Keystroke  → 30% weight
  Mouse      → 20% weight  
  System     → 50% weight  ← Most reliable signal

Alert Levels:
  0.00 - 0.35  =  ✅ BASELINE    (normal)
  0.35 - 0.52  =  👀 WATCH       (log silently)
  0.52 - 0.72  =  ⚠️  SUSPICIOUS  (SOC review)
  0.72 - 1.00  =  🚨 CRITICAL    (immediate response)
```

---

## 📊 Features Captured

### ⌨️ Keystroke Dynamics
| Feature | Description | Privacy |
|---------|-------------|---------|
| Hold Time (HT) | How long each key is pressed | ✅ Safe — no key content |
| Flight Time (FT) | Gap between key release and next press | ✅ Safe |
| Press-Press Time (PPT) | Time between consecutive presses | ✅ Safe |
| Typing Speed (CPM) | Characters per minute | ✅ Safe |
| Key Class | ALPHA / DIGIT / SPECIAL (never the actual key) | ✅ Safe |

### 🖱️ Mouse Dynamics
| Feature | Description | Privacy |
|---------|-------------|---------|
| Speed | Cursor velocity | ✅ Coordinates discarded |
| Acceleration | Rate of speed change | ✅ Safe |
| Jitter | High-frequency direction changes | ✅ Safe |
| Click Rate | Clicks per second | ✅ Safe |
| Curvature Index | Path efficiency metric | ✅ Safe |

### 💻 System Behavior
| Feature | Risk Weight | Why It Matters |
|---------|-------------|----------------|
| Normal processes | Low | Baseline comparison |
| PowerShell / CMD | Medium | Recon indicator |
| nmap / masscan | High | Network scanning |
| FTP / SCP / rclone | High | Data exfiltration |
| mimikatz / psexec | Critical | Credential theft |

---

## 🔒 Privacy by Design

```
What NEVER gets stored:
  ❌ Actual keystrokes (passwords, messages, documents)
  ❌ Screen coordinates (where your cursor was)
  ❌ File contents (what was in the files you opened)
  ❌ Network packet contents

What DOES get stored:
  ✅ Timing numbers (e.g., "key held for 95ms")
  ✅ Velocity numbers (e.g., "mouse moved at 350px/s")
  ✅ Process names (e.g., "chrome.exe was running")
  ✅ File extensions (e.g., ".xlsx was accessed")
```

**Compliance:** Designed with GDPR Article 25 (Privacy by Design) principles. 90-day automatic data retention enforcement.

---

## 🚀 Quick Start

### Option A — Docker (Recommended)
```bash
git clone https://github.com/YOUR_USERNAME/insider-threat-detection
cd insider-threat-detection/insider-threat-v2
docker-compose up --build
```
Open: http://localhost:8080/dashboard

### Option B — Local Python
```bash
git clone https://github.com/YOUR_USERNAME/insider-threat-detection
cd insider-threat-detection/insider-threat

# Windows
SETUP.bat
START_SERVER.bat    # Window 1
START_AGENT.bat     # Window 2

# Then open dashboard/index.html in browser
```

---

## 📁 Project Structure

```
insider-threat-detection/
│
├── insider-threat/              ← Version 1 (Local Python)
│   ├── agent/
│   │   └── collector.py         ← Keystroke + Mouse + System monitor
│   ├── server/
│   │   └── main.py              ← FastAPI + One-Class SVM
│   ├── dashboard/
│   │   └── index.html           ← SOC Dashboard
│   ├── demo_simulator.py        ← 3-user demo
│   ├── SETUP.bat                ← One-click Windows setup
│   ├── START_SERVER.bat
│   └── START_AGENT.bat
│
└── insider-threat-v2/           ← Version 2 (Docker + LSTM)
    ├── server/
    │   ├── main.py              ← FastAPI + OCSVM + LSTM
    │   └── lstm_model.py        ← LSTM Autoencoder (pure NumPy)
    ├── agent/
    │   └── collector.py         ← Same agent, works with v2
    ├── dashboard/
    │   └── index.html           ← Upgraded SOC Dashboard
    ├── demo_simulator.py        ← Docker-aware simulator
    ├── Dockerfile
    ├── docker-compose.yml
    ├── START_DOCKER.bat
    └── STOP_DOCKER.bat
```

---

## 🎭 Demo Scenarios

The simulator creates 3 users with different behavioral profiles:

```
alice_normal      → Always behaves normally
                    Expected: ✅ BASELINE (0.10 - 0.30)

bob_suspicious    → Starts normal, gradually drifts over 3 minutes
                    Expected: 👀 WATCH → ⚠️ SUSPICIOUS (0.35 - 0.65)

carol_critical    → Normal for 2 minutes to build baseline,
                    then SUDDENLY runs nmap + data exfil tools
                    Expected: ✅ BASELINE → 🚨 CRITICAL (0.75+)
```

---

## 📈 Performance Stats

| Metric | Value |
|--------|-------|
| Samples needed for baseline | 50 per modality |
| Samples needed for LSTM | 80 per modality |
| Scoring latency (OCSVM) | < 1ms |
| Scoring latency (LSTM) | ~5ms |
| Dashboard refresh rate | Every 3 seconds |
| Data send interval (agent) | Every 15 seconds |
| Alert detection time | ~30 seconds after anomaly |
| False positive rate (tunable) | ~8% (nu=0.08 in OCSVM) |
| Data retention | 90 days (configurable) |
| Max users (single server) | ~100 concurrent |

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Agent | Python + pynput | Keystroke/mouse capture |
| Agent | psutil | Process monitoring |
| Server | FastAPI + Uvicorn | REST API backend |
| AI Model 1 | Scikit-learn (OCSVM) | Fast anomaly detection |
| AI Model 2 | NumPy LSTM | Sequence anomaly detection |
| Storage | SQLite | Score + alert persistence |
| Container | Docker + Compose | Deployment orchestration |
| Dashboard | HTML5 + Canvas | Real-time visualization |

---

## 🔧 Configuration

### Tune Alert Thresholds (`server/main.py`)
```python
ALERT_LEVELS = [
    ('CRITICAL',   0.72),   # Lower = more sensitive
    ('SUSPICIOUS', 0.52),
    ('WATCH',      0.35),
    ('BASELINE',   0.0),
]
```

### Tune Modality Weights
```python
MODALITY_WEIGHTS = {
    'ks':  0.30,   # Keystroke importance
    'mc':  0.20,   # Mouse importance
    'sys': 0.50,   # System process importance
}
```

### Change User ID (`agent/collector.py`)
```python
USER_ID = "john_doe"   # Change per machine
SERVER_URL = "http://YOUR_SERVER_IP:8080"
```

---

## 🌐 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | Server health check |
| GET | `/dashboard` | SOC Dashboard UI |
| POST | `/ingest` | Receive agent data |
| GET | `/users` | List all monitored users |
| GET | `/scores/{user_id}` | Score history |
| GET | `/alerts/{user_id}` | Alert history |
| GET | `/status/{user_id}` | Model training status |
| DELETE | `/reset/{user_id}` | Reset user baseline |
| GET | `/docs` | Interactive API docs |

---

## 🏢 Real-World Use Cases

1. **Enterprise SOC** — Monitor 100s of employees, get alerted when someone accesses sensitive files in unusual ways
2. **MSSP (Managed Security)** — Offer behavioral analytics as a service to clients
3. **Privileged Access Monitoring** — Extra scrutiny on admin/root accounts
4. **Insider Threat Programs** — HR + Security collaboration tool
5. **Zero Trust Architecture** — Continuous authentication based on behavior
6. **Compliance** — HIPAA, SOX, PCI-DSS behavioral monitoring requirements
7. **Forensics** — Post-incident timeline reconstruction from behavioral logs

---

## ⚠️ Ethical Guidelines

This system is designed for **authorized enterprise monitoring** with:
- ✅ Employee notification and consent
- ✅ HR and Legal oversight
- ✅ Data minimization (no content capture)
- ✅ Anomaly scoring only (no surveillance)
- ✅ Right to explanation for flagged users

**Never deploy without proper legal authorization and employee disclosure.**

---

## 🗺️ Roadmap

- [ ] Email/Slack alerts for CRITICAL events
- [ ] Multi-server deployment (Kafka + Redis)
- [ ] ELK Stack integration (Elasticsearch + Kibana)
- [ ] Active Directory / LDAP user sync
- [ ] Mobile agent (Android/iOS)
- [ ] TensorFlow LSTM upgrade
- [ ] Federated learning (privacy-preserving multi-org)
- [ ] SOAR integration (auto-response playbooks)

---

## 📄 License

MIT License — Free to use for educational and research purposes.

---

## 👤 Author

Built as part of an CyberSecurity Research project.  
Demonstrates practical application of behavioral biometrics in cybersecurity.

---

*"The most dangerous insider is the one who looks just like everyone else — until they don't."*
