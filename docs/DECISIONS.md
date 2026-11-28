# Commonplace — Decision Log

Each entry: date, decision, why, alternatives, consequences, status. Changing an accepted decision needs a new entry approved by the owner.

## D-001 — Desktop-first, macOS first, Windows second (2026-11-28, Accepted)
- **Decision:** Build the MVP for macOS first, then port it to Windows within the MVP. The owner owns both platforms.
- **Why:** SwiftUI is mature and AI writes it reliably, and macOS has strong native tooling. Windows follows quickly because the core and editor are shared.
- **Alternatives:** Windows first. Its text tooling is weaker and AI is less reliable with WinUI 3.

## D-002 — Shared Rust core with UniFFI bindings (2026-11-28, Accepted)
- **Decision:** All non-UI logic (files, index, search, conflicts, history) lives in one Rust library used by every app.
- **Why:**
  - One implementation of data-safety logic across Mac, Windows, Android and iOS.
  - It's fully testable on the Linux cloud sessions where coding happens.
  - The same approach is used in production by Firefox on Android and iOS.
- **Alternatives:** Separate logic per platform. That triples the work and multiplies data-loss risk.
- **Consequences:** C# uses the community `uniffi-bindgen-cs`. Fallback: a hand-written C API (see plan §2.2).

## D-003 — CodeMirror 6 editor embedded in a WebView (editing area only) (2026-11-28, Accepted)
- **Decision:** The note editor is CodeMirror 6, embedded only in the editing area. The rest of the app stays native.
- **Why:**
  - Proven live-preview Markdown editing; it's the same technology as Obsidian's editor.
  - One editor for all platforms.
  - It edits the raw Markdown text, so files are never silently rewritten.
- **Alternatives:** Fully native editors per platform. That's months of high-risk work per platform.
- **Consequences:** A narrow, documented exception to the "no web views" rule (rules.md §7). Requirements: no network, a strict CSP, vault-bounded asset serving, and a theme that matches the native app.

## D-004 — MVP scope: notes, images, tags, search, list; canvas and Android in v2 (2026-11-28, Accepted)
- **Decision:** See DEVELOPMENT_PLAN.md §1.
- **Why:** The owner wants a usable product sooner. Image attachments are included and stored as plain files, so they work with any synced folder now and with real sync later.

## D-005 — Folder-based cross-device use in MVP; real sync (PSC) in v2 (2026-11-28, Accepted)
- **Decision:** In the MVP, the vault is a normal folder. It may sit inside iCloud Drive, OneDrive or Dropbox. The core detects external changes and conflict copies and never overwrites silently.
- **Why:** This gives Mac↔Windows use immediately without building a sync engine before PSC has a contract.

## D-006 — Filename is the title; UUIDv7 IDs in frontmatter, assigned lazily (2026-11-28, Accepted)
- **Why:** This is the Obsidian convention and fully portable. Assigning IDs lazily means opening an existing vault never modifies its files.

## D-007 — Search index outside the vault (2026-11-28, Accepted)
- **Why:** Keeping the index out of the vault means cloud folders never sync or conflict on it. The index is fully rebuildable from the notes.

## D-008 — AI-friendly text project files (XcodeGen, SDK-style csproj) (2026-11-28, Accepted)
- **Why:** AI agents must never hand-edit Xcode `.pbxproj` files. Text configs can be reviewed in diffs.

## D-009 — Development model policy: Sonnet default, Opus by exception (2026-11-28, Accepted)
- **Decision:** See DEVELOPMENT_PLAN.md §9 and AI_USAGE_POLICY.md §8.1.
- **Why:** The owner is on a limited plan, so this maximizes progress per session.
