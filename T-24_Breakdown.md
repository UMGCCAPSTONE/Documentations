# T-24: Connect CI/CD to EC2 — Ticket Breakdown

**Repo:** `AI_Inventory_Backend` (workflow lives here; deploys the full compose stack)
**Sprint:** 3 · **Phase:** 1 (→ M-5 Integration, Jun 26) · **Status:** NOT STARTED — *no PR yet*
**Labels:** `devops` `ci-cd` · **ADR:** [0008 Deployment — Docker Compose on a single EC2 host]
**Critical path:** `T-24 → T-21 → T-29A/B → T-28 → T-31`. This is the unstarted node everything
downstream of deploy waits on — **staff first.**

---

## Why this is the priority

T-24 is the only piece of the deploy pipeline with no PR. Its upstream deps are already done or
green-and-mergeable, so the moment Phase 0 merges them, T-24 is fully unblocked and becomes the
single gate on T-29 (deploy), T-28 (test on live), and T-31 (demo).

## Dependencies

| Dep | What it provides | State |
|---|---|---|
| **T-19** (#30) | GitHub Actions workflow scaffold + CI gate | green/CLEAN — merge in Phase 0 |
| **T-23** (#45 + FE #50) | Production `docker-compose.yml` that builds & runs on the box | green/CLEAN — merge in Phase 0 |
| **T-27** (#48) | Repo-level secrets model + dedicated CI key + EC2 `.env` | green/CLEAN — merge in Phase 0 |
| **T-22** | Provisioned EC2 host (`ec2-user@3.19.63.64`), Docker + buildx present | ✅ done |

**Do not start T-24 until T-19/T-23/T-27 are merged to `dev`** — the deploy step invokes the compose
file (T-23) and consumes the secrets (T-27) inside the workflow (T-19).

## Scope (and explicit non-scope)

- **In scope:** a workflow job that SSHes into the EC2 host on **merge to `main`**, pulls the new
  code, and brings the compose stack up with the new images — **zero manual SSH**.
- **Out of scope → T-21:** zero-downtime/rollback automation, post-deploy health-check gating, and
  image-rebuild orchestration polish. T-24 establishes the SSH-deploy mechanism; T-21 hardens it.

## Required secrets (repo-level, from T-27 / `secrets.md`)

| Secret | Value / source | Notes |
|---|---|---|
| `EC2_HOST` | `3.19.63.64` | T-22 demo box |
| `EC2_USER` | `ec2-user` | confirm against AMI (T-22 noted Ubuntu 24.04 → could be `ubuntu`) |
| `EC2_SSH_KEY` | private half of `umgc-capstone-key.pem` | dedicated CI key per T-27 — not a personal key |
| `GH_PKG_TOKEN` | `read:packages` PAT | pull private `@umgccapstone/contracts` during image build |
| `POSTGRES_PASSWORD` + app secrets | already in EC2 `.env` (T-27) | injected on the box, not from CI |

## Subtasks

- [ ] Add a `deploy` job to the backend workflow, gated `on: push: branches: [main]` and
      `needs: [<CI/validate job>]` so it only runs after the CI gate passes (ADR 0008: deploy on
      merge to `main`).
- [ ] Load the SSH key from `EC2_SSH_KEY` into the runner (`ssh-agent` or
      `appleboy/ssh-action`), `known_hosts` pinned to `EC2_HOST`.
- [ ] On the box: `cd` to the deploy dir, `git pull` (or rsync the checkout), then
      `docker compose -f docker-compose.yml up -d --build` using the EC2 `.env` (T-23 + T-27).
- [ ] Authenticate the image build to GitHub Packages with `GH_PKG_TOKEN` (BuildKit secret —
      mirrors frontend #50) so the private contracts dep resolves.
- [ ] Confirm `docker buildx` on the box is current — **T-22 box needed `0.12.1 → 0.19.3`**; the
      job should fail loudly (not silently old-build) if buildx is stale.
- [ ] Smoke-verify after `up`: `docker compose ps` shows db/backend **Healthy**, migrate
      **Exited(0)**, frontend **Up** (matches the manual P4 run on the box).
- [ ] **Open security-group `:80` inbound** (currently localhost-only per the manual run) so the
      deploy can be verified from outside — coordinate with T-29.
- [ ] Make the job **idempotent + re-runnable** (safe to run on every merge to `main`).
- [ ] Document the deploy flow + the manual fallback (`ssh -i ~/.ssh/umgc-capstone-key.pem
      ec2-user@3.19.63.64`) in the backend README.

## Acceptance criteria (from board, made concrete)

- [ ] GitHub Actions SSHes into EC2 using the stored `EC2_SSH_KEY` secret — no manual key handling.
- [ ] A `deploy` step runs automatically on merge to `main` and requires **no manual SSH**.
- [ ] After a deploy run, the composed stack is up on the box with the new images and the backend
      health endpoint responds.

## Verification

1. Merge a trivial change to `main`; confirm the `deploy` job triggers and goes green.
2. From a machine off the box, `curl http://3.19.63.64/<backend-health>` returns the expected
   response (requires the `:80` SG rule above).
3. Re-run the deploy on an unchanged `main`; confirm it is idempotent and the stack stays Healthy.

## Risks / watch-items

- **`EC2_USER` mismatch** (`ec2-user` vs `ubuntu`) — verify against the live AMI before wiring.
- **Single-host, no rollback yet** (ADR 0008 accepts this for the demo) — a bad merge to `main`
  deploys straight to the box. Mitigate by keeping the CI gate (`needs:`) strict; rollback is T-21.
- **buildx drift on the box** — pin/verify in the job; this already bit the manual run once.
- **SG `:80` closed** — external verification (T-29) fails until opened.

---

*Draft breakdown — not yet filed/edited on the board. Pairs with `Sprint_3_Plan.md`.*
