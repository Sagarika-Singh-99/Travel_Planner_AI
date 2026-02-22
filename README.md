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
