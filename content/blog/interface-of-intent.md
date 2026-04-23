+++
title = "interface of intent"
author = "dominic"
date = "2026-03-12"
categories = [
  "ai"
]
tags = [
  "llm",
  "mcp",
  "ai",
  "strategy"
]
+++

I've been thinking a lot about how we build features. We've all been in those
planning meetings where a great idea from a user comes up, but it's a niche
feature for a power-user. We do the math and realize that building a whole UI
for it would take time away from other important work. So, the feature gets a
"good enough" version that doesn't quite satisfy anyone, or it withers away in
the backlog[^1].

Lately, with the rise of AI assistants and protocols like **MCP (Model Context
Protocol)**, I've started to wonder if there's another way. What if the best
interface for some features is no traditional UI at all, but a solid API and a
clear description?

## An old problem with a new angle

Let's imagine a product I've been tinkering with in my head: **Campain**, a
B2B platform for professional tabletop RPG game masters.

A request comes in for **"Campaign Archival."** A game master wants to regularly
back up all their campaign data (session logs, character sheets, and so on) to
their own cloud storage like Google Drive or S3.

In the past, my first thought would be to design a UI: a new settings page,
OAuth buttons for different cloud providers, a scheduling interface[^2]. It's a lot
of work for a feature that only a subset of users might use, and it adds to the
maintenance load. We'd probably build it, but it would be a trade-off against
other features.

## A different approach

But what if we asked a different question: **"Do *we* need to build the UI for
this?"**

If we build a solid, reliable API for "Data Archival," we can provide the same
capability with a fraction of the effort. A few years ago, this would have only
served the most technical users who could write scripts to interact with the
API. But things are different now if we expose this API through something like
the [**Model Context Protocol**](https://modelcontextprotocol.io/introduction).

MCP allows an AI assistant (the kind that's becoming common in browsers,
sidebars, and terminals) to see and use an application's features as tools.
Instead of spending weeks on a full UI, we can focus on building the core
capability.

A user could then just tell their assistant: _"Export the campaign logs for the
'Golden Brigade' to my S3 bucket in CSV format."_ The assistant would see the
`export_campaign_data` endpoint we've exposed, understand the parameters, and
set up the export. Some newer assistants can even run these tasks on a schedule.

This feels like a more optimistic way to solve the problem. We don't have to
choose between neglecting a user's need and adding complexity to our product. We
can provide powerful tools without cluttering the main interface.

---

> ### A quick sidebar at the technical side of MCP
>
> In this world, our documentation becomes the interface. Here’s how that
> "Campaign Archival" tool might look in an MCP definition for Campain. The
> "UX" is all in the `description` fields.
>
> ```json
> {
>   "name": "configure_storage_archive",
>   "description": "Sets up a data dump of campaign logs to external cloud storage. Use this when a user needs to move their campaign to another platform or make a backup.",
>   "inputSchema": {
>     "type": "object",
>     "properties": {
>       "destination_url": {
>         "type": "string",
>         "description": "The S3, GDrive, or Dropbox target URL."
>       },
>       "data_entities": {
>         "type": "array",
>         "items": {
>           "type": "string",
>           "enum": ["character_stats", "chat_logs", "private_notes"]
>         },
>         "description": "The specific data sets to include in the dump."
>       }
>     },
>     "required": ["destination_url", "data_entities"]
>   }
> }
> ```
>
> By defining this, an LLM can understand the requirements and can prompt the
> user if they forget something like the `destination_url`.

---

## "API-First" finally feels real

Professional developers been talking about "API-First" development for years,
but in my experience, it's often been more of an ideal than a reality. The API
was often just a byproduct of what the UI needed.

This shift towards AI assistants seems to make the "API-First" dream more
practical. An API becomes instantly useful because the LLM acts as a universal
adapter. We don't have to wait for someone to build an integration; the
assistant can read the schema and start using it right away.

Requiring a well-formed API that an MCP can understand also drives better API
development: If an agent can understand how to use it, humans definitely can.
This is not always the case for APIs built for UIs.

This leads to another interesting question: how much of our own product's UI
should we build on top of this agent-ready API layer? That's a topic for another
day.

## How do people find it?

If there's no button for it, how does a user discover the feature? This is a new
kind of friction. In the past, we had feature tours and tooltips. In an
agent-driven world, we have [Tool Discovery](https://modelcontextprotocol.io/specification/2025-06-18/server/tools).

When an agent connects to our product via MCP, it asks for a list of available
tools and when to use them. As engineers, our job becomes less about making a
button look good and more about writing a clear and undeniable description for a
tool. It feels like we're moving towards a "Headless Product" idea, where the
core of Campain isn't the website, but the business logic underneath. We're
building for the user's digital proxies: the agents that can discover,
understand, and act, so the human doesn't have to.

I'll be watching our feature requests to see how this plays out. Will our
advanced users keep asking for new buttons, or will they start asking, "Why
can't my agent create an archive?"

## A thought on Personal UIs

Thinking a bit further ahead, there's another interesting possibility. If UIs
become trivial to generate with AI, we might see the rise of the **Personal
UI**. In a world where Campain doesn't have an "Archive Setup" screen, when a
user needs one, their local AI could generate a temporary UI just for that
task, and get rid of it when they're done[^3]. As the provider of the platform, I
wouldn't mind what that UI looks like, as long as it's calling my secure and
validated API. It's an interesting idea to ponder.

## What about security?

Moving away from UI-constrained inputs brings up a new challenge. A UI can act
as a natural barrier. If a field only accepts numbers, it's harder to inject a
script. When the interface is a direct line to our API, we need to be more
careful.

How do we stay secure without the crutch of a UI?

### Verifying intent

We must treat a request from an agent as a proposal, not a command. For risky
actions, like deleting all campaign archives, we can use a
**Propose-Verify-Execute** loop.

First, we can mark the MCP action with `destructiveHint: true`. This tells the
agent's client that it should probably pause and ask the user for permission
before proceeding.

Additionally, when the agent calls the API, instead of executing the action
immediately, the backend can return a `STAGED\_ACTION\_ID` and lock the resources.
Our product could then send a notification to the user: "Your agent wants to
archive 40GB of data. Click here to authorize." The ID also helps prevent the
agent from retrying an action that hasn't been approved yet.

### Guardrails and policies

Our API layer needs to be smarter. We can implement "Intent Guardrails":
server-side logic that looks at the context of a request. If a tool is called
with a value that's far outside the normal range, the API can use guardrails
(like [NVIDIA NeMo](https://github.com/NVIDIA/NeMo-Guardrails) or Pydantic) to
return an error that the agent can understand.

### Identity and the "acting as" problem

In an agent-driven world, we need to distinguish between a user's permissions
and an agent's scope. Just because I can delete the database doesn't mean my
agent should be able to. We might be moving towards a model of **Just-In-Time
Scoping**, where an agent gets a narrow set of permissions for a specific task,
preventing a rogue agent from causing too much damage.

### Auditing as a first-class citizen

Every call from an agent should be logged with a clear "Reason" and a session
ID. We're not just logging `400 Bad Request` anymore. We're logging the agent's
stated reason for the action. This creates a "paper trail of intent" that can
help us figure out what went wrong if something does.

## Finding the right balance

To help decide when to build a feature with an agentic capability versus a
traditional UI, I've been thinking about two factors: **Frequency** and
**Complexity**.

- **Low Frequency / High Logic (Good for agents):** Features like data archival,
  migrations, or other complex tasks that are essential but not used every day.
  Building these as agent-based capabilities can free up the roadmap.
- **High Frequency / High Emotion (Good for a UI):** The core parts of the
  product, the things that define the brand and that users interact with all the
  time. These deserve the care and attention of a well-crafted UI.

## Final thoughts

The question is shifting from "How do we build this UI?" to "Should we build
this UI?".

By using agentic interfaces for the "boring but necessary"[^4] features, we can free
up our teams to focus on the parts of the product that really matter. Every bit
of UI we *don't* have to maintain is time and energy we can spend on innovation
elsewhere. We can build a world-class core and let the agents handle the
plumbing.

The next time I'm in a planning session, I'm going to ask if an MCP wrapper
would be a more pragmatic solution. It's an interesting new tool in our toolbox,
and I'm excited to see where it leads.

---

[^1]: or in some extremely rare, and strategically important, cases we build it
[^2]: cron-job builders are notoriously hard to make user-friendly
[^3]: or deploys it on their local system
[^4]: the data dumps, the complex configurations, the bulk updates...
