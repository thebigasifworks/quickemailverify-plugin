---
name: clean-email-list
description: Clean a list of email addresses (CSV, spreadsheet column, or pasted list) before a campaign or import. Submits a bulk verification job, waits for it, and writes a cleaned file split into send, risky and remove. Use for lists of more than about 20 addresses.
---

# Clean an email list

Uses the QuickEmailVerify tools `bulk_verify`, `job_status` and `export_job`.

## Steps

1. Collect the addresses. From a file, read the email column. Trim spaces and drop blank rows.
   Duplicates are removed by the server, so no need to dedupe first. Up to 100,000 per job.
2. Call `bulk_verify` with the list. Keep the returned `job_id`. Tell the user the job id.
3. Call `job_status` with the `job_id` until `status` is `complete`.
   Wait at least 10 seconds between checks. Gmail and Microsoft mailboxes are checked slowly on
   purpose, so a big list can take a while. Show progress from `done` / `total`.
   - `waiting`: the account hit today's cap. The job resumes by itself after 00:00 UTC (`resumes_at`). Tell the user and stop polling.
   - `insufficient_credits`: the balance ran out. Rows already checked can still be exported.
4. Call `export_job` with the `job_id`. It returns CSV with columns:
   `email,status,score,provider,disposable,role,catch_all,suggested`.
5. Split the rows and save them next to the original file (ask before overwriting anything):
   - **send**: `valid`
   - **risky**: `catch_all`, `role` (sort catch-alls by `score`, highest first)
   - **remove**: `invalid`, `disposable`
   - **recheck later**: `unknown` (free; run them again in a few hours)
   Where `suggested` is set, list the typo fixes separately for the user to approve.
6. Report the counts per group and where the files are.

## Notes

- Only definitive answers cost credits; `unknown` rows are free.
- `account_status` shows the credit balance before a big job. `usage_analytics` shows recent usage.
- Never print the API key.
