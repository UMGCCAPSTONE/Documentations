# Sprint 1–2 Ticket Grouping & Assignment Plan

**Status:** Working draft — adjust as needed. This document is the central agreed-upon reference for ticket assignment decisions. The board is NOT encoded with these groupings; this file is the source of truth for who picks up what.

**Last updated:** 2026-06-10

---

## Team model

- 6 engineers, pair programming, all working full stack.
- Groupings define **default ownership and sequence**, not fences — any idle engineer may pick up the next **unblocked** ticket from any group, in dependency order.
- **Priority rule:** every group has two tiers. Core tier first. AI-tier tickets are picked up only after the group's core tier is done **and T-12 (Inventory API) has merged** — the proof-of-concept trigger for the underlying structure.
- AI features are **nice-to-have**: deferred to the back end of Sprint 2. Cut order if time runs short: T-10C → T-6C → T-8B polish first; **T-16/T-15 survive longest** so a partial landing still demos a real AI recommendation.

## Group ownership

| Group | Pair | Focus |
|---|---|---|
| **Group 1** | **JJ (Jose) + Arth** | Inventory vertical + AI engine |
| **Group 2** | **Dan + Spencer** | Infrastructure + Suppliers + Menu/AI pipeline |
| **Group 3** | **Robert + Adan** | Foundations + Dashboard/Reports |

**Critical path:** T-33 → T-11 → T-12 → everything else.

---

## Group 1 — Inventory Vertical (~54.5 hrs) — JJ + Arth

*The backbone: dev environment, schema, seed data, the largest API, the core CRUD pages — then the AI engine that feeds on inventory data.*

### Core tier (~41.5h)

| Order | Ticket | Effort | Depends on |
|---|---|---|---|
| 1 | T-23A Local Docker dev environment | 0.5h | T-0 Backend ✓ |
| 2 | T-32 Define Demo / Seed Data | 3h | T-33 |
| 3 | T-11 Database Schema | 8h | T-2, T-32, T-33 |
| 4 | T-12 Inventory API (CRUD) — **most protected ticket in the sprint** | 12h | T-11, T-33 |
| 5 | T-12A Contracts Reconciliation — publish `@umgccapstone/contracts` | ~2h | T-12 |
| 6 | T-7A Page Layout → T-7B DataGrid → T-7C Item Management | 16h | T-12, T-34 |

### AI tier (~13h) — after core + T-12 merge

| Order | Ticket | Effort | Why this group |
|---|---|---|---|
| 7 | T-16 AI Prompt Workflow | 8h | Depends on T-12; consumes inventory data (expiration, cost, margin) — this pair knows those shapes best |
| 8 | T-17 Fallback Logic | 5h | Depends on T-12 + T-16; keeps prompt/fallback behavior in one head |

*Stretch:* T-7S CSV Import/Export

**Why grouped:** One continuous dependency chain from dev environment → schema → API → UI; knowledge compounds at every step. T-23A lives here because it's the environment this pair develops the schema and API inside. The AI tier follows naturally: T-16/T-17 are built directly on top of T-12 — and with Arth (AI/Cloud lead) in this pair, the AI engine sits with its specialist.

---

## Group 2 — Infrastructure + Suppliers + Menu/AI Pipeline (~54.5 hrs) — Dan + Spencer

*Day-one infra, the smallest feature vertical, the deploy pipeline — then the recommendation API and the Menu pages that consume it.*

### Core tier (~29.5h)

| Order | Ticket | Effort | Depends on |
|---|---|---|---|
| 1 | T-19 GitHub Actions · T-22 EC2 | 4h | none — clear days 1–2 |
| 2 | T-14 Supplier API | 6h | T-11 |
| 3 | T-9A Supplier Directory → T-9B Add/Edit Flow | 10h | T-14, T-34 |
| 4 | T-23 docker-compose · T-24 CI/CD→EC2 · T-27 Secrets · T-20 Testing | 9h | unblock as APIs land |

### AI tier (~25h) — after core + T-12 merge

| Order | Ticket | Effort | Why this group |
|---|---|---|---|
| 5 | T-25 Bedrock IAM · T-26 Secure Local Dev Access | 1h | Pure IAM/credentials work, same skill set as the infra tier; **gates all other AI tickets — pull these first when the trigger fires** |
| 6 | T-15 Menu / Recommendation API | 10h | Depends on T-16 (Group 1 AI tier); sequencing works because this group's core finishes earliest |
| 7 | T-8A Menu Builder → T-8B Recommendation Cards | 14h | The pages that consume T-15 — build-the-API-then-its-pages, like every other vertical |

*Stretch:* T-9S Order History · T-8S Advanced Menu Management

**Why grouped:** The infra tickets are the only day-one unblocked work, so this group gets CI running before code volume arrives. Its core tier is the lightest by design — the first group free to start AI work, beginning with the 1-hour IAM tickets that gate everything else. Spencer's frontend depth lands where the heaviest deferred UI (T-8A/8B) lives.

---

## Group 3 — Foundations + Dashboard/Reports (~48 hrs) — Robert + Adan

*The shared scaffolding every page needs, the read-heavy pages built on it — then their AI-powered panels.*

### Core tier (~39h)

| Order | Ticket | Effort | Depends on |
|---|---|---|---|
| 1 | Finish T-0 Frontend (in progress) · T-20A Test Harness | 1h+ | none |
| 2 | **T-33 Shared Contract Package — first ticket of the sprint, all-leads review** | 3h | T-2 ✓ |
| 3 | T-34 Frontend Data Layer & Conventions | 6h | T-33 |
| 4 | T-5 Authentication UI | 10h | T-34 |
| 5 | T-13 Dashboard API | 8h | T-12 |
| 6 | T-6A Summary Cards · T-6B Urgent Alerts | 9h | T-13, T-34 |
| 7 | T-10A KPI Cards · T-10B Category Summary | 8h | T-13, T-34 |

### AI tier (~9h) — after core + T-12 merge

| Order | Ticket | Effort | Why this group |
|---|---|---|---|
| 8 | T-6C Dashboard AI Recommendation Preview Cards | 5h | Lives on the dashboard this group built |
| 9 | T-10C Recommendation History & Waste-Risk report | 4h | Extends this group's Reports page; **first to cut if time runs short** |

*Stretch:* T-10S Advanced Analytics

**Why grouped:** T-33/T-34/T-5 are horizontal foundations — one owner prevents competing conventions, and T-34 lets every group build pages against contract mocks instead of waiting for live APIs. With the Product Owner (Adan) in this pair, T-33's contract decisions and the dashboard/report content stay close to product intent. The AI tier is pure UI additions to this group's own pages.

---

## Order of operations

1. **Days 1–2:** T-33 lands (all-leads review) · Group 2 clears T-19/T-22 · Group 1 clears T-23A.
2. **Sprint 1 exit:** T-32, T-11 done · CI running · T-0 Frontend closed · T-3 (in Review) needs a reviewer — any pair.
3. **Sprint 2 first half:** T-12/T-14 APIs + T-34/T-5 scaffolding in parallel — core tiers only.
4. **T-12 merges → AI tiers unlock.** Group 2 pulls T-25/T-26 immediately (1h, gates the rest); Group 1 starts T-16.
5. **Sprint 2 back half:** core pages finish, deploy pipeline closes (T-23/T-24/T-27/T-20), AI chain proceeds as capacity allows: T-16 → T-15 → T-17/T-8A → T-8B/T-6C/T-10C.

### Cross-group dependencies to watch

- **T-15 (Group 2) depends on T-16 (Group 1)** — the only cross-group link inside the AI work. If Group 1 is delayed on T-16, Group 2 pulls other Backlog work rather than waiting.
- **T-13 (Group 3) depends on T-12 (Group 1)** — Group 3 builds dashboard pages against T-34 mocks until it lands.
- **T-34 (Group 3) blocks all data-bound pages in every group** — second-highest protection after T-12.

## Idle pickup rules

1. Finish your group's current tier first.
2. Pull only **unblocked** tickets (check the `Depends on:` line), in dependency order — top of another group's queue, not the middle.
3. AI-tier tickets only after T-12 has merged, regardless of which group you're in.
4. Stretch tickets only when your group's both tiers are done.

## Open items awaiting team decision

- [ ] Delete or keep the unused `group:*` label definitions on GitHub (created 2026-06-10, never applied to tickets)
- [ ] Soften T-28 / T-31 acceptance criteria to "if AI ships" (currently require AI end-to-end)
- [ ] Close duplicates: AI_Inventory_Backend #4 (T-19), Documentations #6 (T-1), AI_Inventory_Frontend #1 and #6
