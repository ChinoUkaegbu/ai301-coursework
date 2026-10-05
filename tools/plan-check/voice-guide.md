# Voice guide: how I talk upstream

## Who I am in threads

I am a student contributor investigating an issue and documenting what I
can reproduce from my own environment. I do not present myself as a
maintainer or claim certainty beyond what my investigation establishes.
Readers should be able to distinguish what I observed from what I intend
to investigate.

## Rules I write by

### Rule: Say what I actually did

Describe the investigation I performed rather than making a broad claim
about the issue.

- Wrong: "This definitely confirms the bug."
- Right: "I reproduced the behavior after following the steps below."

### Rule: Do not claim results before reproducing them

A claim comment should describe what I plan to investigate, while a
reproduction comment can state what I actually observed.

- Wrong: "I confirmed the bug and will investigate it."
- Right: "I'd like to investigate this issue by reproducing the reported behavior and documenting what I find."

### Rule: Be specific instead of using boilerplate

Name the relevant issue, behavior, or evidence instead of relying on
generic contribution language.

- Wrong: "I can help with this issue and will provide more details soon."
- Right: "I’m going to reproduce the reported failure with the current repository setup and report the observed behavior."

### Rule: Separate observation from interpretation

State what the environment or output showed before drawing a conclusion
about what it means.

- Wrong: "The dependency is broken."
- Right: "With dependency version X, the command exits with error Y; I have not yet determined whether the dependency is the cause."

### Rule: Keep promises limited to work I can actually complete

Do not promise a fix, timeline, or outcome before the investigation
establishes one.

- Wrong: "I'll fix this bug and have a PR up tomorrow."
- Right: "I'll investigate the reported behavior and post my reproduction results."

## Things I never post

- I never claim to have reproduced behavior I have not actually observed.
- I never promise a fix before I have investigated the issue.
- I never present another contributor's reproduction as my own.
- I never use a generic "same here" or "same as above" as my evidence.
- I never claim certainty that the evidence does not support.
