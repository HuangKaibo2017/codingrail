# §3 Correctness & Safety

*Whether code behaves correctly — data integrity, errors, behavioral security.*

R-3-1 **Preserve exit codes & exception semantics** := When catching/re-raising, don't downgrade Specific→Generic error. Original type+exit semantics are contract.
  W: Callers/automation depend on specific types. Downgrading breaks orchestration/retry/monitoring.
  T: catch SpecificError → throw/raise GenericError(...) discarding original type.

R-3-2 **Re-raise after logging — never swallow** := After log, exception must propagate unless explicitly terminal handler w/ documented suppress reason.
  W: Swallowed errors corrupt observability — system appears healthy while failing.
  T: catch/except block not ending in re-raise, error-value return, or explicit terminal action.

R-3-3 **Backward-compatible signatures** := New params must have safe defaults; existing callers work unmodified.
  W: Silent call-site breakage harder than compile error.
  T: signature change (add/remove/reorder param, change return) on fn w/ ≥3 call sites.

R-3-4 **Validate CWD assumptions** := Never assume CWD=project root/home/specific location. Use absolute/anchored paths.
  W: CWD-dependent code breaks in cron/CI/IDE runners/containers.
  T: file-open/path-resolution w/ relative path, no explicit root anchor.

R-3-5 **Audit legacy callers when extending** := When modifying shared fn, check all existing callers: does change degrade output/behavior?
  W: Enhancements to shared code silently corrupt established call paths.
  T: modification to fn w/ both legacy callers (unchanged) + new callers (added).

R-3-6 **Command/shell injection safety** := Use argument-list APIs, never string-concat into shell. Quote display. Document trusted/untrusted inputs.
  W: String concat into shell = #1 injection vector across all languages.
  T: shell/command/SQL where user/external input reaches command string w/o parameterization/sanitization.

R-3-7 **Resource cleanup** := Every acquired resource must have guaranteed cleanup. Use idiomatic: context managers, try-with-resources, defer, RAII.
  W: Leaks exhaust fd/connections/disk in production.
  T: resource acquire w/o visible guaranteed release/close in all paths (incl. errors).
