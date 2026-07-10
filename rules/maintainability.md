# §5 Maintainability & Clarity

*Whether code can be understood and changed.*

R-5-1 **Single responsibility** := Each fn does one thing. Separate compute from I/O, data transform from side effects, logic from orchestration.
  W: Mixed concerns block isolated testing, reuse, comprehension.
  T: fn body containing both explicit I/O + non-trivial computation/branching.

R-5-2 **Self-explanatory names** := Identifiers communicate what thing IS (nouns) or DOES (verbs). Avoid: data/info/result/process/handle/do_/get_ w/o qualifier.
  W: Names read far more than written. Cryptic name → every reader reverse-engineers intent.
  T: identifier matching generic patterns where context reveals more specific purpose.

R-5-3 **Explicit structures over bare containers** := Known-shape data → named type. No bare dict/map/untyped objects across module boundaries.
  W: Bare containers lose IDE autocomplete, static analysis, self-documentation.
  T: fn param/return as generic container w/o shape, or dynamic attr injection on data object.

R-5-4 **Document non-obvious runtime contracts** := If fn accepts/returns complex structure not fully captured by type system, document keys/types/invariants.
  W: Undocumented contracts become indecipherable — only author knows contents.
  T: fn accepting/returning container w/ ≥4 fields, meaning not fully described by type alone.

R-5-5 **Module-level imports** := Imports at top of module, not inside fns/conditionals, unless for circular-dependency break or conditional optional-dep load.
  W: Scattered imports hide true dependency graph from readers, linters, analysis tools.
  T: import/require/include inside function body or conditional block.
