---
name: signal-prospector
description: Find the companies and people with a reason to buy right now, picking the buying signals that actually matter for what the user sells (a company hiring the role they serve, fresh funding, relevant news, a new leader in the buyer's seat, people posting about the exact problem) and returning each one with its dated evidence, ready to email. Use this whenever someone asks who to contact this week, wants warm or timely leads, intent signals, trigger events, "who is hiring for X", "who just raised", people complaining about a competitor, or a reason to reach out now, even if they don't say "signals".
---

# Signal prospector

A list of right-fit companies is good; the same list with a reason to write *this week* is what gets replies. This skill picks the signals that mean "now" for what the user sells, checks companies for them, finds people talking about the problem, and hands back each lead with its evidence and date. You choose the signals and judge them; Layrcake checks companies for live job posts, funding, news and leadership changes, and searches public posts on LinkedIn, X, Reddit and Facebook, each result with a link to check.

## Live data: check once, up front

Call Layrcake's `get_me`. If the Layrcake tools aren't available, still help: work out which signals matter for this offer (step 2) and what each would look like. Then give the connect steps under "Connecting Layrcake" at the end of this file. Never present a signal you couldn't check: a made-up "they just raised" is worse than no signal.

## 1. Get the inputs

- **What the user sells, and to whom**, in a sentence (their profile from `start-from-website` if they ran it).
- **Companies to check** (domains), if they have a list. If not, start from the profile with `start-from-website`, or go straight to posts (step 4).

## 2. Pick the signals that mean "now" for this offer

Pick one to three; more signals cost more and add noise.

| If the user sells… | Signals that matter | Where |
|---|---|---|
| recruiting, or tools for a team (sales, support, engineering) | hiring for that role | `find_intent` hiring with `roles`, and `find_posts` with `intent: "hiring_for_it"` (hiring managers posting that they're hiring) |
| to companies that just got budget (agencies, infrastructure) | funding in the last few months | `find_intent` `funding` |
| security or compliance (SOC 2, ISO 27001, pen testing) | hiring a first security or compliance person, or moving upmarket (an enterprise launch, recent funding) | `find_intent` hiring with security and compliance `roles`, then `news` or `funding` on the best few |
| to a specific leader (a new CMO reviews agencies, a new VP Sales reviews tools) | a leadership change | `find_intent` `job_change` |
| something tied to an event (expansion, a launch, compliance) | news of that kind | `find_intent` `news` |
| a fix for a problem people complain about (or a competitor people dislike) | people posting the pain or asking for a fix | `find_posts` with `intent` |

Say which signals you picked and why, in a line.

## 3. Check companies for signals

Call `find_intent` with up to 10 `company_domains` (start with the user's best 5–10):
- **Hiring** is the default and checked first: leave `signals` out, and only hiring is checked. A company not hiring costs nothing; one hiring now costs 11 credits and its latest funding round comes with it. Add `roles` (job titles) to count only the roles the user serves.
- **Other signals** (`funding` on a company not hiring, `news`, `job_change`) are paid checks at 13 credits each, whatever they find, so they add up fast (six companies × funding = 78 credits): add them only when step 2 picked them, and run them on the best two or three companies rather than the whole list. A company that's hiring already brings its funding for free.
- Add `audience` (what the user sells and to whom): signals then come ordered by how good a reason they are, each with `why_now`.

Say the most it can cost before calling ("Checking 8 companies for hiring: up to 88 credits, nothing for companies not hiring"). Read each company's `domain_status` for what was checked and what it cost; a company that was rate-limited costs nothing, so retry it once. Signals older than the window come back under `historical`: context, not a reason now.

## 4. Find people talking about the problem

Call `find_posts` with a `query` naming the problem in the words buyers use ("cold email deliverability", "CRM migration", "hiring SDRs is slow"), `platforms` (`linkedin` and `reddit` are a good start; `x` for tech), `intent: "pain"` or `"question"` (or `competitor_complaint` to find people unhappy with a tool like the user's, `hiring_for_it` for companies hiring for the work), and `audience`. Keep `limit` at 20.

Say the cost first, including the most it can cost: keyword posts cost 0.5 credits each (2 on Reddit and Facebook), plus a small classification charge (about 0.2 credits per post read, typically 2–4) that applies even when nothing comes back. At `limit: 20`, LinkedIn alone is at most about 12 credits; with Reddit it can reach about 42. Posts that don't match the `intent` are left out, so you only see the ones that do. If social search answers `provider_unavailable`, or the answer says its posts couldn't be classified, try once more a minute later; if it fails again, say post search is down right now and hand over the company signals. If a search returns nothing, try the other intent once (`hiring_for_it` instead of `pain`, say) or a plainer query, rather than repeating it. Each post comes with its label, a reason, the author and a link. **Judge each post by its author, not its label:** a hiring manager saying "I'm hiring an SDR for my team" can be labelled a pitch and is still the best lead; a vendor, an outsourcing firm or a recruiter advertising someone else's role is not a lead, whatever the label.

**Optional, for one strong post:** `find_posts` with `engagers_of` (that post's URL) and `author_filter` (the buyer's job title words) returns the people who reacted or commented, with those titles: 0.5 credits per person plus 0.4 per person read. Offer it; don't default to it.

## 5. Hand it over

1. **The signals picked**, and why.
2. **Companies with a live signal**, strongest first: company, the signal in a line ("hiring a Sales Development Representative"), the date it was seen and the evidence link. One posting per company comes back, so don't claim a count of open roles; flag a role that only loosely matches (a client account manager when the user places new-business AEs).
3. **People posting about the problem**: who, what they said (a short quote), when, the link.
4. **What it cost**, from each answer's `credits_charged`.
5. **The next step**: "Want me to find the decision-makers at these companies and write to each one using its signal?" (the `no-bounce-list` and `receipts-only-emails` skills: a dated signal within 60 days is the strongest opening line).

## Judgement calls

- **No signals found.** Say so plainly: it's an answer, not a failure. Suggest a wider window (`within_days`), more companies, or the post search.
- **An old signal.** Funding more than about 6 months ago, or a job post older than 60 days, is context, not a reason to write now.
- **A post from someone who can't buy** (a student, a vendor, a recruiter) isn't a lead, however on-topic.
- **Posts name people.** Use what they posted publicly as context for a relevant message; never quote someone's post back at them in a way that feels like surveillance ("saw your post about X" is fine; listing their activity is not).

## Example

User: "I place SDRs and AEs. Who should I call this week? Here are my 8 targets." Signals: hiring for SDR and AE roles. `find_intent` on the 8 with `roles: ["SDR", "BDR", "Account Executive"]` and their `audience`: 5 are hiring (55 credits), each with its job link and the date it was seen; the other 3 cost nothing. Funding came back with them, all over a year old: background, not a reason. `find_posts` `query: "hiring SDRs"`, `intent: "hiring_for_it"`, LinkedIn, `limit: 20` (about 2 credits): a head of demand gen posting "I'm hiring an SDR for my team" is a lead; an outsourced call centre hiring reps is not. Hand back the 5 companies and the 1 person with their evidence, and offer to find the hiring managers and write to them.

## Connecting Layrcake

Give these steps once when the tools aren't available. If you know which app you're running in, show only that app's line; otherwise show all three. Don't repeat the free-credits line elsewhere in your reply.

> Checking companies and posts for live buying signals needs Layrcake: free to start with 300 credits (a credit is one US cent), about 30 seconds. Sign up at https://layrcake.dev/connect?utm_source=skillpack&utm_campaign=signal-prospector, then connect it here:
> - **Claude Code, with the Claude SDR plugin** (installed with `/plugin`): run `/reload-plugins`, then `/mcp` and choose Layrcake to sign in.
> - **Claude Code, skill copied on its own:** exit, run `claude mcp add --transport http layrcake https://mcp.layrcake.dev/mcp`, then `claude --continue` to come back to this conversation, and `/mcp` to sign in.
> - **Claude.ai:** Settings → Connectors → Add custom connector. Name it Layrcake and paste this as the URL: `https://mcp.layrcake.dev/mcp` (not the sign-up link). Sign in when asked, then switch Layrcake on in this chat's tools menu. On a Team or Enterprise plan, an owner adds the connector first.
>
> When it's connected, say "go" and I'll carry on from here.
