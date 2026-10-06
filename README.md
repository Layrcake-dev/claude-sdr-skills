# Claude SDR

Six sales skills for Claude that work on real data: find the right companies and the reason to write this week, get emails that won't bounce, write emails where every personal line has a source, launch without burning your domain, and work your replies.

Every data step runs on [Layrcake](https://layrcake.dev): free to start, 300 credits when you sign up.

## The skills

| Skill | Ask Claude something like | What you get | Credits, first run |
|---|---|---|---|
| **Start from your website** | "This is our site. Who should we go after?" | A sharp customer profile and a list of real companies that fit, each checked for the need you solve, with the sentence from their own site that shows it | about 40–200 |
| **The no-bounce list** | "Find the heads of sales at these companies and get their emails" | The right people, with work emails checked before you see them: send, hold or remove | about 50 for 10 people |
| **Receipts-only emails** | "Write to these five people" | Short cold emails where every personal line comes with its source link, and a plain email when there's nothing real to say | about 45 for 5 companies |
| **Signal prospector** | "Who should I call this week?" | Companies hiring the role you serve, raising, or changing leaders, and people posting about the problem, each with dated evidence | about 55–65 |
| **Launch safely** | "Send these" | A draft campaign with checked addresses only, the right time zone and volume, and a pre-flight summary. Nothing sends until you approve | free |
| **Reply desk** | "Any replies?" | Replies sorted by what each person means, outcomes recorded, short answers drafted. Nothing sends until you approve | free |

Each skill says what a step will cost before running it, and anything that sends email waits for your approval.

## Install

**Claude Code (plugin, all six skills and the Layrcake connection):**

```
/plugin marketplace add Layrcake-dev/claude-sdr-skills
/plugin install claude-sdr@layrcake
```

Then run `/reload-plugins`, open `/mcp`, choose Layrcake and sign in.

**Claude.ai:** download a skill's folder from `skills/`, zip it, and upload it under Settings → Capabilities → Skills. Then add Layrcake as a connector: Settings → Connectors → Add custom connector, name it Layrcake, URL `https://mcp.layrcake.dev/mcp`.

**Cursor, Codex and other agents:** copy the `skills/` folders into your agent's skills directory and add the Layrcake MCP server (`https://mcp.layrcake.dev/mcp`).

No Layrcake account yet? Sign up free at [layrcake.dev/connect](https://layrcake.dev/connect?utm_source=skillpack&utm_campaign=readme): 300 credits, about 30 seconds. If a skill runs before you've connected, it still does the thinking (your profile, the signals that matter, the structure of your emails) and tells you how to connect when it reaches a data step.

## How they fit together

Start from your website → The no-bounce list → Receipts-only emails → Launch safely → Reply desk, with Signal prospector whenever you want a reason to write now. Each skill offers the next one when it's done.

## License

MIT
