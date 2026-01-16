---
name: skill-retro
description: Triage and iterate on Codex skills based on feedback. Use when the user asks for "/skill复盘" or "/迭代清单", or when Codex detects existing skills are insufficient and needs to propose updates or a new skill while preventing skill bloat. Requires asking for user approval before creating or updating any skill files under C:\Users\10758\.codex\skills.
---

# Skill Retro

## Overview

Consolidate multi-skill feedback, decide update vs new skill vs no change, and produce an actionable iteration checklist. Always prioritize updating existing skills and ask for explicit approval before writing skill files.

## Workflow Decision Tree

### 1) Collect context and feedback

- Identify tasks and outputs that failed or required manual fixes.
- List which skills were used or should have been used.
- Ask for missing examples only if needed to disambiguate.

### 2) Check for fit with existing skills (anti-bloat)

- Scan available skill descriptions for the closest match.
- If the scope is the same domain or workflow family, choose update over new skill.
- If the scope is adjacent but separable, prefer adding a reference or script first.
- Only consider a new skill when the scope is clearly distinct and reusable.

### 3) Classify outcome

- Update existing skill: refine workflow, add decision rules, add references/scripts/assets.
- Create new skill: distinct domain/workflow, high reuse, cannot be cleanly merged.
- No change: one-off preference or not reusable.

### 4) Require approval before changes

- Present a short rationale, target paths, and a minimal change list.
- Ask: "Proceed to update/create skill files under C:\Users\10758\.codex\skills?"
- Do not write any skill files until the user says yes.

### 5) Produce outputs

- Iteration checklist (actionable, ordered).
- If new skill is recommended, include a draft name and trigger phrase list.
- Provide acceptance criteria for each change.

## Decision Heuristics

- Update existing skill if: same domain, same workflow family, or only missing edge cases/output format.
- Create new skill only if: distinct workflow, likely reusable (>=2 future uses), and would make the existing skill too broad.
- Avoid bloat: prefer 1 small update over 1 new skill; suggest merging or retiring overlapping skills.

## Output Templates

### Iteration Checklist

- Skill: <existing-skill-name or new-skill-name>
- Action: update | new | no-change
- Rationale: <1-2 lines>
- Changes: <SKILL.md / references / scripts / assets>
- Acceptance: <how to verify>
- Priority: P0/P1/P2

### Proposed New Skill (only when needed)

- Name: <kebab-case>
- Description: <trigger and when to use>
- Scope boundary: <what it does and does not do>
- Trigger phrases: <user intents>
- Resources: <scripts/references/assets if any>

## Notes

- Keep SKILL.md concise; move bulky examples into references when needed.
- Use the skill-creator process if creating a new skill (init, edit, validate, package when requested).
