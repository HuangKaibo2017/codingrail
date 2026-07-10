# §4 Resilience & Observability

*Whether code survives stress — concurrency, failure modes, debuggability.*

R-4-1 **Concurrency-safe identifiers** := Generated ID must be unique under concurrent generation. Timestamp-only needs sub-second resolution or random/nonce suffix.
  Why: Same-second collisions → silent overwrites, data loss, constraint violations.
  Trigger: ID based solely on timestamp @ second precision, or monotonic counter w/o collision avoidance.

R-4-2 **Graceful I/O degradation** := Primary-output writes survive partial failure. On destination fail (disk full, permission, fs read-only): fall back to observable alternative — never silently discard.
  Why: RO filesystems/quota/permission changes in prod must not hide primary output.
  Trigger: file/network write producing primary user-visible output w/o fallback path.

R-4-3 **Idempotency for retry paths** := Any operation reachable via retry must be safe to execute N times: no compounding effects, no double-writes, no dup generation w/o dedup.
  Why: Retry storms amplify non-idempotent effects → single failure → cascade corruption.
  Trigger: fn/path named retry/regenerate/rebuild/recover or wrapped by retry middleware.

R-4-4 **Observability fields in error paths** := Every error/retry path must log: operation attempted :: entity that failed :: remaining scope :: exception type w/ context.
  Why: Error logs w/o these 4 fields = unsearchable + unactionable during incidents.
  Trigger: new error-handling block emitting message w/o these fields.
