# HITL Gates — What Akua Sees (and What She Doesn't)

The AI loop is autonomous between gates. Akua is not a task manager — she is a decision-maker at two defined inflection points.

---

## The Two Gates

### Gate 1: Spec Sign-Off (`spec_approval`)

**What Akua reviews:**
- The SPEC artifact the Spec Writer AI produced
- Does the problem statement match her intent?
- Are the acceptance criteria correct and complete?
- Is the scope fence tight enough? (What's explicitly out of scope?)
- Is the regression guard correct? (What must not break?)

**What she does NOT review:**
- Implementation details (that's the Architect's job)
- Code (nothing has been written yet at this stage)
- Estimates or timelines

**Decision options:**
- `approved` → card advances to `architecting`
- `rejected` → card returns to spec_writing with her note
- (no decision needed) → card stays at `spec_approval`; AI does NOT proceed

**Time expectation:** Akua reviews specs same-day. If she can't, the card waits.

---

### Gate 2: Completion Sign-Off (`completion_approval`)

**What Akua reviews:**
- The VERIFY artifact (GATE7 results)
- The AUDIT artifact (build vs plan compliance)
- Does the shipped behavior match the original acceptance criteria?
- Any regressions found?

**What she does NOT review:**
- The code diff (unless she wants to)
- Architecture decisions (those were approved at Gate 1)
- Test output line-by-line (the AI auditor summarizes)

**Decision options:**
- `approved` → card advances to `shipped`; branch merges
- `rejected` → card returns to `building` or `verifying` with her note

---

## What the AI Loop Handles Without Akua

Everything between the two gates is AI-to-AI:

| Transition | Who decides |
|-----------|-------------|
| spec_writing → spec_approval | Spec Writer AI (when spec is ready) |
| spec_approval → architecting | **Akua** ← gate |
| architecting → building | Architect AI |
| building → auditing | Builder AI |
| auditing → verifying | Auditor AI |
| verifying → completion_approval | Verifier AI (when GATE7 passes) |
| completion_approval → shipped | **Akua** ← gate |

---

## Escalations (Escalation Inbox)

An escalation is different from a gate. It's an unexpected question the AI loop cannot answer:

- The spec contradicts a previous decision
- A dependency is missing or broken
- The scope fence would require changing something Akua flagged as sensitive
- GATE7 fails in a way that requires a product decision (e.g., is this the intended behavior?)

Escalations appear in the **Escalation Inbox** (`/hitl`). They are rare by design. If the AI is escalating frequently, it means the spec was incomplete — the fix is better specs, not more HITL touch.

---

## The Anti-Patterns

**Akua should never have to:**
- Review every state transition
- Approve intermediate build decisions
- Watch the AI work
- Unblock the loop because a credential is missing (those live in credentials.md)
- Re-explain the original intent (that's what the SPEC is for)

**If the loop asks Akua something she already specified in the spec, the spec is wrong, not Akua.**
