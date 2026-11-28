# Agent Rules — Commonplace

You are a coding agent working on Commonplace. The owner is **not a programmer**: they set the product vision and approve big decisions. You make technical decisions within this plan and explain anything you surface to them in plain language.

## Before any work
1. Read `docs/HANDOFF.md` (current state) and the task you were given in `docs/DEVELOPMENT_PLAN.md` §10.
2. Read only the specs and files that task needs (`docs/specs/`, the relevant component). Do not scan the whole repo.
3. Check the pinned dependency versions before using any API, and use the official docs for that version.

## Hard rules
- **No vibe coding.** Every change must be maintainable, tested and match the plan and specs.
- **Do one task per session**, then stop. Do not start the next task.
- **Stop and ask the owner** before you:
  - add a dependency not in plan §8,
  - add a new system or abstraction not in the plan,
  - change the file format, core API or editor bridge contract,
  - or make a design choice the plan does not cover.

  Give the options with pros and cons in plain language, plus your recommendation.
- **No hard-coding.** Configurable values live in one config or token place per component. Use the platform's standard spacing, fonts, colors and controls (plan §6).
- **Research first.** Check whether the platform or an approved library already solves the problem before writing new code.
- **Data safety comes first.** Never rewrite text you didn't change. Never overwrite on conflict. Write atomically. Never log note content. Tasks marked 🔒 need an independent review session before merge.
- **No network calls** in the core or editor. No telemetry. No secrets in the repo.
- **No AI-generated art.** Use system icon sets; mockups and simple glyphs are fine.
- Electron is prohibited. The only web view allowed is the CodeMirror editor area (decision D-003).
- Never hand-edit generated files (`*.pbxproj`, UniFFI output). Edit their sources (`project.yml`, `.udl`/proc-macros).

## Definition of done (every task)
- The task's acceptance criteria are met, tests added or updated, and all lint, format and test checks pass locally.
- Run the checks for the components you touched:
  - core: `cargo fmt --check && cargo clippy -- -D warnings && cargo test`
  - editor: `npm run lint && npm test`
  - Mac/Windows apps: their build and test commands in their README.
- The PR follows `.github/pull_request_template.md`. CI is green.
- `docs/HANDOFF.md` is updated: what was done, what's next, any risks.
- Docs are updated if behavior or contracts changed. Decisions go in `docs/DECISIONS.md`.

## Communication
- Be concise. Write in plain language for the owner and avoid jargon.
- Report exactly what you tested and what remains unverified.

## Further rules
- Full project rules: `rules.md`. AI cost policy: `AI_USAGE_POLICY.md`.
- Per-component notes: each component's own `AGENTS.md` in `core/`, `editor/` and `apps/*`, once they exist.
