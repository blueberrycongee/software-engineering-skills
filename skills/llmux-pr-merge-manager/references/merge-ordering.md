# Merge Ordering Heuristics

Use these rules to decide the best merge sequence for multiple PRs.

## 1) Dependency-first

- Identify explicit dependencies in PR descriptions, linked issues, or branch names.
- Merge prerequisite PRs before dependent ones.
- If two PRs touch the same files, prefer the one that unblocks others.

## 2) Risk and size

- Prefer small, low-risk, well-tested PRs early to reduce conflicts.
- Defer large refactors or risky changes until after supporting groundwork lands.
- Separate mechanical changes (renames, formatting) from behavior changes when possible.

## 3) Compatibility sequencing

- For breaking changes, merge backward-compatible groundwork first.
- Land server-side compatibility before client or consumer changes.
- For schema changes, merge non-breaking migrations before removing old fields.

## 4) Operational impact

- Prioritize fixes for CI failures, security, or production-impacting bugs.
- Merge docs or tooling updates early if they reduce future review overhead.

## 5) Practical conflict minimization

- Group PRs by area to avoid repeated conflicts.
- If a PR is stale, rebase it after upstream merges before approving.
