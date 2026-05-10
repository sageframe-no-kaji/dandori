---
name: dandori (段取り)
description: Author a surgical agent task spec — a structured markdown file that an autonomous coding agent reads to execute one bounded unit of work. Use whenever the user wants to draft an agent task, write a spec for an agent or for Claude Code, package a piece of conversation as something an agent can run independently, turn planning decisions into an executable instruction, or convert a discussion into a buildable task. Also trigger when the user says things like "write me a prompt for the agent," "package this for Claude Code," "make this into a task spec," "turn this into something agentic to run," "draft me an instruction the agent can execute," or similar. Produces a markdown file with goal, files, required changes, acceptance criteria, verification commands, and commit format. Handles standalone tasks (no parent context) and tasks that are part of a larger project document. The skill interrogates only gaps it can't infer from context — it never asks for what's already visible.
---

# Dandori

A skill for translating a conversation's decisions into a surgical task spec that an autonomous coding agent can execute independently.

## What this skill produces

A single markdown file in `agent-tasks/`, named `agent-task-YYYY-MM-DD-slug.md`, containing the bounded specification of one unit of work. The agent reads this file and produces the defined output, verifying against explicit acceptance criteria. The format is documented in full in `FORMAT.md`.

The output is not a prompt in the chat sense — it is a structured spec. It exists as an artifact in the project so the agent can read it directly, and so the practitioner has a record of what was authorized.

## When this skill is in scope

This skill is the right move when:

- A conversation has produced enough material (a clear goal, a sense of the file boundaries, a notion of what counts as done) to bound a unit of agent work.
- The practitioner wants to hand work off to an autonomous agent — Claude Code, Codex, Cursor agent mode, or equivalent — and wants the handoff to be surgical rather than exploratory.
- A standalone maintenance or exploration task needs to be authored independently of any larger project context.

This skill is not the right move when:

- The work is conversational — small refactors, single-line changes, debugging-by-discussion. Just do it.
- The conversation hasn't yet produced architectural decisions. Have the design conversation first; author the task afterward.
- The work is too large for a single bounded session. Decompose first, then author one task per bounded unit.

## The intake discipline

The skill interrogates only the gaps it can't fill from context. Every input it needs follows the same rule: **check current context first; ask only what isn't already known**.

Before drafting, the skill needs to know:

1. **Goal** — what this task produces, in 1–3 unambiguous sentences.
2. **Files** — which files get created or modified, with paths from repo root.
3. **Required Changes** — the substantive list of what to do in each file.
4. **Acceptance** — the verifiable criteria for "done."
5. **Verification** — the commands the agent runs to check acceptance.
6. **Commit format** — single commit vs. multiple, message format.

Optional, included only when warranted:

- **Parent doc** — if this task is part of a larger project document (a system design, an RFC, a feature spec, a ho), the slug or path of that document.
- **Context** — background the agent needs that isn't visible elsewhere.
- **Problem** — what's broken (for fix-shaped tasks).
- **Do Not** — explicit out-of-scope items the agent might otherwise wander into.
- **Stop Condition** — when to halt and surface findings instead of continuing.

For each item, the skill scans the conversation for what's already established. If the goal was discussed, draft from that — don't ask. If the file boundaries are obvious from the architecture conversation, use them — don't ask. If something is genuinely unclear or missing, ask — and only ask for what's missing, not the whole intake.

The skill keeps interrogating until every required section can be drafted unambiguously. "Satisfactory" means the spec is unambiguous enough that the executing agent can complete the work without asking the practitioner mid-execution.

## Workflow

1. **Detect inputs from context.** Before asking anything, scan the conversation for goal, file boundaries, change list, acceptance signals, and verification commands. Note what's present and what's missing.

2. **Check the KOKOROE context.** See if the five behavioral guidelines (Context-first, Spec as authorization, Verify by command, Halt and surface, Honor the boundary) are loaded in the executing environment. They live in `KOKOROE.md` in this skill. The check matters because their presence determines how defensive the task spec needs to be — see "KOKOROE context check" below.

3. **Interrogate the gaps.** For each required section that isn't fully derivable from context, ask. Group questions thoughtfully — don't fire one at a time when several are related. Don't ask about things the conversation already established.

4. **Draft the spec.** Read `FORMAT.md` for the full format reference. Read one or more files in `examples/` to calibrate against worked examples. Then draft.

5. **Run the format checklist.** Before declaring the spec ready, verify against the checklist at the end of `FORMAT.md`. Each required section present? Acceptance criteria each verifiable by a runnable command? No pseudocode in Required Changes? No anti-patterns?

6. **Save and present.** Write the file to `agent-tasks/agent-task-YYYY-MM-DD-slug.md` (or wherever the project's conventions place it — check for an existing `agent-tasks/` directory or ask). Present the file path to the practitioner.

## Brief format summary

The full format reference is in `FORMAT.md`. This is the one-line version of each section so the skill can answer simple format questions without reading the full reference.

**Required:**

- Frontmatter: `created`, `type: agent-task`, `status`, optional `parent`, optional `project`.
- Goal: 1–3 sentences. Concrete enough that "done or not" is unambiguous.
- Files: bullet list with create vs. modify marked, paths from repo root.
- Required Changes: numbered or bulleted concrete changes. Not pseudocode.
- Acceptance: checkboxes, each verifiable by a command.
- Verification: copy-pasteable commands matching acceptance one-to-one.
- Commit: single commit vs. multiple, message format.

**Optional, when warranted:**

- Context: background that isn't visible elsewhere.
- Problem: for fix-shaped tasks.
- Do Not: explicit out-of-scope items the agent might otherwise wander into.
- Stop Condition: when to halt and surface instead of continuing.

## KOKOROE context check

The agent task format assumes the executing agent operates under a small set of behavioral guidelines: state assumptions, write minimum code, make surgical changes, define verifiable goals. These guidelines are documented in `KOKOROE.md`.

Their job is to govern how the *executing* agent behaves when it picks up the task spec, not how this authoring skill behaves when writing it. The format works to the extent the executing agent honors them.

Two ways the executing agent can have them loaded:

1. **They live in the project's `CLAUDE.md`** (or equivalent for the practitioner's IDE). Loaded once, applies to every session in that project. Cleanest pattern.
2. **They are embedded in the task spec itself.** Verbose and redundant, but works when there's no `CLAUDE.md` to lean on.

Before drafting, the skill checks whether the guidelines are visible in current context — in a loaded `CLAUDE.md`, in the conversation, or in an installed sibling skill. If yes, it drafts a tight spec. If no, it offers the practitioner two paths:

- "I can drop the guidelines into your project's `CLAUDE.md` once, so future tasks stay tight."
- "I can write a defensive spec that includes the relevant guidelines inline."

The practitioner picks once per project. The skill remembers within the session.

## File location and naming

**Default location:** `agent-tasks/` at the repo root. If the project has an existing convention (e.g., `tasks/`, `specs/`, `agents/`), use that instead — check the file tree first.

**Naming convention:** `agent-task-YYYY-MM-DD-slug.md`.

- Date is the day the task was authored.
- Slug is descriptive and short — three to six words, kebab-case.
- Example: `agent-task-2026-05-09-bump-fastapi-0.115.md`.

The convention sorts naturally by date and carries semantics in the slug. The skill never picks the slug silently — confirm with the practitioner if it's not obvious from context.

## Reference files

- **`FORMAT.md`** — the full format reference. Read this when drafting any non-trivial task. Contains every section's purpose and shape, the translation moves from intent to spec, anti-patterns, and the format checklist.
- **`KOKOROE.md`** — the four behavioral guidelines. Used both as documentation of what the executing agent should be operating under, and as a copy-pasteable file the practitioner can install into their `CLAUDE.md`.
- **`examples/standalone-utility.md`** — a no-parent maintenance task (dependency bump). Shows the minimum-viable shape.
- **`examples/parented-implementation.md`** — a feature implementation task with a `parent:` field and a `Context` section. Shows the fuller shape when a task descends from a larger project document.
- **`examples/exploration-with-stop-condition.md`** — an open-ended inspection task with a `Stop Condition` section. Shows the format for tasks that may surface findings the agent shouldn't act on alone.
