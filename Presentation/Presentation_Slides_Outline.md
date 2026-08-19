# LG Noida AI Training — Slide Outline (PLAIN TEXT, for review)

**Purpose:** this deck is the runbook. When a slide says "SWITCH TO DEMO," the instructor
leaves the deck, builds/shows it live in the tool, then returns to the next slide.
**Format stage:** content only. After we agree, convert to real slides using the template + script.

**Session length: 4 HOURS (locked).** Timings mapped to the client outline.

**Legend:**
- `[DIAGRAM: x]` = a block diagram goes here (list at the end).
- `[⛏ DEMO]` = leave the deck and do it live in the tool.
- `[WHY]` = a "why this tool" slide — reasoning shown BEFORE introducing the tool.
- `(⏱ n min)` = time budget.

**TEACHING RULE (applies to every concept slide):**
Every concept is explained using the ONE running example — **predictive maintenance on
the IM-500 machine**. Concept → same example → build it live. No throwaway examples.
A machine-context slide (S5) and a Glossary handout give the non-technical audience the vocabulary.

Running example: IM-500 injection-molding machine → predict failure (Agent A) → check the
spare part (Agent B) → raise maintenance (Agent C) → log to a Google Sheet.

Total: ~33 content slides. Dashboard is OUT of scope — the flow ends at the Google Sheet.

---

## 0. OPENING (⏱ 5 min)

**S1 — Title**
- LG Electronics Noida — Practical AI for the Factory Floor. Instructor: Arjun.
- "Live demos — code, docs & glossary shared after."

**S2 — What you'll walk out with**
- Understand AI vs GenAI vs Agent vs Agentic AI — in factory terms.
- See a real predictive-maintenance system built live: 3 agents working together.
- Get a take-home manual + glossary to rebuild it yourself, free.

**S3 — How today runs**
- ONE example, grown step by step (not scattered demos).
- We build live; you watch, then you get everything to redo at home.
- 4 hours, one break. Questions welcome throughout.

**S4 — The one example we grow all day** `[DIAGRAM: thread-overview]`
- IM-500 machine → predict failure → check spare part → raise maintenance → log it.
- Same story deepens each module: RAG → 1 agent → many agents → action.

**S5 — Meet the machine: the IM-500** `[DIAGRAM: im500-context]`
- What it is: a plastic **injection-molding machine** on the Bay-3 line — melts plastic, injects it into a mould, makes parts.
- It has **sensors** (vibration, temperature, motor current, servo position…) that tell us its health.
- When a part is about to fail, we want to predict it, order the spare in time, and schedule maintenance BEFORE it breaks. That's our whole example.
- (Vocabulary like EN-77, lead time, Bay-3 is in the Glossary handout.)

---

## 1. CONCEPTS (⏱ 30 min, incl. 5 Q&A) — each explained on the IM-500

**S6 — The 4 words people mix up** `[DIAGRAM: ai-concept-quadrant]`
- AI vs Generative AI vs AI Agent vs Agentic AI — one quadrant, all four framed on the IM-500.

**S7 — AI (on the IM-500)**
- Plain automation/rules: "if vibration > 4.5 mm/s, flag it." No language, no reasoning.

**S8 — Generative AI (on the IM-500)**
- Creates language: reads the sensor note and writes a plain-English shift summary. But it doesn't KNOW our machine unless we give it our docs (that's RAG, next).

**S9 — AI Agent (on the IM-500)**
- LLM + our reference data + a decision: reads "servo error rising," decides "encoder EN-77 failing, ~6 days." This becomes our Agent A.

**S10 — Agentic AI (on the IM-500)**
- Several agents coordinating: predict (A) → check parts (B) → raise maintenance (C). This is exactly what we build in Module 4.

**S11 — The ladder + Q&A**
- Rules → GenAI text → one agent → a team of agents. We climb it today, all on the IM-500.

---

## 2. RAG — RETRIEVAL AUGMENTED GENERATION (⏱ 50 min, incl. 10 Q&A)

**S12 — The problem: a plain chatbot doesn't know YOUR machine**
- Ask a generic AI about the IM-500 servo error EN-77 → it guesses. Dangerous on the floor.

**S13 — What RAG is (one line)** `[DIAGRAM: rag-pipeline]`
- Give the AI the IM-500 documents; it answers ONLY from them, with citations. (Reuse rag_pipeline diagram.)

**S14 — [WHY] Why NotebookLM for RAG**
- No-code, free, Google-stable. Upload PDFs, ask — no database to manage. Reliability first.

**S15 — [⛏ DEMO] Before vs After RAG, on the IM-500** `[DIAGRAM: rag-beforeafter]`
- Ask the EN-77 question with NO docs → vague/guess.
- Upload IM-500 manuals + addendum → same question → exact part, stock, lead time, cited.
- Honest "not in sources" before; full grounded answer after.

**S16 — How it works (light)** `[DIAGRAM: rag-pipeline]`
- IM-500 documents → chunk → index → retrieve → LLM answers grounded. NotebookLM does it automatically.

**S17 — Q&A**

---

### ☕ BREAK (⏱ 10 min) — S18 (holding slide)

---

## 3. AI AGENT — SINGLE AGENT, LIVE (⏱ 40 min, incl. 7 Q&A)

**S19 — What makes it an "agent" (on the IM-500)** `[DIAGRAM: agent-anatomy]`
- LLM (brain) + IM-500 reference data + a decision. (Optionally tools.)

**S20 — [WHY] Why n8n for agents**
- No-code visual builder, free tier, browser-based. Copilot Studio needed a licence; n8n needs none.

**S21 — [WHY] Why Google Gemini as the model**
- Free API tier, strong, instant key. Use a STABLE flash model (e.g. gemini-3.6-flash), not preview (preview = ~20 req/day; stable = ~1,500/day).

**S22 — Our first agent: Failure Prediction (Agent A)**
- Input: an IM-500 sensor reading. Output: failing component + lead time + urgency. Grounded in the IM-500 health manual.

**S23 — [⛏ DEMO] Build & run Agent A in n8n**
- Chat Trigger → AI Agent → Gemini → paste the system prompt (IM-500 data inline).
- Test: "vibration 3.9 mm/s for 4 shifts" → bearing wear, ~7 days.
- Grounding check: ask something not in the IM-500 data → it refuses (no hallucination).

**S24 — Agent B (Spare Parts) — same steps, different data**
- Instructor shows the pattern; participants can rebuild B at home from the manual.

**S25 — Q&A**

---

## 4. AGENTIC AI & ORCHESTRATION (⏱ 50 min, incl. 10 Q&A)

**S26 — From one agent to a team (on the IM-500)** `[DIAGRAM: orchestration-abc]`
- Predict failure (A) → check the part (B) → raise maintenance (C). Handoffs.

**S27 — Orchestration patterns** `[DIAGRAM: orchestration-patterns]`
- Sequential (what we build), Supervisor, Parallel. Why sequential: simple, reliable, matches the real IM-500 flow.

**S28 — [⛏ DEMO] Wire A → B → C into one workflow**
- Copy the three agents onto one canvas, connect, set the handoff prompts.
- Ask ONE question ("servo error 140 counts") → A→B→C → final maintenance request, URGENT, escalate.

**S29 — Why this is powerful (the insight)**
- No single agent could produce the final decision. The 21-day part vs 6-day failure escalation emerges from the TEAM.

**S30 — [WHY] Why log to Google Sheets (not email)**
- A sheet is a permanent RECORD you can filter/share; email is a one-off notification. Native n8n node, same login.

**S31 — [⛏ DEMO] Agent C writes the decision to a Google Sheet** `[DIAGRAM: end-to-end-pipeline]`
- Structured output → a row appears with the maintenance request. The agent takes a real action — it records, not just answers.

**S32 — The full picture, end to end** `[DIAGRAM: end-to-end-pipeline]`
- Question → A → B → C → Google Sheet. One diagram tying the whole day together.

---

## 5. WRAP-UP (⏱ 5 min)

**S33 — Recap + take it home**
- The ladder: RAG → single agent → agent team → action (logged to the sheet). All free, all no-code, all on the IM-500.
- You get: the how-to manual, the prompts, the sheet template, the glossary, this deck.

---

## DIAGRAMS TO CREATE (block diagrams for the deck)
1. `thread-overview` — the one example growing across stages (RAG→1 agent→team→action). NEW.
2. `im500-context` — simple labelled picture of the IM-500 + its sensors (machine context). NEW.
3. `ai-concept-quadrant` — AI / GenAI / Agent / Agentic, each with the IM-500 example. NEW.
4. `rag-pipeline` — EXISTS (RAG_NotebookLM_Uploads/Diagrams). Reuse.
5. `rag-beforeafter` — EXISTS. Reuse.
6. `agent-anatomy` — LLM + reference data + decision (+ tool), on the IM-500. NEW.
7. `orchestration-abc` — A→B→C sequential handoff with what each passes. NEW.
8. `orchestration-patterns` — sequential vs supervisor vs parallel, side by side. NEW.
9. `end-to-end-pipeline` — chat → A → B → C → Google Sheet (ends at the sheet). NEW.

(7 new diagrams, 2 reused. Same clean style as the existing RAG diagrams.)

---

## OPEN QUESTIONS FOR YOU (before we render)
- Slide count OK (~33)?
- Any LG branding / logo / colour requirements for the template stage?
- Glossary: keep as a separate handout (current plan), or also add 1 glossary slide at the end of the deck?
