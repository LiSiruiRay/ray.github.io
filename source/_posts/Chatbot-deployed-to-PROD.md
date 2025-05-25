---
title: Chatbot deployed to PROD
date: 2025-04-11 21:19:37
tags:
  [
    Intern,
    Intern Log,
    LLM,
    Learning Log,
    Tech,
    ChatBot
  ]
categories:
  - Daily Logs
  - Intern Log
  - Tech Summary
---


## 🚀 Launching the Jupiter Chatbot: From MVP to Production

### 🧭 Introduction
- **When I started**: February 21st, 2025
- **Launch date**: April 11th, 2025
- **Company**: [Jupiter Plans](https://www.jupiterplans.com) — focused on direct-pay healthcare access
- **Goal**: Help users naturally and interactively search for medical services based on what they say, not just keywords.

---

### 🧩 Problem We’re Solving
- **Before**: Users were stuck with rigid search bars that required exact names or CPT codes.
- **Now**: They can describe their issue in plain language, and the chatbot guides them step-by-step to the right medical service.
- **Example use case**: A user types “lower back pain” → the bot deduces it could be “MRI Lumbar Spine” and guides them to results, even if that term was never explicitly used.

---

### 🛠️ Technical Evolution (Timeline Style)
#### 1. **Initial Prototype: Diagnosis-Style Bot**
- Focus: symptom-based question flow
- Used **RAG on CPT database** only
- Powered by **OpenAI + RAG**
- Search was mostly **backend + textual**

#### 2. **Second Iteration: Manually Designed Chatflow**
- Introduced **chat nodes** like:
  - `situation_decider`
  - `cpt_handler`
  - `enough_info_decider`
- Used **Dify chatflow builder**
- Integrated OpenAI’s **structured outputs** + **RESTful APIs**
- Deployed via **Docker on internal server**
- Much clearer user flow, but **manually maintained logic trees** were hard to scale.

#### 3. **Current Production Version: Agent + Function Calling**
For detailed updaing logs, please refer to [here](/2025/04/11/Chatbot-4-x-updating-logs/)

- Shifted to **OpenAI function calling** and **agent autonomy**
- Replaced manual chatflow logic with agent-chosen branches
- **Integrated backend database API + semantic search**
- Added **frontend interactivity**: conversational results now appear as **search cards** during chat
- Benefits:
  - 📈 Development speed ↑
  - 🧠 Autonomy ↑
  - 💥 Stability ↑ 80%
  - 💡 Search **experience** now *feels* intelligent

---

### 📚 Technical Stack
| Area         | Stack / Tool                                  |
|--------------|-----------------------------------------------|
| Backend      | Flask + FastAPI + REST API                    |
| LLM          | OpenAI GPT w/ function calling + RAG          |
| Orchestration| Dify , Agent code                             |
| Data Layer   | CPT Knowledge Base + Structured DB            |
| Infra        | Docker + Own Server, Now dify.ai              |
| Frontend     | Integrated chat UI with card-style result view|

---

### 🔁 Sample Chat Flow
> ✅ Shows service guessing, multi-option narrowing, location-based search, and search results delivery in one smooth flow

```text
User: “I need help with my back”
Bot: “Great. Sounds like you’re looking for **MRI Lumbar Spine**. We have both virtual and in-person versions of this service in our network. What is your location? 🗺”
User: “New York, NY”
Bot: “We have services for you! 😊 We will redirect you to the search results page.”
```

---

### 🧠 Key Design Decisions
- Switched from **manual node logic** to **autonomous agent** — greatly improved maintainability
- Combined **semantic search** + **structured search API** to improve accuracy
- Used **structured output** from OpenAI to standardize responses and UI components
- Unified **search bar and chatbot results** for consistency in frontend

---

### 🧪 Lessons Learned
- 🤯 OpenAI can hallucinate if function descriptions aren't clear — structuring matters
- 📏 Shorter, tighter responses work better — too much explanation = user fatigue
- 💡 Having fallback strategies (e.g., if service not found, show same message as search page) prevents dead ends
- 🧵 Iterative feedback helped shape design — fixing verbose or awkward outputs based on real usage

---

### 📈 Future Improvements
- Fine-tune response length and tone further
- Handle edge cases like CPT code inputs more gracefully
- Expand backend service coverage + improve clustering accuracy
- Improve hallucination resistance on multi-service classification

<!-- 
---
### 💼 For Interviews: What This Project Shows
- **System design**: You designed and evolved an end-to-end pipeline (RAG → agent → API + DB)
- **LLM Integration**: You worked deeply with function calling, structured output, and chat agents
- **Frontend + Backend coordination**: You made results show up in a user-friendly way within a chat
- **Product thinking**: You responded to feedback and launched iteratively with real user experience in mind
- **Deployment**: Dockerized and managed your own infra -->
