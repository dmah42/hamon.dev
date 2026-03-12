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

## Why Your Next Big Feature Shouldn't Have a UI

Every product has a graveyard of features filled with "Quality of Life" features
that are technically sound, beautifully designed, and ultimately, a maintenance
burden.

In the Old World[^1], product development is a zero-sum game of pixel real
estate and cognitive load. We've all sat in planning sessions where a Product
Manager identifies a genuine pain point raised by users, the engineering team
estimates it, and we look at it alongside all the other incoming requests,
platform improvements, and forward looking features. We realize that building a
bespoke UI for this niche power-user workflow will displace three other core
features that appeal to the masses or provide strategic advantage for the
product. Ultimately, we either build a "good enough" version that leaves users
frustrated, or it sits on the backlog to die[^2].

The democratization of agentic interfaces and protocols like **MCP (Model
Context Protocol)** has fundamentally shifted that calculus. We have entered an
era where the most sophisticated "UI" we build for a feature might just be a
rock-solid API and a well-described schema.

## The Old Math: The Tyranny of the Button

Let's illustrate this idea with a dummy product. Imagine we’re building
**Campain**, a B2B platform for professional tabletop RPG game masters.

A feature request comes in from our biggest user, Barry Bybax for **"Campaign
Archival."** When a campaign ends, or to allow a campaign to be exported to
another platform, the Game Master wants to set up a regular dump of all raw
session data, character sheets, etc to the Game Master's own cloud storage
(Google Drive, S3, etc).

**In the Old World, the calculus might be:**

- **Design and Implementation Cost:** We need a "Settings > Integrations"
  sub-page to set up the relationship to the Game Master's storage. We need a
  scheduler UI[^3]. We need an export flow with OAuth integrations for different
  cloud providers.
- **Maintenance Cost:** If storage providers change their API, or a new storage
  provider integration is requested, this UI needs updating. We have to QA these
  features across every browser and device.
- **Opportunity Cost:** Whatever time this work takes, it's capacity we're not
  putting against the core product or strategic improvements.

We'd build it, because it's been requested by our most valuable users, and it's
a good idea. But it's work that displaces innovation and potential impact of new
features; features that might keep us relevant in the market. We're building a
bridge for a small group of travelers when we could be building a monorail for
modern commuters.

## The New Calculus: "Why should _we_ build this UI?"

Today, as an architect, my first question is no longer "How do we build this?"
It's **"Does _we_ need to build this?"**

If we build a high-performance, idempotent API for "Data Archival" we've
provided the ability for 20% of the effort. Had we taken this approach in the
old world, it would have satisfied only our most technologically savvy users.
Things have changed if we wrap this API in an [**MCP
server**](https://modelcontextprotocol.io/introduction).

The Model Context Protocol allows an AI agent (the one already sitting in your
user's sidebar, browser, or terminal) to "see" your application's capabilities
as tools it can use. Instead of us spending weeks building and testing the end-
to-end experience, we focus on providing the **capability**.

The user simply tells their agent: _"Export the campaign logs for the 'Golden
Brigade' to my S3 bucket in CSV format."_ The agent looks at our MCP-exposed
API, sees the `export\_campaign\_data` endpoint, understands the parameters, and
sets up the export on the user's behalf. More modern agents are able to run
these as scheduled tasks, satisfying the broader user journey.

**This is the optimism of the new era:** We no longer have to choose between
"neglecting the user" and "bloating the product"; we can provide professional-
grade power tools without cluttering the workshop for the hobbyist.

---

> ### 🛠️ Technical Sidebar: The Anatomy of an MCP Server
>
> In the past, "documentation" was for developers. In the agentic world,
> documentation _is_ the interface. Here is how that "Campaign Archival" utility
> looks when defined as an MCP tool for Campain. Note how the "UX" is
> handled entirely by the `description` fields.
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
> By defining this, the LLM understands the constraints and prompts the user if
> they forget the "destination_url" field.

---

## The Realization of the "API-First" Dream

For a few decades, we've preached a gospel of "API-First" development. If we're
being honest with ourselves, that has rarely been the reality. In most
development houses, the API is the byproduct of the UI's needs.

The agentic shift realizes this vision: For the first time, a API primitive is
instantly useful because the LLM is the "universal adapter" we’ve been missing.
We no longer have to wait for a third party to build an integration: The agent
reads the schema and _just starts using it_.

This, of course, opens the door to the next question: How much do we build our
product UI on top of this agent-ready API layer? A topic for another post...

## The Semantic Discovery Problem

If there isn't a button in the sidebar, how does the user know the feature
exists? This is the new "Semantic Friction" of product discovery. In the old
world, we relied on "Feature Tours" and tooltips. In the agentic world, we rely
on [Tool
Discovery](https://modelcontextprotocol.io/specification/2025-06-18/server/tools).

When an agent "enters" your product via MCP, it performs an inventory of
available tools, and when to use them. As engineers, our job moves from "making
the button blue" to "making the tool description undeniable." We are moving
towards a **Headless Product** strategy where the core of Campain isn't the web
site; it’s the underlying business logic. We are building for the user's digital
proxies: the agents that can discover, understand, and execute so the human
doesn't have to understand how to.

### Monitoring the Death of the Feature Request

This change is observable: Keep an eye on your feature requests.

Are your sophisticated customers still asking for new buttons? Or are they
starting to ask: _"Why can't my agent create an archive?"_

## Embracing the Inconsistent: the Personal UI

There’s a bold future here, too. If UIs are now trivial to "vibe-code" with AI,
we need to embrace the **Personal UI**. In this world where Campain doesn't
have an "Archive Setup" screen at all, when a user needs one, their local AI
can generate a bespoke, ephemeral UI on the fly, tailored exactly to their
specific task that day, and discard it when they’re done[^4]. As the providers
of the platform, we don't care what that UI looks like. We just care that it's
calling our rock-solid, secure, and validated API.

### A Disintermediation Caution

There is of course another potential downside here: The more things shift into
APIs, the more your customers can create their own experiences, the less
opportunity you have to form a narrative or engage with users within your
product. You may need to find new ways to ensure your product has value beyond
the API surface, but ideally this shift in investment has freed up capacity to
do just that.

## Agentic QA: Fuzz-Testing Intent

When we move the "Capability" to an agentic layer, we stop testing pixels and
start testing **contracts**.

We can now deploy "Red Team Agents" whose entire job is to try and break the
"Archival Utility". We don't give them a browser, but we do give them specific
test cases: We can give them the MCP server and tell them: _"Try to archive data
to an invalid bucket"_ or _"Try to dump logs from a campaign you don't own"_.
This is **Agentic Fuzzing.** It's more robust than traditional unit testing
because it tests the _combination_ of tools in ways a human tester might not
think of.

## The Security of Intent: Hardening the Agentic Boundary

Moving away from UI-constrained inputs introduces a new problem: **"Security of
Intent."** In the old world, a UI acts as a physical barrier. If a field only
accepts numbers, an agent (or a human) can't easily inject a script. When the
interface is a direct "Interface of Intent," we lose that front-line defense.

How do we secure intent without the UI crutch?

### Verification of Intent vs. Execution

We must treat an agent's request as a **Proposal**, not a Command. For high-risk
primitives, like "Delete all campaign archives," we implement a
[**Propose-Verify-Execute**
loop](https://www.stackai.com/insights/human-in-the-loop-ai-agents-how-to-design-approval-workflows-for-safe-and-scalable-automation).

First, we make sure to ensure the MCP action is tagged with
`destructiveHint: true`, which tells the agentic client that it _should_ pause
and ask the user for permission before letting the agent proceed.

Additionally, when the agent calls the API, instead of executing the API can
return a `STAGED\_ACTION\_ID`, and the backend locks the resources. The product
then sends a notification to the human user's UI saying: _"Your agent wants to
archive 40GB of data to an unverified S3 bucket. Click here to authorize."_

The ID serves as an idempotency token to ensure the agent doesn't manage to
retry an action multiple times because the human didn't approve quickly enough.

In MCP, this can also be handled by an _elicitation_.

### Semantic Guardrails & Policy Primitives

Our API layer needs to be smarter. We should implement **Intent Guardrails**:
server-side logic that evaluates the context of a request. If a tool is called
with a value that deviates more than 50% from the historical norm, the API
should use guardrails ([NVIDIA NeMo](https://github.com/NVIDIA/NeMo-Guardrails),
or Pydantic, etc) to return a `422 Unprocessable Entity` error that allows the
agent to understand _why_ the intent was rejected.

This can also be handled by _elicitation_ in MCP.

### Identity & The "Acting As" Problem

In an agentic world, we must distinguish between User Permissions and Agent
Scope. Just because a User can delete the database doesn't mean their Agent
should. We are moving toward a model of **Just-In-Time Scoping**, where an agent
is granted a narrow set of permissions for a specific task duration, preventing
a wayward agent from performing a mass-deletion under the user's high-level
credentials.

### Auditability as a First-Class Citizen

Every agentic call must be tied to a clear "Reason" and a session ID. We aren't
just logging `400 Bad Request` anymore. We are logging the agent's **stated
justification** for the action. This creates a "Paper Trail of Intent" that
allows us to perform post-mortem analysis: _"Why did the agent think it was a
good idea to sync data to a Dropbox account in the middle of a battle?"_

## The Strategic Balance: When to go "Headless"

To help teams align on when to deliver a feature via an agentic capability
versus a traditional UI, we can look at two axes: **Frequency** and
**Complexity**.

- **Low Frequency / High Logic (Agentic):** Features like data archival,
  migration, or features that would require complex UIs to deliver. These are
  essential but rare. Delivering these as high-leverage agentic capabilities
  clears the roadmap.
- **High Frequency / High Emotion (Bespoke UI):** Core gameplay loops, strategic
  narrative, or brand-defining interactions. These deserve the high-touch,
  tactile delight that only a bespoke UI can provide.

## Conclusion: The Architect of Choice

The shift from "How do we build this UI?" to "Why should we build this UI?"
is a critical cultural change that no longer means we're being grumpy,
interface-hostile senior engineers, but are offering pragmatic solutions.

By leveraging agentic interfaces for the "boring" necessities[^5], we are
liberating our teams to balance priorities effectively between options
for delivering impact.

Every pixel we _don't_ have to maintain for a background utility is an
"innovation token" we can spend elsewhere. It means we can focus our bespoke UI
efforts on the features that actually matter and benefit from what UI can
provide: The ones that define our product's soul and delight our users. We build
the world-class core, we let the agents handle the plumbing.

The next time you're in a planning session, ask if an MCP wrapper would deliver
the solution more pragmatically. If the answer is yes, take the win. Let's stop
building "just because we have to" and start building where it actually counts.

The graveyard of unloved features is full enough. Let’s start building loved
capabilities instead.

---

[^1]: which, let’s be honest, is still mostly _this_ world
[^2]: or in some extremely rare, and strategically important, cases we build it
[^3]: cron-job builders are notoriously hard to make user-friendly
[^4]: or deploys it on their local system
[^5]: the data dumps, the complex configurations, the bulk updates...
