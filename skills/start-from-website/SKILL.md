---
name: start-from-website
description: Turn a company's website (or a short description, and any best customers) into a precise ideal customer profile and a first list of real matching companies with size, industry and location. Use this whenever someone wants to figure out who to sell to, build a target account list, define their ICP, find companies like their best customers, find local businesses to sell to, or start prospecting from scratch, even if they only paste their URL and say "who should I go after?"
---

# Start from your website

The user wants to know who to sell to and leave with real companies they can act on today, not a persona document. You do the thinking (reading their site, sharpening the profile, picking the right search); Layrcake supplies the live company data.

## Live data: check once, up front

Call Layrcake's `get_me` tool at the start. If it answers, carry on. If the Layrcake tools aren't available, say so in **one line in your first reply** and put the connect steps in that same reply, after the draft profile ("I can draft your profile now; finding real companies needs a free Layrcake connection, details below"), do steps 1–2, then give the connect steps under "Connecting Layrcake" at the end of this file and stop. Don't invent example companies: a made-up list looks like a real one and sends people the wrong way.

## 1. Understand what they sell

Read the website the user gives you (if you can't fetch pages, ask for the homepage copy or a two-sentence description). Quote any prices in the currency the site shows. Work out:

- what they sell, the problem it solves, and **what kind of business buys it**: a software company, a local premises-based business (clinic, gym, restaurant, shop, trade), an agency, or a mix;
- who visibly buys today (logos, case studies, testimonials, pricing tiers), and from the case studies **why** each kind of customer bought (the problem in their own words);
- signs of deal size: self-serve pricing points to small companies; "book a demo" and security pages to larger ones.

Ask once: "Who are two or three of your best customers?" Named testimonials and logos on their site count if they have none to add. If they describe types instead of naming companies ("two dental practices and a co-working space"), use those types as the categories or industries and carry on.

## 2. Write the profile

Every field maps to a real filter, which is why vague words like "SMBs" don't help:

| Field | What to write | Example |
|---|---|---|
| Kind of buyer | software company / local business / agency / other | local business |
| Industries or categories | LinkedIn industry names, or local categories | "Software Development" · "dentist" |
| Countries | ISO two-letter codes | US, GB |
| Places | cities or regions, if it matters | "Manchester, United Kingdom" |
| Headcount | a min and max | 20–200 |
| Buyer titles | who signs, and who feels the pain | "Head of Sales", "Practice Manager" |
| Exclusions | looks right but isn't | competitors, agencies reselling the same thing, acquired companies |
| Buying trigger | what makes now the time | hiring SDRs, uses a competitor's tool, opening a second site |

Show it as a short table and ask "Anything to tighten?" One round of edits is usually enough.

## 3. Pick the search that fits the buyer

The right search is what makes the list good. An industry filter alone ("Software Development, US, 20–200") returns anything in that industry; the searches below find companies that actually have the need.

| If the buyer is… | Search | Cost per company returned |
|---|---|---|
| **A local business** (clinic, gym, salon, restaurant, trade) | `find_companies` with `mode: "local"`, exactly **one** category and **one** location per call (e.g. `categories: ["dentist"]`, `locations: ["Manchester, United Kingdom"]`); run one call per category or town. No `country_codes` in this mode. For a buyer spread across a whole country, start with one or two cities (where the user's best customers are, or the largest metro) at `limit: 5` per category, and widen once the user likes what comes back. Results carry no headcount: enrich only the best before claiming a size. Some small businesses only have a Facebook page: keep them, but use the business name, not `facebook.com`, in later steps | 3 |
| **A company that uses a tool yours replaces or competes with** | `find_companies` with `technologies` (the tool's usual name, e.g. `["Customer.io"]`; most tools are known, and a refused name comes back with close suggestions), plus `country_codes` and `company_headcount`. Only a good signal when using that tool shows the need you solve: a competitor's customers already pay for the problem. **Not** for a tool companies use for their own admin (companies found by "uses Calendly" mostly book their own meetings) | 16 |
| **A company growing the team you serve** (recruiters, sales tools, onboarding tools) | `find_companies` with `hiring: { titles: [...], posted_within_days: 30 }`, plus `country_codes` and `company_headcount` | 11 |
| **A software product with named customers** (on their site or from the user) | Grow outward from the real customers, below ("Lookalikes of your customers") | 33 per company kept; companies that don't fit are free |
| **Anything else** (no named customers to start from) | `find_companies` with `mode: "firmographic"`: `industries`, `country_codes`, `company_headcount`, and `locations` if a city matters. Cheap, and its results already carry size and country; pair it with step 4 to find the ones with the need. (A plain-language `query` also works, but only when it names a role being hired or a technology used: "UK software companies hiring SDRs".) | 2 |

Apply the profile you agreed: pass its `country_codes`, `company_headcount` and `locations` with the search you pick (local mode takes only its one category and one location; `similar_to` takes `country_codes` and `company_headcount` but not industries), so the agreed profile is actually used. Combine where it helps, for example hiring for sales roles *and* UK *and* 20–500 staff.

Keep the first pass small (`limit: 10`, or 5 per seed for lookalikes). Before the first call, say what the whole first pass will cost, search plus check plus profiles ("Lookalikes of 2 customers, 5 each: up to 330 credits, usually about half that, and nothing for companies that don't fit"), and ask if that's fine. A search that finds nothing is free: loosen one thing (a wider headcount, a neighbouring category, a second city) and say what you changed.

### Lookalikes of your customers

For a software product, its real customers are the best guide to the next ones. A lookalike search returns a company's closest **competitors and peers**: Drata's are Vanta and Secureframe; Gorgias's are Kustomer and Trengo. When what made the customer buy is true of its peers too (they all send their users alerts, they all book appointments), those peers are prospects.

1. **Group the customers** you found in step 1 by kind (developer tools, support software, health apps…), with why each kind bought.
2. **Pick 2 seeds** (a third only if the user wants a longer list), one per kind, the customers most like the user's target. Skip a seed whose reason to buy is unusual for its market.
3. **Run `similar_to` on each seed** with the profile's `country_codes` and `company_headcount`, `limit: 5`. Each company comes back with its size, country and industry; ones that don't fit are dropped, counted in `filtered_out`, and cost nothing. A company the data couldn't size is kept with its size blank: say "size unknown", never guess.
4. **Drop the user's existing customers** (anyone on their customer page or named by them) and obvious misfits by kind (a care provider in a list of software products).
5. **Offer to branch out once:** lookalikes of the two best new companies, same filters. One level is enough; further out, the list drifts away from the customers it started from.
6. **Optional:** companies using a competitor of the user's product (the technology search above), when the user names one.

In the hand-over, give each company's reason tied to its seed: "like Drata, sends compliance alerts to every user", "lookalike of Gorgias". Skip step 4's check for these: the seed is the evidence.

## 4. Check each company has the need

A search finds companies of the right kind; it can't tell whether each one actually has the need the user solves (a booking tool's buyer runs booking inside its own product; a Zapier competitor's buyer offers no Zapier connection). When the need is something a company's own website would show, check it before going further:

Turn the need into one plain yes/no question about the company's own offering, as the user would ask it: "Does this company let its customers book appointments inside its product?", "Does this company's product connect to Zapier?", "Does this company sell to law firms?". Call `check_companies` with that `question` and the found `company_domains` (up to 10 per call; it takes about a minute). Say the cost first: 3.5 credits per company answered yes or not found, unclear answers free.

- **yes** comes with a sentence from the company's own site that says it, and its page: keep the company, and keep the quote, since it is the best reason to write later.
- **not_found** means its pages showed no sign of it: drop it, or keep it lower down if the user wants a longer list.
- **unclear** (free): keep it as "unconfirmed" and say so.

Skip this step when the need is obvious for the kind of business (a dental practice obviously needs a cleaner) and when the search itself already proves the need (a company hiring the exact role a recruiter fills). For local businesses, skip it too: whether they already use a tool like the user's usually sits in an embedded booking widget the check can't see, so don't promise that split.

**If the check confirms fewer than 3:** say so plainly, don't spend anything on people or emails for the rest, and propose one different search (with its cost) that is more likely to find companies with the need. Never pad the list with unchecked companies to make it look longer.

## 5. Check the fit, then add depth to the best three

Before handing over, drop obvious misses and say why in a few words: the user's competitors, agencies, companies the result shows as acquired, national chains when the user sells to independent sites, anything outside the agreed countries, and companies with **no visible need** for what the user sells (a company that uses a scheduling tool only to book its own sales demos doesn't need scheduling inside its product). Every company dropped here saves paid lookups later. A short honest list beats a padded one.

Keep each company's `matched_signals` (the job post or technology that found it, with its `evidence_url` and date): the email skill can use a recent one as its reason to write.

Then call `enrich_company` with `want: ["profile"]` on the three best fits (13 credits each when it finds real company facts; free when it doesn't). It returns headcount, industry, country and LinkedIn where known. Technology and local results carry little of this themselves, so this step fills the table; firmographic results and lookalikes searched with the profile's filters already have it, so skip this step for them unless the user wants more. If funding comes up, give its date; a round more than 18 months old isn't a current trigger.

## 6. Hand it over

1. **The profile** (the agreed table).
2. **The companies** as a table: company, domain, industry, headcount, location, and a one-line "why it fits" based only on returned data (the search that found it counts: "hiring 3 SDRs", "uses Calendly"; a check_companies yes counts with its quote and page).
3. **The top three in more depth**: what they do, size, and the buying trigger you'd lead with, if one is visible.
4. **What it cost**, added up from each tool's `credits_charged`.
5. **The next step**: "Want me to find the decision-makers at these companies and get emails that won't bounce?" (the `no-bounce-list` skill).

## Judgement calls

- **Several audiences.** Pick the one with the clearest buyer and say why; offer the others as a second run.
- **A very broad ICP** ("any B2B company"). Push back once: narrow lists get replies. Suggest the narrowest slice their evidence supports.
- **No website or pre-launch.** Build the profile from their description and say it's a first guess to test.
- **Claim only what the data shows.** "Why it fits" comes from returned fields and the search used, never guesses.

## Example

User: "We run a commercial cleaning company in Manchester. Who should we go after?" Local buyer → `find_companies` `mode: "local"` once for `categories: ["dentist"]` and once for `["coworking space"]`, each with `locations: ["Manchester, United Kingdom"]` and `limit: 10` (up to 30 credits each). Drop anything outside Greater Manchester or obviously not a premises-based business, enrich the three best (up to 39 credits), and offer to find the practice managers.

User: "This is knock.app; customers include Drata, Gorgias and Clay. US and UK, 50–1,000 staff." Software with named customers → lookalikes: `similar_to` on drata.com, gorgias.com and clay.com, each with `country_codes: ["US", "GB"]` and `company_headcount: { min: 50, max: 1000 }`, `limit: 5` (up to 495 credits if every lookalike fits, usually about half; misfits free; with a small budget, start from two seeds). Vanta comes back over 1,000 staff and is dropped free; Secureframe (125, US) and Kustomer stay. Hand over each with its seed's reason ("like Drata, alerts every user about failing controls"), drop Knock's existing customers, and offer to branch out from the two best.

## Connecting Layrcake

Give these steps once when the tools aren't available. If you know which app you're running in, show only that app's line; otherwise show all three. Don't repeat the free-credits line elsewhere in your reply.

> Finding real companies needs live data from Layrcake: free to start with 300 credits (a credit is one US cent), about 30 seconds. Sign up at https://layrcake.dev/connect?utm_source=skillpack&utm_campaign=start-from-website, then connect it here:
> - **Claude Code, with the Claude SDR plugin** (installed with `/plugin`): run `/reload-plugins`, then `/mcp` and choose Layrcake to sign in.
> - **Claude Code, skill copied on its own:** exit, run `claude mcp add --transport http layrcake https://mcp.layrcake.dev/mcp`, then `claude --continue` to come back to this conversation, and `/mcp` to sign in.
> - **Claude.ai:** Settings → Connectors → Add custom connector. Name it Layrcake and paste this as the URL: `https://mcp.layrcake.dev/mcp` (not the sign-up link). Sign in when asked, then switch Layrcake on in this chat's tools menu. On a Team or Enterprise plan, an owner adds the connector first.
>
> When it's connected, say "go" and I'll carry on from here.
