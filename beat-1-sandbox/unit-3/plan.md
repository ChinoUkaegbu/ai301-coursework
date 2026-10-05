# Plan: verify profile ownership when creating a review (#67)

## Diagnosis

My reproduction on commit `f89c06fc3ff292df2a04a39ac51319d32a76b779` (comment: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/67#issuecomment-5822453691):

> Logged in as User 2, sent `POST /reviews` with `{"profile_id": "56fc6ff2-f331-4118-8b6e-fff9640bcfe3"}`, which is User 1's profile ID.
> The request returned `200 OK`. The response contained review ID `ff70ad57-ac1f-4ba8-8388-59f5c81d70f7`, with `profile_id` set to User 1's profile ID and status `pending`. A database query confirmed the review was persisted with that profile ID. Its status was `complete` when checked.

Reading the code at that commit:

- `core/services/review_service.py`: `create_review(db, profile_id, user_id)` accepts `user_id` but never uses it. It builds `Review(profile_id=profile_id, status="pending", ...)` directly from the caller-supplied `profile_id` and commits. By contrast, `get_review()` joins `Profile` and filters on `Profile.user_id == user_id`, and `list_reviews()` filters on `Profile.user_id == user_id`.
- `api/routes/reviews.py`: `create_review_endpoint()` calls `create_review(..., user_id=current_user.id)`, then immediately calls `background_tasks.add_task(process_review, db, review.id, data.profile_id)`. Nothing between the request and the processing checks that the profile belongs to the caller.
- `process_review()` loads the profile by id with no user check and, in `_run_ingestion_pipeline()`, adds `IngestedSource` rows for that profile.

This explains the repro: no step compares the profile's owner to the caller, so User 2's request is accepted for User 1's profile and processing runs on it, which is why the review reached `complete`. My repro showed the review row and its status. I did not check the `IngestedSource` rows, so that side effect is from reading the code, not from observation.

## Scope

**In scope**

- `core/services/review_service.py`: make `create_review()` use `user_id`, so a review is only created for a profile the caller owns.
- `api/routes/reviews.py`: make `create_review_endpoint()` return a 404 when the service reports no owned profile, before `add_task` is called.
- Tests for the ownership check.

**Out of scope**

- `get_review()`, `list_reviews()` and the other review endpoints (already scoped to the caller).
- `process_review()` internals, authentication, database schema or migrations.
- Any other endpoint that accepts a `profile_id`. If I find one with the same gap, I will note it on the issue rather than fix it here.

## Files I'll touch

- `core/services/review_service.py`: `create_review()` and its docstring (CONTRIBUTING requires Google-style docstrings).
- `api/routes/reviews.py`: `create_review_endpoint()`.
- The existing unit tests under `tests/unit/` that call `create_review()` (the only other callers), plus new tests there for the ownership check.

## Approach

1. In `create_review()`, query for the profile with both conditions, `Profile.id == profile_id` and `Profile.user_id == user_id`, using the same `select`/`and_` style as `get_review()`. If no profile matches, return `None` without creating a review. Change the return type to `Review | None`, so `mypy` flags any caller that does not handle it.
2. In `create_review_endpoint()`, right after `create_review()`, check for `None` and raise `HTTPException(status_code=404, detail="Profile not found")` before `background_tasks.add_task(...)`. This matches how `get_review_endpoint()` answers 404 for a review that isn't the caller's. The raise sits inside the existing `try`, which already re-raises `HTTPException`. Without the explicit check, `review.id` on `None` would raise `AttributeError`, and the generic handler would turn it into a 500 with a rollback.
3. A profile owned by someone else and a profile that does not exist both return the same 404.
4. Add the tests below. I found no existing test or `xfail` marker for #67, so there is no marker to remove.
5. Run `make check && make test-unit` before pushing, and make sure CI is green.

## Test plan

Before the fix (quoted above): User 2 `POST /reviews` with User 1's profile ID returns `200 OK` and creates review `ff70ad57-ac1f-4ba8-8388-59f5c81d70f7`.

I will re-run my reproduction steps after the fix. Expected results:

- **Repro (User 2, User 1's profile ID):** `404` with detail `Profile not found`, not `200`, and no new review. Check: `SELECT count(*) FROM reviews WHERE profile_id = '56fc6ff2-f331-4118-8b6e-fff9640bcfe3';` is unchanged after the request. If I can find the ingested-sources table, I will also confirm its row count for that profile does not grow.
- **Control (User 1, their own profile ID):** `200 OK`, review created with status `pending`.
- **Control (any user, a random non-existent UUID):** the same `404` as the repro case.
- **Unit tests through the real service:** `create_review()` called with another user's profile returns `None` and adds no `Review`. With the caller's own profile it returns a `pending` review. These fail if the missing check comes back. I will add a route-level test for the `404` too if the repo already has a pattern for testing routes.

## Risks and unknowns

- The existing tests that call `create_review()` may pass a `user_id` that does not own the profile they use. I have not read them. If the new check breaks them, I will update their setup to create the profile for the right user, and say so in the PR.
- I am assuming a 404 is right, to match `get_review_endpoint()`. If maintainers want 403, only the route response changes.
- I do not know how the unit tests set up the database or an authenticated request, so I cannot yet say whether a route-level test fits the existing pattern.
- I have not checked what `process_review()` wrote for User 1's profile in the repro beyond the review row, so I make no claim about those side effects.

## Deviations

- I merged the random-UUID unit test into the not-owned test, since both take the same path in the service (the query finds no row). The random-UUID case is covered by the manual after-fix check, which returned the same "Profile not found".
- I added three unit tests in tests/unit/test_review_service.py (not owned, owned, and a check that the query filters on both profile_id and user_id). All three pass.
- I changed the mock_db_session fixture in the same file so the default ownership lookup returns a profile. Without it, six existing create_review tests failed, because create_review now runs a query. I recorded this as a risk in the plan, and no test assertions changed. The 13 get_review/list_reviews tests marked xfail for issue #65 are unchanged and still report as xfailed.
- I did not add a route-level test, because I haven't seen an existing pattern for one. The route's 404 is covered by the manual after-fix checks.
- I did not check the ingested-sources row count that the plan mentioned, so I make no claim about those side effects.
- Local make typecheck fails on a numpy stub in .venv, and it failed before my change too. Running `mypy --python-version 3.12 api/ core/` passes.
- In the route, I also added a log.warning ("profile_not_found_for_review") when the profile isn't found or isn't owned by the user. The plan didn't mention it. It matches how get_review_endpoint logs a missing review.
- Otherwise the build followed the plan: 404 "Profile not found" for another user's profile and a random UUID, and 200 pending for the owner.
