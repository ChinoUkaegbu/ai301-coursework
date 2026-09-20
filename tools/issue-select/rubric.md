# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check               | Evidence                                                                                         | Pass condition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Weight   |
| ------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Maintainer activity | Repo facts: the last 5 default-branch commits and the maintainer first-response sample           | At least one of the last 5 default-branch commits has a non-bot author, or the maintainer first-response sample contains at least one response from an Owner, Member, or Collaborator within 30 days of the corresponding issue update                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | required |
| Repository activity | Repo facts: archived flag, last push to any branch, and latest release                           | The repository is not archived, and either the last push to any branch occurred within the last 90 days or the latest release occurred within the last 180 days                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | required |
| Newcomer scope      | Issue body, comment thread, and issue history including linked PRs                               | The issue passes if it describes a specific contribution outcome rather than an umbrella/tracking task or pure usage/support request. Reject if the thread shows that maintainers have not settled what should be built, if a maintainer explicitly says the fix requires changes to core internals, or if the issue history shows several abandoned attempts through closed unmerged PRs or repeated abandoned work. Do not reject merely because the task changes multiple files, has multiple acceptance criteria, or proposes multiple implementation approaches. For a new feature, the core behavior and required inputs or assets must be sufficiently specified; an essential design choice or required asset explicitly marked TBD or undecided is a fail. | required |
| Contribution policy | Repo facts: contribution policy, dedicated AI policy files, and linked contributor documentation | The repository has no stated outright ban on AI-assisted or AI-generated contributions. Disclosure, testing, understanding, or human-review requirements are not bans                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | required |

## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict, they
rank accepted issues; unclear counts as fail." -->

Accept if every required check passes. Reject if any required check fails. If a required check is unclear because the evidence needed to establish its pass condition is genuinely absent, treat it as a fail. Preferred checks, if added later, never change the accept/reject verdict.
