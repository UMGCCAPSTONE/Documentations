# Sprint 3 — Execution Plan & Dependency Map

**Date:** 2026-06-24 · **PM:** Dan Nunes · **Window:** Jun 24 → Jul 8 (M-S3) · **Status:** DRAFT for standup

> Derived from the live GitHub board/PR state (both repos) and the authoritative Gantt
> (`gantt_chart/CMSC_Project_Schedule.gan`), reconciled against `dev` on 2026-06-24.
> Consistent with backend ADRs 0004/0005/0007/0008 and frontend ADR 0005.

---

## Operating principle — two parallel tracks

Two tracks run in parallel:

- **Main track** (3 pairs) owns the app end-to-end and ships the recommendation feature on the
  **rule-based fallback** (ADR 0005) — so it **never waits on Bedrock**.
- **AI track** (rotating team) owns **only T-16** (Bedrock prompt workflow, ADR 0007) and merges
  *behind* the recommendation API. If it lands, recommendations flip AI-on automatically (the
  fallback flag and snapshot envelope from ADR 0004 are already carried by the UI); if it doesn't,
  we demo the deterministic fallback.

**The seam is `T-15`:** the recommendation API calls the rule-based ranker (T-17) by default and
Bedrock (T-16) only when available. This is why the critical path has **no dependency on AI**.

---

## Sprint 3 runway (from the Gantt)

| Milestone | Date | Days out |
|---|---|---|
| M-5 Integration Complete | **Jun 26** | 2 |
| M-6 Testing Complete | Jul 1 | 7 |
| M-7 Deployment Complete | Jul 2 | 8 |
| M-8 Final Submission Ready | Jul 6 | 12 |
| M-S3 Project Done | **Jul 8** | 14 |

---

## The blocking map (what gates what)

```
PIPELINE (critical path — Main)
  T-19 CI ──┐
  T-27 sec ─┼──► T-24 CI/CD→EC2 ──► T-21 automate ──► T-29A/B deploy ──► T-28 test ──► T-31 demo
  T-23 cmp ─┘        (NO PR YET)                              ▲                ▲
                                                   T-18 integration ──────────┘
REC VERTICAL (Main, fallback-first)
  T-17 ranker ──► T-15 rec API ──► T-8A generate ──► T-8B cards ──► (T-6C preview)
                       ▲                                              └─► T-10C history
AI TRACK (parallel, non-blocking)
  T-16 Bedrock prompt ──► (plugs into T-15; upgrades source only)
```

**Single biggest risk: `T-24` has no PR and everything downstream of deploy waits on it.**
It is the one node to staff first. Its only upstream deps (T-19/T-23/T-27) are **already green**
PRs — merging them (Phase 0) literally unblocks it. See `T-24_Breakdown.md`.

**Critical path that can sink the demo:** `T-24 → T-29 → T-28 → T-31`. Protect this above all
feature work.

---

## Phase 0 — Clear the runway (today → Jun 25) · *no new code, review only*

| Action | Ticket | State |
|---|---|---|
| Approve + merge | T-19 #30, T-27 #48, T-23 #45 + FE #50 | green / CLEAN — need a peer reviewer each |
| Rebase onto `dev`, then merge | T-9B #47, T-10A #54 | green but DIRTY |

> Closes 6 carryover tickets with zero engineering. Counts as integration (T-18) progress.
> Note: PR authors cannot approve their own PRs — assign a peer reviewer to each.

## Phase 1 — Pipeline live + rec engine real (→ **M-5 Integration, Jun 26**)

**Main track:**
- **T-24** CI/CD SSH-deploy on merge to `main` (ADR 0008) → then **T-21** automate. *(staff first)*
- **T-17** fallback ranker → **T-15** rec API on the fallback path (real recommendations, flagged).
  *This is the AI/main seam — build it AI-agnostic.*
- FE fill-ins: **T-5B** protected routes, **T-10B** category summary, **T-8A** menu builder generate-flow.
- **T-18** integration review opens as pages hit live APIs.

**AI track (parallel):** **T-16** Bedrock prompt workflow — IAM call, Zod-validated parse (ADR 0007).
Target: merge behind T-15 by ~Jul 1.

## Phase 2 — Deploy + test on the box (→ **M-6 Jul 1 / M-7 Jul 2**)

**Main track:** **T-29A/B** deploy to EC2 · **T-28** testing on live · **T-32/T-33** seed data
(depends on T-15 persistence) · FE **T-8B** recommendation cards + actions + four UI states
(ADR FE-0005), carrying the fallback flag.

**AI track:** T-16 merge gate — see go/no-go below.

## Phase 3 — Polish + demo (→ **M-8 Jul 6 / M-S3 Jul 8**)

**Main track:** **T-30** docs · **T-31** demo prep · **T-6C** dashboard AI preview *(only if green)*.

---

## AI go/no-go (decision: ~Jul 1, owned by AI track lead)

- **T-16 green by Jul 1** → flip rec source to Bedrock; demo real AI with automatic fallback.
  *No Main-track change required.*
- **Not green** → ship **fallback-only** (ADR 0005 guarantees a working, honest "rule-based"
  recommendation), **cut T-6C**, demo graceful degradation. The feature still works and still
  tells a clean story.

## Cut order if capacity slips (unchanged from standing plan)

**T-10C → T-6C → T-8B polish.**
**Never cut:** the pipeline (T-24/T-29) or T-15/T-17 — those are the demo. T-16 is the only thing
that can be absent and still leave a working app.

---

## Track ownership summary

| Track | Tickets | Owner |
|---|---|---|
| **Main — pipeline** | T-24, T-21, T-29A/B | (staff T-24 first) |
| **Main — rec vertical** | T-17, T-15, T-8A, T-8B | 3 pairs |
| **Main — app fill-ins** | T-5B, T-9B, T-10A/B, T-18 | 3 pairs |
| **Main — close-out** | T-28, T-30, T-31, T-32/T-33 | all |
| **AI — Bedrock only** | **T-16** | rotating AI team |

*Plan is DRAFT until the team finalizes at standup. No board changes have been made.*
