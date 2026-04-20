---
name: feature-workflow
description: Interactive end-to-end feature development workflow that gathers missing context, plans before coding, implements test-first, closes the review/verification loop, and records project-specific pitfalls so the same mistake is not repeated.
origin: ECC
---

# Feature Workflow

Use this workflow when a feature should move through a complete delivery loop instead of an ad-hoc sequence of prompts.

It is the orchestration layer for:
- discovery
- planning
- TDD implementation
- review and verification
- milestone handoff
- project-scoped anti-repeat notes

This workflow does not replace `plan`, `tdd-workflow`, `code-review`, or `verification-loop`. It coordinates them in the right order.

## When to Activate

- Adding a non-trivial feature
- Changing user-visible behavior across multiple files
- Implementing a feature where requirements are still partly unclear
- Working on a feature that may benefit from optional multi-agent exploration
- Any task where you want a durable handoff and a record of pitfalls to avoid next time

## Inputs to Collect

Before planning, make sure these are known:
- **Feature request** — what needs to be built
- **Current context** — relevant files, current implementation shape, known errors, limitations
- **Constraints** — performance, compatibility, delivery limits, forbidden changes

If any of these are missing, ask for them explicitly.
Ask for missing inputs **one block at a time** in this order:
1. `Feature request`
2. `Current context`
3. `Constraints`

Do not ask for all three in one message unless the user explicitly requests a single combined template.
After the user answers one block, briefly confirm what was learned, then ask only for the next missing block.
Do not move into planning until all three blocks are sufficiently captured.

Do not ask for facts that can be discovered by reading the repo first.

## Phase 0: Read Existing Durable Context

Before asking the user to repeat themselves, inspect these if they exist:
- `.claude/plan/feature-workflow-pitfalls.md`
- `.claude/plan/<feature-slug>.md`

Use them to build a short **Known Pitfalls Checklist** before implementation starts.

Each checklist item should answer:
- what failed before
- what symptom or trigger identifies the risk
- what guardrail prevents it
- what verification proves it is avoided

If no durable context exists yet, continue normally.

## Phase 1: Discovery and Clarification

1. Read the feature request carefully.
2. Explore the relevant code paths before asking broad questions.
3. Restate the current understanding of:
   - feature goal
   - user-facing success criteria
   - impacted modules
   - risks and dependencies
4. List all unclear points.
5. For each unclear point, provide:
   - the current assumption
   - the risk if the assumption is wrong
   - whether it changes behavior, API shape, UX flow, data model, or security
6. Stop and ask the user to confirm high-impact decisions before coding.

Do not make silent assumptions on ambiguous behavior.
When gathering missing baseline inputs, prefer a short conversational sequence:
- first ask for `Feature request`
- then ask for `Current context`
- then ask for `Constraints`

Avoid dumping a full intake questionnaire when one focused question would move the workflow forward.

## Phase 2: Plan Before Code

Apply the `plan` workflow discipline:
- restate requirements
- identify dependencies
- identify affected modules
- identify acceptance criteria
- break implementation into small steps

Do not write production code until the plan is clear.

After planning, write or update `.claude/plan/<feature-slug>.md` with:
- feature request
- current context
- constraints
- confirmed decisions
- open questions
- implementation plan
- known pitfalls checklist
- next step

## Phase 3: Decide Whether Multi-Agent Is Worth It

Do not enable multi-agent work by default.

Offer it only when the task is meaningfully splittable, such as:
- codebase exploration can happen in parallel with docs verification
- independent backend / frontend / docs slices exist
- one review pass can run in parallel with non-overlapping implementation

When it is worth it, explicitly ask the user whether to enable it.

Recommended role mapping in Codex:
- `ecc_explorer` — real execution path and integration-point discovery
- `ecc_docs_researcher` — framework/API/release-note verification
- `ecc_reviewer` — correctness, security, regression, and missing-test review

If the user does not opt in, continue in a single-agent flow.

## Phase 4: Implement with TDD

Hand off implementation to `tdd-workflow`.

Required sequence:
1. list the tests to add
2. write failing tests first
3. confirm the intended RED state
4. implement the minimal code to make tests pass
5. refactor safely
6. confirm coverage expectations

Add `documentation-lookup` when framework or API behavior must be verified against current docs.

Add `security-review` when the feature touches:
- authentication
- authorization
- secrets
- file upload
- payments
- API endpoints
- untrusted user input

## Phase 5: Close the Review / Verification Loop

After implementation, do not stop at a single pass.

Run the loop:
1. `code-review`
2. fix critical/high findings
3. `verification-loop`
4. fix failing gates
5. rerun review and verification if material changes were made

Exit only when one of these is true:
- no critical/high review findings remain and verification is green
- the workflow is blocked by an unresolved product or technical decision

To avoid unbounded churn, limit the fix-and-recheck loop to **3 rounds**.

If the work still does not converge after 3 rounds:
- stop
- summarize remaining blockers
- ask for human decision instead of continuing blindly

## Phase 6: Milestone Persistence

Persist durable context only at logical milestones:
- after plan approval
- after final verification
- when the session is getting crowded and needs a clean handoff

At those milestones, update `.claude/plan/<feature-slug>.md` with:
- current status
- confirmed decisions
- unresolved questions
- what changed since the previous checkpoint
- exact next step

If the feature reveals a reusable failure pattern, append it to `.claude/plan/feature-workflow-pitfalls.md`.

## Pitfall Record Format

Use this compact structure for each pitfall:

```markdown
## <short label>
- Trigger: <what situation causes the mistake>
- Failure: <exact bug / regression / wasted step>
- Guardrail: <what to check before acting>
- Verification: <command or evidence that proves it is fixed>
```

Record only durable, evidence-backed mistakes. Do not dump raw chat history.

## Output Contract

The final response should include:
1. implementation summary
2. files changed
3. tests added or updated
4. review findings or final review verdict
5. verification results
6. docs updated or not updated, and why
7. handoff / pitfalls updated or not updated, and why

## Example Invocation

```text
Use the `feature-workflow` skill.
Communicate in Chinese.

Feature request:
[describe the feature]

Current context:
[describe relevant files, current behavior, errors, or limitations]

Constraints:
[describe performance, compatibility, delivery, or no-touch limits]
```
