---
name: win-wsl-tdd-workflow
description: "Local development guardrails for this user's Windows host with WSL. Use when making code changes or tests on this machine: always plan before edits, use TDD, enumerate edge cases, add regression tests for bug fixes, and run full CI and lint in WSL (install missing tools as needed)."
---

# Win Wsl Tdd Workflow

## Overview

Enforce a plan-first, TDD workflow on a Windows host while executing tests and CI in WSL. Ensure edge cases are explicitly covered and bug fixes always ship with regression tests.

## Core Workflow

### 1) Confirm environment and paths

Confirm the host is Windows and WSL is available. If the WSL distro is unknown, ask for it.
Use WSL for all test, lint, and CI commands. Convert Windows paths to WSL paths
(e.g., `D:\Desktop\LLMux` -> `/mnt/d/Desktop/LLMux`).
Default WSL distro on this machine is `Ubuntu`. Use `wsl -d Ubuntu` for commands
unless the user specifies a different distro.

### 2) Plan before coding

Always provide a short plan (2-5 steps) before writing or editing code. Do not start
code changes until the plan is presented.

### 3) Use TDD and enumerate edge cases

Write tests first, ensure they fail for the intended reason, then implement the code.
Explicitly list edge cases and include tests for them.

### 4) Bug fixes require regression tests

For any bug fix, add or extend tests that reproduce the bug and prevent regressions.
Do not report the fix as complete without the new/updated tests.

### 5) Run full CI and lint in WSL

Find the repo's canonical commands (Makefile targets, scripts, or CI configs) and
run the full test and lint suite inside WSL. If the repo provides a single "check"
or "ci" target, prefer it. Otherwise run the full set of lint + tests.

## Tooling installation guidance

If any required tool is missing in WSL, install it with the repo's documented method.
If no guidance exists, install via the relevant package manager or language toolchain
and note the commands used. Re-run the full CI and lint after installation.

## Notes for this repo (LLMux)

Use the Makefile as the default entry point when present. The `check` target runs
formatting, vet, lint, and tests. Execute it from WSL in the repo root.
