# Evidence guide: where proof lives in a reproduction package

## Environment

### Where it lives

In eval mode, check the `repo-facts` block and the environment section of
the repro report. In live mode, check the student's repro draft and the
repository's documentation or issue context where relevant.

### What good looks like

The report identifies the relevant versions, platform, configuration, or
other environment details needed to understand the reproduction. If the
issue states a specific environment target, the recorded environment
either matches that target or explicitly calls out any meaningful
difference. If the issue does not state an environment target, do not
require a comparison that cannot be made.

## Steps

### Where it lives

Check the reproduction steps in the repro report, including any setup
instructions and the sequence used to reach the reported behavior.

### What good looks like

A stranger can follow the stated setup and steps from the starting state
to the point where the behavior is observed. Required inputs, commands,
or conditions are included rather than assumed.

## Behavior shown

### Where it lives

Check the output excerpts, logs, screenshots, test results, or other
artifacts in the repro report, and compare them with the issue description.

### What good looks like

The artifacts show the behavior described by the issue, or clearly show
that the behavior could not be reproduced. Evidence of a related or
adjacent behavior is not enough. The observed result is distinguishable
from the expected result when the issue provides an expected behavior.

## Honesty

### Where it lives

Compare the report's conclusion and claims with the environment, steps,
and artifacts provided in the repro report. In live mode, also check the
claim comment to make sure it does not assert reproduction before the
investigation has happened.

### What good looks like

The conclusion matches what the evidence actually establishes. A report
may honestly conclude that the issue could not be reproduced when the
attempt is documented. A report fails this standard when it claims
reproduction, causation, or a result that its evidence does not establish.

## Comms

### Where it lives

In eval mode, check the claim comment and repro report against the issue
context and repo-facts block. In live mode, check the draft comments
against the issue thread and the repository's contribution or disclosure
rules.

### What good looks like

The comments identify the relevant issue and describe the student's own
work specifically. They follow each applicable repository convention.
When the repository requires AI-use disclosure, check that the comments
explicitly identify the required AI tool and the extent of assistance.
They must not present another person's reproduction as the student's own
work.
