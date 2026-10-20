# Context

| File | What it is |
| --- | --- |
| `thread.md` | The Slack thread that started this |
| `notes-cs-lead.md` | One page from the CS lead |
| `notes-risk.md` | One page from the Head of Risk |
| `queue-export.jsonl` | 30 days of queue history, one JSON object per line. Export taken 2026-10-17 18:00 UTC |

## Export fields

| Field | Notes |
| --- | --- |
| `machineVerdict`, `reasonCode`, `evidence`, `category` | What the rule said at submit time |
| `csDecision`, `csDecidedAt`, `csNote` | Human decision. `null` when the rule said `allow` or `block`: nobody looks at those today |
| `campaignLaunchedAt` | `null` if the business never launched |
| `googleOutcome` | `approved`, `disapproved`, or `null`. Google's policy review takes up to 14 days, so this is only populated for campaigns launched at least 14 days before the export |

All names and numbers are invented.
