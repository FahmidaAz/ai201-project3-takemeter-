# TakeMeter — Project 3 Planning
**AI201 · Fahmida Azmin**

---

## Community

**r/artificial** — one of the largest AI-focused subreddits (~2.5M members), covering news about model releases, debates about AI's future, and concerns about societal impact. The community includes researchers, developers, enthusiasts, and skeptics, producing a wide range of post types from announcements to heated ethical arguments.

**Why this community?** The distinctions between post types are real and meaningful to participants: a news post about a model release is categorically different from a debate about AI consciousness, which is different from a warning about AI-generated deepfakes. These distinctions reflect how the community itself organizes discourse.

---

## Label Taxonomy

### Label 1: `news`
**Definition:** A post that reports or announces a factual AI development — a model release, research paper, product launch, funding round, policy decision, or company update. The primary purpose is to inform, not to argue or express concern.

**Examples:**
- "OpenAI releases GPT-4o with real-time voice capabilities and improved multimodal understanding."
- "Meta open-sources Llama 3 with 8B and 70B parameter versions available for download."

**What it is NOT:** A post that uses a news event as a jumping-off point for opinion or worry. If the post spends more words on the author's reaction than on the facts, it is `discussion` or `concern`.

---

### Label 2: `discussion`
**Definition:** A post that expresses an opinion, asks a question, proposes a debate, or shares a personal take on AI — without the primary focus being fear or harm. Includes hot takes, comparisons, predictions, and open-ended questions to the community.

**Examples:**
- "Hot take: most AI startups are just OpenAI API wrappers with a landing page and a $20M valuation."
- "Which AI assistant do you actually use daily and why? Not hype — real usage."

**What it is NOT:** A post about risks, harms, or ethical violations. If the opinion is specifically about something dangerous or harmful AI is doing, it is `concern`.

---

### Label 3: `concern`
**Definition:** A post primarily focused on risks, harms, ethical problems, or negative societal consequences of AI — including job displacement, bias, surveillance, misinformation, safety failures, or existential risk. The emotional register is worry, alarm, or critique.

**Examples:**
- "AI hiring tools have documented racial bias and companies are still deploying them at scale. When does this end?"
- "Deepfakes are now good enough that video evidence is becoming legally meaningless. This is terrifying."

**What it is NOT:** A general opinion that happens to be negative. The post must be specifically about harm or risk, not just a critical take on AI hype.

---

## Edge Cases and Decision Rules

### Hard case 1: News post that also expresses alarm
> "OpenAI announces SearchGPT — a search engine that could kill independent journalism."

**Which label?** If the post is mostly reporting facts and the concern is in the headline framing only → `news`. If the post body focuses on the danger and uses the news as a launching pad → `concern`. **Decision rule:** Label based on the primary purpose of the body text, not the title alone.

### Hard case 2: Discussion that touches on harms
> "Do you think AI will replace most programmers in 5 years?"

**Which label?** This is asking for opinions and debate, not warning about active harm. The framing is speculative and conversational → `discussion`. If the same post said "AI is actively destroying programming as a career and nobody cares" → `concern`.

### Hard case 3: Opinion that's actually just a news recap
> "I just read that Mistral released a new model. Thoughts?"

**Which label?** The post is soliciting discussion, not reporting facts → `discussion`. Even though it references a news event, the purpose is to start a conversation.

**Decision rule summary:** When in doubt, ask: *What is this post trying to DO?* Inform → `news`. Debate/ask/opine → `discussion`. Warn/alarm/critique harm → `concern`.

---

## Data Collection Plan

- **Source:** r/artificial on Reddit
- **Method:** Manual copy of post title + first paragraph of body into CSV
- **Filters:** Top → This Year and Top → This Month to get quality, varied posts
- **Target:** 210 examples (~70 per label) for balanced distribution
- **Labeling process:** Apply label definitions above; flag ambiguous cases and apply decision rules

---

## Label Distribution Target

| Label      | Target Count | % of Dataset |
|------------|-------------|--------------|
| news       | 70          | 33%          |
| discussion | 70          | 33%          |
| concern    | 70          | 33%          |
| **Total**  | **210**     | **100%**     |

---

## Stretch Features (Planned)

- [ ] Error pattern analysis
- [ ] Confidence calibration

---

*Last updated: June 2026*
