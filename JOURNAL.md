# PathReview — Module 3 Journal

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/128

**Issue title:** Add a dependency vulnerability scan to the CI pipeline

**Tier:** [ ] Tier 1  [ ] Tier 2  [x] Tier 3

**Problem summary:**
Right now the CI pipeline lints, type-checks, and runs tests, but nothing ever
checks whether the project's third-party dependencies contain *known* security
vulnerabilities. That means a Python package or npm module with a published CVE
can be merged into `main` without anyone noticing. The fix is to add a new job
to `.github/workflows/ci.yml` that runs `pip audit` against the Python
dependencies and `npm audit` against the frontend's `package-lock.json`, and
fails the build when a high-severity (or worse) advisory is found. A successful
fix means every pull request is automatically gated on a clean dependency scan,
so vulnerable packages are caught before merge instead of in production. This
touches only the CI configuration and, optionally, an allow-list/threshold
config — it does not change application code.

**Branch name:** feat/128-dependency-vulnerability-scan

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

---

### "Is this issue right for me?" — checklist reasoning

- **Do I understand what's being asked?** Yes. The ask is concrete: add a CI
  step that runs `pip audit` + `npm audit` and fails on high-severity findings.
  There is no ambiguity about the desired end state.
- **Is the scope bounded?** Yes. The issue names exactly one file to change
  (`.github/workflows/ci.yml`). The change is additive — a new job — so it is
  unlikely to break existing lint/typecheck/test jobs.
- **Do I have (or can I get) the skills?** Yes. It requires reading a GitHub
  Actions workflow and adding a job that invokes two well-documented CLI tools.
  No changes to the Python/React application logic are required.
- **Can I verify the fix?** Yes. Running `pip audit` and `npm audit` locally
  reproduces what CI will do. In fact, `npm audit` on the freshly installed
  frontend already reports 11 advisories (1 critical, 4 high), so I have a
  real, observable signal to build the gating logic against.
- **Scope risks I'm watching:** (1) `npm audit` finding pre-existing high-sev
  issues means the new job would fail the build on day one — I'll need to
  decide between fixing/upgrading those deps, adding a documented allow-list,
  or scoping the failure threshold, and confirm the intended behavior with the
  issue author. (2) `pip audit` needs the dependency set resolved in CI, so I'll
  mirror how the existing jobs install deps (`pip install -e ".[dev]"`).
  This is why the issue is rated Tier 3 / 3–5 hours rather than a trivial one.
- **Note on tier:** This is a Tier 3 issue (higher difficulty than the Tier 1
  recommended for a first contribution). I chose it deliberately because the
  blast radius is small (CI-only, no app code) even though the devops surface
  is more advanced.
