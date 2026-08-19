# Glossary & Machine Context

*LG Electronics Noida — AI Training. Plain-language reference for the terms used in the session.*

> **The machine at the centre of everything: the IM-500.**
> The IM-500 is a plastic **injection-molding machine** on the Bay-3 production line. It melts plastic pellets and injects the melt into a mould under high pressure to make plastic parts. Like any heavy machine it wears out, and a surprise breakdown stops the whole line. It carries **sensors** — vibration, oil and barrel temperature, motor current, screw torque, tie-bar strain, and servo position — that reveal its health. Our whole example is about reading those sensors to **predict** a failure, **find the spare part**, and **schedule maintenance before it breaks**.

## Machine & maintenance terms

- **Bay-3** — The production bay (area of the floor) where the IM-500 sits, with its own spare-parts store.
- **Sensor reading** — A live number from the machine (e.g. vibration in mm/s). Rising trends warn of wear.
- **Predicted failure / lead time** — How long until a part is expected to fail (e.g. "~6 days"). Maintenance must be scheduled before this date.
- **Spare part / part number** — The replacement component and its code (e.g. `EN-77` = servo encoder, `BR-3105` = pump bearing).
- **In stock / min stock** — How many spares are on the shelf, and the minimum to keep. Below minimum = reorder.
- **Supply lead time** — How long the supplier takes to deliver a part (e.g. EN-77 = 21 days). If longer than the failure lead time, it's urgent.
- **EN-77** — The star of the demo: a servo encoder, 0 in stock, 21-day supplier lead time but only ~6 days to failure — so it must be escalated urgently.
- **URGENT / NORMAL** — Priority of a maintenance request. URGENT = part missing or won't arrive in time; escalate to the Bay-3 lead.

## AI concepts

- **AI (Artificial Intelligence)** — Software that follows rules or spots patterns — e.g. "flag if vibration > 4.5". No language, no reasoning.
- **Generative AI (GenAI)** — AI that creates language or content — e.g. writes a shift report from notes. On its own it doesn't know your machine.
- **LLM (Large Language Model)** — The "brain" behind GenAI — a model trained on huge text that understands and writes language (e.g. Google Gemini).
- **AI Agent** — An LLM given reference data and the ability to decide/act — reads an alarm, looks it up, decides an action. (Our Agent A/B/C.)
- **Agentic AI** — Several agents coordinating on a task — predict → check parts → raise maintenance. A team, not a single bot.
- **Orchestration** — Wiring agents together and deciding who hands off to whom. We use a **sequential** pipeline: A → B → C.
- **Handoff** — One agent passing its result as the input to the next agent.
- **RAG (Retrieval-Augmented Generation)** — Give the AI your documents so it answers ONLY from them, with citations — instead of guessing. Stops "hallucination".
- **Hallucination** — When an AI makes up a confident but wrong answer. RAG and grounding prevent it.
- **Grounding / citation** — Tying an answer to a specific source document so it can be trusted and checked.
- **System prompt** — The standing instructions baked into an agent — its role plus the reference data it must use.
- **Structured output** — Forcing the AI to answer in fixed fields (part, priority, days…) so the result goes straight into a spreadsheet.

## Tools we use (and why)

| Tool | What it is | Why we use it |
|---|---|---|
| NotebookLM | Google's no-code RAG tool | Upload PDFs, ask grounded questions — free, stable, zero setup. |
| n8n | No-code visual workflow builder | Build and connect the agents by dragging boxes — free tier, no licence. |
| Google Gemini | The AI model (LLM) | Free API tier, strong, instant key from Google AI Studio. |
| Google Sheets | Spreadsheet | Where the agent logs each maintenance decision — a permanent, shareable record. |

## n8n building blocks

- **Workflow** — One automation on one canvas (e.g. our A→B→C pipeline).
- **Node** — One step/box on the canvas, wired left to right.
- **Trigger** — The first node that starts the workflow — we use a chat box ("Chat Trigger").
- **AI Agent node** — The box that holds an agent; you plug a Chat Model (the LLM) into it.
- **Credential** — A saved login/API key, stored once and reused (e.g. the Gemini key, the Google login).
- **API key** — A secret code that lets one tool (n8n) use another (Gemini).
- **Rate limit / quota** — How many free requests you get per day. Use a stable model (~1,500/day), not a preview (~20/day).
