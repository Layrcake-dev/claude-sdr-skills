---
name: launch-safely
description: Turn a checked contact list and written emails into a campaign that is safe to send: only addresses that passed an email check, suppressed and recently-contacted people left out, volume kept within what the mailboxes can carry, a short sequence that stops on reply, all built as a draft the user reviews before anything sends. Use this whenever someone wants to send their cold emails, launch or set up an outreach campaign, load leads into a sequence, "start sending", or asks whether a campaign is safe to launch, even if they don't mention deliverability.
---

# Launch safely

The user has people to email and emails to send. What goes wrong at this step is rarely the copy: it's bounced addresses that burn a new domain, too many sends from too few mailboxes, emailing someone who opted out, or a sequence that keeps going after a reply. This skill builds the campaign so none of that happens, as a **draft**, and launches only when the user says so. You do the checks and the judgement; Layrcake holds the mailboxes, the suppression list and the send limits, and won't send until a person approves.

## Live data: check once, up front

Call Layrcake's `get_me`. If the Layrcake tools aren't available, you can still help: review the list and the copy against the checks below and lay out the sequence. Then give the connect steps under "Connecting Layrcake" at the end of this file. Never tell the user a campaign is ready to send if you couldn't check it.

From `get_me`, note `guard.launch_recipients_left_today` and `guard.campaign_daily_capacity_left`: how many new people the workspace can start emailing today.

## 1. Get the inputs

- **The list**: name, email, company, and each email's check result (`email_verdict` from enrich, or a `verify_email` result). Usually from the `no-bounce-list` skill.
- **The emails**: a first email per person, or one template with `{{first_name}}`-style variables, plus up to two follow-ups. Usually from `receipts-only-emails`.
- **Who it's from**: the sender's first and last name, for the mailboxes, plus their role and company for the sign-off.

## 2. Check the list before anything is built

Keep only addresses safe to send:

| Check result | What to do |
|---|---|
| `valid` | Keep |
| `catch_all_valid` | Keep |
| `catch_all_unknown`, `risky`, `unknown` | Leave out by default (they were checked; another check won't change them) |
| No check at all | Leave out until checked: offer `verify_email` on them (1.25 credits each) |
| `invalid` | Remove, always |
| No email found | Leave out; say who, so the user can find another contact at that company |

Also leave out duplicates, role inboxes (info@, sales@, support@) unless the user means them, and personal addresses (gmail and similar) for a B2B campaign. Say what you left out and why in one line each: "Left out 3: 2 risky, 1 info@ inbox."

## 3. Check the copy

- Every email has a subject (2–5 words, lower case, no clickbait) and reads as one person writing to another.
- Each follow-up adds something new. Two follow-ups at most, 3–4 days apart.
- No invented facts: a personal line must come from the research behind it.
- Variables the copy uses exist on every lead (a `{{first_name}}` with a blank first name reads badly: fill it or drop the variable).
- A claim in a shared template goes to everyone, so it must be true for everyone ("saw you're growing your sales team" isn't). A per-person fact belongs in a custom field per lead, from the research behind it. When you find one, rewrite the line so it's true for everyone (or move it to a field), show the change, and use the rewrite unless the user objects.
- The email ends with the user's own sign-off in the body (name, role, company): the campaign has no separate signature.

## 4. Make sure there's room to send

- **Volume:** keep a new campaign within what's left today (`launch_recipients_left_today`) and, for new mailboxes, to roughly 20–30 new people per mailbox per day. A larger list goes in over several days; say how many.
- **Mailboxes:** ask whether sending is already set up in Layrcake (the tools don't list mailboxes; `launch_campaign` refuses without them). If not, or if the plan needs more, call `setup_sending` with `emails_per_week` (people × steps spread over the weeks the campaign runs; at least 50), `sender` as `{ "first_name": …, "last_name": … }`, and, if they have one, `brand_hint` (a short brand word for the sending domains). Without a confirmation token it only returns a **quote** and an approval link: domains, mailboxes and the monthly cost. Show it plainly. Nothing is bought until the user approves it on that link. New mailboxes need time to warm up before they send at full volume; say so.

## 5. Build the draft

Call `create_campaign` (free: a draft costs nothing) with:
- `name`: who and what, e.g. "UK fintech heads of sales, Oct"
- `leads`: only the people kept in step 2, with `first_name`, `last_name`, `company_name` and any custom fields the copy uses
- `steps`: the first email (`subject`, `body`), then the follow-ups with `wait_days` (3–4)
- `sending_window`: `{ "timezone": "<zone>", "days": ["mon","tue","wed","thu","fri"], "start_hour": 9, "end_hour": 17 }`, in the recipients' time. Always set the time zone: left out, it is UTC, which puts a US list's emails out before dawn. Use the normal name: `Europe/London`, `Europe/Dublin`, `Europe/Paris`, `Europe/Berlin`, `America/New_York`, `America/Chicago`, `America/Denver`, `America/Phoenix`, `America/Toronto`, `Asia/Kolkata`, `Asia/Singapore`, `Australia/Sydney`. US Pacific time (`America/Los_Angeles`) isn't available yet: for a West Coast list, tell the user and suggest `America/Denver` with the whole window an hour later (10 to 18 for a 9-to-5 day), if they agree. Recipients in different time zones go in separate draft campaigns, one per zone
- an `idempotency_key` (e.g. `campaign-<name>-<date>`)

It stops on reply by itself. Drafts never send. Read the answer's `removed` counts and their reasons (opted out, invalid, blocked by the platform, over a limit, other; people another of the workspace's campaigns emailed in the last 90 days are left out too, counted among these). Tell the user the final number going out and why any were removed.

More people later: `add_leads` with the same checks first.

## 6. Hand it over, then launch only on a clear yes

1. **The pre-flight summary**: people going out (and who was left out, by reason), the steps and their timing, the sending window, mailboxes and today's limit, and the date the last follow-up would go.
2. **The first email as one recipient will see it**, with variables filled in.
3. **Ask: "Launch now?"**

Only after a clear yes, call `launch_campaign` with the `campaign_id`. It first returns a preview with an approval link (and a recheck of who is removed at launch): show it, and ask the user to approve on that link. Layrcake starts sending once a person approves there. If they want changes, change the draft instead, and never launch on a maybe.

## Judgement calls

- **The user wants to include risky or unchecked addresses.** Explain once: a few bounces on a new domain can push every later email into spam. Offer `verify_email` first; if they still want them, suggest a separate small batch.
- **A big list on new mailboxes.** Start small (a day's limit) and grow over the next week; the first days decide the domain's reputation.
- **A reply comes in.** The sequence stops for that person by itself. Hand over to the `reply-desk` skill.

## Example

User: "Send these 10 to the people we found." Six are `valid`, two `catch_all_valid`, two `risky`. Keep eight, leave out the two risky ones and say so; the workspace can start 200 people today and has two warmed mailboxes. `create_campaign` with the eight, the first email and two follow-ups at 3 and 4 days, weekdays 9–17: the answer removes one person emailed by another campaign last month, so seven go out. Show the summary and one filled-in first email, then ask "Launch now?". On yes, `launch_campaign`, and the user approves on the link.

## Connecting Layrcake

Give these steps once when the tools aren't available. If you know which app you're running in, show only that app's line; otherwise show all three. Don't repeat the free-credits line elsewhere in your reply.

> Building and sending the campaign needs Layrcake: free to start with 300 credits (a credit is one US cent), about 30 seconds. Sign up at https://layrcake.dev/connect?utm_source=skillpack&utm_campaign=launch-safely, then connect it here:
> - **Claude Code, with the Claude SDR plugin** (installed with `/plugin`): run `/reload-plugins`, then `/mcp` and choose Layrcake to sign in.
> - **Claude Code, skill copied on its own:** exit, run `claude mcp add --transport http layrcake https://mcp.layrcake.dev/mcp`, then `claude --continue` to come back to this conversation, and `/mcp` to sign in.
> - **Claude.ai:** Settings → Connectors → Add custom connector. Name it Layrcake and paste this as the URL: `https://mcp.layrcake.dev/mcp` (not the sign-up link). Sign in when asked, then switch Layrcake on in this chat's tools menu. On a Team or Enterprise plan, an owner adds the connector first.
>
> When it's connected, say "go" and I'll carry on from here.
