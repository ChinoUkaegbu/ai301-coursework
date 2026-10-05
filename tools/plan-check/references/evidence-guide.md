# Evidence guide: where evidence lives in a plan package

## How a package is laid out (eval mode)

A package is one markdown file with these sections: `## Repo facts`, `## Issue`, `## Thread highlights`, `## Repro evidence`, `## Candidate plan`, and `## Candidate plan comment`. Use only this text.

- **Repo facts** holds repository metadata, the bug-report template, and the contribution policy (CONTRIBUTING). It usually does **not** contain source code.
- **Candidate plan** is often short and may use labels such as `Cause`, `Change` (with `In:` / `Out:`), and `Test`. It may have no separate sections for files, risks, or unknowns. Read the content, not the headings.
- When the package does not show source code, judge the plan's code claims by whether they are consistent with the reproduction and specific enough that a developer could check them. Use `unclear` only when a grade truly depends on a fact the package does not contain. A plan is not unclear just because its code is not shown.

In live mode, the equivalents are the GitHub issue and thread, the student's posted repro comment, `plan.md`, `comment.md`, and the repo's contribution docs.

## Diagnosis and grounding

**Where it lives:** The plan's `Cause` statement (or its diagnosis section), compared with the `## Repro evidence` steps and the "Expected" and "Actual" lines. The `## Issue` text gives the reporter's version of the same behavior.

**What good looks like:** The diagnosis explains every observed step, including the one that works. For example, a view is wrong until re-entered, so the explanation must account for why re-entering fixes it. It does not contradict the repro (same trigger, same symptom, same recovery path), and it does not ignore a detail that challenges it.

For `diagnosis`, check the stated explanation against the repro steps one by one. For `cause`, check that the `Change` acts on the mechanism the diagnosis names, rather than hiding the symptom with a workaround (for example, forcing a redraw on a timer, or special-casing one screen). Do not treat a plausible story as established unless the repro supports it.

On a house issue, the repro evidence is the house repro pack as quoted in the drafts. If the drafts quote none, grade that absence under the checks that depend on it.

## Scope

**Where it lives:** The `In:` and `Out:` lines (or equivalent) in the candidate plan, compared with the issue text, the repro, and the repo facts.

**What good looks like:** The change targets the reported behavior, and the plan says what it leaves alone. Added work is tied to the issue. A check of neighboring paths affected by the same change is fine. Refactors, new features, or changes to unrelated behavior are not.

Judge the work actually proposed, not whether a "Scope" heading exists. A plan can claim a narrow goal while listing broad changes, and a plan with no heading can still have a clear boundary through its `In:` and `Out:` lines.

## Executability

**Where it lives:** The `Change` section: named files, functions or callbacks, and what is added or altered. The repo facts show what those files and conventions are, when they are described.

**What good looks like:** A developer can start the central edit without first working out what the fix is. The plan names where to edit and what to change, and the edit follows from the diagnosis. Small details that any developer would settle while coding do not make a plan unexecutable.

Fail this check when the plan only says "fix the bug", lists files with no change described, or leaves an essential decision open (for example, "either refresh the view or recompute the status" with no choice made).

## Test plan

**Where it lives:** The `Test` section, compared with the repro's steps, inputs, and Expected and Actual lines.

**What good looks like:** The test reuses the repro's trigger and names the observable result that separates broken from fixed (for example, "at step 3 the color flips without leaving the view"). It would fail if the original bug came back. Extra checks on neighboring paths are a plus. A manual test is acceptable if its expected result is observable and specific.

Do not pass a test plan that only says "run the test suite", "verify it works", or "confirm no regressions". Those results look the same before and after the fix.

## Honesty

**Where it lives:** The whole candidate plan, including any risks, unknowns, or assumptions it states. Check its factual claims against the repro block, the issue, and the repo facts. In live mode, also read `## Deviations`.

**What good looks like:** Nothing stated as fact contradicts the evidence, and the plan does not report work or test results that have not happened. Reasonable code-level claims that the package cannot verify (such as a file name, or that a cache file stores a timestamp) are acceptable when they fit the repro and the approach. A plan that says "no risks identified" is not failing for that alone.

A plan with no "Risks" section is not automatically a fail. Look at what it asserts. Fail when a stated fact conflicts with the repro, issue, thread, or repo facts, when the plan reports an unperformed result as done, or when it treats as settled something the package shows to be open (for example, a point the thread is still debating). Do not fail a plan only because its explanation of the mechanism goes beyond what the package can confirm, as long as it is consistent with the repro. In live mode, `## Deviations` must describe the real outcome, including "nothing changed".

## Comms

**Where it lives:** `## Candidate plan comment`, compared with `## Thread highlights`, the `## Issue` text, and the contribution policy in `## Repo facts`.

**What good looks like:**

- **Thread:** It addresses requests or constraints raised in the thread. If the thread has no comments, there is nothing to address, and that is not a failure.
- **Conventions:** It respects the stated contribution policy. If the repo facts require AI-use disclosure (for example, "all AI usage must be disclosed, stating the tool and extent"), the comment must include a disclosure, because plans and comments in this course are drafted with AI assistance. A comment with no disclosure fails the check, however good the plan is. A policy that only welcomes AI, or asks you to review AI output, adds no requirement. Do not invent rules the repo facts do not state.
- **Claims:** What it says about finished work matches the evidence. "Reproduced on X" is fine when the repro block shows it. "Fixed", "tests pass", or "PR is up" is a fail when nothing supports it. Describing future work as future is fine.
- **Content:** It is specific to this issue and states the diagnosis, the change, and the test, not generic boilerplate.

Check the comment against the actual thread and policy text. In live mode, also compare the draft with `voice-guide.md` and report any violated rule in the readable summary, quoting the rule. A voice-guide violation does not change the verdict unless a rubric check covers it.
