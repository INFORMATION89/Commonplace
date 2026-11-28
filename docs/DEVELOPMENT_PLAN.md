# Commonplace — Development Plan

**Status:** Approved baseline for the first viable product (MVP). Supersedes [PREPLAN.md](../PREPLAN.md) wherever they disagree.
**Date:** 2026-11-28
**Decision log:** [DECISIONS.md](DECISIONS.md) · **Agent rules:** [AGENTS.md](../AGENTS.md) · **Current state:** [HANDOFF.md](HANDOFF.md)

This document is the single plan from first commit to MVP release. Coding agents follow it task by task (§10). Any change to a decision here must first be recorded in the decision log and approved by the owner.

---

## 0. Plain-language summary (for the owner)

- **What we build first:** a Mac app for writing Markdown notes with live formatting, images in notes, tags, search and a list view. Then the same app on Windows.
- **How it's built:**
  - The **core** (saving files, search, tags, note IDs) is written once in Rust and shared by every app.
  - The **editor** (the typing area) is written once with CodeMirror, the same editor technology Obsidian uses, and embedded in each app.
  - Everything else (windows, menus, sidebar, settings) is **native**: SwiftUI on Mac, WinUI 3 on Windows.
- **Where your notes live:** ordinary `.md` files and images in a folder you choose. If you put that folder in iCloud Drive, OneDrive or Dropbox, your Mac and Windows PC already share the notes and images. The app detects and protects against conflicting edits. A real sync engine comes in v2.
- **Later:** the canvas and the Android app come in **v2**. Reminders, sharing, AI features and iOS come after that.
- **Your role:** you approve big decisions, test each milestone with the plain-language checklists, and supply real art (app icon, any illustrations).
- **AI's role:** Sonnet sessions do the coding, one numbered task at a time. Opus is used only for the rare cases listed in §9.

---

## 1. Scope

### 1.1 MVP (version 0.1 — Mac, then Windows)

| Area | Included |
|---|---|
| Vault | Choose or create a folder ("vault"). Open an existing Markdown folder (Obsidian-compatible). Watch for changes made outside the app. |
| Notes | Create, rename, move between folders, archive (move to `Archive/`), delete (to system Trash). Autosave. |
| Editor | Live preview: headings, bold, italic, strikethrough, inline code, code blocks, links, bullet/numbered lists, task checkboxes (clickable), quotes, horizontal rules, inline images. Moving into formatted text shows its Markdown. Floating formatting toolbar on text selection. Standard shortcuts, undo/redo, spellcheck. GFM tables are kept exactly and shown as styled source; full table rendering comes after the MVP. |
| Images | Paste, drag in, or insert an image. It's copied into the vault's `attachments/` folder and inserted as a standard Markdown image link, shown inline. |
| Tags | `#tag` in text and `tags:` in frontmatter, both recognized. Nested tags (`#travel/japan`). Tag autocomplete. Tag list in the sidebar. |
| List view | All notes, or notes in a folder or tag. Sort by modified, created or title. Group by date (Today, This week, …). Filter by tag. |
| Search | Full-text search over titles, content and tags, offline. Command palette (⌘K / Ctrl+K) for search and commands. Find in note (⌘F / Ctrl+F). |
| Pinned notes | Pin notes to the top of the sidebar. |
| Origin | Records which device created a note (user-editable device name). |
| Safety | Atomic saves; conflict detection; conflict copies, never silent overwrite; local version history (recent versions kept outside the vault); deletes go to the system Trash. |
| Settings | Vault location, device name, editor text size, attachments folder name. Light/dark follow the system. |
| Platform basics | Native menus and shortcuts, multiple windows, VoiceOver/Narrator basics, reduced motion, dark mode. |

### 1.2 Explicitly not in the MVP

The canvas, the Android app, the sync engine (PSC), reminders, sharing, AI features, shopping lists, iOS, Linux, automatic updates, payments, publishing, and table rendering.

### 1.3 Later versions (direction, not yet specified in detail)

- **v2:** desktop canvas (pan/zoom, cards, groups, stacks) on Mac and Windows; Android app (Compose + Material 3 + Motion UI) with a list-first layout; first real sync via PSC; auto-updates for testers.
- **v3:** date/time reminders, small-group sharing, optional AI actions (read-only first), iOS app, shopping lists.
- Each version gets its own detailed plan section, written and approved before its coding starts, in the same format as this one.

---

## 2. Architecture

```
┌──────────────── Native app (per platform) ────────────────┐
│  SwiftUI (macOS)  ·  WinUI 3 (Windows)  ·  Compose (v2)   │
│  windows, sidebar, list, palette, settings, menus         │
│        │                               │                  │
│        │ generated bindings (UniFFI)   │ message bridge   │
│        ▼                               ▼                  │
│  ┌──────────────┐               ┌─────────────────────┐   │
│  │  Rust core   │◀── file I/O ──│ Editor (CodeMirror) │   │
│  │ (shared lib) │   via native  │ in a WebView, only  │   │
│  └──────────────┘               │ inside the editor   │   │
│        │                        └─────────────────────┘   │
└────────┼──────────────────────────────────────────────────┘
         ▼
   Vault folder (.md + attachments/)   App data dir (index, history)
```

### 2.1 Components and responsibilities

| Component | Language / framework | Owns | Must not |
|---|---|---|---|
| `core/` | Rust (stable), UniFFI bindings | Vault scanning, reading and writing notes and attachments, frontmatter, IDs, tag/link extraction, search index, conflict detection, version history, file watching, workspace metadata | Know about any UI; make network calls |
| `editor/` | TypeScript + CodeMirror 6, bundled with Vite | Text editing, live preview, formatting toolbar, tag autocomplete UI, bridge messages | Touch the file system; make network calls; load remote resources |
| `apps/macos/` | Swift + SwiftUI (AppKit only where SwiftUI lacks a capability), WKWebView for the editor | App shell, navigation, list, sidebar, palette, settings, menus, accessibility, bridging core ↔ editor | Contain business logic that belongs in core |
| `apps/windows/` | C# + WinUI 3 (Windows App SDK), WebView2 for the editor | Same as macOS, for Windows | Same |
| `apps/android/` | Kotlin + Jetpack Compose + Material 3 + Motion UI | **v2** | — |

### 2.2 Key technical decisions

Each entry is recorded in [DECISIONS.md](DECISIONS.md) with its reasoning.

1. **Shared Rust core with UniFFI.** UniFFI generates the Swift and Kotlin bindings; C# uses the community generator `uniffi-bindgen-cs`. Fallback if the C# generator proves unreliable: a thin hand-written C API in `core/ffi-c` called through C# P/Invoke. Decide only if task 5.1 fails.
2. **Editor = CodeMirror 6 in a WebView, editing area only.** This is a documented, narrow exception to the "no web views" rule. CodeMirror edits the raw Markdown text and draws formatting on top of it, so the file is never rewritten behind the user's back. This also satisfies the round-trip rule.
3. **Plain files are the truth; the index is disposable.** The SQLite index lives in the app's local data folder, not in the vault, so cloud folders never sync or conflict on it. Deleting the index loses nothing; it is rebuilt.
4. **Folder-based cross-device use in the MVP.** The vault can sit inside any cloud-synced folder. The core must handle files changing underneath it, files that exist only in the cloud, and conflict copies made by those services.
5. **Filename is the note title** (Obsidian convention, fully portable). Renaming a note renames its file.
6. **Stable IDs in frontmatter** (`id:` using UUIDv7) for notes created by Commonplace. Existing notes get an ID only when the user edits them in Commonplace. The app never rewrites files just because it opened them.
7. **No servers and no network access in the MVP.** No telemetry and no accounts. The WebView has a strict content security policy and a local-only content source.
8. **Text-based project files so AI can edit them safely.** The Mac project is generated from `project.yml` with XcodeGen, because AI must never hand-edit `.pbxproj`. The Windows project uses SDK-style `.csproj` files.
9. **Monorepo.** All components live in this repository and are versioned together.

### 2.3 Repository layout

```
/AGENTS.md, /CLAUDE.md        agent rules (read first)
/core/                        Rust workspace
   crates/commonplace-core/   domain + storage + index
   crates/commonplace-ffi/    UniFFI interface (thin)
/editor/                      CodeMirror package → dist/ bundled into apps
/apps/macos/                  XcodeGen project.yml + Swift sources
/apps/windows/                WinUI 3 solution
/fixtures/vaults/             test vaults (edge-case Markdown, Obsidian samples, conflicts)
/docs/                        plan, decisions, specs, tasks, handoff, testing checklists
/.github/workflows/           CI
```

### 2.4 Data flow for the main operations

- **Open note:** the native app calls `core.read_note(id)` and gets the text plus a content hash. The native app sends the text to the editor (`load`).
- **Edit and save:** the editor sends a `changed` message (debounced, about 500 ms of idle time, value set in one config file). The native app calls `core.save_note(id, text, base_hash)`. The core compares `base_hash` with the file on disk:
  - **Match:** the core writes atomically (temporary file, flush, rename), stores a history snapshot, updates the index, and returns the new hash.
  - **Mismatch** (changed elsewhere): the core writes the user's version as a conflict copy, never over the other version, and returns `Conflict`. The app shows a banner offering to compare or keep both.
- **Paste image:** the editor sends the image bytes and MIME type to the native app. The native app calls `core.add_attachment(note_id, bytes, ext)` and gets back a relative path. The editor inserts `![](attachments/…)`.
- **Show image:** the editor requests `cp-asset://vault/attachments/x.png`. The native app serves the file through a custom URL handler (WKURLSchemeHandler on Mac, WebView2 virtual host on Windows). Only files inside the vault can be served.
- **External change:** the file watcher tells the core, which updates the index and emits an event. If the changed note is open:
  - **No unsaved edits:** the editor reloads it.
  - **Unsaved edits:** the conflict flow above runs.

---

## 3. File format specification (MVP)

### 3.1 Vault layout

```
MyVault/
  Some note.md
  Travel/Japan trip.md
  Archive/Old idea.md
  attachments/2026-11-28-143012-a1b2.png
  .commonplace/
    workspace.json     pinned notes, saved list views (portable, small, sync-safe)
    devices.json       device labels (id → human name)
```

- `attachments/`: the folder name is configurable per vault and stored in `workspace.json`.
- Attachment filenames follow the pattern `YYYY-MM-DD-HHMMSS-<4 random hex>.<ext>`, which keeps them unique across devices without a central counter.
- Hidden folders such as `.obsidian/`, `.git/` and `.trash/` are ignored. Commonplace never touches them.

### 3.2 Markdown dialect

- Baseline is **CommonMark + GFM**: tables, task lists, strikethrough and autolinks.
- Supported extensions:
  - YAML frontmatter.
  - `#tags`: letters, digits, `_`, `-`, `/`. They must start with a non-digit and don't count inside code.
  - Wiki-links `[[Note]]`: recognized and indexed, rendered as links only after the MVP. Text is kept exactly.
- **Anything unknown is kept byte-for-byte.** The core never re-serializes Markdown. It edits only frontmatter keys it owns, using minimal text edits that leave every other key, comment and its order untouched.
- Line endings and encoding: files are read as UTF-8 (a BOM is accepted and kept) and the existing line-ending style is kept. New files use LF.

### 3.3 Frontmatter keys owned by Commonplace

```yaml
---
id: 0193a1b2-...        # UUIDv7, stable across rename/move
created: 2026-11-28T14:30:12-06:00   # ISO 8601 with offset
created_on: dev_7f3c... # device id; human label resolved via .commonplace/devices.json
tags: [recipes, travel/japan]
---
```

- No `modified` key: the file's own modification time is used, which avoids sync churn.
- Other keys are preserved and never removed.
- Recognized from other apps: `tags` (list or string) and `aliases`.

### 3.4 Workspace metadata

- `workspace.json` has a `schema_version` field and a key `pinned` listing note IDs.
- Unknown fields are preserved. Writes are atomic.
- Conflict rule: entries from both versions are merged, never last-write-wins.

### 3.5 Local app data (outside the vault, rebuildable or private)

- `index.sqlite`: notes, tags, links and a full-text (FTS5) table. A schema version change triggers a full rebuild.
- `history/<note-id>/…`: recent versions of each note. Default limits are 50 versions or 30 days, defined in one config place.

---

## 4. Core API (initial contract, refined in task 1.1)

Main types: `Vault`, `NoteMeta { id, path, title, created, modified, tags, pinned }`, `NoteContent { text, hash }`, `SaveResult { Saved(hash) | Conflict(copy_path) }`, `SearchHit { id, title, snippet }`, and `VaultEvent { Added | Changed | Removed | Renamed | ConflictDetected }`.

| Function group | Functions |
|---|---|
| Vault | `open_vault(path)`, `close`, `rebuild_index`, `subscribe(events)` |
| Notes | `list_notes(query: ListQuery)`, `read_note`, `create_note(folder, title)`, `save_note(id, text, base_hash)`, `rename_note`, `move_note`, `archive_note`, `trash_note` |
| Attachments | `add_attachment(note_id, bytes, ext)` |
| Tags | `list_tags`, `suggest_tags(prefix)` |
| Search | `search(text, limit)` |
| Workspace | `pin` / `unpin`, `device_name` / `set_device_name` |
| History | `history(note_id)`, `restore(note_id, version)` |

**Errors are typed** (for example `NotFound`, `Conflict`, `Io`, `InvalidName`, `CloudPlaceholder`) and never crash the app.

## 5. Editor bridge contract (initial, refined in task 2.1)

All messages are JSON with `type` and a schema version. The bridge is defined once in `editor/src/bridge/types.ts`; the Swift and C# mirror types are checked by contract tests.

| Direction | Messages |
|---|---|
| Native → editor | `load {text, noteId, readOnly}`, `theme {colors, font, size}`, `focus`, `command {name}` (bold, heading, …), `tagSuggestions {items}`, `attachmentInserted {path}` |
| Editor → native | `ready`, `changed {text, version}`, `requestTagSuggestions {prefix}`, `pasteImage {base64, mime}`, `openLink {href}`, `selectionFormatState {…}` |

The editor never receives file paths outside the vault and never makes network requests.

---

## 6. Design guidelines

- **Follow each platform's design guidelines:** Apple Human Interface Guidelines on macOS; Fluent 2 and Windows design guidelines on Windows; Material 3 on Android in v2.
- **Standard values only:**
  - Use system spacing, fonts (SF Pro / Segoe UI Variable), colors, accent color and control sizes.
  - Never invent pixel values when a system value exists.
  - App-specific values (the editor content width, for example) are defined once as design tokens:
    - Swift: `DesignTokens.swift`
    - C#: `DesignTokens.xaml`
    - Editor: CSS custom properties fed by the native `theme` message, so the editor always matches the app.
- **Standard components first:**
  - Mac: `NavigationSplitView`, `List`, `Table`, `.searchable`, `Settings` scene, `commands`.
  - Windows: `NavigationView`, `ListView`, `AutoSuggestBox`, `ContentDialog`, Mica.
- **Icons:**
  - Use system icon sets: SF Symbols on Apple only; Segoe Fluent Icons / Fluent UI System Icons on Windows; Material Symbols on Android.
  - AI may propose icon choices, draw simple glyphs when no system icon fits, and make mockups.
  - **AI does not generate art.** The app icon and any illustrations are real art from the owner.
- **Motion:** use system animations, respect reduced-motion, keep motion subtle. The neo-skeuomorphic tactile direction is a v2 design pass, alongside the canvas.
- **Accessibility:** every control has a label, full keyboard navigation, VoiceOver/Narrator tested on the main flows, minimum contrast from system colors.
- **Mockups:** before each UI phase, the agent produces a short screen list and low-fidelity mockups for owner approval (tasks 3.0 and 5.0).

---

## 7. Quality and guardrails

| Guardrail | How |
|---|---|
| Formatting and lint | Rust: `cargo fmt` + `clippy -D warnings`. Editor: Prettier + ESLint (strict TypeScript). Swift: `swift-format`. C#: `dotnet format` + analyzers as errors. |
| Tests | Rust: unit tests and property tests (round-trip, conflicts) on the fixture vaults. Editor: Vitest plus Playwright tests in a real browser. Apps: XCTest/XCUITest on Mac, MSTest + UI smoke test on Windows. |
| CI | GitHub Actions for every PR: core and editor on Linux, Mac app on macOS, Windows app on Windows. A PR can only be merged when CI passes. |
| Data-safety suite | Required tests: never lose or rewrite unknown content; conflict instead of overwrite; behavior when power is lost mid-save; mass-delete protection; behavior with cloud placeholder files. |
| Performance budgets | With a 10,000-note fixture vault: cold index under 10 s; search under 100 ms; opening a note under 100 ms; no dropped frames while typing. Budgets are defined in `docs/specs/PERFORMANCE.md` and checked by benchmark tests. |
| Dependencies | Only those listed in §8. Adding any other dependency requires the owner's approval and a recorded license check. |
| Licenses | `docs/LICENSES.md` inventory. `cargo-deny` and npm license checks run in CI. |
| Security | No network access, a strict editor CSP, vault-bounded file serving, and no secrets in the repository. |
| Review | Every PR: CI plus a self-review against the task's acceptance criteria. Data-safety PRs also get an independent review session (§9). |
| Versioning | SemVer `0.x` before 1.0. The MVP release is `0.1.0`. File-format changes need a migration plan. |

## 8. Approved libraries (verify current version and license in task 0.2; pin exact versions)

- **Core (Rust):**

  | Purpose | Crate |
  |---|---|
  | Bindings | `uniffi` |
  | Markdown parsing (tags, links, titles) | `pulldown-cmark` |
  | YAML reading | `saphyr` or `yaml-rust2`, whichever is better maintained at check time |
  | Index and search | `rusqlite` (bundled, FTS5) |
  | File watching | `notify` |
  | IDs | `uuid` (v7) |
  | Hashing | `blake3` |
  | Errors | `thiserror` |
  | Logging (content-free) | `tracing` |
  | Atomic writes | `tempfile` |
  | Trash | `trash` |
  | Tests | `proptest`, `insta` |

  If a pick is found unmaintained, the agent proposes the replacement to the owner before switching.
- **Editor:** `codemirror` 6 packages (`@codemirror/*`, `@lezer/markdown`), TypeScript, Vite, Vitest, Playwright, ESLint, Prettier. Live preview is our own CodeMirror decorations plugin; a third-party live-preview package may be adopted only after owner approval in task 2.0.
- **macOS:** SwiftUI, WebKit (WKWebView), XcodeGen, swift-format. No third-party UI libraries.
- **Windows:** WinUI 3 / Windows App SDK, current .NET LTS, WebView2, CommunityToolkit.Mvvm, `uniffi-bindgen-cs`.

## 9. AI development workflow

- **Default model: Sonnet.** It's set in `.claude/settings.json`. One task per session: start fresh (`/clear`) between tasks so context stays small.
- **Use Opus only for:**
  1. Changing an architecture decision.
  2. A data-loss or conflict bug that Sonnet failed to fix twice.
  3. The independent reviews of data-safety PRs (tasks marked 🔒) and the pre-release review (task 6.3).
  4. Planning the next version (v2).

  Everything else uses Sonnet. Haiku may do pure documentation formatting.
- **Where each task runs:**
  - ☁️ **Cloud session:** Linux. Core and editor work, which is fully testable there.
  - 🍎 **Claude Code on the owner's Mac:** the desktop app with a local folder. Mac UI work, so it can build and run the app.
  - 🪟 **Claude Code on the owner's Windows PC:** Windows UI work.
  - Cloud sessions may write Mac or Windows code, but it then must be built by CI before the task counts as done.
- **How a task is run:** the owner opens a session and says *"Do task X.Y from docs/DEVELOPMENT_PLAN.md."* The agent:
  1. Reads `AGENTS.md`, `HANDOFF.md` and the task.
  2. Works on a branch.
  3. Writes tests first where practical.
  4. Runs all checks.
  5. Opens a PR that fills in the template.
  6. Updates `HANDOFF.md`.
  7. Stops.
- **Stop and ask the owner** whenever a task would need:
  - a new dependency,
  - a new system or abstraction not in this plan,
  - a format or contract change,
  - a hard-coded value that should be configurable,
  - or a design choice the plan doesn't cover.

  Present options with pros and cons in plain language.
- **Research before building:** check the official docs for the exact pinned version before using an API. Don't rely on memory.

## 10. Step-by-step task list

Each task fits in one Sonnet session. "Done" means the acceptance criteria are met, CI is green, and the PR is merged.
Legend: ☁️ cloud · 🍎 owner's Mac · 🪟 owner's Windows PC · 🔒 data-safety task (independent review required) · 👤 needs owner input.

### Phase 0 — Foundation (no product code)

| # | Task | Where | Acceptance |
|---|---|---|---|
| 0.1 | Agent infrastructure: `AGENTS.md`, `CLAUDE.md`, `.claude/settings.json`, `HANDOFF.md`, PR template, task template | ☁️ | Done in planning (2026-11-28) |
| 0.2 | Library check: confirm latest stable versions, maintenance and licenses of §8. Write `docs/LICENSES.md`. Flag any problem to the owner. | ☁️ | Inventory complete; no "unresolved" items |
| 0.3 | Write specs from §§3–5: `docs/specs/FILE_FORMAT.md`, `CORE_API.md`, `EDITOR_BRIDGE.md`, `PERFORMANCE.md` | ☁️ | Specs match this plan; owner skim-approves |
| 0.4 | Fixture vaults: edge-case Markdown (unknown syntax, odd frontmatter, CRLF, BOM, emoji, huge note), an Obsidian-style vault, cloud conflict-copy names, and a 10k-note generated vault (script, not committed files) | ☁️ | Fixtures + README describing each |
| 0.5 | Repository skeleton: folders, empty Rust workspace, editor package, XcodeGen stub, WinUI stub, CI workflows (lint + test + build per component), `cargo-deny` | ☁️ | CI green on all four jobs |

### Phase 1 — Rust core (☁️ fully)

| # | Task | Acceptance |
|---|---|---|
| 1.1 | Domain types and error types; finalize `CORE_API.md` | Compiles; API doc updated |
| 1.2 | 🔒 Vault scan + safe read (UTF-8/BOM/line endings) + atomic write | Round-trip on all fixtures is byte-identical |
| 1.3 | 🔒 Frontmatter: read; minimal-edit owned keys; preserve everything else | Property tests: unknown keys/comments/order preserved |
| 1.4 | IDs and `created`/`created_on` assignment rules (§2.2-6) | Opening never modifies a file; first edit assigns an ID |
| 1.5 | Tag and link extraction (pulldown-cmark); ignore code | Tests over fixtures |
| 1.6 | SQLite index + FTS5 search; incremental rebuild by size/mtime/hash | 10k vault meets the §7 budgets |
| 1.7 | 🔒 Save with `base_hash` conflict detection + conflict copies; detect cloud conflict-copy files | Conflict tests pass; never overwrites |
| 1.8 | 🔒 Version history store + restore + retention | Tests incl. retention limits |
| 1.9 | Create/rename/move/archive/trash; name validation per OS | Cross-platform name tests |
| 1.10 | Attachments: add, unique naming, vault-bounded path resolution | Path-escape tests (`../`) rejected |
| 1.11 | File watcher → events; debounced; handles bulk changes | Tests with simulated external edits |
| 1.12 | Workspace metadata (pins, device labels) with merge rule | Merge tests |
| 1.13 | UniFFI layer + generated Swift and Kotlin bindings build in CI; C# generation smoke test | Bindings generated in CI |

### Phase 2 — Editor (☁️ fully, tested with Playwright)

| # | Task | Acceptance |
|---|---|---|
| 2.0 | 👤 Research spike: own live-preview plugin vs. existing community packages; recommend | Owner approves choice |
| 2.1 | Package setup, bridge types and a mock native host page for testing | Bridge contract tests |
| 2.2 | Live preview: headings, emphasis, strikethrough, inline code, links, quotes, rules | Playwright visual + behavior tests |
| 2.3 | Lists, task checkboxes (click toggles text `[ ]`↔`[x]`), code blocks with highlighting | Tests; text changes are minimal |
| 2.4 | Inline images via `cp-asset://`; paste/drag sends `pasteImage` | Tests with mock host |
| 2.5 | Floating formatting toolbar + keyboard shortcuts; accessible (ARIA, keyboard reachable) | a11y checks pass (axe) |
| 2.6 | Tag autocomplete; find-in-note; spellcheck on; theme from native message | Tests |
| 2.7 | 🔒 Round-trip guarantee: loading and editing never change untouched text; large-note performance | Property tests over fixtures; typing budget met |

### Phase 3 — macOS app (🍎, Sonnet in Claude Code on the owner's Mac)

| # | Task | Acceptance |
|---|---|---|
| 3.0 | 👤 Screen list + low-fidelity mockups (sidebar, list, editor, palette, settings, conflict banner, onboarding) | Owner approves |
| 3.1 | XcodeGen app shell, link Rust core (XCFramework build script), bundle editor | App launches; CI builds it |
| 3.2 | Onboarding: choose/create vault (security-scoped bookmark so access persists) | Reopens vault after restart |
| 3.3 | Sidebar (folders, tags, pinned) + list view (sort, group, filter) | UI tests |
| 3.4 | Editor host: WKWebView, bridge, `cp-asset://` handler, save flow, theme sync | Edit → file on disk updated |
| 3.5 | Conflict banner + compare/keep both; version history window | Manual + UI tests |
| 3.6 | Note actions (new, rename, move, archive, trash, pin) with menus and shortcuts | UI tests |
| 3.7 | Command palette + global search | Keyboard-only flow works |
| 3.8 | Settings window; device name; multiple windows | Manual checklist |
| 3.9 | Accessibility pass (VoiceOver, keyboard), dark mode, reduced motion | Checklist complete |

### Phase 4 — Mac MVP hardening

| # | Task | Where | Acceptance |
|---|---|---|---|
| 4.1 | Owner test round with `docs/testing/MVP_MAC_CHECKLIST.md`; log issues | 👤🍎 | Issues filed |
| 4.2 | Fix issues found (one session per issue group) | 🍎/☁️ | Checklist passes |
| 4.3 | Cloud-folder test: vault in iCloud Drive edited from two places | 👤🍎 | No lost text; conflicts shown |

### Phase 5 — Windows port

| # | Task | Where | Acceptance |
|---|---|---|---|
| 5.0 | Windows-specific screen adjustments (Fluent conventions) for owner approval | 🪟👤 | Approved |
| 5.1 | C# bindings via `uniffi-bindgen-cs` (fallback per §2.2-1) + WinUI shell + bundled editor | 🪟 | App launches; CI builds |
| 5.2 | Vault picker, sidebar, list, editor host (WebView2 virtual host), save/conflict flow | 🪟 | Same tests as Mac |
| 5.3 | Actions, palette, search, settings, accessibility (Narrator), dark mode | 🪟 | Checklist complete |
| 5.4 | Cross-platform test: same vault in OneDrive/iCloud edited on Mac and Windows | 👤 | No lost text; conflicts shown |

### Phase 6 — MVP release 0.1.0

| # | Task | Where | Acceptance |
|---|---|---|---|
| 6.1 | User docs: getting started, file format, where data lives, privacy | ☁️ | Docs written |
| 6.2 | Release builds for personal/tester use. Mac: locally signed; notarization needs an Apple Developer account (👤 decision). Windows: unsigned or self-signed MSIX. | 🍎🪟 | Installable builds |
| 6.3 | 🔒 Independent pre-release review (Opus): data safety, security, licenses | ☁️ | No critical findings open |
| 6.4 | Tag `v0.1.0`; write the v2 plan section (Opus planning session) | ☁️ | v2 plan approved |

## 11. Open items (not blocking coding; decide when the plan says so)

| Item | Needed by | Notes |
|---|---|---|
| Repository public or private | Task 0.5 | Private repos have limited free CI minutes, and macOS minutes count extra. If minutes run short, the Mac builds run on your own Mac instead of in CI. |
| Apple Developer account (US$99/yr) | Task 6.2 | Needed only to share Mac builds with other people without security warnings. |
| Live-preview implementation choice | Task 2.0 | Research spike decides it. |
| Business model, pricing, code license | Before any public release | Unchanged from the pre-plan; does not affect MVP code. |
| PSC integration contract | v2 planning | The MVP doesn't depend on PSC; the core keeps the sync logic isolated so it can plug in later. |
