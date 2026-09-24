# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

ChinoUkaegbu

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/67#issuecomment-5822443438

I’d like to investigate and reproduce this issue. I’ll verify whether an authenticated user can create a review for a profile other than their own, and document the observed behavior.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/67#issuecomment-5822453691

I reproduced the reported cross-user profile ownership behavior on commit `f89c06fc3ff292df2a04a39ac51319d32a76b779`.

**Environment**

- Windows 11 Home Single Language
- Docker Compose v5.2.0
- PostgreSQL 16 (Docker)
- Python 3.12.1 on the host
- Working tree clean at the commit above

**Steps**

1. Start the local application and database.
2. Log in as User 1 and retrieve the ID of their existing profile from the local development database with:

   ```sql
   SELECT id, user_id
   FROM profiles
   WHERE user_id = '<User 1 ID>';
   ```

   This returned profile ID `56fc6ff2-f331-4118-8b6e-fff9640bcfe3`.

3. Log in as User 2 and use that authentication for the following request.
4. Send `POST /reviews` with:

   ```json
   {
     "profile_id": "56fc6ff2-f331-4118-8b6e-fff9640bcfe3"
   }
   ```

5. Observe the response and verify the created review in the database.

**Observed**

- The request returned `200 OK`.
- The response contained review ID `ff70ad57-ac1f-4ba8-8388-59f5c81d70f7`, with `profile_id` set to User 1's profile ID and status `pending`.
- A subsequent database query confirmed that review `ff70ad57-ac1f-4ba8-8388-59f5c81d70f7` was persisted with that profile ID. Its status was `complete` when checked.

This reproduces the reported behavior: an authenticated user can create a review for a profile belonging to another user. I have not investigated the intended fix.

## Eval iterations

**Run history**

- Initial smoke run - 3/3 scored items
- Initial full run - 18/20 scored items (bar: 18/20: below the bar; category floor unmet: no match in disclosure)
- Targeted rerun (`pkg-01`, `pkg-20`) - 2/2 scored items
- Final confirming full run - 19/20 scored items (bar: 18/20: PASS)

The final full run passed the assignment threshold with 19/20 agreement and matched all category floors:

- clear-accept: 7/8
- disclosure: 1/1
- no-evidence: 4/4
- unfollowable-comms: 3/3
- wrong-target: 4/4

**Package analysis**

`pkg-20`: The rubric's final decision was **reject**, matching the gold label **reject**. The package failed the `repository-conventions` check because the comments did not satisfy the applicable disclosure requirement. After revising the rubric to make the repository-conventions check explicitly account for required AI-use disclosure, the targeted rerun of `pkg-20` matched the gold label.

**Check rationale**

> | Check       | Evidence                                                                                         | Pass condition                                                                                                                                                                                                    | Weight   |
> | ----------- | ------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
> | environment | The environment record in the repro report, read against the issue context and repo-facts block. | The report records the relevant environment details needed to interpret the reproduction. If the issue states a specific environment target, any meaningful difference from that target is explicitly identified. | required |

This wording was revised after the initial full evaluation incorrectly rejected `pkg-01` on the environment check. The revision makes the comparison conditional on the issue actually stating an environment target, rather than treating the absence of a stated target as a mismatch.

**Trade-offs**

The revisions to the `environment` and `repository-conventions` checks changed the outcomes for `pkg-01` and `pkg-20`. I reran both as a targeted canary with `--only pkg-01,pkg-20`; both matched their gold labels (`2/2`). The final full run then achieved `19/20` agreement, showing that the changes fixed the intended disagreements without breaking the other scored categories.

---

Related paths: `eval-run.txt` in this directory; your skill's files in `tools/repro-check/`.
