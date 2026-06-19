# Sprint 2 — Final Week Plan (June 17–24, 2026)

**Status:** Working draft for team distribution. Derived from `Sprint_1-2_Assignment_Plan.md` (standing ownership) and the authoritative Gantt (`gantt_chart/CMSC_Project_Schedule.gan`), reconciled against the live `dev` branches on 2026-06-17.

**Goal:** Land **all remaining Sprint-2 work** by the `M-S2 Sprint 2 Complete` milestone on **Wednesday, June 24**. The AI tier is now unblocked (T-12 merged; T-22/T-25/T-26 done), so this is a full-court press, not a partial.

---

## Milestone anchors (from the Gantt)

| Milestone | Date | Means |
|---|---|---|
| **M-3 Backend Ready** + **M-4 AI Logic Ready** | **Fri Jun 19** | T-12A, T-13, T-16, T-15, T-17 all merged to `dev` |
| **M-9 CI/CD Pipeline Ready** | **Mon Jun 22** | T-23, T-24, T-27, T-20 done — merge-to-EC2 working |
| **M-2 Frontend Ready** + **M-S2 Sprint 2 Complete** | **Wed Jun 24** | All UI (T-5, T-6, T-7, T-8, T-9, T-10) on live APIs |

---

## Status delta since the 2026-06-10 assignment plan

**Done / merged to `dev` (or confirmed complete):**
- Sprint 1 — **fully complete.**
- **T-22 EC2 · T-25 Bedrock IAM · T-26 Secure Local Dev — COMPLETE** ✅ (unblocks the AI tier and the deploy pipeline).
- Sprint 2 merged: **T-34** Data Layer · **T-12** Inventory API · **T-14** Supplier API · **T-5A** Backend auth middleware · **T-7B** Inventory DataGrid · **T-20A/B/C** test harnesses.

**In flight (open branches — close first):**
- `feature/t5-auth-ui` (T-5) · `feature/t9a-supplier-directory` (T-9A) · `feature/T_19_backend-ci` (T-19, needs PR).

**Net remaining for Sprint 2:** T-12A, T-13, T-15, T-16, T-17 (backend/AI) · T-5, T-6A/B/C, T-7A/C, T-8A/B, T-9A/B, T-10A/B/C (UI) · T-20, T-23, T-24, T-27 (pipeline).

---

## Assignments this week (all six engineers)

> Ownership follows the standing assignment plan; idle-pickup rules still apply. Targets below are sequenced to the milestone they feed.

### Group 1 — Jose Escandor (Backend Lead) + Arth Thakkar (AI/Cloud Lead) — *Inventory + AI engine*

| Ticket | Effort | Feeds | Notes |
|---|---|---|---|
| **T-12A** Contracts Reconciliation — publish `@umgccapstone/contracts`, align types/Zod with implemented API shapes, pin in both repos | 2h | Jun 19 | Do first — every downstream page consumes the pinned contract. |
| **T-16** AI Prompt Workflow (Bedrock) | 8h | **M-4 · Jun 19** | **Arth leads.** First link in the AI chain; T-15 (Group 2) waits on this — merge early. |
| **T-17** Fallback Logic | 5h | **M-4 · Jun 19** | Keeps prompt + fallback in one head. |
| **T-7A** Inventory Layout · **T-7C** Item Management | 10h | **M-2 · Jun 24** | Closes the inventory vertical end-to-end (incl. 409 `ITEM_IN_USE`). |
| *Overflow:* pair with Group 2 on **T-8B** once the AI tier lands | — | Jun 24 | Natural fit — T-8B renders the recommendations this pair generates. |

### Group 2 — Dan Nunes (PM) + Spencer Renfro (Frontend Lead) — *Infra + Suppliers + Menu/AI pipeline*

| Ticket | Effort | Feeds | Notes |
|---|---|---|---|
| **T-19** Backend CI · **T-9A** Supplier Directory | 2h | Now | Merge the two open branches early-week. |
| **T-9B** Supplier Add/Edit Flow | 5h | **M-2 · Jun 24** | Refreshes directory + inventory grid on write. |
| **T-23** docker-compose · **T-24** CI/CD→EC2 · **T-27** Secrets · **T-20** Testing | 9h | **M-9 · Jun 22** | Builds on T-23A; now unblocked (T-22/T-25/T-26 ✓). Get merge-to-EC2 live before the UI volume lands. |
| **T-15** Menu / Recommendation API | 10h | **M-3 · Jun 19** | Depends on **T-16** (Group 1). Arth pairs in once T-16 merges. |
| **T-8A** Menu Builder · **T-8B** Recommendation Cards | 14h | **M-2 · Jun 24** | Spencer's depth on the heaviest deferred UI; Group 1 assists on T-8B. |

### Group 3 — Robert Lee (Asst PM) + Adan Medina (Product Owner) — *Foundations + Dashboard/Reports*

| Ticket | Effort | Feeds | Notes |
|---|---|---|---|
| **T-5** Authentication UI | ~5h to close | **M-2 · Jun 24** | In flight. Spencer available to pair on Firebase. Protected routes + persisted auth. |
| **T-13** Dashboard API | 8h | **M-3 · Jun 19** | **Now fully unblocked (T-12 ✓).** Gates T-6A/B and T-10A/B — front-load it. See breakdown below. |
| **T-6A** Summary Cards · **T-6B** Urgent Alerts | 9h | **M-2 · Jun 24** | Read dashboard.summary / dashboard.alerts. |
| **T-10A** KPI Cards · **T-10B** Category Summary | 8h | **M-2 · Jun 24** | Read reports.kpis / reports.category. |
| **T-6C** AI Preview · **T-10C** Rec History | 9h | Jun 24 (cut first) | Depend on T-15/T-16; **T-10C is first to cut** if time runs short. |

**Rough load:** G1 ~25h · G2 ~40h · G3 ~39h. Group 1 finishes its AI tier earliest by design — its overflow goes to Group 2's menu UI (T-8B), which is why the totals balance across the week rather than per-ticket.

---

## T-13 Dashboard API — completion plan

**Owner:** Group 3 (Robert + Adan) · **Effort:** 8h · **Repo:** backend · **Due:** M-3 Backend Ready (**Jun 19**) · **Depends on:** T-11 ✓, T-12 ✓, contracts ✓ — **all met, start now.**

**Why it's the priority:** four of Group 3's own pages (T-6A, T-6B, T-10A, T-10B) and two AI panels (T-6C, T-10C) read from these endpoints. Until T-13 lands they stay on T-34 mocks.

**Endpoints** (`{ data, meta }` envelope, Firebase Bearer via the T-5A middleware):
1. `GET /api/dashboard/summary` → `DashboardSummary` (totalItems, totalSkus, lowStockCount, expiringSoonCount, atRiskValue)
2. `GET /api/dashboard/alerts` → low-stock + expiring-soon items
3. `GET /api/dashboard/recommendations/preview` → latest recommendation snapshots

**Subtasks:**
- [ ] Scaffold the `/api/dashboard` router, mounted under the auth middleware (T-5A)
- [ ] `summary`: compute metrics using the **canonical helpers exported from `@umgccapstone/contracts`** — server-only derived fields, do not re-derive on the client
- [ ] `alerts`: reuse `isLowStock` / `isExpiringSoon`; return each item with the flag that tripped it
- [ ] `recommendations/preview`: **build tolerant of empty** — returns `[]` until T-15 persists recommendations
- [ ] Empty-data states clean on all three endpoints
- [ ] 401 without a token on all three
- [ ] Jest + supertest: happy / empty-data / 401 per endpoint
- [ ] Confirm query keys map 1:1 to `dashboard.summary` / `dashboard.alerts` / `dashboard.recommendations.preview` in the T-34 invalidation map

**Acceptance criteria:** endpoints return correct metrics/alerts/preview; empty states handled; 401 without token.

**Coordination note:** the `recommendations/preview` endpoint reads data T-15 (Group 2) produces. Ship T-13 with that endpoint returning an empty list cleanly — it lights up automatically when T-15 lands, so T-13 stays fully independent of the AI chain and hits Jun 19 on its own.

---

## Order of operations

1. **Wed–Thu (Jun 17–18):** Merge the three in-flight branches (T-19, T-9A, T-5). Group 1 publishes **T-12A** and starts **T-16**; Group 3 starts **T-13**; Group 2 starts the **deploy pipeline** + **T-9B**.
2. **Fri Jun 19 — M-3 + M-4:** **T-13, T-16, T-15, T-17** merged. Backend + AI logic complete. Arth moves from T-16 to assist T-15.
3. **Weekend → Mon Jun 22 — M-9:** Pipeline closed (T-23/T-24/T-27/T-20); merge-to-EC2 verified.
4. **Mon–Wed (Jun 22–24) — M-2:** All UI on live APIs — Group 3 builds T-6/T-10 on live T-13; Group 2 + Group 1 finish T-8A/B; T-7C and T-9B close.
5. **Wed Jun 24 — M-S2:** Sprint 2 complete; hand off to Sprint 3 (integration/testing/deploy).

## Risk & cut order

If capacity runs short, cut in this order (protects the demo): **T-10C → T-6C → T-8B polish.** **T-16/T-15 survive longest** so a partial landing still demos a real AI recommendation. T-13 and the deploy pipeline are **not** cuttable — too many pages and the Sprint-3 handoff depend on them.

## Open items for standup
- [ ] Confirm T-22/T-25/T-26 closed on the GitHub board.
- [ ] Soften T-28 / T-31 acceptance criteria to "if AI ships" (currently require AI end-to-end).
- [ ] Close duplicate issues: Backend #4 (T-19), Documentations #6 (T-1), Frontend #1 and #6.
- [ ] Keep or delete the unused `group:*` GitHub labels.
