<p align="center"><img src="assets/logo.png" width="96" alt="QuickEmailVerify logo"></p>

# QuickEmailVerify plugin

Verify email addresses and clean lists from your agent, in Grok Bot, Cursor or any MCP client.

Every address goes through syntax, DNS, SMTP and catch-all checks and comes back with one of six answers:
`valid`, `role`, `catch_all`, `disposable`, `unknown` or `invalid`, plus a 0-100 score, the mailbox provider
and a typo fix when the domain is a near-miss (`gmial.com` → `gmail.com`).

- **Hosted MCP server:** `https://quickemailverify.com/mcp` (streamable HTTP)
- **Auth:** your API key in the `X-API-Key` header
- **Free key:** https://quickemailverify.com/signup (100 credits, no card)
- **Unknown results are free.** Credits are only spent on definitive answers.

## Install

1. Install **QuickEmailVerify** from the marketplace.
2. Get a key at https://quickemailverify.com/signup. It starts with `ev_`.
3. Open **Plugins → QuickEmailVerify → Configure** and paste it into `QUICKEMAILVERIFY_API_KEY`.

Then ask your agent things like:

- "Is jane@gmial.com a real address?"
- "Clean leads.csv and split it into send, risky and remove."
- "How many credits do I have left?"

### Manual setup (any MCP client)

```json
{
  "mcpServers": {
    "quickemailverify": {
      "url": "https://quickemailverify.com/mcp",
      "headers": { "X-API-Key": "ev_YOUR_KEY" }
    }
  }
}
```

## What's inside

| Component | Purpose |
|---|---|
| `mcp.json` | The hosted QuickEmailVerify MCP server, authenticated with your key |
| `skills/verify-email-addresses` | Check one or a few addresses and explain the answer |
| `skills/clean-email-list` | Bulk-verify a list, wait for the job, write send / risky / remove files |

### Tools

| Tool | What it does |
|---|---|
| `verify_email` | One address → status, score, subscores, provider, typo suggestion |
| `bulk_verify` | Submit up to 100,000 addresses as a background job |
| `job_status` | Progress and answer breakdown of a job |
| `export_job` | A finished job's results as CSV |
| `account_status` | Plan, credits, keys and recent jobs |
| `usage_analytics` | Request counts, latency and answer distribution |

The same server also exposes the [Signup Guard](https://quickemailverify.com/signup-guard) tools
(`check_signup`, `explain_decision`, `review_recent_signups`, `mark_user`, `update_rule`, `run_audit`, `audit_status`)
for screening fake signups.

## Limits

| | Free | Paid |
|---|---|---|
| Verifications per minute | 10 | 60 |
| Addresses per UTC day | 500 | 500 (bigger paid jobs resume the next day) |

Full API reference: https://quickemailverify.com/developers

## Privacy

Your key is sent only to `quickemailverify.com` and never appears in tool results.
See https://quickemailverify.com/privacy.

## Support

support@quickemailverify.com

## License

The plugin files in this repository are licensed under [Apache-2.0](LICENSE).
The QuickEmailVerify name and logo are trademarks; the license does not grant use of them.
