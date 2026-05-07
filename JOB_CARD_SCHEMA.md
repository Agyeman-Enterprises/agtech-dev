# Job Card Schema — Canonical v0.1

A Job Card is the atomic unit of work in AGtech. One card = one shippable outcome.

---

## Fields

```yaml
id:            uuid          # system-generated
sprint_id:     uuid | null   # which sprint this belongs to
title:         string        # ≤ 100 chars, imperative mood ("Build X", "Fix Y")
description:   string | null # context, not scope (scope lives in the SPEC artifact)
type:          new_build | issue_fix
severity:      low | medium | high | critical
state:         see state machine below
source:        akua_manual | akuawatch_alert | alrtme_report
assigned_machine: string | null  # which AI is currently working this card
branch:        string | null     # git branch name (set when building starts)
scope_fence:   json | null   # what is explicitly in scope (set during spec)
regression_guard: json | null # what must not break (set during spec)
must_deliver:  json | null   # hard acceptance criteria (set during spec)
created_at:    timestamp
updated_at:    timestamp
deleted_at:    timestamp | null  # soft delete
```

---

## State Machine

```
                    ┌─────────────────────────────────────┐
                    │                                     │
created ──────► spec_writing ──────► spec_approval* ──► architecting
                                           │                   │
                                        rejected            building
                                                               │
                                                           auditing
                                                               │
                                                           verifying
                                                               │
                                                   completion_approval*
                                                               │
                                                     ┌────────┴──────────┐
                                                  shipped             rejected

* = HITL gate
```

### State definitions

| State | Who owns it | What's happening |
|-------|-------------|-----------------|
| `created` | — | Card filed, not yet assigned to AI |
| `spec_writing` | Spec Writer AI | AI writing the SPEC artifact |
| `spec_approval` | **Akua** | HITL gate — Akua reviews spec before any code is written |
| `architecting` | Architect AI | AI producing the build PLAN |
| `building` | Builder AI | Code being written |
| `auditing` | Auditor AI | Verifying build matches plan |
| `verifying` | Verifier AI | Running GATE7 behavioral tests |
| `completion_approval` | **Akua** | HITL gate — Akua reviews before shipping |
| `shipped` | — | Card closed, code deployed |
| `rejected` | — | Card closed without shipping |

---

## Artifacts

Each card accumulates artifacts as it progresses:

| Artifact type | Written by | Content |
|---------------|-----------|---------|
| `spec` | Spec Writer AI | Full specification: problem, acceptance criteria, scope fence, regression guard |
| `plan` | Architect AI | Build plan: files to change, component design, sequence |
| `audit` | Auditor AI | Audit report: plan compliance, deviations, risk flags |
| `verify` | Verifier AI | GATE7 results: which checks passed/failed |
| `gate7` | Verifier AI | The GATE7.txt content for this card |

---

## Title conventions

- Use imperative mood: "Build X", "Fix Y", "Add Z to W"
- ≤ 100 characters
- No jargon that requires the reader to know the codebase
- Good: `Fix OTP timeout in Supabase auth config`
- Bad: `auth stuff`, `update middleware`, `fix the thing`

---

## Severity guide

| Severity | Use when |
|----------|---------|
| `critical` | Production is down or data is at risk |
| `high` | Core user flow broken, no workaround |
| `medium` | Feature broken, workaround exists |
| `low` | Polish, improvement, non-blocking |
