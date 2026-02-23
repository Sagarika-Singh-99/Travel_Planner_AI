# ✈️ AI Travel Planner — Project Hub

A full-stack AI-powered travel planning app that generates personalised, 
streaming itineraries based on your destination, travel style, and trip length.
Just enter where you want to go — the AI researches, plans, and writes your 
entire trip in real time.

---

## 🌟 What This App Does

The AI Travel Planner takes three simple inputs — a destination, number of days, 
and a travel vibe — and generates a complete, personalised travel itinerary using 
a LangGraph ReAct agent powered by GPT-4o-mini.

The AI doesn't just generate text — it actively:
- Fetches **real weather forecasts** for your travel dates
- Estimates a **realistic budget** based on destination and travel style
- **Searches the web** for up-to-date attractions, restaurants, and travel tips
- Streams the entire response **token by token** in real time, just like ChatGPT

---

## ✨ Features

- 🌍 **Destination input** — any city or country in the world
- 📅 **Days slider** — plan trips from 1 to 30 days
- 🎭 **Travel vibe selector** — Adventure, Relaxation, Culture, Food & Drink, Budget, Luxury
- ⚡ **Live token streaming** — response streams in real time via SSE
- 🌤️ **Weather forecast** — real weather data built into the itinerary
- 💰 **Budget estimation** — daily and total cost breakdown
- 🗺️ **Day-by-day itinerary** — structured morning, afternoon, evening plans
- 💡 **Travel tips** — practical advice on transport, safety, and culture
- 🖼️ **Dynamic background** — destination photo from Pixabay loads automatically
- 🎨 **Colour-coded section cards** — each section (Weather, Budget, Days, Tips) has its own theme
- 📄 **Download PDF** — save your itinerary as a formatted PDF
- 📋 **Copy itinerary** — one-click copy to clipboard
- 📊 **Progress bar** — animated progress with rotating status messages while AI thinks
- 🔗 **Share App** — copy the app link to share with friends

---
## ⚖️ How Is This Different from ChatGPT or Perplexity?

Most people think this is just "ChatGPT with a travel prompt." Here's why it's meaningfully different:

| Feature | 🧠 This App | 💬 ChatGPT |
|---------|------------|-----------|
| **Purpose** | Built specifically for travel planning | General-purpose AI assistant | 
| **Real-time web search** | ✅ Always — via Tavily API | ⚠️ Only in paid plans | 
| **Live weather data** | ✅ Fetched via dedicated weather tool | ❌ Guesses or redirects you | 
| **Budget estimation** | ✅ Calculated based on destination + vibe | ❌ Generic estimates from training data |
| **Uses AI agent with tools** | ✅ LangGraph ReAct agent actively calls tools | ❌ Single prompt, search only, no tool loop |
| **Structured output format** | ✅ Always — Weather, Budget, Days, Tips cards | ❌ Varies every time | 
| **PDF download** | ✅ Built in | ❌ Not available | 

**In short:** ChatGPT and Perplexity generate travel plans from memory or search. This app **researches your trip in real time** using live tools, then writes a structured itinerary — more like a travel agent than a chatbot.


---

## 🔄 How It Works — Step by Step

This app connects 3 separate services together. Here's exactly what happens from the moment you hit **Plan My Trip** to the itinerary appearing on your screen.

---

- Step 1 - You Fill the Form: You open the app, type a destination, pick the number of days, and a travel vibe, 
and hit submit. The browser packages your inputs into a small JSON object.

```json
{ "destination": "Tokyo", "days": 5, "vibe": "adventure" }
```

File involved: `ai-travel-planner` → `src/components/TripForm.tsx`

- Step 2 — React Sends the Request: `TripForm.tsx` sends a `POST` request to the Node middleware. React never talks to the AI backend directly — the middleware sits in between
to keep backend URLs and API keys hidden from the browser.

```
Browser → POST /api/plan-trip → Node Middleware
```

File involved: `ai-travel-planner` → `src/components/TripForm.tsx`


- Step 3 — Middleware Forwards to FastAPI: The Express server receives the request, validates that the fields are present,
logs the trip details, and forwards the exact same JSON body to the FastAPI backend.

```
Node Middleware (index.ts) → POST → FastAPI (main.py)
```

File involved: `ai-travel-middleware` → `src/index.ts`

- Step 4 — FastAPI Validates and Calls the Agent: FastAPI uses a Pydantic model (`TripRequest`) to validate the incoming data,
then calls `stream_agent()` from `agent.py` with the destination, days, and vibe.

File involved: `ai-travel-backend` → `main.py`

- Step 5 — The LangGraph ReAct Agent Runs: This is the AI brain of the app. `agent.py` builds a structured prompt from your
inputs, then the LangGraph ReAct agent starts its **Think → Act → Observe → Respond** loop:

| Step | What happens |
|------|-------------|
| 🤔 **Think** | Agent decides it needs real data before writing — weather, budget, web results |
| 🔧 **Act** | Agent calls 3 tools: `weather_tool`, `budget_tool`, `TavilySearch` |
| 👀 **Observe** | Agent reads all tool results back into its context |
| ✍️ **Respond** | Agent sends the full context to GPT-4o-mini to write the itinerary |

Files involved: `ai-travel-backend` → `agent.py`, `weather_tool.py`, `budget_tool.py`

- Step 6 — The 3 Tools Gather Real Data:

| Tool | File | What it fetches |
|------|------|----------------|
| `weather_tool` | `weather_tool.py` | Real weather forecast for the destination |
| `budget_tool` | `budget_tool.py` | Daily and total cost estimates based on vibe |
| `TavilySearch` | External API | Live web results — attractions, food, travel tips |

All 3 results are passed back to the agent before a single word of the itinerary is written.

Files involved: `ai-travel-backend` → `weather_tool.py`, `budget_tool.py`

- Step 7 — GPT-4o-mini Writes and Streams the Itinerary:
The agent sends the prompt + all tool results to **OpenAI's GPT-4o-mini**.
The model generates the itinerary and streams each **token (word fragment)** 
back the moment it's produced — no waiting for the full response to finish.

```
GPT-4o-mini → token → agent.py → main.py → index.ts → TripForm.tsx → your screen
```

React appends each token to the screen as it arrives — this is why the itinerary
types itself out live, just like ChatGPT.

Files involved: `ai-travel-backend` → `agent.py` → `main.py`  
Protocol used: SSE (Server-Sent Events)

---

### Full Data Flow Summary

```
👤 You (Browser)
    ↓  JSON: { destination, days, vibe }
⚛️  TripForm.tsx          [ai-travel-planner  → Vercel]
    ↓  POST /api/plan-trip
🔀  index.ts              [ai-travel-middleware → Render]
    ↓  Forwards POST to FastAPI
⚡  main.py               [ai-travel-backend   → Render]
    ↓  stream_agent()
🧠  agent.py              [LangGraph ReAct Agent]
    ↓  tool calls
    ├── 🌤️  weather_tool.py   → real forecast
    ├── 💰  budget_tool.py    → cost estimate
    └── 🔍  Tavily API        → live web search
    ↓  all results fed into prompt
✨  GPT-4o-mini           [OpenAI API]
    ↓  streams tokens back (SSE)
🖥️  Your screen           [itinerary appears live]
```

---

## 🐳 Docker & Deployment

Each of the 3 services is deployed independently. Here's how each one runs in production.

---

### ⚛️ Frontend — Vercel

The React app is deployed on **Vercel**, which is optimised for frontend frameworks like React and Vite.

**How it works:**
1. You push code to the `ai-travel-planner` GitHub repo
2. Vercel detects the push automatically via a GitHub integration
3. It runs `npm run build` — Vite compiles your TypeScript + React into static HTML, CSS, and JS files
4. Vercel distributes those files across its global **CDN (Content Delivery Network)**
5. Anyone who visits the app URL gets the files served from the nearest server to them

No Docker needed here — Vercel handles the build environment automatically.

---

### 🔀 Middleware & ⚡ Backend — Render

Both the Node middleware and the FastAPI backend are deployed on **Render**, 
which runs them as always-on servers.

**How it works:**
1. You push code to GitHub
2. Render detects the push and starts a new **Docker container** build
3. Render auto-detects the language (Node.js or Python) and builds the container for you
4. The container installs all dependencies (`npm install` or `pip install -r requirements.txt`)
5. Render runs the start command (`npm run start` or `uvicorn main:app`)
6. The server stays live 24/7, automatically restarting if it ever crashes

**What is a Docker container?**
Think of it as a **sealed mini computer** inside Render's servers.
It has its own copy of Node.js or Python, your exact dependency versions,
and your code — completely isolated from everything else running on the same machine.
This means your app behaves identically in development and in production.

---

### Want more control? Add your own Dockerfile

Render uses auto-detection by default, but if you add a `Dockerfile` to your repo,
Render will use it instead. Example for the FastAPI backend:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

### Environment Variables

Secret keys are **never committed to GitHub**. They are set directly in the 
Vercel and Render dashboards:

| Service | Variable | Where to get it |
|---------|----------|----------------|
| Render (Backend) | `OPENAI_API_KEY` | [platform.openai.com](https://platform.openai.com) |
| Render (Backend) | `TAVILY_API_KEY` | [app.tavily.com](https://app.tavily.com) |
| Render (Middleware) | `FASTAPI_URL` | Your Render backend URL |
| Vercel (Frontend) | `VITE_API_BASE` | Your Render middleware URL |

---

### Deployment Summary

| Repo | Platform | Trigger | Runtime |
|------|----------|---------|---------|
| `ai-travel-planner` | Vercel | Push to `main` | Static CDN |
| `ai-travel-middleware` | Render | Push to `main` | Node.js Docker container |
| `ai-travel-backend` | Render | Push to `main` | Python Docker container |



---

## 🔗 Links

| | Link |
|---|---|
| 🌐 **Live App** | [https://ai-travel-planner-snowy.vercel.app/](https://ai-travel-planner-snowy.vercel.app/) |
| 🖥️ **Frontend Repo** | [ai-travel-planner](https://github.com/Sagarika-Singh-99/ai-travel-planner) |
| 🔀 **Middleware Repo** | [ai-travel-middleware](https://github.com/Sagarika-Singh-99/ai-travel-middleware) |
| 🤖 **Backend Repo** | [ai-travel-backend](https://github.com/Sagarika-Singh-99/ai-travel-backend) |

---

## 🛠️ Tech Stack

| Layer | Technology | Deployed On |
|-------|-----------|-------------|
| Frontend | React 18 + Vite + TypeScript | Vercel |
| Middleware | Node.js + Express + TypeScript | Render |
| Backend | Python + FastAPI + LangGraph | Render |
| AI Model | GPT-4o-mini (OpenAI) | OpenAI API |

---

## 📦 Libraries Used

### Frontend
| Library | Purpose |
|---------|---------|
| React 18 | UI framework |
| Vite | Build tool + dev server |
| TypeScript | Type safety |
| jsPDF | Itinerary PDF generation |
| Pixabay API | Dynamic destination background images |

### Middleware
| Library | Purpose |
|---------|---------|
| Express | HTTP server + SSE proxy routing |
| TypeScript | Type safety |
| dotenv | Environment variable management |

### Backend
| Library | Purpose |
|---------|---------|
| FastAPI | Python API framework + streaming responses |
| LangGraph | ReAct agent orchestration |
| LangChain OpenAI | GPT-4o-mini LLM integration |
| LangChain Tavily | Real-time web search tool |
| Pydantic | Request body validation |
| Uvicorn | ASGI server for FastAPI |
| python-dotenv | Environment variable management |

---

## 🔧 Tools & Services Used

| Tool / Service | What It's Used For |
|---------------|-------------------|
| **OpenAI GPT-4o-mini** | Core LLM that generates the travel itinerary |
| **Tavily Search API** | Gives the AI real-time web search capability for attractions, food, and tips |
| **Pixabay API** | Fetches a high-quality destination photo for the dynamic background |
| **LangGraph** | Orchestrates the ReAct agent loop — think, call tool, observe, respond |
| **Vercel** | Hosts and deploys the React frontend with automatic GitHub deploys |
| **Render** | Hosts and deploys both the Node middleware and FastAPI backend |
| **GitHub** | Version control and source code hosting for all 3 repos |
| **jsPDF** | Generates a downloadable PDF of the itinerary on the client side |

---

*Built by [Sagarika Singh](https://www.linkedin.com/in/sagarika-singh-938aa11bb/)*
