# 🚀 How I Use LLMs — Complete Lecture Notes & Student Guide

> **Based on Andrej Karpathy's Video:** *"How I use LLMs"*  
> **Course Material / Reference Guide** for Students & Developers  

---

## 📌 Table of Contents
1. [Overview & The LLM Ecosystem](#1-overview--the-llm-ecosystem)
2. [Under the Hood: Tokens & Context Windows](#2-under-the-hood-tokens--context-windows)
3. [The Mental Model: The "1 TB Zip File"](#3-the-mental-model-the-1-tb-zip-file)
4. [Model Selection, Tiers & The "Council of LLMs"](#4-model-selection-tiers--the-council-of-llms)
5. [Thinking Models & Reasoning (RL Post-Training)](#5-thinking-models--reasoning-rl-post-training)
6. [Tool Use & Real-Time Web Browsing](#6-tool-use--real-time-web-browsing)
7. [Deep Research: Automated Synthesis](#7-deep-research-automated-synthesis)
8. [Multimodality & Practical Workflows](#8-multimodality--practical-workflows)
9. [Student Best Practices & Mental Checklist](#9-student-best-practices--mental-checklist)

---

## 1. Overview & The LLM Ecosystem

In this video, Andrej Karpathy transitions from foundational concepts (*how LLMs are trained*) to **practical everyday application**—showing how researchers, developers, and students can effectively integrate LLMs into daily life and technical workflows.

### The 2025 AI Ecosystem
While OpenAI’s ChatGPT was the pioneer in 2022, the ecosystem today consists of a diverse set of frontier models and providers:

* **OpenAI**: ChatGPT (GPT-4o, GPT-4o mini, o1, o3-mini) — *The incumbent & feature-rich standard.*
* **Anthropic**: Claude (Claude 3.5 Sonnet, Haiku) — *Exceptional for writing and coding.*
* **Google**: Gemini (Gemini 2.0 Flash, Gemini 2.0 Pro) — *Multimodal & native Google search integration.*
* **xAI**: Grok (Grok 3) — *Fast reasoning and real-time social context.*
* **Open-Source / Open-Weights**:
  * **Meta**: Llama series (Llama 3).
  * **DeepSeek**: DeepSeek V3 & DeepSeek R1 (China).
  * **Mistral / Le Chat**: European open-weights models (France).

### How to Track LLM Performance
* **LMSYS Chatbot Arena**: Uses human blind A/B tests and ELO ratings (like chess) to evaluate models.
* **SEAL Leaderboards (Scale AI)**: Evaluates models on standardized, expert-curated benchmarks.

---

## 2. Under the Hood: Tokens & Context Windows

When you chat with an LLM, the visual "chat bubbles" are an abstraction. Under the hood, the user and the model are jointly building a **one-dimensional sequence of tokens**.

### What is a Token?
* Text is broken down into small chunks called **tokens** (roughly ~4 characters or 0.75 words in English).
* Tools like **`tiktoken`** visualize how text splits into token IDs.
* A single user message might be 15 tokens; the model's reply might be 19 tokens.

> [!NOTE]
> **Beginner Concept Bridge: Tokens & Special Tags**
> * LLMs do not read words or letters directly—they process numerical token IDs.
> * Behind the scenes, hidden **Control Tokens** tell the model who is speaking (e.g., `<|im_start|>user` or `<|im_start|>assistant`). When you click **"New Chat"**, you wipe the token sequence clean and start fresh at 0 tokens.

```
[Context Window / RAM]
User Token Stream  ──►  <|im_start|>user ... <|im_end|>
Model Token Stream ──►  <|im_start|>assistant ... <|im_end|>
```

### Context Window as Working Memory
The **Context Window** is the maximum number of tokens an LLM can keep in active memory during a conversation.
* **Why keep context short and focused?**
  1. **Distraction / Accuracy Drop**: If a conversation accumulates thousands of irrelevant past tokens, the model can become "distracted," decreasing performance on new tasks.
  2. **Computational Cost & Speed**: Every additional token in the context window increases computation time and latency for generating the next token.

---

## 3. The Mental Model: The "1 TB Zip File"

Karpathy provides a humorous but accurate introduction script for an LLM:

> *"Hi, I'm ChatGPT. I am a 1 TB zip file. My knowledge comes from the internet, which I read in its entirety about 6 months ago, and I only remember vaguely. My winning personality was programmed by human labelers at OpenAI during post-training."*

### Key Components of the LLM Mental Model
1. **Pre-training = Knowledge**: Lossy compression of internet text stored inside neural network parameters. It has a strict **knowledge cutoff** date.
2. **Post-training = Personality / Alignment**: SFT (Supervised Fine-Tuning) and RLHF attach a "smiley face" to the zip file, shaping it into a helpful assistant rather than a raw web text generator.
3. **Pure Next-Token Predictor**: By default, a raw LLM is purely a statistical text generator running on static parameters. It has **no built-in calculator, live internet, or execution engine** unless explicitly connected to external tools.

---

## 4. Model Selection, Tiers & The "Council of LLMs"

### Model Tiers & Pricing
* **Free Tier / Small Models** (e.g., GPT-4o mini, Claude Haiku): Fast and cheap, but more prone to hallucinations and weaker reasoning.
* **Paid Tier / Flagship Models** (e.g., GPT-4o, Claude 3.5 Sonnet, Gemini 2.0 Pro): Highly capable for complex coding, creative writing, and analysis ($20/month).
* **Pro Tier / Frontier Reasoning** (e.g., OpenAI $200/month Pro, o1 Pro Mode): Unlocks extended thinking time and maximum accuracy for deep technical problems.

### Strategy: The "Council of LLMs"
For high-stakes tasks, complex debugging, or creative ideation, Karpathy recommends querying **multiple different frontier models** (GPT-4o, Claude 3.5 Sonnet, Gemini 2.0, Grok 3) with the exact same prompt and comparing their outputs.

---

## 5. Thinking Models & Reasoning (RL Post-Training)

A major breakthrough in AI is the rise of **Thinking / Reasoning Models** (e.g., DeepSeek R1, OpenAI o1/o3-mini, Grok 3 Think Mode).

### How Thinking Models Work
Unlike standard LLMs that generate tokens instantly (System 1 thinking), reasoning models undergo **Stage 3 Reinforcement Learning (RL)** training:
* They generate an internal **"thinking bubble"** before answering.
* They explore alternative hypotheses, backtrack when encountering mistakes, and double-check logical steps.

```
Standard LLM (System 1):   Prompt ──► Instant Token Generation (Fast, Fixed Compute)
Thinking LLM (System 2):   Prompt ──► [Internal Monologue / Tree of Thoughts] ──► Final Answer
```

### Trade-offs: Speed vs. Accuracy
* **Pros**: Significantly higher accuracy on complex math, logic, algorithms, and hard software bug fixes.
* **Cons**: Takes longer (can think for 1 to 5+ minutes) and consumes more tokens.
* **Rule of Thumb**: Use fast, non-thinking models for everyday writing or simple lookups; switch to **Thinking Models** when stuck on complex math, algorithms, or code errors.

---

## 6. Tool Use & Real-Time Web Browsing

Because LLMs are static "zip files" with knowledge cutoffs, they rely on **Tools** to interact with real-time data.

### Real-Time Web Search
When an LLM recognizes a question requires current or niche facts (e.g., *"When is White Lotus S3 released?"* or *"Is the stock market open today?"*):
1. The model emits a special **Search Control Token**.
2. The interface pauses, runs a live search query (e.g., via Bing or Google), fetches top web page contents, and **injects the text directly into the Context Window**.
3. The LLM reads the retrieved web text in its working memory and writes an accurate answer complete with citations.

---

## 7. Deep Research: Automated Synthesis

**Deep Research** combines multi-step web searching with reasoning models to perform extended, autonomous research tasks over 5–20 minutes.

### How Deep Research Operates
1. Accepts a complex research directive (e.g., *"Analyze the human trial evidence, efficacy, and safety concerns of Ca-AKG supplements"*).
2. Asks clarifying questions to narrow down scope.
3. Issues dozens of iterative web searches, reads primary research papers, and synthesizes findings into a multi-page, cited research report.

> [!WARNING]
> **Verification Required**: Deep Research reports are *first drafts*. Students and researchers must click citations and verify primary sources, as models can still misinterpret complex academic papers or hallucinate subtle details.

---

## 8. Multimodality & Practical Workflows

Modern LLMs accept multiple media inputs beyond text:

* **Vision**: Analyzing hand-drawn UI sketches to generate working HTML/JS code, reading charts, or analyzing diagrams.
* **Speech-to-Speech**: Fluid, real-time voice conversations (e.g., ChatGPT Voice Mode).
* **Code & Execution**: Writing and executing Python code (using `matplotlib` for charting or data analysis).

### Common Practical Use Cases
* **Coding & Debugging**: Copy-pasting error traces or math code for bug identification.
* **Writing & Formatting**: Drafting emails, refining tone, summarizing long texts, writing poems/haikus.
* **Ideation & Travel Planning**: Recommending travel itineraries, cities, and activities.
* **Health & Common Queries**: Looking up medication ingredients (e.g., DayQuil vs. NightQuil)—*always cross-check against physical packaging!*

---

## 9. Student Best Practices & Mental Checklist

To get the best results from LLMs, keep this 5-point checklist in mind:

1. 🧹 **Manage Context Window**: Start a **New Chat** whenever you switch topics to keep active memory clean and fast.
2. 🎯 **Select the Right Model**: Don't rely on free/small models for complex assignments; know whether you are using a base model, flagship model, or reasoning model.
3. 🧠 **Use Thinking Models for Hard Problems**: Enable reasoning modes (e.g., DeepSeek R1, o1, Grok Think) for complex math, algorithms, or stubborn code bugs.
4. 🌐 **Leverage Web Search for Current Facts**: Use search-enabled models (e.g., Perplexity or ChatGPT Search) for recent events or niche facts.
5. 🔍 **Verify Primary Sources**: Treat LLM outputs as high-quality drafts—always verify critical facts, code outputs, and medical claims against primary sources!

---
*Created as part of the LLM Educational Series.*
