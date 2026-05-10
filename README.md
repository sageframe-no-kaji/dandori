# Dandori

*The practitioner's prep discipline for autonomous coding agents.*

> Dandori is the trades word for the prep that makes execution flow: the carpenter's setup, the chef's mise en place, the surgeon's instrument tray. This skill does the dandori for an autonomous coding agent—translating a planning conversation into a surgical task spec the agent executes independently, while preserving a durable record of what was authorized.

**Status:** v0.1. Published as a working artifact and an intellectual claim. The discipline matures as it gets used.

## What's Broken

Coding agents don't lack capability; they lack discipline. Given a chat-shaped instruction, an autonomous agent will overcomplicate the code, decide architecture it was never authorized to decide, hide its confusion, and ship work no one asked for.

The available behavioral guidelines for these agents are a starting point, but they collapse two distinct disciplines into one: the practitioner's authoring discipline (how to prepare work for handoff) and the agent's execution discipline (how to operate inside a bounded spec). Those need separate names and separate files.

## What Dandori Does

Dandori is the prep discipline. The practitioner has a planning conversation that produces decisions—architecture, file boundaries, what counts as done. Dandori translates those decisions into a surgical task spec: a structured markdown file the agent reads to execute one bounded unit of work. Goal, files, required changes, acceptance criteria, verification commands, commit format.

The spec is durable. It lives in the project as an artifact, not a chat message, and tells you what the agent was authorized to do.

The agent that executes the spec operates under **kokoroe** (心得)—the practitioner's internalized discipline of care, intention, and specificity. Five guidelines:

1. **Honor the given.** Read what's loaded before acting.
2. **If the spec did not ask, do not give.** Every changed line traces to the spec.
3. **If a command cannot prove it, it is not done.** Acceptance is the definition of done.
4. **Surprise is signal; halt.** Stop and surface findings rather than rationalize.
5. **Propose, do not decide.** Architecture belongs to the practitioner.

These install in your project's `CLAUDE.md` once. The executing agent operates under them every session.

## What Dandori Is Not

- Not a prompt-engineering tool. The spec is a structured artifact, not a clever phrasing.
- Not a project management system. It produces one artifact per unit of work; it does not track, organize, or prioritize.
- Not generic AI-coding advice. Karpathy's framing already does that. Dandori is the specific prep work for handoff.
- Not a replacement for the planning conversation. The conversation is where decisions get made. Dandori captures and translates them.

## How Dandori Differs

The closest comparable is Andrej Karpathy's behavioral framing, packaged by Forrest Chang at [github.com/forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills). That repo distills Karpathy's tweets on LLM coding pitfalls into four general guidelines: think before coding, simplicity first, surgical changes, goal-driven execution.

Dandori departs in two ways.

**It separates authoring from execution.** Karpathy collapses "how to prepare a task" and "how to execute a task" into one set of rules. Dandori names them as two disciplines: dandori (the practitioner's prep) and kokoroe (the agent's stance). They sit on opposite sides of the handoff.

**The kokoroe is rooted in practice, not paraphrase.** The five koans come from years of operating an actual methodology with autonomous agents. They include the cases Karpathy doesn't address: surprise as signal, the boundary between agent and practitioner authority, the spec as authorization that traces every change.

## Naming

**Dandori** (段取り) is the Japanese trades word for the prep discipline that makes execution flow: what the carpenter does before cutting, what the chef does before service. The worker who's good at dandori is the one whose work goes smoothly because the prep was thorough.

**Kokoroe** (心得) is the related concept of internalized discipline—what the practitioner has taken to heart and brings to the work. Where dandori is the prep, kokoroe is the stance carried into execution.

Together they name the two halves of careful work: prepare clearly, then execute with discipline.

## Where Dandori Sits

Dandori is part of two larger projects.

**The Ho System.** Dandori is the execution-handoff layer of a broader methodology for human-AI collaboration. The Ho System organizes work as a chain: seed → system design → README → ho overview → individual hos. A ho is a bounded planning step; an agent task is the executable unit beneath it. Dandori produces the agent task. Other parts of the system (`ho-seed-collaborator`, `ho-system-design-collaborator`, `ho-readme-collaborator`) handle the upstream planning steps.

**Constructive Interference.** The intellectual project where the discipline of practitioner-led AI collaboration is being argued and developed. Essays at [sageframe.substack.com](https://sageframe.substack.com) develop the framing this skill operationalizes—the access-judgment gap, authorship as accountability, non-interchangeability between practitioner and agent, the same lever, precedential thinking. Dandori demonstrates one node of the methodology those essays describe.

If you arrived here from Karpathy or from Anthropic's skills documentation, the wider framework is at sageframe.substack.com. If you arrived from the writing, this is what one node of the methodology looks like in practice.

## Where the Koans Come From

The five koans aren't generic AI advice. Each is rooted in something I've argued in [Constructive Interference](https://sageframe.substack.com).

1. **Honor the given** — the discipline of [clear intent without predetermined outcome](https://sageframe.substack.com/p/walking-without-google-maps), reading the terrain before mapping it.

2. **If the spec did not ask, do not give** — the visibility gap named in [Prompting, Not Programming](https://sageframe.substack.com/p/prompting-not-programming): disciplined AI work shows its scaffolding, every change traceable to an authorized decision.

3. **If a command cannot prove it, it is not done** — the operational answer to [Bad Vibes](https://sageframe.substack.com/p/bad-vibes): felt confidence collapses when the verification work was skipped.

4. **Surprise is signal; halt** — [Walking Without Google Maps](https://sageframe.substack.com/p/walking-without-google-maps) again, this time for the secret camino: the unexpected belongs to the practitioner, not to be paved over.

5. **Propose, do not decide** — [Thinking Outside the Skull](https://sageframe.substack.com/p/thinking-outside-the-skull) on distributed cognition: participants in a cognitive system do different kinds of work, and the architectural decisions stay with the human.

## How It Works

The skill is a markdown package the agent reads when triggered. Four files plus three worked examples:

- `SKILL.md` — entry point. The agent loads this when it detects a request that fits the skill's description.
- `KOKOROE.md` — the five guidelines, installable in your project's `CLAUDE.md` so the executing agent operates under them every session.
- `FORMAT.md` — the full reference for the agent task spec format. Loaded when drafting any non-trivial task.
- `examples/` — three worked examples spanning the format's range: standalone utility, parented implementation, exploration with stop condition.

When triggered ("draft me a task spec for X," "package this for Claude Code," "turn this into something agentic to run"), dandori reads the conversation for what's already established, asks only for what's missing, drafts a spec following `FORMAT.md`, and saves it to `agent-tasks/agent-task-YYYY-MM-DD-slug.md`. The practitioner hands the spec to an executing agent, which operates under kokoroe.

## Architecture

```
dandori/
├── SKILL.md            # entry point; the LLM reads this first
├── KOKOROE.md          # the five guidelines (installable in CLAUDE.md)
├── FORMAT.md           # the spec format reference
└── examples/
    ├── standalone-utility.md
    ├── parented-implementation.md
    └── exploration-with-stop-condition.md
```

No runtime, no daemon, no service. The agent that loads the skill reads the markdown and acts on it.

## Tech Stack

Markdown. That's the whole stack. Dandori is a methodology artifact, not a runtime.

Compatible with Claude Code (Anthropic), Codex, Cursor agent mode, and any environment that respects the Claude Code skills format. Tested with Claude Code.

## Current State

| | |
|---|---|
| **Now** | v0.1 published. Examples cover the format's range. KOKOROE installs cleanly. Tested in production work. |
| **Next** | A slide deck on the five koans, intended as a companion for readers who want the wisdom layer without the implementation detail. |
| **Later** | `agent-tasks/` as a recognized convention across multiple skills, if adoption goes that direction. |

## Installation

Clone into your `.claude/skills/` directory (project-local or global):

```bash
cd .claude/skills
git clone https://github.com/<your-handle>/dandori.git
```

Install kokoroe at the project level so the executing agent operates under it every session. Either append to your project's `CLAUDE.md`:

```bash
cat .claude/skills/dandori/KOKOROE.md >> CLAUDE.md
```

Or `@`-reference it from `CLAUDE.md`:

```markdown
@.claude/skills/dandori/KOKOROE.md
```

## Usage

In any conversation with Claude Code (or compatible agent), say something like:

- "Draft an agent task to bump fastapi from 0.110 to 0.115"
- "Package what we just decided as a spec for the agent"
- "Turn this conversation into a buildable task"

The skill triggers, asks for any missing details, drafts the spec to `agent-tasks/`, and presents the file path. Hand the spec to an agent; the agent executes under kokoroe.

## Requirements

- An autonomous coding agent that respects the Claude Code skills format (Claude Code, Codex, Cursor agent mode, etc.)
- A project with a `CLAUDE.md` (or willingness to create one) for the kokoroe install
- Markdown rendering for reading the spec files (any editor)

## Development

The skill is documentation, not code. Edits are markdown edits. To contribute:

1. Fork the repo
2. Edit the relevant file (`SKILL.md`, `KOKOROE.md`, `FORMAT.md`, or an example)
3. Open a PR with a clear description of what changed and why

## License

MIT. See [LICENSE](LICENSE).

The methodology is yours to adopt, adapt, or argue with. Attribution is appreciated; modification, redistribution, and commercial use are all fine.

---

*Authored by Andrew Todd Marcus. Part of the [Ho System](https://atmarcus.net) and [Constructive Interference](https://sageframe.substack.com). Last meaningful update: 2026-05-09.*
