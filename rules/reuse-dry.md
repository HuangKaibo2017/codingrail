# §2 Reuse & DRY

*Whether existing code already does this — before writing new.*

R-2-1 **Search codebase before writing** := Grep for helpers/formatters/converters/domain fns before adding overlapping code.
  Why: Duplicate utilities drift/bloat/diverge under different maintenance cycles.
  Trigger: new fn named format*/build*/parse*/convert*/serialize*/deserialize*.

R-2-2 **Stdlib first** := Prefer stdlib over hand-rolled equivalents.
  Why: Stdlib = tested, documented, understood, zero supply-chain risk.
  Trigger: new 3rd-party dep or >3-line hand-roll duplicating known stdlib capability.

R-2-3 **Native platform & dep first** := Before hand-rolling, exhaust: (1) stdlib (2) declared dep (3) runtime built-in (4) OS feature. Write only after.
  Why: Reimplementing what's installed/tested = pure waste.
  Trigger: new fn >5 lines not importing/using relevant available capability.

R-2-4 **Reuse existing state, don't redefine** := Reference loaded config/context/structure; don't rebuild same state from same source.
  Why: Parallel state objects desynchronize under concurrent/sequential mods.
  Trigger: new data structure populated from same source as existing one in diff.

R-2-5 **SSOT for constants** := Hardcoded values in ≥2 locations → one shared constant.
  Why: Dup constants = hidden coupling; changing one leaves other stale.
  Trigger: literal value in both interface default + internal fallback, or ≥2 independent locations.

R-2-6 **Consolidate near-duplicate logic** := Two blocks w/ same inputs → similar outputs must share one implementation.
  Why: Near-duplicates drift → silent behavioral divergence.
  Trigger: two fns/blocks w/ ≥70% structural similarity (same input/output shape, similar steps).
