## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/50

**Issue title:** Add a has_tests boolean to the repo analysis output

**Tier:** [ ] Tier 1 

**Problem summary:**
The repo analyzer currently produces an analysis of a repository but doesn't report anything about test coverage, which is a signal recruiters and reviewers care about. This issue asks for a new has_tests boolean to be added to the analysis output, determined by checking for common test indicators — a tests/ or test/ directory, a pytest.ini file, or files matching the test_*.py naming pattern. The fix touches agent/tools/github_tool.py (likely where repo contents are fetched/listed) and agent/tools/repo_analyzer.py (where the analysis result is assembled), so the detection logic needs to plug into how the repo's file tree is already being read. A successful fix means any repo run through the analyzer returns has_tests: true or false accurately, without needing to clone the full repo if that can be avoided.

**Branch name:** feat/50-has-tests-detection

**Setup confirmation:** App runs locally at localhost:5173

**Cohort ledger:** Issue added to cohort ledger
