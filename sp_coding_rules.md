# Coding Rules := Implementer's Playbook

> Best code is code never written. 

Rules govern **how you write code**. Review rules → `sp_codereview_rules.md`.

***

## §0 The Decision Ladder

Climb from bottom. Stop at first rung that holds. Code only at top.

1. Need to exist? (YAGNI)
2. Already in codebase? Grep. Reuse.
3. Stdlib does it? Use it.
4. Native platform? Use it.
5. Installed dep? Use it.
6. One line? Make it so.
7. Only then: minimum code.

Ladder runs **after** understanding. Read, trace, then climb.

**Bug fix = root cause.** Grep all callers. Fix shared fn once. Patch only reported site → siblings still broken.

***

## §1 While Coding — Apply Rules

Check against `sp_codereview_rules.md` (SSOT):

TOON\[9]{trigger,rule,check}:
"Add abstraction/interface/factory",R-1-3,"2nd concrete use case? No → don't"
"Consider new dep",R-2-2 R-2-3,Exhaust stdlib→declared→runtime→OS first
"Add flag/config/code path",R-1-2,Consumer needs this TODAY? No → delete
"Write data transform",R-6-3,Can stdlib do it in fewer lines?
"Write new function",R-5-1,One job. Split compute from I/O
"Name anything",R-5-2,Stranger understands w/o reading body?
"See near-duplicate logic",R-2-6,≥70% overlap → shared helper
"Write same value twice",R-2-5,One named constant. SSOT
"Add passthrough module",R-6-4,Re-exports only → delete

### Implementer-Only Rules

- **Mark shortcuts:** comment ceiling + upgrade path: `# ponytail: single lock. >1k writers → striped lock.`
- **Run tests:** warnings/deprecations/logs are findings. Output pristine.
- **Leave ONE check:** non-trivial → smallest thing that fails. Trivial → skip.
- **Commit atomic:** one thing per commit. Message explains **why**.

***

## §2 After Coding — Self-Review

Self-review via `sp_codereview_rules.md` Lite mode. Fix before formal review.

```
Ladder → Coding Rules → self-review → formal review
(prevent)   (guide)      (detect)      (gate)
```

***

## §3 Anti-Patterns

TOON\[7]{excuse,reality}:
"We'll need it next sprint",You don't know next sprint. Wait.
"Just one small dep",Every dep compounds: risk conflicts breakage.
"Too simple for ladder",Simple hides most unexamined assumptions. 30s.
"I'll clean up next PR",Next PR never comes. Cleanup = fastest compounder.
"Existing code does it",May be wrong. Don't copy mistakes.
"Faster to write myself",Faster for you once. Slower for everyone forever.
"Abstraction makes it testable",Concrete code is testable. Test behavior.
