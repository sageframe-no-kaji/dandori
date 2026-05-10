---
name: dandori
description: Author a surgical agent task spec—a structured markdown file an autonomous coding agent reads to execute one bounded unit of work. Use when the user wants to draft an agent task, write a spec for an agent or Claude Code, package a conversation as something an agent can run independently, turn planning decisions into an executable instruction, or convert a discussion into a buildable task. Also trigger on phrases like "write me a prompt for the agent," "package this for Claude Code," "make this into a task spec," "turn this into something agentic to run," or "draft an instruction the agent can execute." Produces a markdown file with goal, files, required changes, acceptance criteria, verification commands, and commit format. Handles standalone tasks and tasks descending from a larger project document. Interrogates only gaps it can't infer from context; never asks for what's already visible.
---

# Dandori (段取り)

Dandori is the practitioner's prep discipline—the carpenter's setup, the chef's mise en place. This skill does the dandori for an autonomous coding agent: it translates a conversation's decisions into a surgical task spec the agent can execute independently.

## What this skill produces

A single markdown file in `agent-tasks/`, named `agent-task-YYYY-MM-DD-slug.md`, containing the bounded spec for one unit of work. The agent reads it and produces the output, verifying against explicit acceptance criteria. The format is documented in `FORMAT.md`.

The output is a structured spec, not a prompt. It exists as an artifact in the project so the agent can read it directly and the practitioner has a record of what was authorized.

## When this skill is in scope

Right move when:

- The conversation has produced enough (clear goal, file boundaries, definition of done) to bound a unit of agent work.
- The practitioner wants a surgical handoff to an autonomous agent (Claude Code, Codex, Cursor agent mode, or equivalent), not an exploratory chat.
- A standalone maintenance or exploration task needs authoring independently of any project context.

Not the right move when:

- The work is conversational—small refactors, single-line changes, debugging-by-discussion. Just do it.
- The conversation hasn't produced architectural decisions yet. Have the design conversation first; author the task after.
- The work is too large for a single bounded session. Decompose first; author one task per unit.

## The intake discipline

The skill interrogates only the gaps it can't fill from context. The rule: **check current context first; ask only what isn't already known**.

Before drafting, the skill needs:

1. **Goal**—what this task produces, in 1–3 unambiguous sentences.
2. **Files**—which files get created or modified, paths from repo root.
3. **Required Changes**—what to do in each file.
4. **Acceptance**—the verifiable criteria for done.
5. **Verification**—the commands the agent runs to check acceptance.
6. **Commit format**—single commit vs. multiple, message format.

Optional, when warranted:

- **Parent doc**—if the task descends from a larger project document (system design, RFC, feature spec, ho), the slug or path.
- **Context**—background not visible elsewhere.
- **Problem**—what's broken (for fix-shaped tasks).
- **Do Not**—out-of-scope items the agent might wander into.
- **Stop Condition**—when to halt and surface findings instead of continuing.

For each item, scan the conversation for what's established (goal as discussed, file boundaries from architecture, etc.) and draft from it. Ask only what's missing, not the whole intake.

Keep interrogating until every required section can be drafted unambiguously—meaning the executing agent can complete the work without asking the practitioner mid-execution.

## Workflow

1. **Detect inputs from context.** Scan the conversation for goal, file boundaries, change list, acceptance signals, and verification commands. Note what's present and what's missing.
2. **Check the kokoroe context.** See if the five guidelines (Context-first, Spec as authorization, Verify by command, Halt and surface, Honor the boundary) are loaded in the executing environment. They live in `KOKOROE.md`. Their presence determines how defensive the spec needs to be—see "Kokoroe context check" below.
3. **Interrogate the gaps.** For each required section that isn't fully derivable from context, ask. Group questions thoughtfully; don't fire one at a time when several are related. Don't ask about things the conversation already established.
4. **Draft the spec.** Read `FORMAT.md` for the full format reference. Read one or more files in `examples/` to calibrate against worked examples. Then draft.
5. **Run the format checklist.** Before declaring the spec ready, verify against the checklist at the end of `FORMAT.md`. Each required section present? Acceptance criteria each verifiable by a runnable command? No pseudocode in Required Changes? No anti-patterns?
6. **Save and present.** Write the file to `agent-tasks/agent-task-YYYY-MM-DD-slug.md` (or wherever the project's conventions place it—check for an existing `agent-tasks/` directory or ask). Present the file path to the practitioner.

## Brief format summary

The full reference is in `FORMAT.md`. One-line version of each section:

**Required:**

- Frontmatter: `created`, `type: agent-task`, `status`, optional `parent`, optional `project`.
- Goal: 1–3 sentences. Concrete enough that "done or not" is unambiguous.
- Files: bullet list with create vs. modify marked, paths from repo root.
- Required Changes: numbered or bulleted concrete changes. Not pseudocode.
- Acceptance: checkboxes, each verifiable by a command.
- Verification: copy-pasteable commands matching acceptance one-to-one.
- Commit: single commit vs. multiple, message format.

**Optional, when warranted:**

- Context: background not visible elsewhere.
- Problem: for fix-shaped tasks.
- Do Not: out-of-scope items the agent might wander into.
- Stop Condition: when to halt and surface instead of continuing.

## Kokoroe context check

The dandori format assumes the executing agent operates under kokoroe—the five guidelines documented in `KOKOROE.md`. Their job is to govern how the *executing* agent behaves, not how this authoring skill behaves. The format works to the extent the executing agent honors them.

Two ways the agent can have them loaded:

1. **In the project's `CLAUDE.md`** (or equivalent IDE config). Loaded once, applies every session. Cleanest pattern.
2. **Embedded in the task spec itself.** Verbose, but works when there's no `CLAUDE.md` to lean on.

Before drafting, check whether the guidelines are visible in current context—a loaded `CLAUDE.md`, the conversation, or an installed sibling skill. If yes, draft a tight spec. If no, offer the practitioner two paths:

- "I can drop the guidelines into your project's `CLAUDE.md` once, so future tasks stay tight."
- "I can write a defensive spec that includes the relevant guidelines inline."

The practitioner picks once per project. The skill remembers within the session.

## File location and naming

**Default location:** `agent-tasks/` at the repo root. If the project has another convention (e.g., `tasks/`, `specs/`, `agents/`), use it—check the file tree first.

**Naming:** `agent-task-YYYY-MM-DD-slug.md`.

- Date is when the task was authored.
- Slug is three to six words, kebab-case, descriptive.
- Example: `agent-task-2026-05-09-bump-fastapi-0.115.md`.

Sorts by date, carries semantics in the slug. Never pick the slug silently—confirm with the practitioner if not obvious from context.

## Reference files

- **`FORMAT.md`**—the full format reference. Read when drafting any non-trivial task. Contains every section's purpose and shape, translation moves, anti-patterns, and the format checklist.
- **`KOKOROE.md`**—the five guidelines. Documentation of what the executing agent operates under, and a copy-pasteable file for the practitioner to install in their `CLAUDE.md`.
- **`examples/standalone-utility.md`**—a no-parent maintenance task. Minimum-viable shape.
- **`examples/parented-implementation.md`**—a feature implementation with `parent:` and `Context`. Fuller shape for tasks descending from a project document.
- **`examples/exploration-with-stop-condition.md`**—an open-ended inspection task with `Stop Condition`. For tasks that may surface findings the agent shouldn't act on alone.
