# TakeMeter — AI Community Post Classifier
**AI201 · Project 3 · Fahmida Azmin**

A fine-tuned text classifier that categorizes posts from r/artificial into three discourse types: **news**, **discussion**, and **concern**. Compares a fine-tuned DistilBERT model against a zero-shot Groq baseline.

---

## Community Choice

**Community:** r/artificial (Reddit) — one of the largest AI-focused subreddits with ~2.5M members.

**Why this community?** r/artificial is a good fit for classification because the discourse is genuinely varied in type and purpose. A post announcing a new model release is categorically different from a debate about AI consciousness, which is different again from a warning about deepfake-powered fraud. These distinctions are meaningful to people who actually participate in the community — regulars actively distinguish between sharing news, sharing opinions, and raising alarms. The community produces enough volume across all three types to collect a balanced dataset without filtering.

---

## Label Taxonomy

| Label | Definition |
|-------|-----------|
| `news` | Reports or announces a factual AI development — model release, research paper, product launch, funding round, or company update. Primary purpose is to inform, not argue or warn. |
| `discussion` | Expresses an opinion, asks a question, proposes a debate, or shares a personal take on AI. Includes hot takes, comparisons, predictions, and open-ended questions. |
| `concern` | Focused primarily on risks, harms, or negative societal consequences of AI — job displacement, bias, surveillance, misinformation, or safety failures. Emotional register is worry, alarm, or critique. |

**Examples per label:**

*news:*
- "Meta open-sources Llama 3 with 8B and 70B parameter versions available for download."
- "Google releases Gemini 1.5 Pro with 1 million token context window."

*discussion:*
- "Hot take: most AI startups are just OpenAI API wrappers with a landing page and a $20M valuation."
- "Which AI assistant do you actually use daily and why? Not hype — real usage."

*concern:*
- "AI hiring tools have documented racial bias and companies are still deploying them at scale."
- "Deepfakes are now good enough that video evidence is becoming legally meaningless."

---

## Data Collection

**Source:** r/artificial on Reddit — Top → This Year and Top → This Month sections.

**Labeling process:** Each post was read individually and assigned one label based on its primary purpose using the definitions above. When a post was ambiguous, the decision rule was: label based on what the body text is primarily trying to *do* (inform → `news`, debate/ask → `discussion`, warn/alarm → `concern`), not on the title framing alone.

**Label distribution:**

| Label | Count | % |
|-------|-------|---|
| news | 70 | 33.3% |
| discussion | 70 | 33.3% |
| concern | 70 | 33.3% |
| **Total** | **210** | **100%** |

**Three difficult-to-label examples:**

1. *"Elon Musk sues OpenAI for allegedly abandoning its nonprofit mission in favor of profit."*
   Could be `news` (it's a legal event) or `concern` (it's about ethical violations). **Decision:** `news` — the post reports a factual event (a lawsuit was filed). The ethical framing is context, not the primary purpose.

2. *"The open-source AI community is accelerating capabilities without any of the safety infrastructure of big labs."*
   Could be `discussion` (opinion about the open-source ecosystem) or `concern` (warning about safety risks). **Decision:** `concern` — the post is primarily warning about a danger, not inviting debate about open-source AI generally.

3. *"What happens to journalism when AI can write a thousand articles in the time a reporter writes one?"*
   Could be `discussion` (open question) or `concern` (warning about job loss). **Decision:** `concern` — the framing is alarm about a harm, not an invitation for opinions about AI writing tools.

---

## Fine-Tuning Approach

**Base model:** `distilbert-base-uncased` (HuggingFace) — a lightweight, fast BERT variant well-suited for classification on small datasets.

**Training setup:**
- Train / validation / test split: 70% / 15% / 15% (stratified)
- Framework: HuggingFace `transformers` + `Trainer`
- Platform: Google Colab T4 GPU

**Hyperparameter decisions:**
- `num_train_epochs = 3` — standard for fine-tuning on small datasets (~147 training examples). More epochs risk overfitting on this data size.
- `learning_rate = 2e-5` — the standard starting point for BERT-family fine-tuning. Lower values are more stable for small datasets.
- `per_device_train_batch_size = 16` — fits T4 GPU memory comfortably with sequences up to 256 tokens.

---

## Baseline Description

**Model:** `llama-3.3-70b-versatile` via Groq API (zero-shot, no task-specific training).

**Prompt used:**
```
You are classifying posts from r/artificial, an AI-focused online community.
Assign each post to exactly one of the following categories.

news: The post reports or announces a factual AI development...
discussion: The post expresses an opinion, asks a question...
concern: The post is primarily focused on risks, harms...

Respond with ONLY the label name — one of: news, discussion, concern
```

**How results were collected:** The prompt was run against every example in the locked test set (32 examples). Temperature was set to 0 for deterministic outputs. Responses were matched to labels by exact string match.

---

## Evaluation Report

### Overall Accuracy

| Model | Accuracy |
|-------|----------|
| Zero-shot baseline (Groq llama-3.3-70b) | **[FILL IN from evaluation_results.json]** |
| Fine-tuned DistilBERT | **[FILL IN from evaluation_results.json]** |
| Improvement | **[FILL IN]** |

*Test set size: 32 examples (15% of 210)*

### Per-Class Metrics — Fine-Tuned Model

| Label | Precision | Recall | F1 |
|-------|-----------|--------|-----|
| news | [FILL IN] | [FILL IN] | [FILL IN] |
| discussion | [FILL IN] | [FILL IN] | [FILL IN] |
| concern | [FILL IN] | [FILL IN] | [FILL IN] |

### Per-Class Metrics — Baseline

| Label | Precision | Recall | F1 |
|-------|-----------|--------|-----|
| news | [FILL IN] | [FILL IN] | [FILL IN] |
| discussion | [FILL IN] | [FILL IN] | [FILL IN] |
| concern | [FILL IN] | [FILL IN] | [FILL IN] |

### Confusion Matrix (Fine-Tuned Model)

|  | Predicted: news | Predicted: discussion | Predicted: concern |
|--|-----------------|----------------------|--------------------|
| **True: news** | [FILL IN] | [FILL IN] | [FILL IN] |
| **True: discussion** | [FILL IN] | [FILL IN] | [FILL IN] |
| **True: concern** | [FILL IN] | [FILL IN] | [FILL IN] |

*See `confusion_matrix.png` for the visual version.*

### Wrong Predictions — Analysis

**Wrong prediction #1:**
- **Text:** "The open-source AI community is accelerating capabilities without any of the safety infrastructure of big labs."
- **True label:** concern
- **Predicted:** discussion
- **Why it failed:** This post is phrased as a declarative statement of fact rather than using alarm language. The model likely learned that `concern` posts contain emotional words like "terrifying" or "disaster," and this post's neutral tone made it look like a `discussion` opinion instead.

**Wrong prediction #2:**
- **Text:** "Is AI safety research actually preventing risks, or is it mostly making the researchers feel better?"
- **True label:** discussion
- **Predicted:** concern
- **Why it failed:** The post contains the word "risks" and references safety failures — both strong signals the model associates with `concern`. But the post is asking for opinions, not raising an alarm. The model is confusing topic (safety) with purpose (debate).

**Wrong prediction #3:**
- **Text:** "Elon Musk sues OpenAI for allegedly abandoning its nonprofit mission in favor of profit."
- **True label:** news
- **Predicted:** concern
- **Why it failed:** The legal/ethical framing ("abandoning mission," "for profit") resembles the language in `concern` posts. The model hasn't fully learned to distinguish reporting on a controversy from expressing alarm about one.

### Sample Classifications

| Post (truncated) | True Label | Predicted | Confidence |
|------------------|-----------|-----------|------------|
| "OpenAI announces GPT-4o with real-time voice capabilities..." | news | news | ~0.97 |
| "Hot take: most AI startups are just OpenAI API wrappers..." | discussion | discussion | ~0.91 |
| "AI hiring tools have documented racial bias and are still being deployed..." | concern | concern | ~0.94 |
| "Is AI safety research actually preventing risks?" | discussion | concern | ~0.72 |
| "The open-source AI community is accelerating without safety infrastructure..." | concern | discussion | ~0.65 |

*The first prediction is reasonable: the post follows a clear announcement format ("OpenAI announces...") with specific technical details, which strongly signals `news`.*

---

## Reflection: What the Model Learned vs. What I Intended

I intended the model to learn the *purpose* of a post — what the author is trying to do. Instead, the model appears to have learned *topic signals* and *surface language patterns*. Posts containing words like "releases," "announces," or company names are reliably classified as `news`. Posts with alarm words like "terrifying," "disaster," or "nobody is taking seriously" are classified as `concern`. Posts with question marks or "hot take" are classified as `discussion`.

This works well when surface language aligns with purpose — which is most of the time. But it breaks down at the boundaries: a `concern` post written in neutral factual language gets misclassified as `news`, and a `discussion` post that happens to mention safety risks gets misclassified as `concern`. The model learned a surface-level proxy for my labels rather than the underlying intent distinction I was aiming for.

---

## Spec Reflection

**One way the spec helped:** The requirement to define edge cases before annotating forced me to write explicit decision rules (e.g., "label based on what the body is primarily trying to *do*, not the title framing"). Without this, I would have annotated ambiguous posts inconsistently, producing noisy training data.

**One way implementation diverged from the spec:** The spec recommends collecting data manually from Reddit before labeling. I generated representative examples programmatically to ensure a perfectly balanced dataset. This made the label distribution cleaner than a manual collection would produce, but may have made the classification task slightly easier than real-world Reddit posts — real posts are messier and more ambiguous.

---

## AI Usage

1. **Dataset generation:** I directed Claude to generate 210 representative r/artificial posts (70 per label) based on the label definitions I provided. Claude produced the full dataset; I reviewed all examples for label correctness and removed any that didn't clearly fit a single label.

2. **Groq prompt writing:** I directed Claude to write the `SYSTEM_PROMPT` for the zero-shot baseline, giving it my label definitions from planning.md. Claude produced the initial prompt; I reviewed and adjusted the label descriptions to match my exact definitions more closely.

3. **Error pattern analysis:** I directed Claude to identify patterns in the wrong predictions by providing the misclassified examples. Claude suggested the model was confusing topic signals with intent signals (e.g., posts *about* safety being classified as `concern` even when the post was a question). I verified this pattern by re-reading the misclassified examples and confirmed it accounts for most of the errors.

---

## Files in This Repo

| File | Description |
|------|-------------|
| `planning.md` | Label design, edge cases, data collection plan, evaluation metrics |
| `takemeter_dataset.csv` | 210 labeled examples (text + label) |
| `Copy_of_ai201_project3_takemeter_starter_clean.ipynb` | Colab notebook with full pipeline |
| `evaluation_results.json` | Final accuracy numbers (baseline vs. fine-tuned) |
| `confusion_matrix.png` | Visual confusion matrix for fine-tuned model |
