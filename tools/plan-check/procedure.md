# Procedure: how this skill grades a plan package

## Read order

1. Determine whether the package is being graded in live mode or eval mode. Follow the corresponding evidence rules in `SKILL.md`.

2. In live mode, read `scope.md` first, as required by `SKILL.md`. Stop if the issue is outside the permitted repository or the repository scope is unresolved. Read `voice-guide.md` and record any rules that the draft plan comment must follow. In eval mode, do not read either file.

3. In eval mode, read the entire package before grading. Identify and record the issue context, repository facts, reproduction evidence, candidate plan, and candidate plan comment. Use only the package text as evidence.

4. In live mode, read `plan.md` and the draft plan comment to identify the proposed diagnosis, scope, implementation approach, tests, risks, and assumptions. Treat the drafts as the complete candidate submission; do not use other unquoted files in the student's working directory as evidence for the plan.

5. Identify the issue description and relevant issue-thread discussion. Record the reported problem, any relevant requests or constraints, and any repository conventions stated in the available evidence.

6. Read the reproduction evidence before deciding whether the diagnosis or proposed fix is correct. In live mode, use the student's posted reproduction comment on the issue. For a house issue, use the house reproduction pack quoted in the drafts. In eval mode, use the reproduction-evidence block in the package.

7. Read the repository facts available in the evidence. Identify the relevant files, code behavior, and conventions that can support or contradict the proposed implementation.

8. Before grading, record the key observed behavior, the supported explanation of the issue, the proposed change, the expected fixed behavior, and any unresolved questions. Do not treat an unsupported assumption as an established fact.

## Evidence gathering

1. For `diagnosis`, extract the plan's explanation of the bug and compare it with the observed behavior in the reproduction evidence. Record the specific observation that supports or contradicts the diagnosis.

2. For `scope`, extract what the plan proposes to change and what it explicitly excludes. Compare the proposed changes with the issue description, reproduction evidence, and relevant repository facts. Record any unrelated changes, uncontrolled expansion, or missing boundary that prevents the change from remaining focused on the issue.

3. For `cause`, identify the cause asserted by the plan and the mechanism through which the proposed change is expected to fix the bug. Compare that explanation with the reproduction evidence and, where the repository facts describe code behavior, that behavior too. If they do not describe code, judge whether the explanation is consistent with the reproduction evidence and specific enough to check. Record whether the evidence supports the proposed cause or instead points to a different cause or a symptom-level workaround.

4. For `executability`, identify the proposed implementation approach, relevant files, and decisions the plan expects the developer to make. Compare these with the diagnosis and repository facts. Record whether another developer could begin implementing the central fix without independently discovering the solution or resolving a missing essential decision.

5. For `test-plan`, extract the reproduction steps, inputs, and observed behavior. Compare them with the plan's proposed tests and expected results. Record which relevant behavior the tests exercise, what observable result is expected after the fix, and whether that result distinguishes the original bug from the intended fixed behavior.

6. For `uncertainty`, identify factual claims, assumptions, risks, and unresolved questions in the plan. Compare factual claims with the reproduction evidence and repository facts. Record any material assumption presented as established fact, and any unresolved question that could materially change the implementation but is not acknowledged. In live mode, if `plan.md` has a filled `## Deviations` section, compare it with the original plan, and treat any unrecorded change or unsupported claim of completed work as an `uncertainty` failure.

7. For `thread-conventions`, compare the candidate plan comment with the issue description, relevant thread discussion, and repository conventions stated in the available evidence. Record any relevant request or constraint the comment disregards, any conflict with an established convention (including a stated AI-use disclosure requirement that the comment does not satisfy), or any unsupported claim about work already completed.

8. For every check, retain the specific fact or short quotation that supports the eventual grade. Do not substitute general impressions such as "looks good" for evidence.

9. If evidence cannot be found in the permitted sources, record what is missing. Do not fetch external evidence in eval mode or silently fill gaps with assumptions.

## Check execution

1. Execute the checks in this order: `diagnosis`, `scope`, `cause`, `executability`, `test-plan`, `uncertainty`, and `thread-conventions`. Evaluate each check independently against its evidence and pass condition in `rubric.md`.

2. For each check, compare the evidence gathered in the previous stage with the exact pass condition in the rubric. Judge the correctness and usefulness of the proposed plan, not its formatting, length, or use of headings.

3. Assign `pass` only when the available evidence establishes that the pass condition is satisfied.

4. Assign `fail` when the evidence contradicts the pass condition or establishes that it is not satisfied.

5. Assign `unclear` when the available evidence is insufficient to determine whether the pass condition is satisfied. Do not treat missing evidence as proof that a claim is false, and do not assume that an unsupported claim is true.

6. When evidence is missing, first check the permitted sources identified in the evidence-gathering stage. If the evidence remains unavailable, record the gap and assign `unclear` when the check cannot be decided. Do not invent repository behavior, issue-thread requirements, reproduction results, or implementation details.

7. Grade each check using only the evidence relevant to that check. A check may use evidence gathered for another check, but it must still be evaluated against its own pass condition.

8. In live mode, separately compare the draft plan comment with `voice-guide.md`. Record any violated voice-guide rule and quote the rule in the readable summary, as required by `SKILL.md`. Voice-guide violations do not independently change the rubric verdict unless a rubric check covers the same issue.

9. Before assembling the verdict, verify that every rubric check has exactly one grade and that its supporting evidence identifies the fact or quotation that determined the result.

## Verdict assembly

1. Read the verdict rule in `rubric.md`. Do not invent a different acceptance threshold or override the rubric based on personal judgment.

2. Apply the rule to all check grades. A failed required check results in `reject`. An unclear required check must also be treated as a failure under the rubric's verdict rule. Preferred checks, if present, do not change the verdict.

3. Return `accept` only when the rubric's acceptance conditions are satisfied. Otherwise, return `reject`.

4. Prepare a short readable summary with one line per check, stating its grade and the evidence that determined it. In live mode, include any voice-guide violations and quote the applicable rule.

5. Produce the final machine-readable result as a fenced JSON block containing the item identifier, every check name and grade, the supporting evidence for each check, and the overall verdict. Use `accept` or `reject` for the verdict and `pass`, `fail`, or `unclear` for each grade.

6. Ensure the JSON is valid, includes every check in the rubric, and is the last fenced JSON block in the output. Do not write anything after it.
