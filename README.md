---
name: codingrail
description: "AI coding guardrail — sp_coding_rules (implementer playbook) :: sp_codereview_rules (reviewer checklist) :: SSOT rules :: language-agnostic"
license: MIT
metadata:
  author: Aaron Huang (aaron.kb.h@gmail.com)
  version: "1.0.0"
---

# codingrail := Design Rationale
> Notation: `:=` define | `->` maps-to | `::` chain | `∎` end. **sp** = system prompt. ∎

## Overview

Two docs, one SSOT. `sp_coding_rules.md` -> implementer :: `sp_codereview_rules.md` -> reviewer :: rules share `rules/` SSOT

TOON\[2]{doc,role,flow}:
sp\_coding\_rules.md,Implementer,Decision Ladder → While Coding → Self-Review → Anti-Patterns
sp\_codereview\_rules.md,Reviewer,Decision Flow → Lite/Full → Diff Pre-Scan → PR Gate → Priority

## Arch Decisions

### 1. Two roles -> Two docs

Implementer: decision ladder, in-flight checks, anti-patterns. Reviewer: depth protocol, rule ordering, gate table. Merge → one role always filters noise.

### 2. SSOT in `rules/` := no dupes

`sp_coding_rules.md §1` maps to `sp_codereview_rules.md` rules in implementer voice. 4 implementer-only rules (shortcuts, tests, checks, atomic) — unverifiable from diff.

### 3. Rule bodies in `rules/` := index in `sp_codereview_rules.md`

31 rules @ 480 lines inline → waste context. Now: \~200-line index + `rules/` links. Load only active-section per stage. Grouped by content domain.

### 4. Decision Flow := WHEN/USE, not static packs

```
Step 1 — Security: shell/creds/deserial/SQL/dyn-eval/network → force Security + Full
Step 2 — Depth: Lite (self-check|<50|hotfix→10 rules) | Full (forced|≥50|no signal→31 rules + PR Gate)
Step 3 — Pre-Scan: 20 escalation signals → force-apply matched
```

### 5. Language-agnostic

Universal patterns: "shell exec w/ string concat" not `subprocess.run`; "data class/struct/record" not `@dataclass`.

### 6. Ponytail lineage

Preamble: "Best code = code never written." 7-rung Decision Ladder encoded in §2 Reuse + §6 Simplification.

## File Map

TOON\[9]{file,role}:
README.md,this file
sp\_coding\_rules.md,implementer playbook
sp\_codereview\_rules.md,reviewer checklist + decision flow
rules/intent-scope.md,R-1-1..R-1-4
rules/reuse-dry.md,R-2-1..R-2-6
rules/correctness-safety.md,R-3-1..R-3-7
rules/resilience-observability.md,R-4-1..R-4-4
rules/maintainability.md,R-5-1..R-5-5
rules/simplification.md,R-6-1..R-6-4

## Usage

**Implementer:** `sp_coding_rules.md` → climb ladder §0 → §1 table checks → self-review via `sp_codereview_rules.md` Lite.

**Reviewer:** `sp_codereview_rules.md` → §0 stage select → load active `rules/` → walk in order → PR Gate output.

**System designer:** Add rule: (1) `rules/*.md` W+T, (2) index in `sp_codereview_rules.md`, (3) §8 if P0/P1, (4) §0Step2 if Lite changes, (5) `sp_coding_rules.md §1` row if implementer-relevant. Never dup.
