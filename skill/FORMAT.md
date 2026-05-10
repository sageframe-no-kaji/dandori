# Agent Task Format

The full format reference for an agent task: the surgical spec an autonomous coding agent reads to execute one bounded unit of work. Loaded by `dandori` when drafting any non-trivial task; also usable as direct documentation for practitioners authoring by hand.

Each task is one bounded unit of work—small enough for a single agent session, large enough to hand off as a standalone artifact. The agent reads one spec, produces defined output, and verifies against explicit acceptance criteria.

---

## Kokoroe context

Agent tasks are executed under kokoroe—the five guidelines documented in `KOKOROE.md`. Their job is to govern how the *executing* agent behaves; the format below assumes they're loaded.

When they're not, the spec compensates—more explicit `Do Not` items, more conservative `Required Changes`, stricter `Acceptance`. The cleaner pattern is to load kokoroe once at the project level (see `KOKOROE.md` for installation) and let each spec stay tight.

---

## Naming and location

**Naming:** `agent-task-YYYY-MM-DD-slug.md`

- Date the task was authored.
- Slug: three to six words, kebab-case, descriptive.
- Example: `agent-task-2026-05-09-bump-fastapi-0.115.md`.

Sorts by date; carries semantics in the slug.

**Location:** `agent-tasks/` at the repo root.

If the project has another convention (e.g., `tasks/`, `specs/`), use it—check the file tree first. The principle: all agent tasks live in one directory so they can be pattern-matched (find every task that touches a particular file, find every task that references a specific dependency).

---

## Format

Agent tasks have required sections (always present, even if brief) and optional sections (included when warranted). Order matters—agents read top to bottom.

### Required sections

#### Frontmatter

````yaml
---
created: YYYY-MM-DD
type: agent-task
status: ready              # ready | in-progress | complete | blocked
parent: <slug or path>     # optional—fill only if this task descends from a larger project doc
project: <slug>            # optional—fill only when relevant
---
````

The `parent` field links to whatever project document this task descends from—a system design, an RFC, a feature spec, a ho. When the task is standalone, omit the field entirely. Don't fill it with a placeholder.

#### Goal

One to three sentences. What this task produces. Concrete enough that "done or not done" is unambiguous.

Bad: "Improve the parser."
Good: "Implement `AnthropicParser` that reads Anthropic conversation exports and yields validated `ConversationRecord` objects, with full unit test coverage against synthetic fixtures."

#### Files

A bullet list of files the task creates, modifies, or reads. Use absolute paths from repo root. Indicate `Create:`, `Modify:`, or `Read-only:` (read but not changed).

````markdown
**Files**

- Create: `src/shuji/parsers/anthropic.py`
- Create: `tests/parsers/test_anthropic.py`
- Create: `tests/fixtures/anthropic/synthetic_5.json`
- Read-only: `src/shuji/parsers/base.py` (Parser protocol reference)
- Modify: `src/shuji/parsers/__init__.py` (add to `PARSERS` list)
````

If the file list is large, group by area. If a file is created and later modified within the same task, list it once with the more substantial action.

#### Required Changes

The substantive section. What the agent does, in enough detail that the work is unambiguous but not so much that it becomes pseudocode. Each change is its own subsection or numbered item.

For each change:

- **What**—the concrete artifact (function signature, test name, schema column).
- **Why**—one line, only if not obvious from context.
- **How**—only if the approach isn't obvious. Default to leaving how to the agent.

Example:

````markdown
**Required Changes**

1. **Define `AnthropicParser` class** in `src/shuji/parsers/anthropic.py`.
   Implements the `Parser` protocol from `src/shuji/parsers/base.py`.

   - `can_parse(path: Path) -> bool` returns True if the file is a JSON array
     of objects with the expected Anthropic export shape.
   - `parse(path: Path) -> Iterator[ConversationRecord]` yields one record
     per conversation. Raise on first malformed entry; no lenient mode.

2. **Add to PARSERS list** in `src/shuji/parsers/__init__.py`.
   Append `AnthropicParser` to the list.

3. **Synthetic fixtures** in `tests/fixtures/anthropic/`.
   ...
````

The agent produces the named artifacts. Architectural intent was settled in the planning conversation and captured in the parent doc or Goal; the agent doesn't reinterpret it here.

#### Acceptance

The pass/fail criteria. The agent verifies these before declaring the task complete.

````markdown
**Acceptance**

- [ ] `AnthropicParser` class exists in `src/shuji/parsers/anthropic.py` and implements the Parser protocol.
- [ ] All unit tests in `tests/parsers/test_anthropic.py` pass.
- [ ] `AnthropicParser` registers in the `PARSERS` list.
- [ ] Linting clean (ruff, mypy).
- [ ] No new dependencies added beyond what `pyproject.toml` already declares.
````

Use checkboxes—machine-readable, and the practitioner can scan them.

#### Verification

The commands the agent runs to verify acceptance. Concrete, copy-pasteable.

````markdown
**Verification**

```bash
# Run unit tests
uv run pytest tests/parsers/test_anthropic.py -v

# Lint
uv run ruff check src/shuji/parsers/anthropic.py
uv run mypy src/shuji/parsers/anthropic.py

# Smoke test the registration
uv run python -c "from shuji.parsers import PARSERS; print([p.__name__ for p in PARSERS])"
```
````

Verification commands match acceptance criteria one-to-one. If a criterion can't be verified by a runnable command, name the manual check explicitly.

#### Commit

The agent's commit instruction. Brief—message format and any commit-time discipline.

````markdown
**Commit**

Single commit. Message format:

```
feat(parsers): add AnthropicParser

Implement AnthropicParser per the task spec. Yields ConversationRecord
from Anthropic conversation exports. Strict error handling; no lenient mode.

Tests: 9 passing.
```
````

### Optional sections

Include when warranted; omit when not.

#### Context

A few sentences when the task can't be understood from Goal alone—reserved for background from the parent doc or planning conversation that the agent needs.

````markdown
**Context**

The parser plugin protocol was decided in the system design (see parent doc).
Each parser implements `can_parse` (cheap dispatch check) and `parse` (the
work). Strict error handling was chosen over lenient mode in v1; the agent
does not need to revisit that decision.
````

Used when the task references decisions made elsewhere that the agent can't see.

#### Problem

For fix-shaped or maintenance tasks: what's broken, what's the symptom. Skipped for feature-shaped tasks where Goal already states the work.

````markdown
**Problem**

The `summary` column was added in an earlier task but never populated. The
titler module expects to read summaries from existing rows, so unpopulated
rows raise. Backfill needed.
````

#### Do Not

Explicit out-of-scope items the agent might otherwise wander into. Reserved for cases where the boundary is non-obvious.

````markdown
**Do Not**

- Do not add a migration framework. Schema changes are ALTER TABLE in the
  task that introduces them.
- Do not modify `ConversationRecord`'s public fields. The model is shared
  with the indexer; changing it has cross-cutting implications.
- Do not add lenient parsing modes. Strict was decided in the system design.
````

A guard against drift. Use sparingly; every entry should be a failure mode the agent might hit.

#### Stop Condition

When to halt and surface to the practitioner instead of continuing. Used for tasks where the work might surface unexpected complexity or ambiguity.

````markdown
**Stop Condition**

If a real Anthropic export reveals shape mismatches with the schema (fields
not in the synthetic fixtures), stop and surface findings before modifying
the data model. Schema decisions belong to the practitioner, not the agent.
````

Especially useful for tasks that include real-data inspection—the inspection itself may produce findings the agent shouldn't act on alone.

---

## Translation moves: from intent to executable spec

A planning conversation produces decisions. An agent task spec turns those decisions into something the executing agent can run. The translation isn't mechanical—it's a craft move. Patterns:

### Move 1: Decisions → interface specifications

A decision like "data model: pydantic v2" becomes a concrete artifact in the task: "Define `ConversationRecord(BaseModel)` with fields `id: str`, `source: str`, `messages: list[Message]`, ..." The decision said what; the task says what artifact and where.

### Move 2: Architectural commitments → file boundaries

A commitment like "shuji owns SQLite; bridges and Library Surface read via shuji" becomes file boundaries in the task: "Add to `src/shuji/`, not `src/shodo/api/`." The architectural commitment becomes a literal directory rule.

### Move 3: Out-of-scope items → Do Not entries

The conversation's "out of scope" list converts into Do Not entries when the boundary is non-obvious. "This task doesn't include the indexer" doesn't need a Do Not (the file paths make it obvious). "This task uses strict parsing only" might need one (a thoughtful agent could rationalize a lenient mode as helpful).

### Move 4: Deferred discoveries → Stop Conditions

Decisions that defer to execution-time real-data inspection become Stop Conditions. The planning conversation said "we'll discover the schema when we see the real data"; the task spec says "stop and surface if X is encountered."

### Move 5: Verification dependencies → ordering

If task A's verification depends on task B's output, A comes after B in the task sequence. Sometimes the practitioner has to reorder mid-decomposition because a verification dependency surfaces. That's fine.

---

## Anti-patterns

- **Pseudocode in Required Changes.** Specs that try to write the implementation. The agent does the implementation; the task says what to produce.
- **Acceptance criteria without verification commands.** A criterion the agent can't run a command for is a criterion the agent can't verify.
- **Verification that doesn't match acceptance.** Acceptance says "tests pass," verification doesn't list how. Mismatch.
- **Do Not entries that aren't failure modes.** Padding with hypothetical drift. Each Do Not should describe a way the agent might wander.
- **Stop Conditions on every task.** If every task has one, the practitioner is over-supervising. Reserve for tasks that warrant a halt.
- **Tasks that combine multiple goals.** "Implement X and also fix Y while you're in there." Split. One Goal per task.
- **`parent` filled with a placeholder.** If there's no parent doc, omit the field. Don't write `parent: none` or `parent: standalone`.
- **Slugs that don't disambiguate.** `agent-task-2026-05-09-fix.md` tells you nothing. `agent-task-2026-05-09-fix-titler-summary-null-handling.md` does.

---

## Format checklist

Before declaring a task ready:

- [ ] Frontmatter complete (created, type, status; parent if descended from a project doc)
- [ ] Goal: one to three sentences, unambiguous
- [ ] Files: full paths, create/modify marked
- [ ] Required Changes: each change is concrete, not pseudocode
- [ ] Acceptance: checkboxes, each one verifiable
- [ ] Verification: commands match acceptance one-to-one
- [ ] Commit: message format specified
- [ ] Optional sections (Context, Problem, Do Not, Stop Condition) present only when warranted
- [ ] Slug is descriptive enough to disambiguate among other tasks
