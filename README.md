# Two contributions to AI evaluation tooling

## 1. Same-named inputs silently overwrote each other (ControlArena #953)

**Problem.** `evaluate_monitors_post_hoc` names each output file from the input's stem, monitor name and index, all written into one directory. Two input logs both called `shared.eval` in different folders therefore produced the same output path. The function returned one path for two inputs, and the second result replaced the first.

**Reproduction.** On upstream commit `fa57942`, I created two logs with distinct samples and ran the function. It returned one distinct path for two inputs.

**Fix.** Colliding inputs (compared case-insensitively) each get a numbered subdirectory. Unique names keep their existing paths. The design keeps both results rather than rejecting the collision, since the caller supplied two valid logs.

**Tests.** A regression test writes two same-named logs with different eval IDs, runs the function, and reads both saved logs back to check their eval and sample IDs. It fails on the base commit (`assert 1 == 2`) and passes with the fix. The affected module passes on asyncio and trio. CI on the PR is green.

Links: issue #953, PR #954.

## 2. A test that could pass on an empty export (garak #2470)

**Gap.** A new test checked that an AVID export was written beside its source report and validated each record. The validation ran inside a `for` loop, so an empty export passed without checking any record.

**Review.** I posted a review on the PR proposing an assertion that the export contains at least one record before validation. I applied it locally: all four cases pass on the PR head, and an empty file now fails.

Link: review on PR #2470.

## What this shows

Both items came from reproducing or reading the actual code before commenting, with the evidence linked so a maintainer can check it without re-running the work.
