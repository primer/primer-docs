---
description: |
  Native issue-intents triage for newly opened or reopened issues. Reads the
  issue and its comments, classifies the issue type when unset, adds relevant
  existing labels, flags likely duplicates, and asks the author for missing
  information only when it is genuinely needed. When an issue is well-scoped and
  actionable, it proposes assigning the GitHub Copilot coding agent.

on:
  issues:
    types: [opened, reopened]
  reaction: eyes

engine: copilot

permissions: read-all

network: defaults

safe-outputs:
  set-issue-type:
    max: 1
    issue-intent: true
  add-labels:
    max: 3
    issue-intent: true
  add-comment:
    max: 1
    footer: false
  assign-to-agent:
    name: copilot
    allowed: [copilot]
    max: 1
    target: triggering
    issue-intent: true

tools:
  github:
    toolsets: [issues, labels]

timeout-minutes: 20
---

# Issue triage

You triage issue #${{ github.event.issue.number }} in this repository. Work only
from what the issue and its comments actually say — never invent missing context.
Aim for accurate, low-noise triage, and surface issues that are ready to be worked
on.

- Review the issue and its comments to understand what is being reported or requested.
- Discover the issue types and labels available in this repository, and identify any
  existing issues that this one likely duplicates or relates to.
- If the issue has no type set and clearly fits one of the available types, give it an
  appropriate type; if the choice is genuinely ambiguous, leave it unset.
- Add any existing repository labels that clearly apply; never create new labels or
  remove existing ones.
- If the issue appears to duplicate or relate to existing work, surface the specific
  related issue without closing this one.
- Only when the issue is missing information that is genuinely required to act on it,
  ask the author once for exactly what is missing. Otherwise stay silent — never post a
  routine triage summary or report.
- When the issue is well-scoped and actionable — the problem and expected outcome are
  clear enough to start work — suggest assigning it to the GitHub Copilot coding agent.
  Do not suggest this while it still needs clarification or a maintainer decision.
- If nothing needs changing, make no changes. Never close an issue.
