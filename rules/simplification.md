# §6 Simplification & Deletion

*What should not exist — final pass.*

R-6-1 **Delete dead code** := Remove unused fns, unreachable branches, commented-out blocks, assigned-but-never-read vars. VCS preserves history.
  W: Dead code misleads, passes stale security scans, accumulates.
  T: any code element w/ zero references after diff applied.

R-6-2 **Scan for stdlib/native/dep replacements** := For non-trivial logic (>3 lines algorithmic, not boilerplate): verify stdlib/installed-dep/platform provides it in fewer lines.
  W: Hand-rolled reimplementations = maintenance burden w/ zero upside.
  T: Full Review always active. Climb: (1) stdlib (2) declared dep (3) runtime built-in (4) OS feature. Any rung holds → fail w/ replacement.

R-6-3 **Shrink verbose logic** := Manual loops building collections, conditionals assembling flags, multi-step transforms → prefer idiomatic one-liner.
  W: Idiomatic = shorter, better-tested, instantly recognized by language community.
  T: logic block whose purpose is standard transform that stdlib/core expresses in fewer lines.

R-6-4 **Wrapper/passthrough modules → delete** := Module whose purpose is re-exporting symbols w/o added logic/transform/abstraction.
  W: Passthrough layers complicate grep, stack traces, build graphs — indirection w/ zero value.
  T: module (index/barrel/init) whose effective content after removing exports is empty/trivial.
