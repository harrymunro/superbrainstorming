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

## Session notes (Obsidian vault)

Persistent prose (session notes, decisions, research) lives in Harry's vault
at `/Users/harry/Documents/Obsidian Vault`, project folder `Business/Superbrainstorming/`, index `Superbrainstorming MOC.md`. The
repo keeps code and its docs; the vault keeps what should outlive a session.

- **Confidentiality**: the vault holds notes for other clients and private
  notes. Never read, list, search or write outside `Business/Superbrainstorming/`. Scope
  every `obsidian` CLI call with `path=` or `folder=`; vault-wide commands
  (`search` without `path=`, `files` without `folder=`, `properties`,
  `orphans`, `deadends`, `recents`, `random`, `bases`, `daily:*`) are
  forbidden here.
- **Start**: read `Superbrainstorming Session Log.md` for continuity; open a note under
  `Sessions/` only when you need the detail. If the folder or log does not
  exist yet, create `Superbrainstorming MOC.md` and `Superbrainstorming Session Log.md` (a table of date,
  note and summary, newest row first).
- **Finding past context**: use the `vault-search` skill (`vault-search
  "<question>"`) before reading or grepping notes by hand. It is scoped to
  this folder by `.claude/vault-search.toml` and ranks passages with Jev
  (Harry's decision; never edit that file).
- **End**: write `Sessions/YYYY-MM-DD<letter>-topic.md` and add a one-line row
  at the top of the table in `Superbrainstorming Session Log.md`. Frontmatter: `project:
  Superbrainstorming`, `source: claude`, `date: YYYY-MM-DD`, `type:
  session|meeting|decision|research|note`, `summary: <one line>`. Link new
  evergreen notes from `Superbrainstorming MOC.md`.
- The `obsidian` CLI needs the app running; if it errors, read and write the
  files directly under the same folder.
