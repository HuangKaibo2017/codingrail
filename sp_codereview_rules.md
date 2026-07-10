# Code Review Rules (MECE, SSOT)

> Best code is code never written. 

SSOT for all review rules. Bodies in `rules/`, one file per section.

***

## §0 Decision Flow

Protocol selects depth+rules. Load only chosen stage. First match wins.

### Step 1 — Security Overlay (always first)

**WHEN** diff touches: shell/concat-input | creds/secrets | deserial-untrusted | raw SQL | dyn-eval | network+user-endpoints

**THEN** force R-3-6 R-3-7 R-4-1 R-4-2 R-4-3 R-4-4 → **Full Review** (overrides any Lite match).

***

### Step 2 — Depth Selection

**Lite Review** :: self-check | <50 LOC + no I/O/dep | hotfix/typo/rename/refactor | `size: small`

TOON\[5]{section,load,rules}:
Intent,rules/intent-scope.md,R-1-1 R-1-2
Reuse,rules/reuse-dry.md,R-2-1 R-2-2
Correctness,rules/correctness-safety.md,R-3-2 R-3-6 R-3-7
Simplify,rules/simplification.md,R-6-1 R-6-3
Maintain,rules/maintainability.md,R-5-5

Walk: Intent→Reuse→Correctness→Simplify→Maintain. Per rule: read body, check Trigger. P0 fail→BLOCK. N/A→skip. Output `net: <N> fixes.`

**Full Review** (default) :: Security forced | ≥50 LOC | `size: large` | no signal | in doubt

TOON\[6]{section,load,rules}:
§1 Intent,rules/intent-scope.md,R-1-1..R-1-4
§2 Reuse,rules/reuse-dry.md,R-2-1..R-2-6
§3 Correctness,rules/correctness-safety.md,R-3-1..R-3-7
§4 Resilience,rules/resilience-observability.md,R-4-1..R-4-4
§5 Maintain,rules/maintainability.md,R-5-1..R-5-5
§6 Simplify,rules/simplification.md,R-6-1..R-6-4

Walk §1→§6. Per rule: read body, check Trigger → Pass/Fail/N/A. Fail+P0→BLOCK. Fill §7 Gate (all cells Pass/Fail/Needs Follow-up). Output:

```
## Review (stage: Full)
| Rule | Status | Note |
|---|---|---|
| R-x-y | Pass/Fail/N/A | one-line |

### BLOCK (P0): R-x-y reason.
### Needs Follow-up: R-x-y reason → suggest pointer.

### PR Gate
| Area | Status |
|---|---|
| Scope | Pass/Fail/Needs Follow-up |
| ... | ... |

net: <N> fixes required.
```

Clean → `net: 0 changes required.`

***

### Step 3 — Diff-Aware Pre-Scan (Full only)

Scan diff. Match → force-apply rule if not active.

TOON\[20]{signal,rule}:
"File additions outside declared scope",R-1-1
"Added interface/abstract/protocol/trait",R-1-3
"Added flag/config/branch w/ 1 ref",R-1-2
"New format\*/build\*/parse\*/convert\* fn",R-2-1
"New dependency or import",R-2-2 R-2-3
"New fn >5 lines w/o relevant imports",R-2-3
"Two fns w/ similar structure in diff",R-2-6
"Error translation Specific→Generic",R-3-1
"catch/except w/o re-raise",R-3-2
"Shell/command + string concat",R-3-6 + Step1
"Dyn-eval or deserial untrusted",R-3-6 + Step1
"Resource acquire w/o explicit close",R-3-7
"Relative path / CWD-dependent path",R-3-4
"Signature change w/ ≥3 call sites",R-3-3
"Timestamp ID @ second precision",R-4-1
"Fn name: retry/regenerate/rebuild",R-4-3
"import inside function body",R-5-5
"Param typed bare container dict/map/obj",R-5-3
"Dynamic field inject on data class",R-5-3
"Identifier: get\_data/process/handle/do\_",R-5-2

***

## §1 Intent & Scope

*Whether code should exist.* → [rules/intent-scope.md](rules/intent-scope.md)

TOON\[4]{rule,title}:
R-1-1,Scope alignment
R-1-2,YAGNI — no speculative capability
R-1-3,No premature abstraction
R-1-4,Dependency direction

***

## §2 Reuse & DRY

*Whether existing code already does this.* → [rules/reuse-dry.md](rules/reuse-dry.md)

TOON\[6]{rule,title}:
R-2-1,Search codebase before writing
R-2-2,Stdlib first
R-2-3,Native platform & dep first
R-2-4,Reuse existing state
R-2-5,SSOT for constants
R-2-6,Consolidate near-duplicate logic

***

## §3 Correctness & Safety

*Whether code behaves correctly.* → [rules/correctness-safety.md](rules/correctness-safety.md)

TOON\[7]{rule,title}:
R-3-1,Preserve exit codes & exception semantics
R-3-2,Re-raise after logging — never swallow
R-3-3,Backward-compatible signatures
R-3-4,Validate CWD assumptions
R-3-5,Audit legacy callers when extending
R-3-6,Command/shell injection safety
R-3-7,Resource cleanup

***

## §4 Resilience & Observability

*Whether code survives stress.* → [rules/resilience-observability.md](rules/resilience-observability.md)

TOON\[4]{rule,title}:
R-4-1,Concurrency-safe identifiers
R-4-2,Graceful I/O degradation
R-4-3,Idempotency for retry paths
R-4-4,Observability fields in error paths

***

## §5 Maintainability & Clarity

*Whether code can be understood/changed.* → [rules/maintainability.md](rules/maintainability.md)

TOON\[5]{rule,title}:
R-5-1,Single responsibility
R-5-2,Self-explanatory names
R-5-3,Explicit structures over bare containers
R-5-4,Document non-obvious runtime contracts
R-5-5,Module-level imports

***

## §6 Simplification & Deletion

*What should not exist.* → [rules/simplification.md](rules/simplification.md)

TOON\[4]{rule,title}:
R-6-1,Delete dead code
R-6-2,Scan for stdlib/native/dep replacements
R-6-3,Shrink verbose logic
R-6-4,Wrapper/passthrough modules → delete

***

## §7 PR-Level Review Gate

All cells: Pass / Fail / Needs Follow-up.

TOON\[13]{area,rules,question}:
Scope,R-1-1 R-1-2,Solves stated problem only? No speculative/out-of-scope?
Abstraction,R-1-3,Every abstraction paid for by ≥2 consumers?
Reuse,R-2-1..R-2-3,Searched helpers/stdlib/deps before new code?
Constants,R-2-5,Shared values in single source of truth?
Behavior,R-3-1 R-3-3,Exit semantics/exceptions/caller behavior preserved?
Paths,R-3-4,File/resource paths work regardless of CWD?
Security,R-3-6,Shell/command/SQL parameterized not concatenated?
Resources,R-3-7,Every acquired resource guaranteed released?
Resilience,R-4-1..R-4-3,IDs collision-resistant I/O resilient retries idempotent?
Observability,R-4-4,Error paths log operation+entity+scope+exception?
Maintainability,R-5-1 R-5-3,Each fn single-purpose explicit data contracts?
Names,R-5-2,Identifiers convey purpose w/o reading body?
Deletion,R-6-1..R-6-3,Dead code removed hand-rolled→stdlib where possible?

***

## §8 Priority Summary

TOON\[4]{priority,rules,action}:
P0,R-3-1 R-3-2 R-3-4 R-3-6 R-3-7 R-4-1,Must fix — merge blocker
P1,R-1-1 R-1-2 R-2-1 R-2-5 R-3-3 R-4-2\~R-4-4,Should fix — strongly recommended
P2,R-1-3 R-2-2 R-2-3 R-2-6 R-5-1 R-5-3 R-5-5 R-6-1\~R-6-3,Improve — merge w/ tracking
P3,R-1-4 R-2-4 R-5-2 R-5-4 R-6-4,Separate PR — non-blocking

***

## Boundaries (NOT covered)

- Style/formatting → project formatter
- Static types → project type checker
- Test design → test plan (here: tests exist+pass only)
- Performance → profiler/benchmark quantified regression only
- Docs prose → docs pipeline (here: inline contracts R-5-4 only)

Review complements automation; never duplicates.
