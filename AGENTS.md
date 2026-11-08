# app-itonami-fixtures — agent notes

## CI is an oracle, not a gate

`.github/workflows/ci.yml` runs the JVM / Clojure (and, where noted, babashka) tests as an
**oracle** job: a reference measurement only. This follows the CI exception in the workspace
`AGENTS.md` (owner decision 2026-11-07, JUN-579).

- Job names start with `oracle-` and each job prints an "ORACLE job" notice.
- It is **not a required check**. No native build, gate or packaging path depends on it, and
  a native result must never silently fall back to it.
- There is no Amu selfhost native gate for this repo yet. That is **blocked**, tracked in JUN-1007.
- The job runs `kami.app-fixtures-test` through the monorepo `tools/cljk_loader.cljk` (pinned to the
  same sha as `deps.edn`), which exposes `.cljk` to the JVM as `.cljc`. Local result 2026-11-08
  (OpenJDK 21): 8 tests / 26 assertions, 0 failures, matching kbb. `clojure -M:test` alone cannot read
  `.cljk`. clj-kondo was dropped: it lints no `.cljk` files, so it measured nothing.
- Some oracle jobs may fail for reasons unrelated to a change (for example a dependency repo
  that cannot be cloned). Treat a failure as a measurement to report, not as a merge condition.
