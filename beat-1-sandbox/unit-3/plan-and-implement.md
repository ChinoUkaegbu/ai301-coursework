# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

ChinoUkaegbu

**Plan comment**

Link: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/67#issuecomment-6000117804

Text of the comment as posted:

I reproduced this on commit `f89c06f` (details in my earlier comment: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/67#issuecomment-5822453691). Logged in as User 2, `POST /reviews` with User 1's `profile_id` returned `200 OK` and created review `ff70ad57-ac1f-4ba8-8388-59f5c81d70f7`, which later reached `complete`.

Reading the code, `create_review()` in `core/services/review_service.py` accepts `user_id` but never uses it; it builds the `Review` straight from `profile_id`. In `api/routes/reviews.py`, `create_review_endpoint()` then schedules `process_review` on that `profile_id` right away. `get_review()` and `list_reviews()` both filter on `Profile.user_id`, so creation is the one path without that scope. I haven't changed anything yet.

Plan:

1. In `create_review()`, look up the profile by `profile_id` and `user_id`, and return `None` when there is no match, as `get_review()` does for a record that isn't the caller's.
2. In `create_review_endpoint()`, return a 404 when the result is `None`, before `add_task`, matching `get_review_endpoint()`. Another user's profile and a non-existent one then look the same, and processing never starts for a profile the caller doesn't own.
3. Add tests for the repro, the caller's own profile, and a random UUID. I found no existing test or `xfail` marker for this issue.

Out of scope: the read paths, `process_review()`, auth, the schema, and other endpoints that take a `profile_id`. If I find one, I'll note it here rather than change it in this fix.

Open questions: existing tests call `create_review()`, and I haven't checked whether they pass a `user_id` that owns the profile, so they may need setup changes. I'm also assuming 404 to match the read paths; tell me if you'd prefer 403.

I drafted this plan with help from Claude. I ran the reproduction myself and reviewed the plan before posting.

---

## Your branch

**Branch**

fix/67-review-ownership-check

**Evidence**

Before the fix, then after the fix. Same requests (Swagger UI) and same database commands in both.

### Before

```
Before the fix (commit f89c06fc3ff292df2a04a39ac51319d32a76b779)
Requests sent through the Swagger UI (Authorize, then POST /reviews). Date: 2026-10-05.

----------------------------------------------------------------------
1. Log in as User 1 (Swagger Authorize)
----------------------------------------------------------------------
2026-10-05 18:40:17 [info     ] user_login                     email=user1@example.com request_id=58250f66-5add-432b-8b9c-3e61c0e4cbb6 user_id=e90f824d-9f6b-4c52-b605-7ea5f37ef8a8
INFO:     127.0.0.1:58230 - "POST /auth/login HTTP/1.1" 200 OK
2026-10-05 18:40:17,228 INFO sqlalchemy.engine.Engine ROLLBACK

----------------------------------------------------------------------
2. Find User 1's profile ID
----------------------------------------------------------------------
$ docker exec -it pathreview-ai301-fa26-s1-db-1 psql -U pathreview -d pathreview_dev -c "SELECT id, user_id FROM profiles WHERE user_id = 'e90f824d-9f6b-4c52-b605-7ea5f37ef8a8';"
                  id                  |               user_id
--------------------------------------+--------------------------------------
 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | e90f824d-9f6b-4c52-b605-7ea5f37ef8a8
(1 row)

----------------------------------------------------------------------
3. Log in as User 2 (Swagger Authorize)
----------------------------------------------------------------------
2026-10-05 18:45:06 [info     ] user_login                     email=user2@example.com request_id=cfb6be1e-2129-47fe-aee0-1eb677380de7 user_id=6ebf032a-7d0a-498f-8391-ea7864c271cf
INFO:     127.0.0.1:60662 - "POST /auth/login HTTP/1.1" 200 OK

----------------------------------------------------------------------
4. As User 2, POST /reviews with User 1's profile ID
----------------------------------------------------------------------
Request body:
{
  "profile_id": "56fc6ff2-f331-4118-8b6e-fff9640bcfe3"
}

Response: 200 OK
{
  "id": "0e9d4243-6a81-4c11-b3e6-8a2e7f1fe8c6",
  "profile_id": "56fc6ff2-f331-4118-8b6e-fff9640bcfe3",
  "status": "pending",
  "sections": null,
  "overall_score": null,
  "error_message": null,
  "created_at": "2026-10-05T16:47:44.551622Z",
  "updated_at": "2026-10-05T16:47:44.551622Z"
}

----------------------------------------------------------------------
5. Confirm the review was saved
----------------------------------------------------------------------
$ docker exec -it pathreview-ai301-fa26-s1-db-1 psql -U pathreview -d pathreview_dev -c "SELECT id, profile_id, status FROM reviews WHERE id = '0e9d4243-6a81-4c11-b3e6-8a2e7f1fe8c6';"
                  id                  |              profile_id              |  status
--------------------------------------+--------------------------------------+----------
 0e9d4243-6a81-4c11-b3e6-8a2e7f1fe8c6 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
(1 row)

----------------------------------------------------------------------
6. All reviews now on User 1's profile (baseline: 5 rows)
----------------------------------------------------------------------
$ docker exec -it pathreview-ai301-fa26-s1-db-1 psql -U pathreview -d pathreview_dev -c "SELECT id, profile_id, status FROM reviews WHERE profile_id = '56fc6ff2-f331-4118-8b6e-fff9640bcfe3';"
                  id                  |              profile_id              |  status
--------------------------------------+--------------------------------------+----------
 8fad24b9-9157-4dd7-a252-530cf449e867 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 d07e0d00-7e3a-4521-80f1-c68310215d81 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 beca0c6c-0cc2-493d-93e7-1a3695b33879 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | failed
 ff70ad57-ac1f-4ba8-8388-59f5c81d70f7 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 0e9d4243-6a81-4c11-b3e6-8a2e7f1fe8c6 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
(5 rows)

Result: User 2 (6ebf032a-7d0a-498f-8391-ea7864c271cf) created a review on a profile owned by
User 1 (e90f824d-9f6b-4c52-b605-7ea5f37ef8a8), and it was processed to "complete".
```

### After

```
After the fix (branch fix/67-review-ownership-check)
Requests sent through the Swagger UI (Authorize, then POST /reviews). Date: 2026-10-05.
Same commands as before.txt.

----------------------------------------------------------------------
1. As User 2, POST /reviews with User 1's profile ID (the repro)
----------------------------------------------------------------------
Request body:
{
  "profile_id": "56fc6ff2-f331-4118-8b6e-fff9640bcfe3"
}

Response (before the fix: 200 OK and a new review): 404
{
  "detail": "Profile not found"
}

----------------------------------------------------------------------
2. Reviews on User 1's profile after step 1 (before the fix: 4 rows, then a 5th was added)
----------------------------------------------------------------------
$ docker exec -it pathreview-ai301-fa26-s1-db-1 psql -U pathreview -d pathreview_dev -c "SELECT id, profile_id, status FROM reviews WHERE profile_id = '56fc6ff2-f331-4118-8b6e-fff9640bcfe3';"
                  id                  |              profile_id              |  status
--------------------------------------+--------------------------------------+----------
 8fad24b9-9157-4dd7-a252-530cf449e867 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 d07e0d00-7e3a-4521-80f1-c68310215d81 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 beca0c6c-0cc2-493d-93e7-1a3695b33879 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | failed
 ff70ad57-ac1f-4ba8-8388-59f5c81d70f7 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 0e9d4243-6a81-4c11-b3e6-8a2e7f1fe8c6 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
(5 rows)

Result: still 5 rows, no new review id.

----------------------------------------------------------------------
3. Control: as User 2, POST /reviews with a non-existent profile ID
----------------------------------------------------------------------
Request body:
{
  "profile_id": "00000000-0000-4000-8000-000000000000"
}

Response: 404
{
  "detail": "Profile not found"
}

Result: same response as step 1.

----------------------------------------------------------------------
4. Control: as User 1, POST /reviews with User 1's own profile ID
----------------------------------------------------------------------
Request body:
{
  "profile_id": "56fc6ff2-f331-4118-8b6e-fff9640bcfe3"
}

Response: 200 OK
{
  "id": "fa11eee2-2be2-4570-a9c8-ddc7b6bb4b95",
  "profile_id": "56fc6ff2-f331-4118-8b6e-fff9640bcfe3",
  "status": "pending",
  "sections": null,
  "overall_score": null,
  "error_message": null,
  "created_at": "2026-10-05T17:32:32.496517Z",
  "updated_at": "2026-10-05T17:32:32.496517Z"
}

----------------------------------------------------------------------
5. Reviews on User 1's profile after step 4
----------------------------------------------------------------------
$ docker exec -it pathreview-ai301-fa26-s1-db-1 psql -U pathreview -d pathreview_dev -c "SELECT id, profile_id, status FROM reviews WHERE profile_id = '56fc6ff2-f331-4118-8b6e-fff9640bcfe3';"
                  id                  |              profile_id              |  status
--------------------------------------+--------------------------------------+----------
 8fad24b9-9157-4dd7-a252-530cf449e867 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 d07e0d00-7e3a-4521-80f1-c68310215d81 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 beca0c6c-0cc2-493d-93e7-1a3695b33879 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | failed
 ff70ad57-ac1f-4ba8-8388-59f5c81d70f7 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 0e9d4243-6a81-4c11-b3e6-8a2e7f1fe8c6 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
 fa11eee2-2be2-4570-a9c8-ddc7b6bb4b95 | 56fc6ff2-f331-4118-8b6e-fff9640bcfe3 | complete
(6 rows)

Result: the owner's own request still creates a review (6th row, new id fa11eee2...), processed to "complete".

Summary:
- User 2 on User 1's profile: 200 OK + new review (before) -> "Profile not found", no new row (after).
- Non-existent profile ID: "Profile not found", same as above.
- User 1 on their own profile: 200 OK + pending review, still works.
```

## Eval iterations

**Run history**

1. Full run: 17/20 (clear-accept 5/7, scope-creep 4/4, thread-convention 1/2, unbuildable 3/3, wrong-cause 4/4). Below the 18/20 bar. Disagreements: pkg-05 and pkg-14 (gold accept, I rejected on `uncertainty`) and pkg-20 (gold reject, I accepted).
2. Partial re-grade with `--only pkg-05,pkg-14,pkg-20,pkg-04` after revising `uncertainty` and `thread-conventions`: 4/4. Not a full run, so it does not count toward the bar.
3. Full run: 19/20 (clear-accept 6/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4). Passes the bar. The one disagreement is pkg-09 (gold accept, I rejected on `uncertainty`). This matches the agreement line in `eval-run.txt`.

**Package analysis**

Package: **pkg-20** (ghostty-org/ghostty#11261, category thread-convention).

Gold label: **reject**. My rubric's final verdict: **reject**. In my first full run my rubric said **accept**, which was a disagreement.

Why it reads this way: the plan itself is strong. Its diagnosis, scope, test plan and stated risks all follow from the repro and the thread. What sinks it is the comment. The repo facts say: "All AI usage in any form must be disclosed, stating the tool used and the extent of the assistance". The candidate plan comment opens "I reproduced both fuzz cases on current main (report above; the no-hyperlink control isolates growth-during-print as the trigger)" and ends "One open question flagged in the plan: whether the check should live per-use or only at the two growth-adjacent sites, pending the print benchmark." Nowhere in it does it say an AI tool was used, so it conflicts with an established repository convention, which is what `thread-conventions` checks.

My first rubric only said the comment must "not conflict with established repository conventions". The grader saw no explicit conflict. My evidence guide also told it not to invent AI-disclosure rules. I changed the pass condition so that a disclosure rule stated in the repo facts must be satisfied, and the second run rejected pkg-20 for the right reason.

**Check rationale**

Check quoted from `tools/plan-check/rubric.md` (`thread-conventions`, pass condition):

> The comment addresses relevant requests or constraints raised in the thread (an empty thread adds none), does not conflict with established repository conventions, and makes no unsupported claims about work already completed. Where the repository facts state a disclosure requirement, such as an AI-use disclosure rule, the comment must satisfy it. Plans and comments in this course are drafted with AI assistance, so a stated disclosure rule applies, and a comment with no disclosure fails. A policy that only welcomes AI or asks for human review adds no requirement.

Why it reads that way: my original pass condition was only the first sentence's idea: the comment addresses the thread, does not conflict with conventions, and makes no unsupported completion claims. That let pkg-20 through. I added the disclosure sentences after seeing that pkg-20's repo facts require disclosure while the comment gave none. I added "an empty thread adds none" because a package like calib-01 has 0 thread comments and should not fail for that. I added the last sentence because pkg-05's policy only says AI tools are welcome and the author must review the output, and gold accepts it, so that kind of policy must not trigger a failure.

**Trade-offs**

This check gives up two things. First, it assumes every plan comment in this course is AI-assisted, so a stated disclosure rule fails a comment with no disclosure, even if a particular student wrote theirs by hand. Second, it ignores other process rules in the repo facts, such as pkg-20's vouch flow for first-time contributors, so a comment that skipped a rule of that kind would still pass.

Canaries: after the change I re-ran `--only pkg-05,pkg-14,pkg-20,pkg-04`. pkg-04 (the other thread-convention package, gold reject) stayed reject, and pkg-05 and pkg-14 (clear-accepts whose repo facts say nothing requiring disclosure) stayed accept, so the new sentences did not make the check stricter on them. The follow-up full run confirmed it with 19/20 and thread-convention 2/2.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
