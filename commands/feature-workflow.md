---
description: Legacy slash-entry shim for the feature-workflow skill. Prefer the skill directly.
---

# Feature Workflow Command (Legacy Shim)

Use this only if you still invoke `/feature-workflow`. The maintained workflow lives in `skills/feature-workflow/SKILL.md`.

## Canonical Surface

- Prefer the `feature-workflow` skill directly.
- Keep this file only as a compatibility entry point.

## Arguments

`$ARGUMENTS`

Treat `$ARGUMENTS` as the initial feature request when present.

## Delegation

Apply the `feature-workflow` skill.
- Collect or confirm `Feature request`, `Current context`, and `Constraints` before planning.
- Explore the repo before asking broad questions.
- Use `plan` discipline before coding.
- Delegate implementation to `tdd-workflow`.
- Offer multi-agent help only when the task is meaningfully splittable, and ask before enabling it.
- Close the loop with `code-review` and `verification-loop`.
- Read and update project-level handoff and pitfall notes under `.claude/plan/` at logical milestones.
