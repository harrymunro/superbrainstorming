# Superbrainstorming: Contributor Guidelines

Superbrainstorming is a Claude Code plugin with one skill, `brainstorming`. It's a fork of [Superpowers](https://github.com/obra/superpowers) that keeps the brainstorming step and goes straight to implementation once the human partner approves the design.

## Layout

- `skills/brainstorming/SKILL.md`: the skill. This file shapes agent behavior.
- `skills/brainstorming/visual-companion.md` and `skills/brainstorming/scripts/`: the optional browser companion (a zero-dependency Node server).
- `hooks/bootstrap.md`: the context injected at session start. `hooks/session-start` reads it and emits it in the right JSON shape; `hooks/run-hook.cmd` is the cross-platform wrapper.
- `.claude-plugin/`: plugin and marketplace manifests. The version lives in `plugin.json` only.
- `tests/`: `brainstorm-server/` (Node tests for the companion) and `hooks/` (bash tests for the session-start hook).

## Principles

- **One skill.** Scope is brainstorming, then implementation. Don't add planning, execution or review skills back in. If something needs one, it belongs in a separate plugin.
- **Trust the model.** The target is current frontier models. Prefer short, plain instructions over emphatic rules, and don't add scaffolding a capable model does on its own.
- **Keep the gate.** Every path ends with the human partner approving the design before implementation starts. After approval, implementation starts immediately.
- **Keep the voice.** "Your human partner" is deliberate wording inherited from Superpowers. Keep it.
- **No dependencies, no remote calls.** The plugin runs with bash and Node's standard library. The visual companion loads nothing from remote hosts.

## Changing skill or bootstrap text

Wording in `SKILL.md` and `bootstrap.md` changes agent behavior, so test it in real sessions before merging:

1. In a clean Claude Code session with the plugin installed from your working copy, send `Let's make a react todo list`. The agent should invoke `superbrainstorming:brainstorming`, classify the work as architectural, and ask about purpose before writing code.
2. In an existing repo, ask for a small change. The agent should classify it as bounded, present a short in-chat design, stop, and then start implementing as soon as you approve it, without writing a plan document.

Describe what you tried and what happened in the PR.

## Tests

```bash
cd tests/brainstorm-server && npm ci && npm test
bash tests/hooks/test-session-start.sh
```

## Upstream

Don't open PRs against `obra/superpowers` with changes from this fork. If you fix something in the shared visual companion code that affects upstream too, open a separate, upstream-shaped PR following their `AGENTS.md`.
