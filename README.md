# Dandori

*Prep discipline for autonomous coding agents.*

> Dandori (段取り) is the trades word for the prep before the work—the carpenter's bench laid out before the saw bites, the chef's mise en place set before the burner is lit. The work goes smoothly because the dandori was thorough. This skill does the dandori for an autonomous coding agent: a planning conversation becomes a surgical task spec the agent executes on its own.

## The problem

You ask an agent to fix a bug. It fixes the bug—then keeps going. Seven more commits. Two hundred lines you didn't ask for. Abstractions. Refactors. A threading system to fix a bug that only existed because of the new feature it decided to add.

The diagnosis is always the same: *scope creep*. The fix people reach for is always the same: better prompts. That's the wrong fix.

A prompt is a sentence. A handoff is an artifact. The agent didn't go rogue because the prompt was imprecise; it went rogue because you gave it a conversation when the job called for a handoff.

Two centuries of military doctrine—from the Prussian *Auftragstaktik* through the US Army's *mission command*—have been about getting the handoff between commander and subordinate right. The trades have had a word for the prep that makes the handoff possible: *dandori*. Engineers working with autonomous agents are reinventing both from scratch, badly, in chat windows.

The fix isn't a more carefully phrased question. The fix is a different kind of object: a bounded spec the agent reads as the specification of one unit of work, not as the latest turn in an ongoing chat.

*[Everybody Is Shipping Work Nobody Asked For](https://sageframe.substack.com/p/everybody-is-shipping-work-nobody?r=1b4mpz)* — the essay that named this problem and built this tool.

## How it works

You have a planning conversation—that's what chats are for, untangling and understanding. Dandori turns the decisions of that conversation into a markdown spec at `agent-tasks/agent-task-YYYY-MM-DD-slug.md`: goal, files, required changes, acceptance criteria, verification commands, commit format. You hand the spec to the agent. The agent works inside it.

The agent's job stops being *infer what the user wants* and becomes *execute what the spec says*. Agents are good at executing what the spec says. We've been asking them to infer what we want.

```
dandori/
├── SKILL.md       # entry point
├── KOKOROE.md     # the five guidelines
├── FORMAT.md      # the spec format reference
└── examples/      # three worked examples
```

Markdown only. No runtime. The skill is a methodology artifact, not a service.

## Kokoroe

The agent operates under **kokoroe** (心得)—the internalized discipline a craftsperson carries into their work. Five guidelines, installable once in your project's `CLAUDE.md`:

1. **Honor the given.** Read what's loaded before acting.
2. **If the spec did not ask, do not give.** Every changed line traces to the spec.
3. **If a command cannot prove it, it is not done.** Acceptance is the definition of done.
4. **Surprise is signal; halt.** Stop and surface findings rather than rationalize.
5. **Propose, do not decide.** Architecture belongs to the practitioner.

These don't constrain the agent's intelligence—they make it usable.

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
- **If the spec did not ask, do not give** — [Everybody Is Shipping Work Nobody Asked For](https://sageframe.substack.com/p/everybody-is-shipping-work-nobody?r=1b4mpz)
- **If a command cannot prove it, it is not done** — [Bad Vibes](https://sageframe.substack.com/p/bad-vibes)
- **Surprise is signal; halt** — [Walking Without Google Maps](https://sageframe.substack.com/p/walking-without-google-maps), the secret camino
- **Propose, do not decide** — [Thinking Outside the Skull](https://sageframe.substack.com/p/thinking-outside-the-skull)

## License

MIT.

---

*Andrew Todd Marcus · [atmarcus.net](https://atmarcus.net) · [sageframe.substack.com](https://sageframe.substack.com) · [pinkteaming.net](https://pinkteaming.net)*
