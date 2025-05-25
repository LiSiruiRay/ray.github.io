---
title: Chatbot 4.x updating logs
date: 2025-04-11 22:14:02
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
# 🚀 Jupiter Chatbot – Version 4.x Dev Log

> **TL;DR**  
Between March 25 and April 11, I worked on launching the Jupiter chatbot into production. This post documents the major updates, improvements, and issues we faced in version 4.x. From a simple chat prompt to a fully autonomous search agent with backend integration and feedback, this journey reflects iterative development, real-world debugging, and agent system design.

---

## 🧪 v4.0 (March 25) – Basic Chat Agent

- Launched a simple agent to help users search for medical services.
- The agent had:
  - A single prompt for helping people find services.
  - Access to a **CPT-based knowledge base**.
  - Access to a **backend API** that searched for services in our database.

### ❌ Problems:
- Lacked logic to handle more nuanced or vague user queries.
- Failed to find very common services like **primary care**.
- Could not prompt the user for more clarification if the search was too vague.

---

## 🤖 v4.3 (March 29) – Adding Structure + Semantic Search

- Added logic to distinguish between types of care:
  - **Primary care**, **Urgent care**, **Emergency care**, and **Medical services**.
- Designed and implemented:
  - **Semantic search** for natural language input using vector embeddings.
  - **Keyword formatting** via **Algolia** to ensure accurate service name matching.
  - A more advanced flow that could **prompt the user** when their input was too vague.
- Introduced a multi-step reasoning process where the bot could **narrow down search results**.

### ❌ Problems:
- The chat tone was too formal and lacked personality.
  - We wanted a **friendlier tone with emojis** for a more approachable experience.
- Misclassified care levels:
  - Couldn't always distinguish primary vs urgent vs emergency.
  - **Concierge practices** were misinterpreted.
- Search accuracy was still inconsistent:
  - Even if a service existed in the database, the bot sometimes failed to retrieve it.

---

## 🧭 v4.3.1 – Refined Chat Flow and Classification

- Rewrote the system prompt to **restrict the chat flow** and align it more closely with our ideal script:
  - Greet user → Guess service → Ask for location → Redirect to results
- Improved classification by:
  - Giving **examples and criteria** to help users understand what counts as ER, urgent, or primary care.
  - Implemented a **priority system** that checked:
    1. Emergency care
    2. Urgent care
    3. Primary care
    4. Other medical services
- Fixed concierge classification by:
  - **Restricting keywords** that led to misclassification.
  - Ensuring Algolia uses **minimum keywords** to generate the correct formatted search keys, even if input was slightly off.

### ❌ Problem:
- We decided that **Algolia was not sufficient** going forward.
  - We needed a **backend search system** that supported fuzzy keyword matching, not just exact service names.
  - The result of this search should also be usable by the bot to guide further decisions.

---

## ☁️ Midway: Migration to Dify Cloud

- Migrated the chatbot from a **self-hosted server** to the **Dify.ai cloud service** for better production readiness.
- While migrating, I:
  - **Contributed to the Dify GitHub repo** by reporting bugs and suggesting solutions.
  - Debugged multiple cloud-related issues.

### 🐛 Problems Encountered:
1. **Frontend rendering failed** after deploying to Dify Cloud.
2. Could not create a knowledge base — likely due to Dify’s handling of **repeated documents**.

### 🛠 Fix:
- As a backup plan, I explored using **external APIs** for file storage and search.
- Ultimately fixed the issue by **splitting large files into smaller documents** to avoid duplication conflicts.

---

## 📝 v4.3.1.1 – UX Copy Update

- Updated the **"no result" response wording** in the chatbot.
- Matched the tone and style of the **search bar experience** to maintain consistency between chatbot and UI search.

---

## 🧠 v4.4 (April 11) – Full Production Launch 🎉

- Major milestone: **Backend search API** completed.
- Vectorized all service descriptions in our database using **PostgreSQL + embeddings**.
- Implemented **service sampling** logic:
  - The bot can now match a keyword input to the correct service name and **CPT code**.
- Fully **replaced Algolia** with the new backend API.
- Deployed to production with:
  - **Interactive result cards** shown in the chat.
  - A **feedback system** (👍 / 👎) to track user satisfaction.

### ❌ Known Issues:
- The chatbot **prompt is too long**, causing **fast memory loss** in the LLM.
- Needs **keyword association logic**:
  - Input like “lower back” should match “lumbar” services.
- Still misjudges some valid medical questions:
  - For example, “What are the different prices for a knee replacement?” may be rejected as not valid.
- **Inconsistent return styles**:
  - Sometimes the bot gives a link to results.
  - Other times, it explains the results inside the chat.
- Occasionally **repeats itself**.

### 🔧 Planned Improvements:
- Add support for **prescription and drug-related services**.
- Shorten and optimize the prompt for **memory preservation**.
- Implement **concept linking** and synonym matching.
- Standardize return methods for consistent UX.

---

## 🏁 Final Thoughts

Over the course of version 4.x, we’ve evolved from:
- A one-prompt agent → to a **multi-branching flow** → to an **autonomous search agent**.
- Keyword search → to **semantic understanding** + structured decision-making.
- Self-hosted infra → to a **cloud-native**, production-ready platform.

More to come!
