# §1 Intent & Scope

*Whether code should exist — before correctness/style.*

R-1-1 **Scope alignment** := Solve only stated problem; flag scope creep.
  Why: Out-of-scope inflates review surface + risk.
  Trigger: file additions outside ticket's scope/touched paths.

R-1-2 **YAGNI — no speculative capability** := If flag/config/branch/abstraction has zero consumers, flag it.
  Why: Speculative flexibility rots into load-bearing dead code.
  Trigger: new option/flag/abstraction w/ zero callers or 1 ref (self).

R-1-3 **No premature abstraction** := No interface/abstract/factory w/ single impl. No abstraction before 2nd use case.
  Why: Indirection w/o proven payoff = pure overhead.
  Trigger: new Abstract*/interface/protocol/trait/factory where only 1 concrete impl exists.

R-1-4 **Dependency direction** := High-level/policy must not depend on low-level impl. Dependencies flow toward stable abstractions.
  Why: Inverted deps create cycles, block independent replacement.
  Trigger: cross-layer import where shared/policy imports from specific adapter/impl.
