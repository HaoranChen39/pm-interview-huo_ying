# Hellyeah · Onboarding moderation (PM interview)

You are the product manager for onboarding at Hellyeah. This is a slice of the
real codebase: brand tokens, a few UI primitives, the rule that decides whether
a newly onboarded business may advertise on Google Ads, and the screen the
customer success (CS) team uses today.

We are a small team. The PM here decides what gets built, builds a lot of it,
and is accountable for whether it worked. This exercise mirrors that.

Budget about **four hours**, spread over up to five days. Use whatever tools you
normally use, including AI coding assistants. We will ask to see how you used
them.

And in today's interview, we'd discuss how you dissect these questions and brainstorm solutions. 

## Setup

Node 20 or newer.

```bash
npm install
npm run dev        # http://localhost:5173
npm test           # baseline tests, keep them green
```

## Background

**What Hellyeah does.** Hellyeah runs Google Ads for small businesses that have
no marketing team. A dentist or a furniture shop signs up, tells us about their
business, tops up a wallet, and we plan, create and launch their campaigns.
Every customer's campaigns run under one Google Ads manager account that we
own, called the MCC.

**Why we check businesses before they spend.** Google does not allow ads for
certain categories: gambling, adult content, weapons, alcohol, tobacco,
cannabis, crypto trading, payday loans, illegal activity. When Google
disapproves a campaign for one of these, the mark goes against our MCC, not
just that customer. Two disapprovals in the same category within 90 days
trigger an account-level strike, which pauses every customer's campaigns for
up to 72 hours while we appeal. One bad customer can take down all of them.

**How the check works today.** When a business submits its profile, a rule
reads the name, website and description and returns one of three verdicts. It
is deterministic, keyword-based, and has no LLM in it.

| Verdict | Meaning | What happens |
| --- | --- | --- |
| `allow` | No risk signals | Onboarding continues, nobody looks |
| `review` | An ambiguous word such as "trading", "exchange", "broker" or "desk" | Lands in a queue for the customer success (CS) team |
| `block` | An explicit match such as "sportsbook" or "payday loans" | Denied with a message, nobody looks |

Businesses that already finished onboarding before the rule existed are
allowed through regardless.

**The queue.** The CS team sees the review queue as a JSON dump on an internal
page. They look up each business by hand, decide in Slack, and nothing is
recorded. If the same business comes back a week later, someone reviews it
again.

**What went wrong recently.**

- Three weeks ago a short-term lender called Loan in a Flash came through as
  `allow`. The rule looks for the word "payday" and they never used it. Google
  disapproved their campaign. That is one disapproval on the MCC in the
  lending category. A second one would be a strike.
- Last week a barbecue restaurant called Stake House BBQ was hard-blocked
  because "stake" is on the list of gambling brands. They emailed to ask why.
- The review queue has 31 open items. The oldest has been waiting since last
  Wednesday. CS says most of what they review turns out to be fine.

**The people.**

- **Priya Nair**, CS lead. Her team works the queue and talks to the customers
  who get blocked.
- **Daniel Okafor**, Head of Risk. Owns the Google policy relationship and the
  appeal if a strike happens.
- **Marco**, founder.

**You.** The PM for onboarding. Marco has asked you what we should actually
do. You have the thread, one page of notes from Priya, one page from Daniel, a
30-day export of the queue, and the codebase.

## The situation

Last week the CS lead posted in `#onboarding` that the review queue is "eating
the team". The Head of Risk replied. The thread is in `context/thread.md`.

What you have:

| File | What it is |
| --- | --- |
| `context/thread.md` | The Slack thread, verbatim |
| `context/notes-cs-lead.md` | One page from the CS lead on what she wants |
| `context/notes-risk.md` | One page from the Head of Risk on what he wants |
| `context/queue-export.jsonl` | 30 days of queue history: machine verdicts, CS decisions, and Google's outcome where we have it |
| `src/lib/moderation.ts` | The rule. Deterministic, no LLM. Returns `allow`, `review` or `block` with a reason code and evidence |
| `src/data/profiles.ts` | Twelve profiles waiting right now |
| `src/App.tsx` | What CS sees today: a JSON dump |
| `src/components/ui/` | Button, Card, Chip, Eyebrow, InsightBar, Stat, StatusBadge |

Nobody has written a task list. That is your job.

## What we are asking

1. **Decide what to ship.** Read the context. Pick the problem that matters
   most and say why the others can wait. You will not have time for everything;
   that is the point.
2. **Ship it.** Working code on a branch, in this repo, that a CS teammate could
   use tomorrow. In-memory state is fine. Keep the existing tests green and add
   tests for anything you change in the rule.
3. **Write the memo.** `MEMO.md`, one page, these headings in this order:
   - **The problem I chose**, and the two or three I did not, with one line each on why
   - **What I shipped**
   - **What I deliberately left out**
   - **How I would know it worked**: the metric, how it gets measured, what number would make you reverse the change
   - **With half the time**, what I would have cut
   - **Worst realistic outcome**, and how reversible it is
   - **Where AI helped, and where it was wrong**: at least one concrete case of each
4. **Show your work.** Keep your commit history as it happened; do not squash.
   Export or link your AI session transcript(s) into `transcript/`. We read
   them. A clean transcript with no corrections is a yellow flag, not a green
   one.

Push your branch to this repo and email us the link. Do not open a pull request.

## Constraints

**Brand.** The styles mirror the production design system. The rules:

- One accent. Spark Orange (`bg-spark`, `text-spark`) is the only brand color.
  Use it for the single most important thing on screen, nothing else.
- Never hardcode a hex. Use semantic classes (`bg-background`,
  `text-foreground`, `border-border`, `bg-card`, `text-muted-foreground`) and
  brand utilities (`text-mint`, `text-amber`, `text-danger`, `text-peri`,
  `text-cream`, `border-line`). The data palette carries meaning: mint is good,
  amber needs attention, danger is stopped, peri is done.
- Radii are `rounded-card` (14px), `rounded-control` (10px), `rounded-badge`
  (6px), `rounded-pill`.
- Numbers, IDs and timestamps are `font-mono tabular-nums` or `.font-data`.
  Labels and eyebrows are mono uppercase.
- Type roles come from `src/styles/type.css`. Prefer them over one-off sizes.
- Light is default. Anything you build should still read inside `.zone-dark`.
- Hairlines over shadows. Content visible before any animation.

Full token set: `src/styles/tokens.css`. Tailwind bridge: `src/styles/theme.css`.

**Policy.** Google's restricted categories are not negotiable. How we detect
them, how confident we need to be, and what happens on `review` are all yours.

## What we evaluate

We score five things. Nothing else counts.

| | What we look for |
| --- | --- |
| **Judgment** | You picked the right problem for the evidence you had, and you can defend what you skipped |
| **Execution** | It runs, it is tested, a CS teammate could use it, it respects the system it lives in |
| **Technical depth and AI leverage** | You directed the tools, verified them, and caught them when they were wrong |
| **Drive** | Where information was missing or contradictory, you noticed, made a call, and kept moving |
| **Measurement** | You know how you would find out you were wrong, and how fast you could undo it |

We do not score pixel polish, lines of code, or speed.

## The live session

Seventy-five minutes, after we have read your submission:

- 20 min: you walk us through it. We push back.
- 30 min: we change something and you extend the work live, tools on screen.
- 15 min: from this to a roadmap: metrics, how human decisions become rules, where an LLM belongs and where it does not.
- 10 min: questions both ways.
