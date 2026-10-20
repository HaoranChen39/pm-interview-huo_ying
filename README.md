# Hellyeah · Onboarding moderation (PM take-home)

You are the product manager for onboarding at Hellyeah. This is a slice of the
real codebase: brand tokens, a few UI primitives, the rule that decides whether
a newly onboarded business may advertise on Google Ads, and the screen the
customer success (CS) team uses today.

We are a small team. The PM here decides what gets built, builds a lot of it,
and is accountable for whether it worked. This exercise mirrors that.

Budget about **four hours**, spread over up to five days. Use whatever tools you
normally use, including AI coding assistants. We will ask to see how you used
them.

## Setup

Node 20 or newer.

```bash
npm install
npm run dev        # http://localhost:5173
npm test           # baseline tests, keep them green
```

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
