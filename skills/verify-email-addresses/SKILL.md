---
name: verify-email-addresses
description: Check whether one or a few email addresses are real before sending, signing someone up, or adding them to a CRM. Use when the user asks if an address is valid, deliverable, a typo, disposable, a catch-all, or safe to email.
---

# Verify email addresses

Use the `verify_email` tool from the QuickEmailVerify MCP server. One call per address.
For more than about 20 addresses, use the `clean-email-list` skill instead.

## Steps

1. Call `verify_email` with the address exactly as the user gave it.
2. Read `status` first. It is one of six answers:

| status | Meaning | What to tell the user |
|---|---|---|
| `valid` | The mailbox exists. | Safe to send. |
| `role` | A shared inbox like sales@ or info@. | Deliverable, but a team reads it. Fine for support, weak for cold email. |
| `catch_all` | The domain accepts every address, so the mailbox can't be confirmed. | Risky. Use `score` to rank, send in small batches. |
| `disposable` | A throwaway or burner domain. | Don't send. Block it on signup forms. |
| `unknown` | The mail server gave no clear answer this time. | Not charged. Try again later. |
| `invalid` | The address or domain is dead. | Don't send. It will bounce. |

3. If `suggested` is set, the domain looks like a typo of a big provider
   (for example `gmial.com`). Offer the corrected address.
4. Use `score` (0-100) to rank. Nothing scores above 85 today; a clean valid mailbox is 80-85.
   `reason` explains a failure (SMTP reply, `no_mx`, `syntax`).

## Notes

- Definitive answers cost 1 credit. `unknown` is free, and repeat checks within 30 days come from cache for free.
- Never print the API key. It is already sent as a header by the plugin.
- If a tool error says `invalid API key`, ask the user to set `QUICKEMAILVERIFY_API_KEY` in the plugin settings.
  A free key (100 credits, no card) is at https://quickemailverify.com/signup.
- If it says `out of credits` or `rate limited`, report it plainly. Free keys allow 10 checks a minute, paid keys 60.
