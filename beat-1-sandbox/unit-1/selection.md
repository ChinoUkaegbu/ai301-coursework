# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/67

**Verdict output**

```
Maintainer activity — pass. All 5 most recent main commits are authored by Aburke225 (human, non-bot), newest 2026-09-16.

Repository activity — pass. isArchived: false, last push 2026-09-16 — 4 days ago, well inside 90.

Newcomer scope — pass. A specific bug with a named cause: create_review() in core/services/review_service.py ignores the authenticated user ID and doesn't check that profile_id belongs to the caller. No umbrella/tracking framing, no support question, zero comments so no unsettled design debate, no linked PRs or abandoned attempts (timeline has only two labeled events). The repo's own scripts/issues_manifest.json entry (I-15) scopes it to 2 files and a 2–4 hour effort, verified 2026-09-16.

Contribution policy — pass. No AI ban anywhere: no AI_POLICY.md, docs/CONTRIBUTING.md contains no AI/LLM/generated-code prohibition, and the PR template asks only for tests, CI, and xfail-marker removal — conditions, not a ban.

Fit note: this is a strong match for your profile — Python backend service/auth logic, and the repo's workflow makes you remove the @pytest.mark.xfail marker so your fix proves itself through the test suite, which is exactly the testing practice you said you wanted. Per the Path Review house rule, claim it in a comment even if classmates have; credit attaches to your PR.

{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/67",
  "checks": [
    {"name": "Maintainer activity", "grade": "pass",
     "evidence": "All 5 latest main commits authored by non-bot human Aburke225, newest 2026-09-16 'chore: track five more manifest entries against the tracker'"},
    {"name": "Repository activity", "grade": "pass",
     "evidence": "isArchived: false; pushedAt 2026-09-16, 4 days before today (2026-09-20), within the 90-day window"},
    {"name": "Newcomer scope", "grade": "pass",
     "evidence": "Specific bug: 'core/services/review_service.py ignores that argument'; no comments, no linked or abandoned PRs, manifest entry I-15 scopes it to 2 files / 2-4 hours"},
    {"name": "Contribution policy", "grade": "pass",
     "evidence": "No AI_POLICY file; docs/CONTRIBUTING.md has no AI-contribution prohibition; PR template requires only tests/CI/xfail removal"}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

13/20 scored items (bar: 18/20: below the bar)

5/7 scored items

5/7 scored items

6/7 scored items

18/20 scored items (bar: 18/20: PASS)

**Issue analysis**

I chose issue-19 as my scored issue for analysis. My rubric's decision was `reject`, while the gold label was `accept`. My rubric rejected it on the Newcomer scope check because the issue described two potential causes and several possible architectural changes, including multiprocessing, selective matching, and moving rewrite application to a separate thread. My rubric treated that as insufficiently settled scope for a first contribution. The gold label accepted it, so this was a case where my scope check was stricter than the grading answer.

**Check rationale**

> The issue passes if it describes a specific contribution outcome rather than an umbrella/tracking task or pure usage/support request. Reject if the thread shows that maintainers have not settled what should be built, if a maintainer explicitly says the fix requires changes to core internals, or if the issue history shows several abandoned attempts through closed unmerged PRs or repeated abandoned work. Do not reject merely because the task changes multiple files, has multiple acceptance criteria, or proposes multiple implementation approaches. For a new feature, the core behavior and required inputs or assets must be sufficiently specified; an essential design choice or required asset explicitly marked TBD or undecided is a fail.

I wrote this check to distinguish a genuinely bounded first contribution from issues that only look small because they have a short title or description. I also wanted the rubric to avoid automatically rejecting an issue just because it touches multiple files or has several implementation steps. The final part about TBD design choices is intended to catch feature requests where the contributor cannot know what they are actually expected to build.

**Trade-offs**

This check can be too strict when an issue describes several possible implementation approaches but still has a clear underlying problem that the contributor could investigate with the maintainers. Issue-19 demonstrated this trade-off: my rubric rejected it as too broad, while the gold label accepted it. I kept the check because the final full run reached 18/20, which passed the required bar, and the check also helped avoid over-rejecting issue-01 after I revised it. The trade-off is that the rubric may miss some legitimate first issues when their implementation details are intentionally left open.

---

## Selection rationale

1. **Fit to my interests and time:** I chose issue #67 because it directly fits my goal of improving my backend skills. The issue focuses on making sure review creation is properly scoped to the correct user's profile, which gives me an opportunity to understand how the backend service layer handles authorization and data ownership. The issue appears to have a specific expected outcome rather than requiring a large new feature, so I think it is manageable within the time available.

2. **What the verdict identified correctly and what I weighed:** My skill correctly identified #67 as an accepted issue because it has a concrete outcome and does not appear to have an active assignee or linked pull request. I also considered factors the rubric could not fully capture, such as how much I would learn from working in the backend service layer, how comfortable I would be understanding the existing code, and how much time I would need to write and test the change.

3. **Anticipated difficulty in claiming it:** I do not expect claiming the issue itself to be especially difficult. The main challenge will probably be understanding the existing review service and its authorization patterns, then making sure the ownership check is implemented correctly without affecting other review functionality. I will also need to write or update tests to verify that users cannot create reviews outside their own profile.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
