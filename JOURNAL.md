## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/50

**Issue title:** Add a has_tests boolean to the repo analysis output

**Tier:** [ ] Tier 1 

**Problem summary:**
The repo analyzer currently produces an analysis of a repository but doesn't report anything about test coverage, which is a signal recruiters and reviewers care about. This issue asks for a new has_tests boolean to be added to the analysis output, determined by checking for common test indicators — a tests/ or test/ directory, a pytest.ini file, or files matching the test_*.py naming pattern. The fix touches agent/tools/github_tool.py (likely where repo contents are fetched/listed) and agent/tools/repo_analyzer.py (where the analysis result is assembled), so the detection logic needs to plug into how the repo's file tree is already being read. A successful fix means any repo run through the analyzer returns has_tests: true or false accurately, without needing to clone the full repo if that can be avoided.

**Branch name:** feat/50-has-tests-detection

**Setup confirmation:** App runs locally at localhost:5173

**Cohort ledger:** Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** https://github.com/aarohi-agrawal/pathreview/tree/feat/50-has-tests-detection/reproduce_has_tests_bug.py

**Reproduction summary:**
I found that `RepoAnalyzer._detect_tests()` already implements correct test-detection logic and already outputs `has_tests`, but `GitHubTool` never populates the `file_structure` field it depends on — so `has_tests` always evaluates to `False` in the real pipeline. I confirmed this by calling `RepoAnalyzer.parse()` directly with and without a `file_structure` key, showing the detection logic works correctly when given data but is never given real data today.

**PLAN.md link:** https://github.com/aarohi-agrawal/pathreview/tree/feat/50-has-tests-detection/PLAN.md


**Blockers or open questions:**
Need to confirm whether fixing `has_ci` (which has the same root-cause bug) is in scope for this issue or should be a separate PR — will ask in Slack/office hours before finalizing the Week 9 build.

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
Implemented `_get_file_structure()` in `GitHubTool`, which fetches the repo's file tree via GitHub's Trees API and wires it into the metadata dict as `file_structure`. This is the field `RepoAnalyzer._detect_tests()` was already reading but never receiving, so `has_tests` (and `has_ci`) now reflect real repo contents instead of always returning `False`. Added `tests/unit/test_github_tool.py` with 3 tests covering the happy path, graceful fallback on API failure, and existing input-validation behavior. Confirmed via `make check` (181 errors, down from 182 baseline) and `make test-unit` (53 pre-existing failures unchanged, 3 new tests passing) that no regressions were introduced.

**Next steps:**
Manually verify against a couple of real GitHub repos, then open a draft PR and request feedback in Slack.

**Blockers:**
Still deciding whether fixing `has_ci` (same root cause) belongs in this PR or a separate one — will raise in Slack/office hours.
