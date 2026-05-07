# AGtech.dev v0.1

> AI-native sprint methodology for Agyeman Enterprises software builds.

AGtech is how we ship. Every feature begins as a **Job Card**. Every Job Card moves through a defined state machine worked by a 5-AI loop. Humans (Akua) only touch the loop at two gates: **spec sign-off** and **completion sign-off**.

---

## What's in this repo

| File/Folder | Purpose |
|-------------|---------|
| `JOB_CARD_SCHEMA.md` | Canonical Job Card field definitions + state machine |
| `SPRINT_PLAN_FORMAT.md` | Sprint plan template + acceptance criteria |
| `roles/` | Role prompts for each AI in the 5-AI loop |
| `.github/ISSUE_TEMPLATE/` | GitHub Issue template (Job Card format) |
| `HITL_GATES.md` | What Akua reviews and what she doesn't |
| `sprints/` | Sprint archives — every sprint plan ever run |

---

## The State Machine

```
created → spec_writing → spec_approval* → architecting → building
       → auditing → verifying → completion_approval* → shipped

* = HITL gate (Akua signs off)
Rejected exits from any state.
```

## The 5-AI Loop

| Role | Responsibility |
|------|---------------|
| Spec Writer | Turns a Job Card into a full SPEC artifact |
| Architect | Produces a build PLAN from the spec |
| Builder | Implements the plan |
| Auditor | Verifies the build matches the plan |
| Verifier | Runs GATE7 behavioral tests, signs completion |

All five roles run in autonomous loop. They escalate via `escalate_to_hitl` only when genuinely blocked.

---

## Org Chart

```
Akua (CEO)
  └── JARVIS (CTO / Tech Supervisor)
        └── AGtech MCP Server  ← the mechanism
              └── 5-AI Loop
                    └── Job Cards
```

Akua sets direction. JARVIS triages everything. The loop ships.
