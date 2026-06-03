# LogiCrisis — My Complete Project Guide
### (For Interviews, Hackathon Pitches, and Self-Reference)

---

## 1. What Is This Project? (One Simple Paragraph)

**LogiCrisis** is an AI simulation where multiple AI agents (like virtual workers) cooperate to fix India's broken supply chain after a disaster. Imagine floods, port strikes, and road closures hit India's logistics network at the same time — trucks can't move, cargo is stuck, cold-chain goods are spoiling. My project creates a realistic simulation of this crisis and trains AI agents to solve it by negotiating with each other, forming teams, and rerouting shipments — all without a human telling them what to do.

**One-line pitch:** *"I built a multi-agent AI environment where LLMs learn to rescue India's supply chain after real-world disruptions — trained using reinforcement learning on live weather, currency, and geopolitical data."*

---

## 2. The Real-World Problem It Solves

India's logistics is fragile. A single flood in Mumbai, a port strike in Chennai, or a road closure near Delhi can cascade into a nationwide supply chain crisis — medicines don't reach hospitals, cold-chain food spoils, factories run out of parts.

Current solutions:
- Humans manually reroute shipments (slow, expensive)
- Basic rule-based software (can't adapt, can't negotiate)

My solution:
- AI agents that reason, negotiate, and cooperate like a crisis response team
- They learn from experience using Reinforcement Learning (specifically GRPO)

---

## 3. The Map / World (What the AI Operates In)

The simulation runs on a real map of India — **10 major cities** connected by **26 routes**:

```
Mumbai ── Pune ── Hyderabad ── Bangalore ── Chennai
  |                    |
Surat ── Ahmedabad ── Delhi ── Jaipur
              Kolkata ─────────────────────┘
```

**Cities:** Mumbai, Delhi, Kolkata, Chennai, Bangalore, Hyderabad, Pune, Ahmedabad, Jaipur, Surat

**Disruptions that can hit:**
- Flood (blocks multiple routes)
- Port Strike (shuts down coastal nodes)
- Road Closure (cuts specific corridors)

---

## 4. The 5 AI Agents (The "Team")

Each agent has a specific job, just like a real logistics company:

| Agent | Role | What It Does |
|---|---|---|
| **Carrier** | Freight transporter | Reroutes trucks/ships, moves cargo |
| **Warehouse** | Storage manager | Stages cargo, manages cold storage |
| **Customs Broker** | Clearance expert | Clears cargo stuck at borders/ports |
| **Insurer** | Risk assessor | Insures cold-chain cargo, assesses risk |
| **Shipper** | Client negotiator | Prioritizes cargo, negotiates SLAs |

**Key point:** Each agent can only see its own region and its own cargo queue — NOT the full map. This is called **partial observability** — like real-world workers who don't have god's-eye view. They have to communicate and trust each other.

---

## 5. What Actions Can Agents Take?

13 different actions across 4 categories:

| Category | Actions |
|---|---|
| **Logistics** | `reroute`, `request_transfer`, `prioritize_cargo`, `deploy_cold_storage` |
| **Negotiation** | `make_bid`, `accept_bid`, `reject_bid`, `counter_propose` |
| **Coalition** | `propose_coalition`, `join_coalition`, `leave_coalition`, `assign_coalition_role` |
| **Default** | `wait` |

**Example action (JSON):**
```json
{
  "agent_id": "carrier_0",
  "action_type": "reroute",
  "cargo_id": "C001",
  "route_id": "Mumbai-Pune",
  "reasoning": "Direct route to destination, currently unblocked"
}
```

---

## 6. The Reward System (How Agents Learn What's Good)

6 reward signals, each scored 0.0 to 1.0:

| # | Signal | What It Rewards |
|---|---|---|
| R1 | **Delivery** | +1.0 for on-time delivery, partial credit for late |
| R2 | **Coalition** | Bonus for working in fair teams |
| R3 | **Negotiation** | Reward for accepted bids at fair prices |
| R4 | **Cold Chain** | Penalty if temperature-sensitive cargo spoils |
| R5 | **Efficiency** | Good use of budget and vehicle capacity |
| R6 | **Anti-Cheat** | Penalty for suspicious/exploitative behavior |

All 6 are added together. Agents learn to maximize total reward over time.

---

## 7. The 9 Tasks (Easy → Expert)

| # | Task | Difficulty | What Makes It Hard |
|---|---|---|---|
| 1 | Single Route Recovery | Easy | 2 agents, 5 cargo, 1 disruption |
| 2 | Coalition Logistics | Medium | Must form teams + handle cold-chain |
| 3 | Cascade Failure | Hard | 60% of all routes blocked |
| 4 | Cold Chain Emergency | Medium-Hard | 100% cargo is temperature-sensitive |
| 5 | Negotiation Sprint | Medium | Must bid/counter-propose to score |
| 6 | National Recovery | Expert | All 5 agents, 25 turns, 4 disruptions |
| 7 | Earthquake Relief ★ | Hard | Must triage by priority (medical > food > shelter) |
| 8 | Capacity Crunch ★ | Hard | Only 25% truck capacity — must use bid market |
| 9 | JIT Breakdown | Medium-Hard | Zero credit for even 1 minute late |

★ Tasks 7 and 8 are intentionally **impossible for simple rule-based agents** — they require real AI reasoning. This is what proves LLM training adds value.

**Baseline scores (simple heuristic, no AI):**
- 7 out of 9 tasks: PASS
- Average score: **0.6553**
- Tasks 7 & 8: FAIL (need LLM reasoning)
- With GRPO-trained LLM: Expected to PASS all 9

---

## 8. The Data Used

### Synthetic Dataset (Built-In)
The environment generates its own scenarios:
- Cargo items with types: `bulk`, `standard`, `cold_chain`, `urgent`
- Random disruptions per task
- Agent budgets, capacities, deadlines — all configurable

### Live Real-World APIs (6 sources!)

| Source | What It Provides | Key? |
|---|---|---|
| **Open-Meteo** | Real weather for all 10 Indian cities | No key needed |
| **OpenWeatherMap** | Richer weather detail | Free key optional |
| **ExchangeRate-API** | Live USD/INR rate — tariff shock signal | No key needed |
| **GDELT 2.0** | Global news scan for India strikes/protests/floods | No key needed |
| **World Bank API** | Crude oil price → fuel cost signal | No key needed |
| **NewsAPI** | Breaking India trade/port news headlines | Free key optional |

**How live data is used:**
- Real weather alert for Mumbai → simulation adds a flood disruption on Mumbai routes
- USD/INR swing > 5% → port cargo gets tariff pressure
- GDELT detects "strike" news in Kolkata → road closure added there
- Crude oil price up → Carriers factor in higher transport costs

This means the simulation responds to **actual real-world events happening today**.

---

## 9. The Training Pipeline (How AI Learns)

**Algorithm: GRPO (Group Relative Policy Optimization)**
- This is the same algorithm used to train DeepSeek-R1 (the reasoning model that went viral)
- Instead of telling the AI the "right answer," GRPO shows it many attempts and rewards the better ones

**Base model:** Llama 3.2 3B (small, fast, open-source)

**Fine-tuning method:** 4-bit QLoRA via Unsloth (runs on a single GPU, even free Colab)

**Training process:**
1. Environment generates a crisis scenario
2. LLM agent sees its observation (partial view) as natural language
3. LLM outputs a JSON action with reasoning
4. Environment executes it, returns 6 reward signals
5. GRPO updates the model: good actions get reinforced, bad ones get suppressed
6. Repeat for thousands of episodes

**The trained adapter is hosted on HuggingFace:** `Sana06112003/logicriasis-adapter`

---

## 10. Technical Stack

| Layer | Technology |
|---|---|
| Environment / Simulation | Python, custom `LogiCrisisEnv` (OpenEnv spec) |
| API Server | FastAPI + Uvicorn |
| LLM Training | TRL (GRPO) + Unsloth (4-bit QLoRA) |
| Base LLM | Llama 3.2 3B Instruct |
| Frontend Demo | Leaflet.js (map) + Chart.js (rewards) |
| Live Data | Open-Meteo, GDELT, ExchangeRate-API, World Bank |
| Deployment | Docker + HuggingFace Spaces (port 7860) |
| Hackathon Spec | Meta PyTorch OpenEnv (reset/step/state API) |

---

## 11. The API (How the Environment Is Used)

Any external AI agent can plug into this environment via REST API:

| Endpoint | What It Does |
|---|---|
| `POST /reset` | Start a new crisis episode (pick task + seed) |
| `POST /step` | Send agent actions, get back observations + rewards |
| `GET /state` | See the full world state (ground truth) |
| `GET /tasks` | List all 9 tasks |
| `POST /grade` | Get a final score (0.0 – 1.0) for the episode |
| `GET /training_log` | Check if fine-tuning is running (last 80 log lines) |
| `GET /docs` | Swagger UI — interactive API documentation |

---

## 12. How to Explain This in 60 Seconds (Elevator Pitch)

> *"I built LogiCrisis — a multi-agent reinforcement learning environment that simulates a supply chain crisis in India. Imagine floods, port strikes, and road closures hitting all at once. My system has 5 AI agents — a carrier, warehouse manager, customs broker, insurer, and shipper — and they have to cooperate, negotiate bids, form coalitions, and reroute shipments to deliver cargo before it spoils or deadlines expire.*
>
> *What makes it unique is that the disruptions are driven by real live data — actual weather from Open-Meteo, currency signals from ExchangeRate-API, and news events from GDELT. So the simulation reacts to things happening in the real world right now.*
>
> *I trained a Llama 3.2 model using GRPO — the same algorithm behind DeepSeek-R1 — on 9 progressively harder tasks. A simple rule-based agent passes 7 out of 9. The trained LLM is designed to pass all 9, including the 2 tasks that require genuine reasoning: humanitarian triage in the earthquake relief task and market-based bidding in the capacity crunch task."*

---

## 13. Likely Interview / Hackathon Questions

**Q: Why GRPO instead of PPO or supervised fine-tuning?**
> GRPO doesn't need a separate value/critic model — it compares groups of outputs and rewards relatively better ones. It's cheaper to run, works well on small models, and is what DeepSeek used. Supervised fine-tuning can't work here because there's no "correct" answer dataset for complex multi-agent negotiation.

**Q: How is partial observability handled?**
> Each agent only receives its own region's data, its own cargo queue, and neighbor bids/proposals. There's no shared state. To succeed on harder tasks, agents must learn to infer what other agents are doing from indirect signals — a simplified version of Theory of Mind.

**Q: Why 6 reward signals instead of just delivery score?**
> A single delivery reward leads to myopic agents that ignore cold-chain spoilage, overspend budgets, or cheat by exploiting the action space. The 6 signals cover every dimension of a real logistics operation and the anti-cheat signal (R6) prevents reward hacking.

**Q: What's novel about your environment design?**
> Three things: (1) Live real-world data feeds directly into the simulation — it's not static. (2) Partial observability + coalition formation forces genuine cooperation rather than just independent optimization. (3) Two tasks (7 and 8) are intentionally impossible for heuristics, creating a measurable ceiling that proves LLM training adds value.

**Q: Why India's supply chain specifically?**
> India has a uniquely complex logistics network — multiple transport modes, cross-state customs, seasonal floods, and high cold-chain failure rates. It's a high-stakes, real problem with verifiable data. It also maps cleanly to a graph problem (cities = nodes, routes = edges) which is ideal for RL.

**Q: How do you prevent agents from just spamming `wait`?**
> The efficiency reward (R5) penalizes agents that don't utilize their capacity. The delivery reward (R1) gives zero for undelivered cargo. Waiting is sometimes optimal, but repeatedly waiting means you miss deadlines and get penalized on both R1 and R5.

---

## 14. Results Summary

| Metric | Value |
|---|---|
| Tasks | 9 (Easy → Expert) |
| Agents | 2–5 per task |
| Heuristic baseline score | 0.6553 / 1.0 |
| Heuristic pass rate | 7 / 9 tasks |
| LLM target pass rate | 9 / 9 tasks |
| Live data sources | 6 real APIs |
| Training algorithm | GRPO (TRL + Unsloth) |
| Base model | Llama 3.2 3B Instruct |
| Adapter hosted at | `Sana06112003/logicriasis-adapter` on HuggingFace |

---

## 15. How to Run It Locally (Quick Reference)

```bash
# Start the API server (port 8000)
python -m uvicorn api.app:app --reload --port 8000

# Open the frontend UI
open demo/ui.html

# Run the heuristic baseline (no GPU, no API key needed)
python inference.py

# Run the LLM agent (needs HF token)
HF_TOKEN=your_token python run_agents.py

# Check API docs
open http://localhost:8000/docs
```
