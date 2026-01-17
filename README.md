# 🌐 GENEYE: AI-Driven Social Optics for Positive Feed

**GENEYE** is a next-generation Chrome extension that empowers users to curate a **positive and mindful social media experience** by leveraging **AI and NLP-driven content filtering**. It analyzes posts in real-time, detects negativity, hate speech, toxicity, and spam, and helps users customize their feeds for a healthier digital environment.

---

## 🚀 Why GENEYE?

Social media is often filled with negativity, spam, and toxic content.  
GENEYE acts as your **AI-powered digital lens** — filtering harmful or irrelevant content while amplifying meaningful conversations.  

Key highlights:  
- **Real-time AI/NLP detection**  
- **Customizable filtering categories** (e.g., political, sarcastic, rumours/false claims)  
- **Privacy-first design** (no external data storage)  
- **Personalized feed shaping** through adaptive learning  

---

## ✨ Features

- 🔍 **AI-Powered Real-Time Filtering**: Detects toxic, cynical, hateful, political, and spam content.
- ⚙️ **Customizable User Controls**: Adjustable sensitivity sliders and category toggles for fine-grained control.
- 🔒 **Privacy-Focused**: No personal data stored or shared; all settings saved locally.
- 📊 **Analytics Dashboard**: Visualize the type and frequency of filtered content.
- 🖥️ **Cross-Platform Vision**: Built for Twitter/X first, with plans to expand to LinkedIn, Facebook, and Reddit.
- 🎨 **Clean & Intuitive Design**: A polished UI for seamless interaction.

## 🧐 Main Focus on:- 

- Real-time AI Analysis: Analyzes posts using Llama 3.3 70B via OpenRouter API.
- Multi-dimensional Classification: Scores content across 7 classification categories (0-1 scale).
- Configurable Thresholds: Toggle individual filters based on research requirements.
- Blur Mode: Option to blur instead of hide flagged content.
- Visual Indicators: Clear labeling of filtered content types.
- 24-hour Caching: Reduces API usage with intelligent caching.
- Statistics Dashboard: Track classification results and filter effectiveness.
- Debug Mode: Detailed logging for troubleshooting and research analysis.
---

## 🏗️ System Architecture

The project follows a **modular and scalable design**:

GENEYE follows a **modular, MV3 (Manifest V3)** browser-extension architecture with clear separation of UI, page integration, and AI decisioning.

Here’s the **detailed architecture diagram**:

![GENEYE Architecture](/Geneye_Methodology_Mermaid_Diagram.png)


### 1) High-Level Components

- **Popup UI (Frontend)**  
  - Settings (API key, thresholds, category toggles)  
  - Quick on/off control, analytics snapshot  
  - Persists preferences in `chrome.storage`

- **Content Script (Page Layer)**  
  - Parses DOM to extract post text  
  - Sends analysis requests  
  - Applies actions (hide/blur/replace + rationale tooltip)

- **Service Worker (Background)**  
  - Orchestrates AI/NLP calls  
  - Normalizes model responses into category scores  
  - Enforces rules & returns decisions to content script

- **AI/NLP Engine (Hybrid)**  
  - **LLM Scoring (via API)**: Rich classification (toxicity/cynicism/hate/spam)  
  - **Local Models (optional)**: Lightweight toxicity heuristic for quick triage  
  - **Rule Layer**: Thresholding, overrides, user feedback adaptation

- **Storage**  
  - `chrome.storage` for settings, thresholds, flags  
  - Optional local cache for recent post hashes & scores

### 2) Data Flow Diagram (Mermaid)

flowchart LR
  A[DOM on Social Site] --> B[Content Script: extract text]
  B --> C{Local Heuristic?}
  C -- Low risk --> D[Apply UI directly]
  C -- Borderline/High --> E[Service Worker]
  E --> F[AI/NLP Engine]
  F --> E
  E --> G{Decision}
  G -- Hide/Blur --> H[Content Script UI: shield/blur]
  G -- Allow --> D
  I[Popup UI] -->|Update thresholds / toggles| E
  I -->|Save settings| J[chrome.storage]
  E -->|Read/Write|

  ### 3) Request–Response Sequence
  sequenceDiagram
  participant User
  participant Page/DOM
  participant ContentScript
  participant ServiceWorker
  participant AI/NLP

  User->>Page/DOM: Scrolls feed
  ContentScript->>Page/DOM: Find posts & extract text
  ContentScript->>ServiceWorker: analyze(text, context)
  ServiceWorker->>AI/NLP: score(text) {toxicity,cynicism,hate,spam}
  AI/NLP-->>ServiceWorker: scores + rationale
  ServiceWorker->>ServiceWorker: apply thresholds/rules
  ServiceWorker-->>ContentScript: decision {hide/blur/allow, reason}
  ContentScript->>Page/DOM: apply overlay/blur/tooltip
  User->>Popup UI: adjusts sliders/toggles
  Popup UI->>ServiceWorker: update config
  ServiceWorker->>chrome.storage: persist settings

 ### 4) Folder Structure (Reference)

 geneye/
├─ src/
│  ├─ manifest.json          # MV3 config
│  ├─ popup/
│  │  ├─ index.html
│  │  ├─ popup.js
│  │  └─ popup.css
│  ├─ content/
│  │  ├─ content.js          # DOM parsing + UI actions
│  │  └─ overlay.css
│  ├─ background/
│  │  └─ service-worker.js   # AI orchestration, rules, storage
│  ├─ ai/
│  │  ├─ prompt-templates.js # LLM prompts & schema guards
│  │  ├─ router.js           # Model routing (LLM vs local)
│  │  └─ heuristics.js       # Lightweight local checks
│  └─ utils/
│     ├─ storage.js          # chrome.storage helpers
│     └─ constants.js        # categories, defaults, schemas
└─ assets/
   ├─ icons/
   └─ branding/


---

## 🤖 AI & NLP Workflow

1. **Text Extraction:** The content script identifies posts and extracts their text in real-time.
2. **Preprocessing:** Text is cleaned (stopwords, emojis, and noise removed) before AI processing.
3. **AI Scoring:**  
   - **LLMs (Llama/GPT)** via OpenRouter API assign toxicity, cynicism, and sentiment scores.  
   - **NLP Classifiers** (e.g., BERT, DistilBERT) handle spam and hate speech detection.  
4. **Rule-Based Filtering:** Posts exceeding configured thresholds are flagged for removal or blur.
5. **User Feedback Integration:** User input improves thresholds dynamically for smarter filtering.
6. **Dashboard Analytics:** A breakdown of filtered content by category helps users understand trends.

---

## 🛠️ Tech Stack & Tools

| Category             | Tools & Technologies                            |
|----------------------|-------------------------------------------------|
| **Browser Extension**| Chrome Extensions (Manifest v3), JavaScript, HTML, CSS |
| **Frontend UI**      | Tailwind CSS, Vanilla JS                        |
| **AI/NLP Models**    | Llama, GPT, BERT-based toxicity models          |
| **APIs**             | OpenRouter API for LLM-based text analysis     |
| **Visualization**    | Chart.js, D3.js                                |
| **Storage**          | Chrome Storage API (local, sync)               |
| **Version Control**  | GitHub                                          |

---

## 🔄 Workflow Overview

1. **User Opens Social Media:** GENEYE’s content script scans the page dynamically.
2. **Text Sent for AI Analysis:** Posts are scored for sentiment, toxicity, cynicism, and spam probability.
3. **Real-Time Filtering:** Posts crossing thresholds are blurred, hidden, or replaced.
4. **User Adjusts Filters:** Via the extension’s dashboard, thresholds and categories can be fine-tuned.
5. **Feedback Improves AI:** User input helps refine scoring and build a personalized filtering system.

---

## 🌟 Future Enhancements

- 🌐 Multi-platform support for Facebook, LinkedIn, Reddit.
- 📲 Mobile browser support.
- ⚡ Hybrid AI: Combine lightweight offline models with API-based LLMs to optimize performance.
- 🔔 Real-time notifications for harmful or trending content.
- 🧠 Advanced analytics with sentiment heatmaps.

---

## 👨‍💻 Creator

| Name                     |
|--------------------------|
| **KARTIK PANDEY**        |

---
## ✍️📄 Research Paper


Check Out the Research Paper ![GENEYE Research Paper](GENEYE_RESEARCH_PAPER.pdf) 


---

## 📜 License

This project is licensed under the **MIT License** – feel free to use, modify, and contribute.

---

## 🙌 Contributing

We welcome contributions!  
- Fork the repo  
- Create a new branch (`feature/your-feature`)  
- Commit and push your changes  
- Open a pull request  

For any Further Queries, Please Contact at My E-Mail :-  {"kartik0pandey00@gmail.com"}


Together, let’s make the internet a **healthier, more positive space!**



**Thank you**

---

































