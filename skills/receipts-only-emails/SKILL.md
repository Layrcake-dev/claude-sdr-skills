---
name: receipts-only-emails
description: Research each prospect's company (and optionally the person) and write short cold emails where every personal line is backed by a cited, real source, using a plain line when there's nothing real to say and skipping prospects the research shows aren't a fit. Use this whenever someone wants cold emails, outreach copy, first lines, personalised openers, an email sequence for a prospect list, or "write to these people", especially when accuracy matters and invented personalisation would embarrass them.
---

# Receipts-only emails

Most AI cold email fails the same way: it invents a "congrats on the launch" that never happened, and the prospect knows at once. This skill writes emails where every personal claim has a receipt: a real source the user can check. You do the writing and the judgement; Layrcake does the research and returns each fact with its source URL and a verbatim excerpt.

## Live data: check once, up front

Call Layrcake's `get_me` tool at the start. If the Layrcake tools aren't available, you can still help with structure and a clearly labelled non-personalised draft, then give the connect steps under "Connecting Layrcake" at the end of this file and stop. Never fill the gap with personalisation from memory or guesswork: that is exactly the failure this skill exists to prevent.

## 1. Get the inputs

- **Prospects**: name, title, company domain (often from `no-bounce-list`).
- **What the user sells**, in a sentence, and the outcome a customer gets.
- **Proof they're allowed to use**: customer names, numbers, results. If they have none, emails carry no proof line; never invent one.
- **The ask**: a reply, a 15-minute call or a link. Default to a soft question; it gets more replies than a calendar link from a stranger.

**Two prospects at one company:** research the company once, write to the best-placed person, and keep the other as a backup or a later step, never both on the same day. If that leaves fewer companies than the user asked for, take the next prospect from another company to make up the number.

## 2. Research the companies

For each company (up to 5 on a first pass), call `research_company` with `company_domain`, `audience` (what the user sells and to whom), and an `idempotency_key` unique per company (e.g. `research-<domain>-<date>`). Standard depth costs 9 credits when it finds facts and nothing when it doesn't. Say it before starting: "Researching 5 companies: about 45 credits."

It returns an `operation_id`. Poll `get_operation` every few seconds until `state` is `succeeded`, `partially_succeeded` or `failed` (a cached result comes back finished at once). The finished answer has `result.claims` (each with `source_url`, a verbatim `excerpt` and `published_at` when the source states a date), `result.why_now` (claims dated within the last 60 days), and the real charge at `result.cost_breakdown.total_credits`.

**Optional, for the one or two prospects that matter most:** `prospect_picture` with `person` and `depth: "standard"` (roughly 30–40 credits) adds a verified contact, intent signals and research in one call. Offer it; don't default to it.

## 3. Decide: fit, fact, or plain

**First, is it a fit?** If the research shows the company clearly doesn't need what the user sells (a research lab for a booking tool, a competitor, a company that has wound down), skip it and say why in one line. A good skip is worth more than a weak email.

**Then pick the one fact** most likely to make this person think "fair point, that's relevant to me":

1. **A recent, dated trigger** (within 60 days): from `why_now` (funding, a launch, a new market, a leadership change), or a **job post** the company search found (`matched_signals` with `signal: "hiring"`, its `evidence_url` and date). A live job ad for the role the user serves is often the strongest reason to write: "Saw you're hiring a Sales Manager for EMEA."
2. **A concrete fact about what they do or who they serve**, tied to the user's offer. Undated facts are fine here, as context, never as news.
3. **Nothing usable** → write the plain version, as long as the fit is clear from earlier steps (the search that found them, their profile). If the research came back empty and you can't tell whether they need it, say so and let the user decide rather than guessing.

A fact only counts if it's in the returned claims with a source. Check that its source is about *this* company (the domain, or a clear mention of it); if a claim might be about a namesake, leave it out and say so. Present something as news ("saw you just…") only when its `published_at` is within 60 days.

## 4. Write the email

Under about 90 words: short enough to read on a phone at a glance. One of three shapes, whichever fits the fact:

**Trigger → relevance → question.** When there's a recent event.
> Saw Acme opened its Leeds office last month. Teams scaling to a second site usually hit [problem] around now. We help [who] [outcome]. Worth a quick look, or not a priority yet?

**Observation → problem → proof → question.** With a solid fact but no event.
> Acme's checkout runs across 14 markets. Keeping [thing] consistent across that many usually means [problem]. [Customer] cut [metric] by [number] with us. Open to seeing how?

**The short question.** When there's nothing personal to say, honestly.
> Quick one: is [problem] something your team is working on this quarter? We help [who] [outcome]. Happy to share how if useful.

What keeps it credible:
- **One personal fact, stated plainly.** No flattery ("huge fan of…"), no pretend familiarity ("loved your recent post").
- **Never invent proof.** Only customers, numbers and results the user gave you.
- **Avoid lines that read as AI:** "I hope this finds you well", "I came across your profile", "in today's fast-paced world", "leverage", "synergy", "revolutionise", "game-changer", "just following up".
- **Subject line:** 2–5 words, lower case, no clickbait: "acme's leeds office", "question about [problem]".

## 5. Hand it over

For each prospect:
1. **The email**: subject and body, ready to paste.
2. **The receipt**: the fact used, its source URL, its date if stated, and the excerpt (for a job post: its title, link and date). The user checks this; it never goes in the email.
3. **The shape used**, or "plain version: no recent sourced fact", or "skipped: not a fit, because…".

Then what it cost (from each research answer's `cost_breakdown.total_credits`) and the next step: a short two-touch follow-up, or loading these into a campaign that sends only after the user approves it.

## Judgement calls

- **The user wants more personalisation than the facts support.** Explain once: a wrong personal detail costs more trust than a plain email. Then do the plain version well.
- **Follow-ups** each add one new useful thing (a proof point, a relevant resource, a different angle), never "just bumping this". Stop at three touches.
- **Large lists:** do the first 5, let the user approve the style, then continue in batches, saying each batch's cost first.

## Example

Prospect: Simon Gammon, Modulr (modulrfinance.com). The user places sales people into B2B software companies. `research_company` returns "Modulr launched commercial Variable Recurring Payments", dated 18 June, sourced to ffnews.com. That's older than 60 days, so it's context, not news:

> **Subject:** sales hiring at modulr
> Simon, with Modulr taking commercial VRP to market this year, I imagine pipeline targets went up with it. Good SDRs and AEs are slow to find. We place sales people into B2B software companies on contingency: a shortlist in 10 days, no fee unless we place. Is sales hiring on your radar, or covered?

Receipt: "Modulr announced the launch of commercial Variable Recurring Payments (cVRP)", ffnews.com, 18 Jun 2026. Credits: 6.

## Connecting Layrcake

Give these steps once when the tools aren't available. If you know which app you're running in, show only that app's line; otherwise show all three. Don't repeat the free-credits line elsewhere in your reply.

> Personalising from real, sourced facts needs live research from Layrcake: free to start with 300 credits (a credit is one US cent), about 30 seconds. Sign up at https://layrcake.dev/connect?utm_source=skillpack&utm_campaign=receipts-only-emails, then connect it here:
> - **Claude Code, with the Claude SDR plugin** (installed with `/plugin`): run `/reload-plugins`, then `/mcp` and choose Layrcake to sign in.
> - **Claude Code, skill copied on its own:** exit, run `claude mcp add --transport http layrcake https://mcp.layrcake.dev/mcp`, then `claude --continue` to come back to this conversation, and `/mcp` to sign in.
> - **Claude.ai:** Settings → Connectors → Add custom connector. Name it Layrcake and paste this as the URL: `https://mcp.layrcake.dev/mcp` (not the sign-up link). Sign in when asked, then switch Layrcake on in this chat's tools menu. On a Team or Enterprise plan, an owner adds the connector first.
>
> When it's connected, say "go" and I'll carry on from here.
