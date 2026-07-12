# Glossary

**Accepted**
: A submission whose Judge0 status indicates every evaluated hidden test passed.

**Access token**
: Short-lived bearer JWT used to authorize API calls.

**Async judging**
: Target architecture where the API enqueues execution and returns before Judge0
  completes, while workers durably finalize results.

**Blue Eclipse**
: MLBoost's indigo/lavender product palette.

**Controlled beta**
: Limited public release with explicit cohort, scope, quotas, monitoring, known
  limitations, and rollback gates.

**Ensemble Spark**
: MLBoost's original brand mark: three learner signals converging into one
  amplified result.

**Hidden test**
: Testcase whose input/expected output is not disclosed to learners.

**Judge0**
: Isolated code-execution system used to compile/run untrusted submissions.

**Mock fallback**
: Development behavior where failed live requests return bundled sample data.
It must be disabled outside development.

**P0 / P1 / P2**
: Launch priority: blocker, required for general availability, and market/product
completion respectively.

**Refresh session**
: Long-lived httpOnly cookie credential used to validate the session and issue
new access tokens.

**Run**
: Execute code with custom input without saving a submission.

**Submit**
: Persist and judge code against problem testcases.

**SLO**
: Service-level objective for availability, latency, correctness, or recovery.
