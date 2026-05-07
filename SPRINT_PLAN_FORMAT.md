# Sprint Plan Format — v0.1

A sprint plan is a time-boxed commitment. One sprint = one set of Job Cards that will be shipped together.

---

## Sprint Header

```markdown
# [SPRINT TITLE]
**Sprint ID:** [uuid from AGtech]
**State:** planned | active | complete
**Dates:** YYYY-MM-DD → YYYY-MM-DD
**Goal:** One sentence. What does done look like?
```

---

## Cards in This Sprint

| ID | Title | Severity | Type | Owner |
|----|-------|----------|------|-------|
| AGT-NNN | ... | high | new_build | Builder AI |

---

## Acceptance Criteria

These are sprint-level, not card-level. The sprint is COMPLETE when:

- [ ] All cards in state `shipped` or `rejected` (no `in-progress` states remain)
- [ ] Akua has signed completion_approval for each card that requires it
- [ ] `npx playwright test` passes on the deployed build
- [ ] No regressions in previously passing tests

---

## Out of Scope

Anything not listed in the Cards table above. If it comes up during the sprint, file a new card for the next sprint.

---

## Lessons Learned (fill at sprint close)

What worked, what didn't, what to carry forward.

---

## Template Usage

1. Copy this file to `sprints/YYYY-WNN-[slug].md`
2. Fill in the header, goal, and card table BEFORE starting any work
3. Akua signs off on the plan (or rejects/reorders cards) before state changes to `active`
4. At close, fill in Lessons Learned
