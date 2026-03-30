# Telegram Fitness Agent: AI Personal Coach

A production-grade Telegram fitness agent built with Python, LangGraph, and Groq. This agent functions as a persistent fitness coach that tracks progress, negotiates daily plans, and adapts to user feedback through a multi-engine state machine.

Implements a **Psychology-Based Negotiation Protocol**, a **Recovery & Scaling System**, and **ChromaDB-powered Long-Term Memory** for contextual coaching across sessions.

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────┐
│                  Telegram Bot                    │
│                   (app.py)                       │
├──────────┬──────────┬──────────┬────────────────┤
│ Onboard  │  Daily   │ Negoti-  │   Recovery     │
│ Graph    │  Engine  │ ation    │   Engine       │
│(graph.py)│(daily.py)│(negot.py)│ (recovery.py)  │
├──────────┴──────────┴──────────┴────────────────┤
│              Prompt Templates (prompts.py)        │
├──────────────────┬──────────────────────────────┤
│  Google Sheets   │   ChromaDB Vector Store       │
│  (database.py)   │   (memory.py)                │
│  Structured Data │   Semantic Long-Term Memory   │
└──────────────────┴──────────────────────────────┘
```

- **LangGraph State Machine** — Multi-turn onboarding with structured data extraction.
- **Daily Coaching Engine** — Morning plans, interactive task tracking, evening reflections.
- **Negotiation Engine** — 3-round protocol: Explain → Offer Alternatives → Pull Rank.
- **Recovery Engine** — Detects consistency gaps and triggers Micro-Habit scaling.
- **Google Sheets DB** — Persistent store for user profiles, plans, and message logs.
- **ChromaDB Memory** — Semantic vector search over all past conversations for long-term context recall.

---

## 🧠 Long-Term Memory (ChromaDB)

The bot uses **ChromaDB** with `all-MiniLM-L6-v2` sentence embeddings to solve the context forgetting problem.

**How it works:**
1. Every message (user + assistant) is dual-written to both Google Sheets and ChromaDB.
2. When a user sends a message, ChromaDB performs a **semantic search** across their entire conversation history.
3. The most relevant past messages are injected into the LLM prompt as additional context.

**Example:** If a user mentioned a knee injury on Day 1 and asks for leg exercises on Day 10, the bot automatically recalls the injury context and avoids recommending lunges.

---

## 🔥 Key Features

- **Adaptive Onboarding** — LLM-driven classification to determine Fitness Maturity (Beginner → Advanced).
- **Interactive Task Management** — Plans delivered with inline buttons for real-time progress tracking.
- **Evening Reflection** — Collects difficulty feedback to adjust tomorrow's plan intensity.
- **Smart Streak Handling** — Differentiates between excused misses (sick/work) and habit breaks.
- **Automatic Scaledown** — Scales tasks down to 5-10 minute Micro-Habits after consecutive misses.
- **Semantic Memory** — Remembers user context across sessions using ChromaDB vector search.

---

## 🛠️ Technical Stack

| Component | Technology |
|:---|:---|
| LLM | [Groq](https://groq.com/) — Llama-3.3-70B-Versatile |
| Bot Framework | `python-telegram-bot` v20+ |
| State Orchestration | `LangGraph` |
| Structured Database | Google Sheets API via `gspread` |
| Vector Memory | `ChromaDB` + `sentence-transformers` |
| Runtime | Python 3.11+ |

---

## 📂 Project Structure

```
telegram-fitness-agent-bot/
├── app.py                  # Entry point — Telegram handlers & command routing
├── config.py               # Environment config & client initializations
├── database.py             # Google Sheets abstraction layer (SheetDB)
├── memory.py               # ChromaDB long-term memory (semantic search)
├── graph.py                # LangGraph onboarding state machine
├── prompts.py              # All LLM system prompts & persona templates
├── engines/
│   ├── __init__.py
│   ├── daily.py            # Morning plans, task completion, evening check-in
│   ├── negotiation.py      # 3-round psychology-based plan negotiation
│   └── recovery.py         # Missed days detection & habit scaling
├── requirements.txt
├── Procfile                # Railway/Heroku deployment config
├── runtime.txt             # Python version specification
├── .env.example            # Environment variable template
└── .gitignore
```

---

## 🚀 Installation & Setup

### 1. Prerequisites
- Python 3.11+
- Telegram Bot token from [@BotFather](https://t.me/botfather)
- Groq API Key from [Groq Console](https://console.groq.com/)
- Google Cloud Service Account (JSON key) with Sheets & Drive API enabled

### 2. Google Sheets Configuration
1. Create a new Google Sheet.
2. Enable **Google Sheets API** and **Google Drive API** in Google Cloud Console.
3. Create a **Service Account**, download the JSON key.
4. Share the Google Sheet with the Service Account email as **Editor**.
5. Copy the Google Sheet URL.

### 3. Clone and Install
```bash
git clone https://github.com/PythonicDG/telegram-fitness-agent.git
cd telegram-fitness-agent
pip install -r requirements.txt
```

### 4. Environment Variables
Create a `.env` file in the root directory:
```env
TELEGRAM_BOT_TOKEN="your_telegram_bot_token"
GROQ_API_KEY="your_groq_api_key"
GOOGLE_SHEET_URL="https://docs.google.com/spreadsheets/d/your_sheet_id/"
GOOGLE_SERVICE_ACCOUNT_JSON='{"type": "service_account", ...}'
```

> **Note**: For `GOOGLE_SERVICE_ACCOUNT_JSON`, paste the entire JSON key content as a single-line string.

### 5. Run
```bash
python app.py
```

---

## 🎮 Bot Commands

| Command | Description |
|:---|:---|
| `/start` | Initialize onboarding or resume active session |
| `/plan` | Generate daily mission with interactive task buttons |
| `/checkin` | Evening reflection to evaluate and adjust difficulty |
| `/status` | User dashboard — Goal, Level, Streak, Habits |
| `/resume` | Manual trigger for Recovery Engine after inactivity |
| `/reset` | Wipe all user data (Sheets + ChromaDB) for fresh start |

---

## 🚢 Deployment

Deployment-ready for **Railway**, **Koyeb**, or **Heroku**.

> **Note on ChromaDB**: ChromaDB stores vectors locally in `chroma_db/`. On platforms with ephemeral filesystems (Railway, Heroku), use a **Volume Mount** to persist this directory across deploys.

---

## 📝 License

This project is licensed under the MIT License.
