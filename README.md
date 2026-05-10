# Dandori

*Prep discipline for autonomous coding agents.*

> Dandori (段取り) is the trades word for the prep before the work—the carpenter's bench laid out before the saw bites, the chef's mise en place set before the burner is lit. The work goes smoothly because the dandori was thorough. This skill does the dandori for an autonomous coding agent: a planning conversation becomes a surgical task spec the agent executes on its own.

## Kokoroe

The agent operates under **kokoroe** (心得)—the practitioner's internalized discipline of care, intention, and specificity. Five guidelines, installable once in your project's `CLAUDE.md`:

1. **Honor the given.** Read what's loaded before acting.
2. **If the spec did not ask, do not give.** Every changed line traces to the spec.
3. **If a command cannot prove it, it is not done.** Acceptance is the definition of done.
4. **Surprise is signal; halt.** Stop and surface findings rather than rationalize.
5. **Propose, do not decide.** Architecture belongs to the practitioner.

## How it works

You have a planning conversation. Dandori turns it into a markdown spec at `agent-tasks/agent-task-YYYY-MM-DD-slug.md`—goal, files, required changes, acceptance criteria, verification commands, commit format. You hand the spec to an agent. The agent operates under kokoroe and executes.

```
dandori/
├── SKILL.md       # entry point
├── KOKOROE.md     # the five guidelines
├── FORMAT.md      # the spec format reference
└── examples/      # three worked examples
```

Markdown only. No runtime. The skill is a methodology artifact, not a service.

## Install

Clone into your `.claude/skills/`:

```bash
cd .claude/skills
git clone https://github.com/sageframe-no-kaji/dandori.git
```

Install kokoroe at the project level so the agent loads it every session:

```bash
cat .claude/skills/dandori/KOKOROE.md >> CLAUDE.md
```

Or `@`-reference it from `CLAUDE.md`:

```markdown
@.claude/skills/dandori/KOKOROE.md
```

## Use

In any session with Claude Code (or compatible agent):

- "Draft me an agent task to bump fastapi from 0.110 to 0.115"
- "Package what we just decided as a spec"
- "Turn this conversation into something the agent can run"

The skill triggers, asks for what's missing, and saves the spec. Hand it to the agent.

## Provenance

Dandori is part of the [Ho System](https://atmarcus.net), a methodology for human-AI collaboration. The five koans aren't generic advice—each is rooted in an essay in [Constructive Interference](https://sageframe.substack.com).

- **Honor the given** — [Walking Without Google Maps](https://sageframe.substack.com/p/walking-without-google-maps)
- **If the spec did not ask, do not give** — [Prompting, Not Programming](https://sageframe.substack.com/p/prompting-not-programming)
- **If a command cannot prove it, it is not done** — [Bad Vibes](https://sageframe.substack.com/p/bad-vibes)
- **Surprise is signal; halt** — [Walking Without Google Maps](https://sageframe.substack.com/p/walking-without-google-maps), the secret camino
- **Propose, do not decide** — [Thinking Outside the Skull](https://sageframe.substack.com/p/thinking-outside-the-skull)

## License

MIT.

---

*Andrew Todd Marcus · [atmarcus.net](https://atmarcus.net) · [sageframe.substack.com](https://sageframe.substack.com) · [pinkteaming.net](https://pinkteaming.net)*
