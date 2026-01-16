---
name: llmux-pr-merge-manager
description: "Review, prioritize, and merge multiple LLMux GitHub PRs using gh CLI. Use when asked to triage PRs, perform enterprise-grade review for distributed open-source microservice standards, decide merge order, and complete merges following open-source best practices."
---

# LLMux PR Merge Manager

## Overview

Handle multiple LLMux PRs end-to-end: triage, review, ordering, and merge. Minimize user interruptions; ask only when a decision requires explicit user choice. Respond in Simplified Chinese after task completion.

## Core Workflow

### 1) Load repo policy and context

Read `CONTRIBUTING.md`, `.github/` workflows, and any CODEOWNERS or merge policy docs. Follow repo-specific rules over this skill if they conflict.

### 2) Ensure gh auth and gather PRs

Use `gh auth status` to confirm auth. If not logged in, ask the user to authenticate.
List open PRs with `gh pr list` and capture base branch, labels, checks, approvals, and dependencies (look for "Depends on" notes).

### 3) Decide merge order

Use `references/merge-ordering.md` to rank PRs by dependency, risk, and impact. Prefer low-risk and dependency-unblocking merges first.

### 4) Review each PR

For each PR:

- Inspect metadata, checks, and approvals with `gh pr view`.
- Read the diff via `gh pr diff` (checkout locally when needed).
- Apply the enterprise review checklist from `references/review-checklist.md`.
- Confirm tests and CI are green or run targeted tests if required by repo policy.

### 5) Act on findings

- If the PR is clean: approve and merge using repo-preferred method (merge/squash/rebase). If unspecified, prefer squash for small PRs and merge commit for multi-commit histories that preserve intent.
- If issues exist: leave actionable review comments and request changes. If the fix is small and safe, prepare a follow-up commit or PR when allowed by policy.

### 6) Finalize

Update any required changelog or release notes if the repo policy expects it. Continue until all eligible PRs are merged or blocked by explicit decisions.

## Communication

Do not provide status updates unless a decision is required (e.g., overrides to required checks, policy conflicts, or risky merges). Once all tasks are complete, reply in Simplified Chinese with a concise outcome and any required user decisions.

## References

- Use `references/review-checklist.md` for the enterprise microservice review checklist.
- Use `references/merge-ordering.md` for merge ordering heuristics.
