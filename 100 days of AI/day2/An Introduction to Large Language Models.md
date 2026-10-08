# 📚 Intro to Large Language Models: Lecture Notes & Study Guide

Welcome to the comprehensive study guide and lecture notes for **Intro to Large Language Models**, based on Andrej Karpathy's 1-hour introductory talk. 

This repository and document are designed for students and self-learners stepping into Natural Language Processing (NLP) and Artificial Intelligence. It breaks down complex technical terms into digestible concepts with dedicated **Beginner Concept Bridges**.

---

## 📋 Table of Contents
1. [Module 1: What is a Large Language Model (LLM)?](#module-1-what-is-a-large-language-model-llm)
2. [Module 2: Stage 1 — Pre-training (Base Models)](#module-2-stage-1--pre-training-base-models)
3. [Module 3: Architecture & Interpretability](#module-3-architecture--interpretability)
4. [Module 4: Stage 2 & 3 — Fine-Tuning & Alignment](#module-4-stage-2--3--fine-tuning--alignment)
5. [Module 5: LLM Ecosystem & Leaderboards](#module-5-llm-ecosystem--leaderboards)
6. [Module 6: Scaling Laws](#module-6-scaling-laws)
7. [Module 7: Capabilities & Tool Integration](#module-7-capabilities--tool-integration)
8. [Module 8: Multimodality](#module-8-multimodality)
9. [Module 9: Frontiers — System 2, Self-Improvement & LLM OS](#module-9-frontiers--system-2-self-improvement--llm-os)
10. [Module 10: Security & Vulnerabilities](#module-10-security--vulnerabilities)
11. [Quick Concept Cheat Sheet](#quick-concept-cheat-sheet)

---

## Module 1: What is a Large Language Model (LLM)?

* **The Two-File Analogy**: At its core, a runnable LLM package consists of just **two files** on a computer:
  1. **Parameters File**: The neural network weights or "memory". For Meta's **Llama 2 70B**, there are 70 billion parameters. Stored as 16-bit floating-point numbers (**Float16**, 2 bytes per number), this file takes up **140 GB**.
  2. **Run File**: A lightweight program that executes the neural network's forward pass architecture. It can be written in **~500 lines of plain C code** without external software dependencies.
* **Local Execution vs. Closed APIs**:
  * **Open-Weights Models** (e.g., Llama 2): Meta released the weights, architecture, and research paper, allowing anyone to download and run the model offline on a local machine (such as a MacBook) without internet access.
  * **Proprietary Models** (e.g., OpenAI's ChatGPT/GPT-4, Anthropic's Claude): Kept behind web interfaces and commercial APIs; users cannot download or inspect the underlying parameter weights.
* **Inference vs. Training**: Running text generation (**inference**) is computationally cheap and can run on consumer hardware. Obtaining the parameters (**training**) is a massive computational endeavor requiring GPU clusters.

> [!NOTE]
> 💡 **Beginner Concept Bridge: Parameters & Float16**
> * **What is a Parameter / Weight?** Think of a neural network as a giant mathematical function with billions of adjustable dial settings (weights). During training, the computer adjusts these dials until the network accurately outputs correct answers.
> * **What is Float16?** Computer numbers can be stored with different levels of precision. "Float16" (16-bit floating point) uses 2 bytes of storage per number, balancing memory efficiency with calculation precision.

---

## Module 2: Stage 1 — Pre-training (Base Models)

* **LLMs as Lossy Compression**: Pre-training compresses a massive portion of the internet (roughly **10 Terabytes** of text from web crawls) into a 140 GB parameter file. This achieves a **~100x lossy compression ratio**.
* **Compute & Hardware Scale**:
  * Training **Llama 2 70B** required a cluster of **~6,000 specialized GPUs** running continuously for **~12 days**, costing approximately **$2 million**.
  * Modern frontier models require 10x+ greater compute and data, costing tens or hundreds of millions of dollars.
* **The Objective: Next-Word Prediction**:
  * The network receives a sequence of words (e.g., *"The cat sat on a..."*) and predicts the probability distribution for the next word (e.g., *"mat"* with 97% probability).
  * To predict the next word accurately across arbitrary web pages, the model is forced to absorb vast facts about geography, history, science, and programming into its weights.
* **"Dreaming" Internet Text (Sampling)**: Base models sample text iteratively by feeding their own output back as input. They "dream" synthetic web pages—such as Java code, Amazon listings, or Wikipedia pages—mimicking the statistical patterns of their training data.
* **Hallucinations**: Because pre-training is a lossy compression holding a "gestalt" of world knowledge rather than a database lookup, models generate realistic-looking but fabricated facts (like fake ISBN book numbers or incorrect dates).

> [!NOTE]
> 💡 **Beginner Concept Bridge: Lossy Compression & Hallucinations**
> * **Lossy vs. Lossless Compression**: A `.zip` file is *lossless*—it restores your exact document pixel for pixel. A `.jpg` image or LLM parameter file is *lossy*—it keeps the overall pattern and background knowledge but loses exact verbatim quotes. When the model fills in forgotten details with plausible patterns, it **hallucinates**.

---

## Module 3: Architecture & Interpretability

* **The Transformer Neural Network**: Modern LLMs rely on the **Transformer architecture**. While the exact mathematical operations at each layer are fully known, the way billions of parameters interact to solve complex problems remains largely inscrutable.
* **Empirical Artifacts & Mechanistic Interpretability**: LLMs are treated as empirical systems—we observe their behavior through tests. The emerging field of **mechanistic interpretability** seeks to reverse-engineer what individual internal neurons do.
* **The Reversal Curse**: Knowledge storage in LLMs is asymmetric. For example, GPT-4 correctly answers *"Who is Tom Cruise's mother?"* (Mary Lee Pfeiffer), but fails when asked *"Who is Mary Lee Pfeiffer's son?"* because information is stored directionally during next-word training.

---

## Module 4: Stage 2 & 3 — Fine-Tuning & Alignment

Base models make poor assistants—if you give a base model the prompt *"How do I fix a flat tire?"*, it might respond with *"Question 2: How do I change engine oil?"* because it is merely completing a list of test questions found on the web.

* **Supervised Fine-Tuning (SFT)**:
  * **Swapping the Dataset**: Pre-training uses web text (high quantity, variable quality); fine-tuning uses curated Q&A dialogues (lower quantity, ultra-high quality).
  * Human annotators write prompt-response pairs following detailed company guidelines.
  * Fine-tuning requires far fewer documents (~100,000 conversations) and takes a fraction of the time/cost (e.g., 1 day on a smaller GPU cluster).
  * **Alignment**: Shifts the model's behavior from an internet document generator into a helpful, harmless, and truthful assistant.
* **Stage 3: Reinforcement Learning from Human Feedback (RLHF)**:
  * **Comparison Labels**: Writing custom answers is hard for human labelers (e.g., writing a haiku), but ranking multiple candidate outputs is easy.
  * RLHF uses these human rankings to train a reward model, further refining the assistant's output quality.

| Stage | Dataset Type | Compute / Time | Output Result |
|---|---|---|---|
| **Stage 1: Pre-training** | ~10 TB Raw Internet Crawl | ~6,000 GPUs / ~12 Days (~$2M) | Base Model ("Internet Document Completer") |
| **Stage 2: Fine-Tuning (SFT)** | ~100k High-Quality Q&A Conversations | Small Cluster / ~1 Day | Assistant Model ("Helpful Chatbot") |
| **Stage 3: RLHF** | Human Preference Rankings (A/B Comparisons) | Reward Model Optimization | Aligned Assistant Model |

---

## Module 5: LLM Ecosystem & Leaderboards

* **LMSYS Chatbot Arena**: An evaluation system where human users conduct blind A/B tests between two unidentified models and vote on the better response. Models are ranked using an **ELO rating system** (similar to chess).
* **Proprietary vs. Open-Weights**:
  * **Closed Models** (GPT-4, Claude) lead the top of the leaderboard.
  * **Open-Weights Models** (Llama series, Mistral/Zephyr) lag slightly behind but allow full customization, fine-tuning, and offline deployment.

---

## Module 6: Scaling Laws

* **Predictable Scaling**: Next-word prediction accuracy is a smooth, mathematically predictable function of two main factors:
  1. $N$: The number of **parameters** in the network.
  2. $D$: The volume of **training data** (tokens).
* **Performance Guarantee**: As parameters and data increase, next-word prediction loss decreases predictably. This lower loss correlates directly with higher scores on real-world reasoning and academic tests without needing new algorithmic breakthroughs.

---

## Module 7: Capabilities & Tool Integration

Modern LLMs go beyond generating text in their heads by delegating specialized tasks to external tools:

* **Web Browsing**: Emitting search tokens to query search engines, extract web page context, and cite sources.
* **Calculator & Code Interpreter**: Generating Python code (using `matplotlib` or numerical libraries) to perform exact calculations and plot charts rather than doing mental math.
* **Image Generation**: Calling tools like DALL-E directly from prompt context.

> [!NOTE]
> 💡 **Beginner Concept Bridge: Tool Use**
> Just as a human mathematician uses a calculator for complex arithmetic or a browser for current news, an LLM emits special control tokens that signal external computer programs to run and return exact answers back into its working memory.

---

## Module 8: Multimodality

* **Vision**: Models process images alongside text (e.g., analyzing a pencil sketch of a website layout and writing functional HTML/JavaScript code for it).
* **Audio**: Direct speech-to-speech architectures enable real-time, low-latency voice interaction.

---

## Module 9: Frontiers — System 2, Self-Improvement & LLM OS

* **System 1 vs. System 2 Thinking (Kahneman)**:
  * **System 1 (Current LLMs)**: Fast, instinctive generation. The model uses a fixed amount of computation per output token, regardless of question difficulty.
  * **System 2 (Future Goal)**: Slow, deliberate reasoning. Allowing the model to spend 30 minutes exploring a "tree of thoughts" to trade extended computation time for higher problem-solving accuracy.
* **Self-Improvement (The AlphaGo Analogy)**:
  * AlphaGo surpassed human chess/Go masters by playing millions of games against itself (**self-play**).
  * **The Open Challenge**: Go has an unambiguous win/loss reward signal. General language lacks an automatic, objective reward function, making general self-improvement a major open research problem.
* **The LLM as an Operating System (LLM OS)**:
  Rather than viewing an LLM as a simple chatbot, it can be viewed as the central **kernel process** of a new computing stack:
  * **CPU**: The LLM neural network engine.
  * **RAM**: The **Context Window** (the short-term memory limit for text input).
  * **Hard Drive**: External files and web search accessed via **Retrieval-Augmented Generation (RAG)**.
  * **Peripherals**: Python execution, vision, audio, and web browsers.

---

## Module 10: Security & Vulnerabilities

As LLMs become computing platforms, new security attack vectors emerge:

1. **Jailbreaks**: Bypassing safety filters to generate prohibited content:
   * *Roleplay*: Pretending to be a deceased grandmother telling a bedtime story about chemical manufacturing.
   * *Base64 Encoding*: Asking harmful questions in encoded text formats that safety filters failed to cover during alignment.
   * *Universal Transferable Suffixes*: Appending algorithmically generated gibberish tokens that force safety bypasses.
   * *Visual Jailbreaks*: Embedding subtle noise patterns inside images to override text guardrails.
2. **Prompt Injection**: Overriding the system's original instructions with malicious third-party inputs:
   * *Indirect Injection*: A hidden instruction embedded in white text on a web page forces an LLM to display phishing links or exfiltrate private user documents.
3. **Data Poisoning / Backdoor Attacks**: Injecting malicious trigger phrases (e.g., *"James Bond"*) into web-scraped training datasets that cause the model to malfunction or bypass safety checks when activated.

---

## ⚡ Quick Concept Cheat Sheet

| Term | Definition |
|---|---|
| **Parameters** | The numerical weights/dials (e.g., 70 Billion) inside the neural network that store world knowledge. |
| **Float16** | A 16-bit number storage format taking 2 bytes per parameter. |
| **Pre-training** | Unsupervised training on ~10 TB of raw web text to create a Base Model via next-word prediction. |
| **Fine-Tuning (SFT)** | Supervised training on ~100k curated Q&A dialogues to align a Base Model into an Assistant. |
| **RLHF** | Fine-tuning using human preference rankings to optimize assistant quality. |
| **Hallucination** | Generating plausible-sounding but false statements due to lossy memory compression. |
| **Context Window** | The maximum amount of text (RAM) an LLM can process in a single prompt. |
| **LLM OS** | Seeing the LLM as an OS kernel managing CPU, Context RAM, RAG Disk, and Tool Peripherals. |

---

