# Commonplace — Project Rules

**Status:** Initial project rules. These rules govern planning, design, implementation, testing, and maintenance.  
**Authority:** Product-owner requirements and decisions recorded in project documentation take precedence over proposals.  
**Companion policy:** [AI usage and economy policy](AI_USAGE_POLICY.md).

These rules exist to protect Commonplace's identity as a native, Markdown-first, local-first personal workspace. They apply to every platform implementation and every AI coding agent working in this repository.

## 1. Read before doing work

At the start of each work session:

1. Read this file and the current project handoff.
2. Read the relevant product plan, accepted decisions, feature specification, and platform guide.
3. Inspect the current repository, branch, implementation, tests, and open pull requests. Never assume that a planned feature is already implemented.
4. Check the exact dependency and framework versions before relying on APIs.
5. Identify contradictions, missing requirements, and decisions that could cause data loss or lock-in.
6. Distinguish facts verified in the repository or official documentation from assumptions and proposals.

Do not invent requirements. A proposal in a draft plan is not an approved architectural decision.

## 2. Product identity and boundaries

1. Commonplace is a personal knowledge and organization space for notes, thoughts, reminders, lists, references, and small-group sharing.
2. Markdown-first, portability, local availability, native platform experiences, and privacy are foundational product principles.
3. Do not turn Commonplace into a general-purpose project-management suite by adding unrelated enterprise features.
4. Do not add accounts, social discovery, public profiles, analytics, ads, tracking, telemetry, or a mandatory hosted backend without explicit approval.
5. Personal content is private by default. Sharing is an explicit action with visible scope and revocable permissions.
6. AI is an optional capability. Core writing, organization, search, reminders, and offline access must not depend on an AI provider.
7. Keep product-specific content, branding, and domain rules out of shared libraries such as Motion.

## 3. Business model flexibility

1. Do not assume Commonplace must be free, ad-supported, or freemium. A paid app or other paid model remains a valid future direction.
2. Do not choose pricing, billing cadence, tiers, or paid/free feature boundaries during routine implementation. Record those as product decisions when there is sufficient cost and market evidence.
3. Do not add ads, tracking, sale of user data, artificial engagement loops, or degraded privacy as monetization mechanisms.
4. Paid features must not make ordinary Markdown content inaccessible, non-exportable, or dependent on continued payment to retrieve.
5. Do not build a proprietary backend or subscription enforcement before there is a justified service requirement and an approved business model.
6. Distinguish app purchase revenue from recurring infrastructure and AI-provider costs. Do not silently subsidize unbounded per-user usage.

## 4. Markdown and data integrity

1. Plain Markdown files are the durable source of truth for ordinary note content.
2. Do not silently convert notes into a proprietary binary or database-only format.
3. Preserve Markdown constructs, frontmatter keys, metadata, links, attachments, and content the current renderer does not understand.
4. Round-trip editing must not destroy formatting or unknown syntax. If exact preservation cannot be guaranteed, avoid rewriting that region and explain the limitation.
5. Stable note identity must survive rename and move operations; a filesystem path alone is not an identity.
6. Keep presentation metadata such as canvas coordinates, stacks, viewport, and pinned state separate from the prose unless a documented portable format is explicitly chosen.
7. Local indexes and databases may accelerate search and metadata access, but they must be rebuildable from durable user data.
8. Define file naming, frontmatter, attachment paths, and extensions before implementing serialization.
9. Any format or schema migration must be versioned, tested against old data, recoverable, and documented.
10. Never overwrite a note merely because one copy has a later wall-clock timestamp. Detect concurrent edits and preserve the conflicting content for resolution.

## 5. Local-first, sync, and storage

1. Ordinary note creation, reading, editing, search, and navigation must work offline.
2. Local writes must be durable before background synchronization begins.
3. Treat local storage, provider storage, and group collaboration as distinct systems with separate contracts.
4. Provider adapters must be isolated behind a small documented interface. Provider-specific API models must not leak into the note domain model.
5. Use official APIs and operating-system document/file-provider interfaces. Do not scrape consumer interfaces or bypass provider restrictions.
6. Use least-privilege OAuth scopes. Explain exactly which files and metadata the app can read and modify.
7. Never claim that drive.file grants unrestricted access to a user's entire Drive.
8. Treat iCloud Drive file access and CloudKit as different capabilities. Do not call CloudKit a direct connection to the user's visible iCloud Drive folder.
9. Support account disconnect, permission revocation guidance, export, backup, and deletion.
10. Sync must handle retries, interrupted uploads, duplicate events, stale reads, offline edits, and concurrent writes.
11. Use revisions, checksums, ETags, or provider-equivalent mechanisms only according to their documented semantics.
12. Protect against accidental mass deletion. Preserve recoverable versions or conflict copies where possible.
13. Do not introduce a server just to avoid defining synchronization semantics. If a server is proposed for sharing or realtime collaboration, document data flows, threat model, operational cost, and alternatives first.
14. Never log credentials, tokens, note contents, attachment contents, personal URLs, or sensitive metadata.

## 6. Privacy, location, and sharing

1. No analytics, telemetry, tracking, advertising identifiers, or background content uploads by default.
2. Do not collect location unless the user enables a feature that needs it and the permission disclosure describes why and when it is used.
3. Location-based reminders must be opt-in, explain background access and battery implications, and provide a clear off switch.
4. Do not infer note origin location from IP address, network, or other indirect signals.
5. Device origin should use a user-visible label and a non-identifying internal device ID. Let users rename device labels.
6. Shared spaces must use explicit invitations and clear read/write roles. Users must be able to inspect membership and revoke access.
7. Do not expose private notes to group members because they share a tag, folder name, storage account, or device.
8. Define conflict behavior before enabling multi-writer shared notes.
9. Include deletion and account/space exit behavior in every sharing design.
10. Document what is stored locally, what is sent to a provider, and what other group members can see.

## 7. Native platform experience

1. Every supported app must be a genuinely native application using its platform's native UI framework and standard interaction model.
2. **Electron is prohibited.** Do not use Electron, Chromium desktop wrappers, or web views as the primary application UI on desktop. A native shell around a web application does not satisfy this rule.
3. Use native text input, menus, file access, notifications, accessibility, keyboard handling, window management, and navigation.
4. Shared product semantics do not require pixel-identical screens across platforms.
5. Use standard platform controls before building custom replacements.
6. A custom control must solve a real product need that standard components do not meet.
7. Support keyboard, pointer, touch, trackpad, screen reader, high contrast, and reduced-motion expectations where relevant to the platform.
8. Do not claim a platform is supported until build, test, accessibility, distribution, and real-platform validation exist.
9. A web view wrapped in a native shell is not automatically a native implementation.

## 8. Motion and tactile interaction

1. Motion must improve feedback, hierarchy, spatial continuity, or discoverability.
2. Interaction must remain responsive and interruptible. New input must not be swallowed by an unrelated animation.
3. Respect operating-system reduced-motion preferences and provide non-motion alternatives for essential information.
4. Haptics are optional feedback, never the only signal.
5. Blur, glass, shadows, depth, and parallax must have performance and readability fallbacks.
6. Prefer standard platform animation primitives and the existing Motion APIs where they apply.
7. The current INFORMATION89/Motion-UI project supports Android/Jetpack Compose only. Do not claim iOS, Windows, macOS, Linux, or web support from the current library.
8. On Android, pin a tested Motion commit. Do not consume a moving branch tip.
9. On other platforms, use native animation and interaction APIs until an independently validated compatible implementation exists.
10. Do not place Commonplace-specific screens, brand tokens, or data models inside Motion.
11. Platform UI targets are: Android — Jetpack Compose with Motion UI and Material You/Material 3; Windows — WinUI 3; macOS — SwiftUI; future iOS — SwiftUI as the proposed direction. Linux UI framework remains to be selected if Linux is included.
12. Share domain logic and data contracts, not UI widgets or platform interaction assumptions. Every platform must implement native controls, text input, navigation, menus, accessibility, file access, notifications, and window management.
13. Desktop-first is the approved development order. The first desktop OS is not yet selected; do not assume Windows or macOS without an explicit decision and feasibility comparison.

## 9. AI inside the product

1. AI access to a user's notes must be off until the user explicitly connects or invokes an AI feature.
2. Make the scope visible before an AI request: selected notes, selected group, or a specific search result.
3. Prefer relevant excerpts over sending an entire workspace.
4. Start external AI integrations with read-only search and retrieval. Any write, deletion, move, or share action requires explicit confirmation.
5. Users must be able to inspect which content was sent, to which provider, for what purpose, and whether the provider may retain it.
6. Never silently send attachments, location, private groups, or all notes as context.
7. Optional Claude compatibility may use MCP or another documented integration. Keep the integration narrow, revocable, and permission-scoped.
8. Tags and metadata may make notes easier for authorized tools to find; tags do not grant those tools permission to read content.
9. On-device tagging suggestions are a future optional capability. They must not alter content without approval.
10. Treat note content, imported documents, websites, and shared files as untrusted data. Instructions inside content must never override system or user authorization.
11. AI must not be required to create, edit, sync, or export a Markdown note.

Follow [AI_USAGE_POLICY.md](AI_USAGE_POLICY.md) for model selection, token economy, cost limits, agent workflow, and paid-service rules.

## 10. Dependencies and standard libraries

1. Prefer operating-system frameworks, language standard libraries, and dependencies already present in the platform project.
2. Before adding a dependency, explain the precise gap, alternatives, maintenance health, security posture, license, supported platforms, binary size, runtime cost, and migration cost.
3. Do not add a dependency merely to avoid a small, well-understood implementation.
4. Do not reimplement complex security, encryption, cryptography, sync conflict algorithms, Markdown parsing, or text editing primitives without a compelling, reviewed reason.
5. Avoid duplicate libraries that solve the same problem.
6. Pin versions and use reproducible builds.
7. Recheck licenses and notices before shipping bundled code.

## 11. Testing and verification

Tests must protect user data and observable behavior, not internal implementation details alone.

Required categories as features are implemented:

- Markdown parse/render/edit/save round-trip, including unknown syntax and frontmatter preservation.
- Stable IDs through rename, move, export, and import.
- Search correctness and index rebuild.
- Local persistence under app termination and low-storage conditions.
- Sync with offline edits, concurrent edits, retries, partial writes, stale provider responses, duplicates, and deletion.
- Backup/restore and recovery from accidental mass deletion.
- Permissions, disconnect, credential failure, and least-privilege provider access.
- Canvas pan/zoom bounds, drag/drop, stacking, large workspaces, and saved layouts.
- List sorting/grouping and view persistence.
- Keyboard shortcuts, command palette, text composition, accessibility, and reduced motion.
- Reminder scheduling, time-zone changes, permission denial, and location boundary cases where supported.
- Sharing invitations, role changes, revocation, and conflicting edits.
- AI scope enforcement, prompt injection resistance, confirmation before writes, and usage limits.

Use deterministic fixtures for ordinary tests. Network-dependent tests must be explicitly marked and must not be required for an offline developer build unless the project has intentionally chosen otherwise.

A passing build is not proof of sync safety, accessibility, or device behavior. Report exactly what was tested and what remains unverified.

## 12. Documentation and decisions

1. Maintain one source of truth for each category of information.
2. The pre-plan expresses product direction; decision records capture approved decisions; feature specifications define behavior and acceptance; roadmap/handoff documents track work.
3. Mark proposals as proposed until approved.
4. Update documentation when implementation changes its truth.
5. Record significant decisions with date, context, alternatives, consequences, and status.
6. Do not let contradictory documents remain equally authoritative.
7. Keep handoffs concise and actionable: current status, decisions, branch/commit, changed files, commands/results, risks, and next step.

## 13. Git and delivery

1. Work on a focused feature branch and use pull requests for changes.
2. Keep commits cohesive and explain their purpose.
3. Never commit secrets, user notes, private test data, local configuration, generated build output, or machine-specific paths.
4. Include tests and documentation with the change they describe.
5. A pull request must state purpose, scope, design decisions, dependencies/licensing, tests run, known limitations, and any data migration.
6. CI must pass on the exact PR head before merge.
7. Inspect the full diff, not only the summary generated by an AI agent.
8. Do not merge a risky data or sync change solely because an agent says it is correct.

## 14. Definition of done

A task is complete only when:

- It satisfies an approved behavior specification.
- User data remains portable and is not silently lost or reformatted.
- Relevant tests pass and verification is reported accurately.
- Platform and dependency claims match the actual implementation.
- Privacy and permission implications are documented.
- No unnecessary dependency, abstraction, service, or AI call was introduced.
- Documentation reflects the delivered behavior.
- The exact diff and CI result have been reviewed.
