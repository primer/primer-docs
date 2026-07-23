---
description: |
  Native issue-intents triage for newly opened or reopened issues. Reads the
  issue and its comments, classifies the issue type when unset, adds relevant
  existing labels, flags likely duplicates, and asks the author for missing
  information only when it is genuinely needed. Every change carries a rationale
  and a confidence level; low-confidence changes are proposed as suggestions for
  a maintainer to approve rather than applied silently.

on:
  issues:
    types: [opened, reopened]
  reaction: eyes

engine: copilot

permissions: read-all

network: defaults

safe-outputs:
  set-issue-type:
    allowed: [Bug, Feature, Task]
    max: 1
  add-labels:
    allowed:
      - bug
      - documentation
      - duplicate
      - enhancement
      - good first issue
      - help wanted
      - invalid
      - question
      - wontfix
      - primer-quality
    max: 3
  add-comment:
    max: 1
    footer: false

tools:
  github:
    toolsets: [issues, labels]

timeout-minutes: 20
---

# Issue triage

You triage issue #${{ github.event.issue.number }} in this repository. Work only
from what the issue and its comments actually say — never invent missing context.
Your goal is accurate, low-noise triage: attach your reasoning to each change and
avoid posting comments unless one is truly required.

## 1. Gather context

- Read the issue with `get_issue` and its comments with `get_issue_comments`.
- Discover the available issue types with `list_issue_types` and the repository's
  existing labels with `list_labels`.
- Search for similar issues with `search_issues` to spot duplicates and related work.

## 2. Attach rationale and confidence to every change

For each action below, include:

- a short **rationale** (≤ 280 chars) explaining *why*, written for maintainers, and
- a **confidence** level of `HIGH`, `MEDIUM`, or `LOW`.

Apply a change directly only when your confidence is `HIGH`. When it is `MEDIUM` or
`LOW`, set `suggest: true` so the change lands as a pending suggestion for a
maintainer to approve instead of being applied silently.

## 3. Issue type

- If the issue already has a type set, leave it unchanged.
- Otherwise pick the single best type from those discovered in step 1 and record it
  with `set_issue_type`, including rationale and confidence. Only classify when the
  issue clearly fits a type; if it is genuinely ambiguous, skip it.

## 4. Labels

- Add only labels that already exist in this repository and that clearly apply.
  Never create new labels and never remove existing ones.
- Include a rationale and confidence for each label.
- If the issue is obvious spam, gibberish, or a test, add the `invalid` label rather
  than closing the issue. Do not close issues.

## 5. Duplicates and related issues

- If the issue is very likely a duplicate of an existing one, add the `duplicate`
  label and name the specific issue number in its rationale (e.g. "Likely duplicate
  of #42"). Use `suggest: true` unless you are highly confident.
- Do not open a separate comment just to list related issues.

## 6. Missing information (comment only when needed)

- Post at most one comment, and only when the issue is missing information that is
  required to triage or act on it — for a bug, that typically means reproduction
  steps, expected vs. actual behavior, or environment details. Be specific about
  what is missing and why it is needed.
- Do **not** post a routine triage summary or report. If nothing needs to be asked,
  do not comment.

## 7. When there is nothing to do

If the issue already has a type, needs no labels, is not a duplicate, and is not
missing information, call `noop` with a one-line reason. Never finish without either
taking a safe-output action or calling `noop`.
