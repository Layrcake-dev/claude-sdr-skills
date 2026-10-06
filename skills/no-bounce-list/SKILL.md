---
name: no-bounce-list
description: Find the right decision-makers at a set of companies (or matching a role, industry and location) and return work emails that have each been checked for deliverability, marked send, hold or remove, plus mobile numbers only on request. Use this whenever someone wants contacts, a lead list, emails for prospects, "who should I email at these companies", to enrich or clean a list, or to check whether emails will bounce, even if they don't say "verify".
---

# The no-bounce list

The user wants people they can actually email: the right person at the right company, with an address that won't bounce and burn their sending domain. You choose who matters and judge the results; Layrcake finds the people and their emails, and checks every email before returning it.

## Live data: check once, up front

Call Layrcake's `get_me` tool at the start. If the Layrcake tools aren't available, say so in **one line at the top of your first reply** ("I can plan this now; finding people and checking emails needs a free Layrcake connection, steps below"), then give real value without data: the titles to target (step 1), how many people per company, and what the lookup will cost. Then give the connect steps under "Connecting Layrcake" at the end of this file and stop. Never write guessed emails (firstname@company.com and similar): a guessed address that bounces costs the user more than no address.

## 1. Decide who to look for

- **A list of company domains** (often from `start-from-website`): people at those companies.
- **A description** ("heads of sales at UK fintechs"): people by role, industry and place.
- **A spreadsheet of names or emails**: skip to step 3 (names) or step 4 (emails to check).

Pick two to four titles people actually hold: the person who signs and the person who feels the problem ("VP Sales", "Head of Sales"; "Founder" or "Owner" at companies under about 20 staff; "Practice Manager" at a clinic). Specific titles find the right people; broad ones ("Manager") find everyone.

## 2. Find the people

Call `find_leads` with:
- `audience`: one sentence, e.g. "Heads of Sales at UK fintech companies with 50–200 staff"
- `titles`, plus `company_domains` (up to 100) and/or `industries`, `company_headcount`
- `locations` too when the user cares where people are (it works alongside `company_domains`, so "people at these companies, in the UK" is one search)
- `limit: 25` for a first pass

It costs 0.25 credits per person returned, at least 3 credits when anyone is returned; nobody found is free. Results have names, titles, companies and LinkedIn, but no emails yet.

Read two things in the answer:
- **`title_filter.withheld`** (only present when the title check left people out): people the sources found whose titles didn't match. If it's there and you have too few people, add the titles they might hold and search again.
- If an industry or location is rejected, the answer names it: swap it for LinkedIn's wording ("Software Development", not "SaaS") and say what you changed.

## 3. Choose who to look up, then get their emails

Pick **up to 10**: the closest title match at each company, at most two per company. Before paying, leave out rows that are clearly wrong: a person far from the place the user asked for, a title that doesn't fit the brief, a row that is really a company page. Say who you picked and why in a line or two.

Each email found costs 5 credits; nothing found costs nothing. Say the most it can cost before starting: "Looking up 10 people: up to 50 credits."

For each person call `enrich` with `person: { full_name, company_domain }`, adding `linkedin_url` whenever you have it (it matches more reliably), and `want: ["work_email"]`. Add `"mobile_phone"` **only if the user asked for phone numbers** (a mobile found by a separate search costs 17 credits).

**Every email enrich returns has already been checked.** Its `email_verdict` is the check's own result, so you don't need `verify_email` for these. An address that failed the check is never returned (and never charged): enrich tries its next source instead, and if nothing passes it answers with no email, free.

If enrich finds no email for someone: try again with their `linkedin_url` if you didn't use it, or pick the next-best person at that company. Don't retry with guessed spellings.

If enrich comes back with a **different name, title or location** than the person you searched for (another person, or an old job), don't treat it as a match: flag it, and use it only if the user agrees.

Never show a phone number the user didn't ask for, even if one comes back.

## 4. Sort by what the verdict means for sending

| `email_verdict` | Tell the user |
|---|---|
| `valid` | **Send** |
| `catch_all_valid` | **Send.** The domain accepts everything, but this address looks real |
| `catch_all_unknown`, `risky`, `unknown`, or no verdict (the check couldn't finish) | **Hold.** Send in small numbers, if at all; this is how bounce rates creep up |

For emails the user brings themselves (a pasted list), call `verify_email` on each (1.25 credits) and use the same table, plus `invalid` → **Remove**.

## 5. Hand it over

1. **The list** as a table: name, title, company, email, verdict (send / hold), LinkedIn, and mobile only if requested. Ready-to-send rows first.
2. **The summary**: "7 of 10 ready to send, 1 to hold, 2 not found."
3. **What it cost**, added up from each tool's `credits_charged`.
4. **The next step**: "Want me to research these companies and write a first email to each, using only real facts?" (the `receipts-only-emails` skill).

Offer a CSV if they want to import it.

## What to expect, honestly

On B2B software companies, expect roughly 6–8 sendable emails from 10 lookups. On small local businesses, expect fewer: they often have no findable work email. If too few survive, say so and offer the two ways on, with costs: more people at the same companies, or more companies (`start-from-website`).

## Judgement calls

- **Tiny companies (under ~20 staff):** the founder or owner is usually the buyer.
- **A company returns nobody with those titles.** Say so, and suggest the founder or a revenue or growth lead there instead (founder-led and product-led companies often have no sales title).
- **An email at a different company's domain** than the person's company: flag it; it may be an old job.
- **Personal addresses** (gmail and similar) in a list the user brings: flag them; rarely the right channel for B2B.

## Example

User: "Find the sales leaders at these 10 companies and get their emails." `find_leads` with `titles: ["VP Sales", "Head of Sales", "Chief Revenue Officer"]`, the 10 `company_domains`, `limit: 25` (about 3–6 credits); pick the best person at each company; `enrich` each with their LinkedIn (up to 50 credits, only for emails that pass the check); hand back "7 ready to send, 1 to hold, 2 not found" with the exact credits charged.

## Connecting Layrcake

Give these steps once when the tools aren't available. If you know which app you're running in, show only that app's line; otherwise show all three. Don't repeat the free-credits line elsewhere in your reply.

> Finding real people and checking their emails needs live data from Layrcake: free to start with 300 credits (a credit is one US cent), about 30 seconds. Sign up at https://layrcake.dev/connect?utm_source=skillpack&utm_campaign=no-bounce-list, then connect it here:
> - **Claude Code, with the Claude SDR plugin** (installed with `/plugin`): run `/reload-plugins`, then `/mcp` and choose Layrcake to sign in.
> - **Claude Code, skill copied on its own:** exit, run `claude mcp add --transport http layrcake https://mcp.layrcake.dev/mcp`, then `claude --continue` to come back to this conversation, and `/mcp` to sign in.
> - **Claude.ai:** Settings → Connectors → Add custom connector. Name it Layrcake and paste this as the URL: `https://mcp.layrcake.dev/mcp` (not the sign-up link). Sign in when asked, then switch Layrcake on in this chat's tools menu. On a Team or Enterprise plan, an owner adds the connector first.
>
> When it's connected, say "go" and I'll carry on from here.
