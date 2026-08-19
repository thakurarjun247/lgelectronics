# Add-on Manual — Example 2: Production Planning (4 agents)

**Optional "if time permits" / take-home example.** Same tools and steps as the main manual — only the domain and prompts change. Build the n8n mechanics exactly as in the main manual (`LG_Noida_AI_Training_Manual.md`, sections 6–8); this file gives you the example, the data, the four prompts, and the wiring.

---

## 1. The idea

A second, office-side example: planning what the plant should manufacture this week.

| Agent | Job |
|---|---|
| 1 — Planning Agent | Flags low-inventory, low-supply and high-demand items |
| 2 — Production Capacity Check Agent | Checks if the item has a line and whether capacity exists |
| 3 — Coordinator Agent | Builds the plan; works out the units to manufacture |
| 4 — Master Agent | Formats the final plan and sends it to the PLANT HEAD |

**How it maps to the session:** take **ONE** agent (the Planning Agent) as the single agent in Module 3, then combine all four in Module 4. The full 4-agent group is the "if time permits" / take-home version.

**Flow:** `Planning → Capacity Check → Coordinator → Master`

---

## 2. Sample data (embed in the prompts, like the IM-500 example)

Item demand & inventory:

```
SKU     | Product              | Stock | Min | Weekly demand | Supply
AC-101  | Split AC 1.5T        | 40    | 60  | 120           | OK
WM-220  | Washing Machine 7kg  | 15    | 30  | 80            | LOW
RF-330  | Refrigerator 260L    | 90    | 50  | 60            | OK
MW-440  | Microwave 25L        | 5     | 20  | 50            | OUT
TV-550  | LED TV 43"           | 25    | 40  | 100           | OK
```

Production line capacity (per week):

```
AC-101 -> Line A, 100/wk
WM-220 -> Line B, 60/wk
RF-330 -> Line C, 80/wk
MW-440 -> Line D, 40/wk
TV-550 -> Line E, 90/wk
```

---

## 3. The four system prompts

### Agent 1 — Planning Agent  (this is the single agent for Module 3)

```
You are the Planning Agent for the LG appliance plant (office planning). From the item list below, flag every item that needs production this week. Use ONLY this data - don't invent items.

Items (SKU | product | stock | min | weekly demand | supply):
- AC-101 | Split AC 1.5T | 40 | 60 | 120 | OK
- WM-220 | Washing Machine 7kg | 15 | 30 | 80 | LOW
- RF-330 | Refrigerator 260L | 90 | 50 | 60 | OK
- MW-440 | Microwave 25L | 5 | 20 | 50 | OUT
- TV-550 | LED TV 43" | 25 | 40 | 100 | OK

Flag an item if ANY is true: stock < min, OR weekly demand > stock, OR supply is LOW/OUT.
Output: each flagged item with SKU, product, and the reason(s) (low stock / high demand / supply risk).
```

### Agent 2 — Production Capacity Check Agent

```
You are the Production Capacity Check Agent. Given a flagged item and the units it needs, check its production line and whether weekly capacity can cover them. Use ONLY this data.

Line capacity (weekly):
- AC-101 -> Line A, 100/wk
- WM-220 -> Line B, 60/wk
- RF-330 -> Line C, 80/wk
- MW-440 -> Line D, 40/wk
- TV-550 -> Line E, 90/wk

Rule: if required units <= line capacity -> capacity OK. If required > capacity -> SHORTFALL (report units over capacity).
Output: item, line, weekly capacity, capacity OK or SHORTFALL (+ shortfall amount).
```

### Agent 3 — Coordinator Agent

```
You are the Coordinator Agent. Build the production plan from the flagged items and the capacity check. Use ONLY the numbers you are given.

For each item:
- required units = weekly demand - current stock (minimum 0)
- planned units this week = the smaller of (required units) and (line capacity)
- backlog = required units - planned units (carry to next week, if any)

Output a table per item: SKU, required units, planned units this week, backlog.
```

### Agent 4 — Master Agent

```
You are the Master Agent. Take the finished production plan and write the final, decision-ready message for the PLANT HEAD. Be concise.

Output:
- To: Plant Head
- Priority items first (supply OUT/LOW)
- Per item: planned units this week, line, backlog (if any)
- One-line summary: total units to manufacture this week
```

---

## 4. Build it (same mechanics as the main manual)

- **Single agent (Module 3):** build the **Planning Agent** exactly like Agent A in the main manual (Chat Trigger → AI Agent → Gemini model → paste the Planning prompt). Test: `Plan this week's production.`
- **Combine (Module 4):** copy all four onto one canvas, wire `Chat Trigger → Planning → Capacity Check → Coordinator → Master`, and set the handoff prompts:

```
Planning:        Prompt = "Take from previous node automatically"

Capacity Check:  Prompt = "Define below" (Expression):
                 {{ $json.output }}

Coordinator:     Prompt = "Define below" (Expression):
                 Flagged items: {{ $('Planning Agent').first().json.output }}
                 Capacity check: {{ $('Capacity Check Agent').first().json.output }}

Master:          Prompt = "Define below" (Expression):
                 {{ $json.output }}
```

- **Optional:** give the Master Agent a Structured Output Parser + a Google Sheets "Append Row" node (as in main manual section 10) to log the weekly plan to a sheet.

> Same model + rate-limit rules as the main manual (section 9): use a stable Flash model, one quota per model per day.

---

## 5. Test inputs

```
Planning Agent : Plan this week's production.
   -> flags AC-101 (high demand + low stock), WM-220 (low stock + LOW supply),
      MW-440 (OUT + below min), TV-550 (below min + high demand)

Full chain     : Plan this week's production and send it to the plant head.
   -> Master Agent returns a per-item plan with planned units, lines, backlog,
      priority items (MW-440, WM-220) first, and a total-units summary.
```
