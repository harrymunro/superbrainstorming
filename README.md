# Superbrainstorming

Brainstorm the design with your coding agent, agree on it, then let the agent build it.

Superbrainstorming is a fork of [Superpowers](https://github.com/obra/superpowers) by Jesse Vincent and [Prime Radiant](https://primeradiant.com). It keeps the brainstorming step and removes everything that came after it. When brainstorming is finished and you've approved the design, the agent starts implementing in the same session. There's no implementation plan document, subagent relay, or separate execution workflow.

## Why only brainstorming?

Superpowers is a complete methodology: brainstorm a design, turn it into a detailed implementation plan, then execute the plan task by task through fresh subagents with test-driven development and code review at each step. That pipeline was built for the models of its time. The plan was written to be, in the upstream README's words, "clear enough for an enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing to follow." Handing each task to a fresh subagent kept every unit of work small enough for the model to stay on track.

Current frontier models, such as Claude Opus 5.5, Claude Sonnet 5.5 and Claude Fable 5.1, can hold an approved design in context, break it into steps themselves, write and run tests as they go, and work for long stretches without drifting from what was agreed. For them, a written plan that restates the spec as bite-sized tasks mostly adds tokens and wall-clock time, and passing work between subagents adds hand-offs where context gets lost.

Better models haven't fixed misunderstanding. They can still build the wrong thing, and build it well. The agent can't know what you meant, who it's for, or which trade-offs you care about until someone asks. Brainstorming is the part of Superpowers that deals with that, so it's the part this fork keeps.

## How it works

When you ask your agent to build something, the `brainstorming` skill takes over before any code is written.

1. **Classify the work.** The agent says out loud which of three paths it's taking, so you can override it:
   - **Spike**: a feasibility question. The agent proposes a quick probe, gets a nod, and reports back a recommendation. Anything it builds is throwaway.
   - **Bounded**: a well-scoped change to code that already exists, like a new flag, a small endpoint or a one-file fix. The agent asks the questions that matter and presents a short design in chat.
   - **Architectural**: new projects, new subsystems, or changes to how components fit together. The agent asks questions one at a time, proposes two or three approaches with a recommendation, presents the design section by section, and writes a spec to `docs/specs/YYYY-MM-DD-<topic>-design.md`.
2. **Establish shared understanding.** The agent works out what you're trying to achieve, who it's for and what success looks like, then writes that back to you so you can correct it before any design work starts.
3. **Get approval.** Nothing gets built until you approve the design: a nod for a spike, a "yes" to the in-chat design for bounded work, or approval of the written spec for architectural work.
4. **Build it.** Your approval is the go-ahead. The agent implements straight from the approved design in the same session, tracks its own steps, follows your project's conventions for tests and commits, and checks its work before calling it done. If it finds the design was wrong, it stops and brings you a proposed revision instead of quietly changing course. It finishes with a summary of what it built, how it verified it, and any decisions it made along the way.

If you tell the agent to skip the design and just build, it will.

## Installation

Superbrainstorming is a Claude Code plugin. In Claude Code:

```bash
/plugin marketplace add harrymunro/superbrainstorming
/plugin install superbrainstorming@superbrainstorming
```

Then start a new session.

If you also have Superpowers installed, disable or uninstall it through `/plugin` first. Both plugins provide a `brainstorming` skill and a session-start hook, and Superpowers' version hands off to its planning workflow instead of implementing.

**Check it works:** in a fresh session, send `Let's make a react todo list`. The agent should invoke `superbrainstorming:brainstorming`, say that it's treating this as architectural, and ask you what the app is for before it writes any code.

### Other agents

The skill is a standard `SKILL.md` in `skills/brainstorming/`, so any agent that supports Agent Skills can load it. Without the session-start hook it may not trigger on its own, so ask for it by name. Upstream Superpowers ships integrations for many other harnesses if you want to port one.

## What's inside

- **`skills/brainstorming/`**: the skill, plus the optional visual companion.
- **`hooks/`**: a session-start hook that injects `hooks/bootstrap.md`, a short note that tells the agent to brainstorm before building and to build once the design is approved.

### Visual companion

When a question is easier to answer by seeing it (layouts, mockups, diagrams), the agent can offer to open a local browser tab and show you options there. It's opt-in per session and needs Node.js. It runs entirely on your machine and loads nothing from remote hosts. Mockups are saved under `.superbrainstorming/` in your project, so add that to your `.gitignore`.

## What changed from Superpowers

**Kept**
- The `brainstorming` skill: the three-path router, the approval gate, one-question-at-a-time design, spec self-review and the visual companion.
- The Claude Code session-start hook.

**Changed**
- Brainstorming now ends in implementation. Approving the design (or, for architectural work, the written spec) starts the build. Upstream, it started the `writing-plans` skill.
- The bootstrap is a short, plain note. It replaces the `using-superpowers` skill, which used emphatic all-caps rules to make sure models followed it.
- Specs go to `docs/specs/` instead of `docs/superpowers/specs/`.
- The visual companion shows plain text branding and no longer loads the Prime Radiant logo, which upstream uses as usage telemetry.

**Removed**
- Skills: `writing-plans`, `executing-plans`, `subagent-driven-development`, `test-driven-development`, `systematic-debugging`, `verification-before-completion`, `requesting-code-review`, `receiving-code-review`, `using-git-worktrees`, `finishing-a-development-branch`, `dispatching-parallel-agents`, `writing-skills`, `diagnosing-superpowers` and `using-superpowers`.
- Integrations for harnesses other than Claude Code (Codex, Cursor, Gemini, OpenCode, Pi, Hermes, Kimi, Devin, Muse and others), along with their tests and packaging scripts.
- Upstream's design docs, release notes and community files.

### When to use Superpowers instead

If you're working with smaller or older models, or you want enforced TDD, per-task code review and git worktree management, use [Superpowers](https://github.com/obra/superpowers). Its extra structure is there for good reasons, and those reasons still hold for models that need it.

## Development

```bash
# Visual companion server tests
cd tests/brainstorm-server && npm ci && npm test

# Session-start hook tests
bash tests/hooks/test-session-start.sh
```

Changes to `skills/brainstorming/SKILL.md` or `hooks/bootstrap.md` change agent behavior. Try them in real sessions before merging: at least the `Let's make a react todo list` check above, plus a bounded change to an existing repo.

## Credits

Superbrainstorming is derived from [Superpowers](https://github.com/obra/superpowers), created by [Jesse Vincent](https://blog.fsck.com) and the team at Prime Radiant. The brainstorming skill and visual companion are their work, and the full upstream git history is preserved in this repository. Please report problems with this fork here, not upstream.

## License

MIT. See [LICENSE](LICENSE).
