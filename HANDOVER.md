# LG Noida AI Training — Project Handover

**Purpose of this document:** hand this project to whoever (likely an AI assistant) picks it up next, so they can continue with zero prior context. Read this file first, then `examples.txt` and `TECH_STACK.txt`.

**Last updated:** 2026-08-19

---

## 1. What this project is

We are preparing a **4-hour instructor-led AI training** for LG Electronics, Noida. It is a live-demo session (no per-person hands-on; demo code shared afterwards). Audience is from a **manufacturing background, non-technical**. The instructor is Arjun.

**Two hard constraints on every tool we choose:**
1. Effectively free (a 30-day trial is acceptable).
2. No-code / low-code, reliable, "just works."

The full session structure and timings live in `LG_Noida_AI_Training_Outline.txt` (read-only — do not edit; it is the client's source of truth).

---

## 2. The teaching design (most important idea)

We do **not** use a separate throwaway example for each concept. We take **one thread** and grow it across the modules:

**RAG → single agent → group of agents (orchestration).**

The chosen thread is **Predictive Maintenance** for an injection-molding machine (IM-500). Two agents are built live:

- **Agent A — Failure Prediction Agent:** predicts machine failure from sensor readings/thresholds.
- **Agent B — Spare Parts Agent:** checks whether the needed spare part is in inventory.

Flow across modules:
- **Module 2 (RAG):** build the RAG for both agents. Instructor builds one live; students build the other.
- **Module 3 (Single agent):** each RAG becomes a single agent. Instructor builds one; students build the other.
- **Module 4 (Orchestration):** the **same two agents** join **one group** and work together (predict failure → check parts). If time permits, build a second example from scratch (e.g., Production Planning).

Full detail (including the second, optional Production Planning example and the 4-agent list) is in `examples.txt`.

---

## 3. Decided tech stack

| Module | Tool | Status |
|---|---|---|
| 1. Concepts | Google AI Studio (or slides only) | Free forever |
| 2. RAG | **NotebookLM** | Free forever — **proven working** |
| 3. AI Agent | **n8n Cloud** | **TESTING** (not final) |
| 4. Orchestration | **n8n Cloud** (multi-node) | **TESTING** (not final) |
| 5. Dashboard (optional) | Looker Studio | Free forever |

Why n8n and not others (full log in `TECH_STACK.txt`):
- **Microsoft Copilot Studio:** blocked — needs an org M365 license the instructor doesn't have.
- **n8n Cloud:** current pick. Account created; live instance at `https://arjunthakurdev.app.n8n.cloud/`.

**RAG-tooling decision:** build RAG **only once, in NotebookLM**. In n8n the agent reuses the same reference data inline (no second vector-store build) — so we never build RAG twice. Continuity between NotebookLM RAG and the n8n agent is explained verbally, not rebuilt.

---

## 4. Folder structure and what each file is for

```
lgelectronics/
├── LG_Noida_AI_Training_Outline.txt     Client's original outline + timings (READ-ONLY, source of truth)
├── HANDOVER.md                          THIS FILE — read first
├── TECH_STACK.txt                       Tool decision per module + trial log (why tools were dropped)
├── examples.txt                         The workshop content spine (the two agents + progressive build)
├── todo.txt                             Progress tracker + "resume tomorrow" quick-start
│
└── Module2_RAG/
    ├── AgentA_FailurePrediction/
    │   ├── IM500_MachineHealth_FailurePrediction_Manual.pdf   Agent A RAG source (sensors, thresholds, failure signatures, lead times)
    │   └── IM500_Predictive_Addendum.pdf                      Adds a NEW sensor + part (Encoder EN-77) for the before/after RAG demo
    ├── AgentB_SpareParts/
    │   └── IM500_SpareParts_Inventory_Catalog.pdf             Agent B RAG source (component→part map, stock levels, lead times, reorder rules)
    ├── Diagrams/
    │   ├── rag_pipeline.(png|svg)        "How RAG works" block diagram (predictive-maintenance framing)
    │   └── rag_beforeafter.(png|svg)     "Before vs After RAG" comparison (servo-error / EN-77 example)
    └── Archive_injectionmolding_errorcodes/
        └── (3 old PDFs)                  Superseded error-code demo docs, kept for reference only — NOT part of the spine
```

Notes:
- `.svg` files are the **editable** source for each diagram; `.png` are the rendered images for slides/docs.
- Module 3 and Module 4 folders **do not exist yet** — they are the next things to create.

---

## 5. What is DONE

- Tech stack decided; trial log recorded.
- Teaching design and the two live agents locked (`examples.txt`).
- **Module 2 RAG demo proven live in NotebookLM** using the earlier injection-molding docs: asking about an item not in the sources returned an honest "not in sources" (no hallucination); after uploading the addendum, the same question returned a complete, cited answer synthesized across all sources. This is the "before vs after RAG" money shot.
- Module 2 sample docs **re-aligned to the predictive-maintenance spine** (Agent A + Agent B), old docs archived.
- Both RAG diagrams rebuilt in predictive-maintenance framing.

---

## 6. What to do NEXT (in order)

1. **Write the Module 2 RAG walkthrough document** (Word). Should contain: what RAG is + why it matters; exact NotebookLM steps from a zero account; the before/after demo script using the new predictive-maintenance docs; a short chunking/retrieval explainer; and both diagrams embedded. Content is all ready in this folder.
2. **Verify the before/after demo with the NEW docs:** upload Agent A manual + Agent B catalog to NotebookLM; ask a question that needs the addendum (e.g., the servo encoder EN-77 failure + stock + lead-time risk); confirm it says "not found" before the addendum and answers fully after.
3. **Module 3 (n8n):** at `https://arjunthakurdev.app.n8n.cloud/`, build Agent A and Agent B as single AI-Agent nodes (Google Gemini Chat Model; free Gemini API key from aistudio.google.com; reference data inline in the system prompt). Test each.
4. **Module 4 (n8n):** wire Agent A → Agent B into one group (predict failure → check spare parts) to demonstrate orchestration/handoff. Keep a screen recording as backup (n8n is still unproven).
5. Create `Module3_Agent/` and `Module4_Orchestration/` folders (build steps, prompts, screenshots, backup recording), mirroring Module 2.
6. **Final deliverables:** one consolidated master document + a refreshed slide deck (existing slides are outdated).

Time reality: budget roughly a half-day of prep to get one agent + a 2-agent orchestration solid in n8n. Live demo time is minutes once rehearsed.

---

## 7. Gap note (now RESOLVED)

> **Gap that existed:** the Module 2 sample docs were injection-molding **error-code** documents, but the decided spine is **predictive maintenance** (failure prediction + spare parts).
>
> **Resolution (2026-08-19):** the sample docs were re-aligned to the predictive-maintenance spine — Agent A failure-prediction manual + addendum, and Agent B spare-parts catalog. The old error-code PDFs were moved to `Module2_RAG/Archive_injectionmolding_errorcodes/`. The two RAG diagrams were regenerated to match. This gap is closed; it is recorded here and in `todo.txt` so whoever takes over knows the history.

---

## 8. Open decisions / watch-outs

- **n8n is not final.** If it proves unreliable during prep, fall back to Gemini "Gems" for the single agent, and a diagram + concept walkthrough for orchestration.
- n8n signup asked for an office email; the instructor used his own domain (`arjunthakur.dev`) to get in.
- Login for Google tools: use personal Gmail or the `arjunthakur.dev` domain; avoid the official LG email (corporate policy may block Labs tools).
- The instructor's preference: keep everything as no-code as possible, minimum tools.
