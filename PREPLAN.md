# Commonplace — Pre-plan

**Status:** Initial pre-plan; proposals are not implementation commitments.  
**Date:** 2026-11-27  
**Repository:** https://github.com/INFORMATION89/Commonplace

This document turns the initial product brief into a proposed direction, feature scope, and development sequence. The separate plan attachment mentioned in the request was not present among the files attached to this conversation or located in the relevant Library search. This pre-plan therefore uses the detailed notes included in the request. The missing attachment should be reviewed and reconciled when supplied; it has not been treated as reviewed.

## 1. Product vision

Commonplace is a personal place to collect, organize, revisit, and selectively share things worth keeping: notes, thoughts, references, tasks, reminders, lists, and small collections.

It should combine:

- The portability and longevity of plain Markdown files.
- An editor that renders formatting by default, inspired by Obsidian Live Preview.
- A spatial desktop canvas where notes can be arranged in groups and stacks.
- A conventional list view that remains equally useful.
- Simple, reliable synchronization across a user's own devices.
- Optional connections to storage providers the user already controls.
- Native interaction and visual conventions on each platform.
- Tactile, restrained neo-skeuomorphic motion and surfaces.
- Optional, permission-based access for AI tools and small groups.

Commonplace should feel like a personal notebook and collection space for everyday life: a place to capture thoughts, ideas, memories, references, and things the user wants to keep or share. It is not primarily a productivity system, project-management tool, or general-purpose database builder.

## 2. Product principles

1. **Markdown is the durable content format.** Users must be able to open, edit, back up, and move note files without Commonplace.
2. **Local-first.** Notes remain available and editable offline. Network availability is not required for ordinary writing.
3. **Native by platform.** Each app uses its platform's standard UI framework, input model, accessibility services, menus, notifications, file access, and conventions.
4. **Behavioral consistency, not pixel uniformity.** Platforms share product concepts and data contracts while retaining native controls and layout decisions.
5. **The canvas is an organizing surface, not a replacement for notes.** Notes remain portable documents; canvas positions and group layouts are separate workspace metadata.
6. **Simple sharing, deliberate permissions.** Personal content stays private unless the user explicitly shares a note or group space.
7. **Privacy by default.** No telemetry, AI transmission, location capture, or background data sharing without a clear and separate user choice.
8. **Tactility serves usability.** Depth, spring motion, haptics, and gestures communicate state and affordance rather than becoming decoration.
9. **Small dependency surface.** Prefer platform frameworks and standard libraries. Add third-party libraries only for a demonstrated gap.
10. **AI is optional.** The product remains fully useful without an AI provider, AI subscription, or API key.
11. **Human input and creativity come first.** Commonplace is an everyday personal tool for writing, collecting ideas, keeping memories, organizing personal information, and sharing selectively. Protect the user's own thinking, creativity, and expression. Do not make productivity optimization, work management, or constant task tracking the product's primary purpose.
12. **AI assists rather than authors the user's experience.** AI may make useful tasks easier, but it must not replace human input, make personal decisions on the user's behalf, or silently take ownership of human-authored content.
13. **Shared coordination, independent ownership.** Commonplace will integrate with Personal Systems Coordinator (PSC) for cross-device coordination and interoperability shared across the application family. Commonplace retains ownership of notes, workspace semantics, and app-specific rules; PSC must not become a universal database or silently expose data between apps.

## 3. Product position and differentiation

Commonplace should learn from existing products without reproducing their entire feature sets.

| Product | Strengths to study | What Commonplace should learn |
|---|---|---|
| Craft | Rich documents, pages/cards, folders, tags, Collections, tasks, calendar, daily notes, whiteboards, offline sync, shared spaces, publishing, APIs and MCP | A coherent set of writing, organization, and visual surfaces; fast navigation; optional external-tool access |
| Obsidian | Plain Markdown files in a local vault, offline access, user-controlled folder location, extensible links and metadata | File ownership, portability, transparent storage, and an editor that displays formatting while writing |
| Konode | Direct-to-provider sync, narrow permissions, user-owned storage, optional end-to-end encryption, no intermediary data server | Provider adapters, explicit storage choice, least-privilege access, and transparent sync status |

**Commonplace's intended distinction:** a native, Markdown-first personal workspace with a spatial canvas and uncomplicated views, while keeping the underlying notes as ordinary files.

Do not compete with Craft by copying every database, publishing, team, or AI feature. Prioritize the smaller set of capabilities that make Commonplace distinct and excellent.

See [Research and feature comparison](docs/RESEARCH.md) for a more detailed review and links to primary sources.

## 4. Content and data model proposal

The format contract must be specified before implementation.

### 4.1 Notes

- Each ordinary note has a Markdown source file.
- Markdown remains readable and editable outside the app.
- Use a documented, conservative Markdown dialect. Extensions must be documented and degrade gracefully in other editors.
- Support optional YAML frontmatter for stable metadata such as title, identifier, tags, aliases, and timestamps.
- Preserve unknown frontmatter keys and unknown Markdown constructs when editing. Never silently strip information the app does not understand.
- Use relative paths for local attachments where practical; document any asset-folder convention.
- Stable note IDs should survive renames and moves. Do not use a path as the only identity.

### 4.2 Workspace metadata

Keep presentation metadata separate from the note's prose:

- Canvas coordinates, zoom, viewport, groups, stack order, and pin state.
- Saved list views, sort order, filters, and workspace preferences.
- Device-origin metadata and app-specific properties that should not be forced into ordinary note content.

The exact representation (sidecar files, a local index, or both) remains an architecture decision. It must be exportable and recoverable. A local database may accelerate search and indexing, but it must not become the only copy of note content.

### 4.3 Tags and AI-readable metadata

- Support visible tags and machine-readable tag metadata.
- Use stable, documented tag names and preserve hierarchy if hierarchical tags are adopted.
- Tags must be available to local search, exports, and explicitly authorized external tools.
- Do not equate “AI-readable” with “automatically sent to an AI provider.”
- Future on-device suggestions may recommend tags, but must be optional and must not change notes without user approval.

### 4.4 Origin and device information

- Show the device/platform from which a note was created or last edited.
- Keep a human-readable device label separate from internal sync identifiers.
- Location can be shown only when the user explicitly enables location capture for that purpose. Location must not be inferred from IP addresses.
- Users must be able to inspect and remove origin metadata where appropriate.

### 4.5 Tasks, reminders, and shopping lists

- Simple tasks should remain understandable in Markdown where possible.
- Date/time reminders are application behavior layered on top of note/task data.
- Location-based reminders require separate permission, clear explanation, battery-aware implementation, and platform-specific validation. They are not a basic editor feature.
- Shopping lists can be a specialized note type with structured items, checked state, quantity, optional store/location, cost, and a personal index of frequently bought items. Its data must remain exportable and must not require a proprietary backend.

## 5. Primary experiences

### 5.1 Writing and editing

- Markdown-first document model.
- Live rendering by default: headings, emphasis, lists, links, checkboxes, quotes, code, and supported extensions render in place.
- Selecting or editing a formatted region should expose the underlying Markdown naturally, following the Live Preview model.
- Keyboard shortcuts, selection, undo/redo, text composition, and accessibility are first-class.
- The editor must not lose syntax it cannot fully render.
- Support attachments without making a proprietary document bundle the only way to retain a note.

### 5.2 Desktop canvas

The primary desktop home is proposed as a navigable, zoomable canvas:

- Pan in all directions and zoom in/out.
- Place notes as cards or surfaces.
- Organize notes into named groups.
- Allow stacks for notes grouped by tags or user-defined criteria.
- Drag, resize, move, reorder, collapse, and open notes with predictable gestures.
- Save layouts and viewport state.
- Pin important tabs or notes.
- Keep canvas organization separate from note contents.
- Keep a clear route back to search and the list view.

The canvas must be tested with large workspaces. It must not render every note at full fidelity when only a small region is visible.

### 5.3 List view

The list is a complete alternative, not a reduced fallback:

- Show the same notes represented by the workspace.
- Group, sort, and filter by date, tag, device, title, and other supported metadata.
- Allow users to create and save their own view configurations.
- Provide predictable keyboard navigation and accessible list semantics.

### 5.4 Search and command palette

- Fast full-text search across titles, content, tags, and supported metadata.
- Keyboard-accessible command palette for search, navigation, creation, and common actions.
- On mobile, a pull-down gesture from the top may reveal search/command access; it must not conflict with system gestures or scrolling.
- Search must work locally and offline.
- Semantic/AI search is a later optional layer, not a prerequisite for useful search.

### 5.5 Reminders

- Start with reliable date/time reminders using native notification and scheduling systems.
- Show reminder state and permission state clearly.
- Location-based reminders are a later feature. Geofencing differs across platforms and requires explicit opt-in.
- A reminder must remain attached to its note/task and survive app restarts and device sync appropriately.

### 5.6 Sharing with friends and small groups

Separate three concepts:

1. **Device sync:** keep one user's own devices consistent.
2. **Provider storage:** read/write the user's files in Google Drive, iCloud Drive, or another chosen provider.
3. **Shared spaces:** explicitly grant other people access to selected content.

These are not interchangeable. A folder sync adapter alone does not create safe collaboration.

The first sharing milestone should define private/invited groups, read/write roles, invitation revocation, conflict behavior, and an understandable activity history. Real-time co-editing is not a launch assumption; it requires a separate technical justification.

## 6. Storage and synchronization strategy

### 6.1 Recommended foundation

- Keep a local copy of all content.
- Define a provider-adapter boundary rather than scattering Google/iCloud/WebDAV logic through the app.
- Use documented APIs or operating-system file-provider interfaces.
- Make the current sync provider and status visible to the user.
- Preserve a local backup or recoverable version history.
- Detect concurrent modifications and preserve conflicting edits instead of silently overwriting one with another.
- Make sync retryable, observable, and resilient to interrupted uploads.
- Use checksums/revisions and atomic or staged writes where the provider supports them.
- Treat deletion as a first-class event; use tombstones or equivalent safeguards so deleted files do not reappear unexpectedly.
- Never treat a successful upload as proof that all devices received the change.

### 6.2 Provider research and initial order

**Filesystem / user-selected folder**  
Best first interoperability proof. Let users select a folder and store ordinary Markdown files and assets in it. On each OS, use supported file-access/document-provider APIs and explain when a provider keeps files online-only.

**Google Drive**  
Konode demonstrates a least-privilege approach using the Google Drive API's drive.file scope and an app-owned folder of JSON files. For Commonplace, validate how to expose readable Markdown files, how the app discovers only files granted to it, how OAuth refresh works, and how file revisions/conflicts are handled. Do not assume drive.file grants unrestricted access to every existing file in Drive; user-selected files and files created by the app are the relevant boundary.

**iCloud Drive**  
Use Apple-native file/document access to user-visible files where supported. Do not describe CloudKit as a direct iCloud Drive folder connector: CloudKit is an app-container data service, not a general file browser for the user's visible Drive folder. A CloudKit-backed sync service could be a separate Apple-specific backend, but it would be a different storage promise.

**WebDAV / other providers**  
A generic WebDAV adapter may offer broad compatibility, but it needs careful authentication, TLS, server compatibility, ETag/revision, and conflict testing. Add providers only when the adapter contract is stable.

**Do not implement every provider in the first milestone.** Select one initial provider only after the file format and conflict model are proven.

### 6.3 Lessons from Konode

Konode's design is useful reference material, not a sync engine to transplant:

- It writes directly to a provider chosen by the user.
- Google Drive access uses the narrow drive.file scope.
- Its setup flow uses one backend per sync group.
- It writes small per-device JSON files, then discovers and pulls peer files on a schedule because ordinary file-storage backends do not offer the required push path.
- It offers optional end-to-end encryption and keeps credentials on the device.
- Its documented iCloud position distinguishes iCloud Drive from CloudKit.

Commonplace contains frequently edited, human-authored documents and future shared spaces. It therefore needs stronger concurrent-edit handling, recoverable history, and shared-access semantics than a browser-bookmark synchronizer.

### 6.4 Shared backend and cross-app coordination: Personal Systems Coordinator (PSC)

Commonplace is planned to use [Personal Systems Coordinator (PSC)](https://github.com/INFORMATION89/Personal-Systems-Coordinator) as the shared backend/coordination foundation for the application family. PSC is an independent project and integration target, not a Commonplace module, a note-storage format, or an external storage provider.

The current PSC repository describes a local-first, provider-neutral coordination foundation in research and architecture planning. Commonplace adopts PSC as the shared coordination layer across the application family, with a permanent decentralized/provider-based model: no PSC-operated central backend, first-party cloud, hosted relay, managed storage or mandatory account.

**Responsibility boundaries**

- **Commonplace owns** Markdown note content, note identity and semantics, workspace layouts, task/reminder behavior, sharing rules, and the decision about which Commonplace data categories are eligible for synchronization.
- **PSC owns the shared coordination layer** defined by its separately approved contracts: portable data envelopes/manifests, versioned interoperability metadata, provider-adapter boundaries and capability descriptions, generic synchronization mechanics (checkpoints, revisions, idempotent delivery, retries, and sync status), and explicitly authorized cross-app references. These are target responsibilities to validate against PSC's research and decisions; this document does not claim that APIs, SDKs, or implementations already exist.
- **The user-selected provider or folder** remains the storage/transport destination under PSC's current direction. It is not interchangeable with PSC itself.
- **Each application retains its domain model.** PSC must not import Commonplace, Solstice, Meridian, or another app's business logic. Commonplace must not access another application's private database or call provider-specific APIs from its domain layer.

**Required Commonplace integration shape**

1. Commonplace maps only explicitly selected, versioned data categories into PSC's portable synchronization contract. Notes and workspace metadata remain owned by Commonplace.
2. A small Commonplace adapter translates between Commonplace's domain model and PSC's versioned, platform-neutral contract. PSC/provider details must not spread through the editor, canvas, search, or note model.
3. PSC coordinates generic exchange and provider operations; Commonplace defines the meaning of its records and the conflict policy appropriate to each data type. Do not apply a universal last-write-wins rule.
4. Every application and data category must have an isolated identity/namespace and explicit authorization scope. Linking PSC for one application must not grant another application access. Cross-app exchange requires a separate user-approved purpose and permission.
5. Provider credentials stay in platform-secure storage and are scoped to the provider/app integration. Do not assume PSC is an identity provider, token vault, or single-sign-on service; its current project documents do not approve those roles.

**Decentralized execution boundary**

PSC is permanently decentralized and provider-based. It will not operate a centrally hosted backend, first-party content relay, managed storage service, or mandatory cloud account. Its coordination logic runs in the applications and/or user-controlled devices as defined by the approved technical design; data moves through providers or folders selected and controlled by the user. Optional services operated by unrelated providers remain external dependencies, not a PSC-owned cloud. Any future proposal to change this is outside the approved direction and requires a new explicit product decision.

**Privacy and availability requirements**

- Core Commonplace writing, reading, editing, local search, and export work without PSC, network access, or a connected provider.
- Synchronization is an explicit, inspectable choice. Users can see the Commonplace data categories involved and the destination, and can pause, disconnect, revoke provider access, and recover local data.
- Telemetry, analytics, contribution of data to improve Meridian, external AI access, and cross-device synchronization remain separate purposes with separate consent.
- PSC/provider failures, expired authorization, and offline devices must delay synchronization safely rather than block local work or discard changes.
- No note, attachment, location, credential, or private-space content is shared with another app merely because both apps use PSC.

Before Commonplace depends on PSC, its repository must publish an accepted integration contract and a tested proof of concept for the required provider/platform path. See the [PSC project](https://github.com/INFORMATION89/Personal-Systems-Coordinator) for its current research, principles, and unresolved decisions.

## 7. Native implementation and platform-specific UI

Native applications are a non-negotiable product constraint. **Do not use Electron**, Chromium-based desktop wrappers, or a web application inside a native shell as a substitute for native apps.

Use the platform's own UI framework and design language:

- **Android:** Jetpack Compose with Motion UI and Material You/Material 3. Motion UI is an Android-specific library; do not treat it as a cross-platform UI toolkit.
- **Windows:** WinUI 3.
- **macOS:** SwiftUI, using AppKit interop only where a genuine platform capability requires it.
- **iOS (when scheduled):** SwiftUI is the proposed native direction; confirm platform-specific requirements during its architecture spike.
- **Linux:** native toolkit/framework remains an open decision; evaluate GTK 4/libadwaita and other desktop-native options rather than Electron. Linux synchronization is conditional: prove that reliable, maintainable sync is feasible; otherwise exclude Linux from the supported sync matrix. A possible local-only Linux app is a separate decision.

Shared product behavior, domain rules, data formats, and synchronization contracts must not force identical UI. Each app implements native controls, navigation, menus, text input, accessibility, notifications, windowing, file access, and system integration for its platform.
- Use native text input, file access, menus, notifications, haptics, accessibility, windowing, and navigation.
- Keep product and data contracts consistent across apps; visual and interaction implementations may differ.
- Prefer standard platform components for ordinary controls. Do not create a custom replacement for every standard button, text field, tab, menu, or notification.

**Motion integration status:** the current INFORMATION89/Motion-UI repository is an Android Jetpack Compose library. Its README documents Android support only; iOS, Windows, macOS, and Linux are not current targets. The initial Motion UI module provides an app-configurable Material 3 theme/token foundation; further component surfaces are staged separately.

Therefore:

- Use Motion on Android where its existing APIs improve springs, gesture handoff, interruption, morphing, or tactile feedback.
- Pin a tested Motion commit/submodule revision; do not depend on an unreviewed moving branch.
- Use native platform motion APIs on Apple and Windows platforms.
- Treat Motion's interaction behavior as a source of design contracts, not as evidence that the library is already cross-platform.
- Do not add Commonplace-specific logic, content, or visual branding to Motion.

## 8. Neo-skeuomorphic interaction direction

The visual direction is tactile but restrained:

- Clear surfaces, subtle depth, material layering, pressed states, and meaningful elevation.
- Spring-driven transitions where they improve continuity.
- Drag and drop that feels physically connected to the pointer/finger.
- Optional haptic confirmation for meaningful actions.
- Clear affordances for stacked notes, cards, tabs, and groups.
- Avoid excessive glass, shadows, parallax, or simulated physical controls.
- Follow system reduced-motion and accessibility settings. Never hide essential information behind motion.

## 9. AI and external-tool compatibility

AI is an optional capability that serves the user's intent. Commonplace should remain useful and complete without an AI provider, and AI must never become the product's identity or the default way of interacting with personal information.

### 9.1. Human-first, creativity-led product

Commonplace is intended for everyday personal use: writing thoughts, collecting ideas, keeping memories, organizing personal information, saving references, and sharing selected things with a small group. Human input, creativity, and personal expression take priority.

AI may reduce friction around supporting tasks such as spelling correction, file organization, summaries, information retrieval, and analysis of existing material. It must not displace the user's own thinking or turn Commonplace into a productivity system whose main purpose is to optimize work.

### 9.2. AI as a normal product capability ("Invisible AI")

Treat AI as an implementation capability behind useful product functions, not as a separate experience that must constantly announce itself.

- Prefer task-oriented actions that describe the outcome, such as "Organize", "Correct spelling", or "Summarize", over making an AI chatbot or "Ask AI" surface the default entry point.
- Arc's Tidy Tabs is a useful product reference: the user invokes a clear organization action and receives the result; the fact that AI powers the feature does not need to dominate the interaction. This is a philosophical reference, not a commitment to copy Arc's interface.
- Do not add AI branding, model controls, chat surfaces, or prompt-writing steps merely to make the product feel AI-powered. Expose the relevant provider and processing information in settings or at an appropriate point when it helps the user understand the data flow.
- The user should initiate an AI-assisted action. Do not continuously process documents, monitor writing, or send context to a model in the background by default.
- Keep the detailed UX, visual treatment, and placement of future AI-powered actions undecided until the relevant product experiences are designed. This pre-plan establishes product principles, not screen specifications.

### 9.3. MCPs and external tools

Treat MCPs and other external-tool integrations as limited assistants that operate under Commonplace's permissions and the user's intent.

- Offer optional Claude compatibility through a narrowly scoped integration; MCP is a candidate protocol, not a required dependency.
- Useful assistant tasks may include spelling and grammar assistance, organizing files, summarizing documents, analyzing information the user selects, and retrieving relevant material.
- MCPs must not replace the user's input, make final personal decisions, or silently rewrite, move, delete, share, or otherwise alter human-authored material.
- Start with read-only access and explicit workspace/note selection. Any write operation requires a user-authorized action and must remain scoped, inspectable, and reversible where practical.
- Treat note content, imported documents, websites, and shared files as untrusted data. Instructions inside that content must never override system or user authorization.

### 9.4. Authorship, processing scope, and provenance

Preserve the distinction between the user's work and material produced or changed by an AI system.

- Any AI-generated text inserted into a note or file must be clearly identifiable as AI-generated. If a person later edits it, preserve that history rather than presenting the entire passage as wholly human-authored.
- Keep generated suggestions or artifacts separate from the original content until the user chooses to incorporate them. Never silently overwrite human-authored text.
- For document-processing operations, retain enough provenance to identify the source document version and the specific fragments actually included, partially included, or excluded from the request. Where an output can be linked to source fragments, validate those references against the recorded input.
- Distinguish what Commonplace can verify (for example, which content it sent) from what it cannot verify (for example, whether a model attended to or understood every fragment). Never imply stronger traceability than the system can substantiate.
- Future summaries and analyses may expose their relationship to the original material, but the presentation and interaction model remain open for later UX design.

### 9.5. Privacy and availability

- Make note tags and metadata readable through documented formats.
- Avoid sending an entire vault by default. Search first, retrieve relevant excerpts, and load full notes only when required and authorized.
- Do not send note content, attachments, or location data to any model without a clear user action and disclosure.
- Future on-device tag suggestions should be optional, local, and separately configurable.
- Core note taking, editing, search, reminders, canvas, export, and sync must work without AI.

## 10. Proposed release sequence

This is a suggested sequence, not a committed calendar. Dates should be set only after platform and synchronization spikes produce realistic estimates.

### Phase 0 — Product and architecture definition

- Approve the Markdown dialect, frontmatter schema, file/asset layout, and workspace-metadata boundary.
- Confirm desktop-first development. Keep the first desktop operating system explicitly undecided until platform feasibility spikes and development constraints are compared.
- Run a focused Linux synchronization feasibility spike. If reliable, maintainable synchronization would require disproportionate effort or compromise other platforms, remove Linux from the supported sync matrix; consider a local-only Linux app separately only if it has sufficient product value.
- Define the native UI architecture and platform boundaries for Windows (WinUI 3), macOS (SwiftUI), Android (Jetpack Compose + Motion UI + Material You), and future iOS (SwiftUI); identify the Linux native UI decision as open if Linux remains in the supported matrix.
- Compare viable shared-core approaches without selecting a UI framework that erases native platform behavior. Prototype the core/domain boundary and native bindings on the candidate desktop platforms before committing to a language/runtime.
- Choose the first desktop OS only after the comparison and establish its native application shell.
- Prototype the editor and canvas independently.
- Review the PSC repository and agree the Commonplace/PSC responsibility boundary, including data ownership, provider adapters, versioned contracts, authorization, and offline fallback.
- Map the Commonplace data categories that may be synchronized through PSC; document what remains local and what requires separate opt-in.
- Define the PSC integration adapter and prove one representative data exchange without coupling Commonplace's domain model to a provider.
- Test one candidate storage provider through the PSC contract with interrupted writes and concurrent edits; do not build a competing provider-specific sync engine inside Commonplace.
- Validate the local execution model and native platform adapters for PSC; coordination must run on user devices and/or user-controlled components, with storage and transport delegated to user-selected providers or folders. A PSC-operated hosted service is out of scope.
- Decide what the first shared-group model means.
- Record provider, privacy, security, and licensing decisions.
- Define the minimum data/provenance contract for optional AI operations (user-authorized scope, source document version, included/excluded fragments, and generated-content authorship) without prematurely selecting their UX.

**Exit:** architecture decisions are written, PSC's permanently decentralized boundary is documented, the desktop-first/native architecture is validated by small prototypes, the first desktop OS is selected with evidence, and the first milestone has measurable acceptance criteria.

### Phase 1 — Local-first MVP / internal alpha

- Native app shell for the first approved desktop platform, implemented with its native framework (WinUI 3 or SwiftUI according to the approved first target).
- Create, edit, rename, move, archive, and delete Markdown notes.
- Live-rendering Markdown editor.
- Local persistence and search.
- Tags and basic metadata.
- Desktop canvas with pan/zoom and groups.
- Standard list view with sorting and filtering.
- Keyboard shortcuts, quick search, command palette, and pinned tabs.
- Export/import and recovery/backup baseline.
- Basic origin-device metadata.
- A narrow PSC adapter/port with a local mock or no-op implementation, so Commonplace's domain layer is not coupled to PSC availability or implementation details.
- Automated tests for Markdown round-trip, storage, search, canvas state, and the PSC adapter boundary.

**Exit:** a user can safely use Commonplace for daily notes offline without being locked into a proprietary file format.

### Phase 2 — Private alpha / first sync

- Integrate the first supported sync destination through PSC's accepted, versioned contract and provider adapter; do not put provider-specific sync logic in Commonplace's domain layer.
- Explicit opt-in for the selected workspace/data categories and provider; provide inspect, pause, disconnect, provider-access revocation, and recovery controls.
- Local-first conflict detection and conflict-preserving resolution, with Commonplace defining data-type semantics and PSC handling only generic coordination mechanics.
- Sync state, errors, retry, device information where supported by the PSC contract, and recovery/version history.
- Initial date/time reminders if native scheduling is validated.
- Limited sharing prototype or read-only share/export; no claim of full collaboration.

**Exit:** two devices can edit offline and converge without silently losing a note; recovery from a failed or partial sync is tested.

### Phase 3 — Public beta

- Complete the agreed first-platform experience.
- Add the next approved platform with its own native UI implementation; do not port a web UI or wrap the first platform's interface.
- Harden cloud-provider behavior, account disconnect, credential revocation, backup, and deletion.
- Add group-space access model and invitations if approved.
- Add shopping-list type and its item index.
- Evaluate MCP/Claude integration with read-only scope and confirmed writes.
- Verify that Commonplace can coexist with Solstice and other PSC clients without shared database access, namespace collisions, or cross-app data leakage.
- Validate accessibility, localization, performance, and migration from ordinary Markdown folders.

**Exit:** real-world testers can use the supported platforms, sync, and sharing without data loss or unclear privacy behavior.

### Phase 4 — Version 1.0

- Publish only platforms that meet their build, test, accessibility, and distribution acceptance requirements.
- Stable Markdown and metadata format with documented compatibility rules.
- Reliable sync and recovery on supported providers.
- Explicitly defined small-group sharing and permissions.
- Native date/time reminders; location reminders only if their permission, battery, and background behavior have passed platform tests.
- Core canvas/list/editor/search experiences polished and accessible.
- Document the supported PSC contract version/range and any required migration or compatibility policy.
- Keep Commonplace's core behavior available when PSC or a provider is unavailable.
- Versioned migration policy, backup/restore documentation, privacy documentation, and release notes.
- No critical known data-loss, security, or sync-integrity defects.

### Later / not a launch dependency

- Additional local coordination capabilities and provider adapters, only when justified by user needs and compatible with PSC's permanently decentralized model.
- On-device AI tag suggestions.
- More advanced AI retrieval and personal-knowledge workflows.
- More storage providers.
- More advanced location automation.
- Advanced shopping item indexing, historical costs, and store comparisons.
- Real-time collaborative editing, if user demand and architecture justify it.

## 11. Versioning proposal

Use Semantic Versioning once public compatibility promises exist:

- Before 1.0: 0.MINOR.PATCH; minor versions may change behavior and data contracts during development.
- 1.0.0: first stable release for explicitly listed platforms and data formats.
- After 1.0: increment PATCH for compatible fixes, MINOR for backward-compatible features, MAJOR for breaking changes.
- Use prerelease labels such as 0.4.0-alpha.1, 0.8.0-beta.1, and 1.0.0-rc.1.
- Keep app release numbers separate from internal milestones where necessary.
- Any change to Markdown serialization, IDs, workspace metadata, or sync protocol requires migration and rollback planning.

## 12. Business model flexibility and commercial feasibility

Commonplace may launch as a paid app, a one-time purchase, a paid upgrade, or eventually a subscription/service. The business model is undecided. Keep the option to charge for the app open; do not assume that Commonplace must be free or freemium. No pricing, billing cadence, tier boundary, or payment provider is approved yet.

Before committing to a model or building payment infrastructure, produce a documented commercial-feasibility assessment. Compare plausible models against the product's audience, expected value, alternatives, distribution channels and long-term costs. Evaluate, where relevant, paid-up-front licensing, one-time purchase, paid major upgrades, optional subscriptions tied to ongoing value, paid add-ons, and support or business licensing. Do not assume every model is suitable.

The assessment must distinguish one-time development costs from recurring costs and include development and maintenance; security updates; platform-specific testing; customer support and localization; distribution and payment processing fees; refunds and applicable tax/consumer obligations in target markets; third-party API, provider, data, model and AI usage costs; rate limits and price changes; and realistic usage scenarios. Keep external provider costs distinct from PSC-operated infrastructure: PSC will not operate a hosted service, relay, or managed storage to support a revenue model.

Avoid premature payment infrastructure, artificial limits, ads, tracking, data sale, degraded privacy, or infrastructure that would contradict PSC's permanently decentralized model. The shared PSC integration must not force a first-party cloud account or remove the user-controlled-provider/local-only path. Paid status must never remove Markdown portability, export, user access to their files, or privacy controls. AI use and AI provider costs must remain separately disclosed and controlled. Any recurring charge must correspond to a clearly explained ongoing benefit or cost.

## 13. Licensing, third-party resources, and provenance

Licensing and resource eligibility are product and release requirements, not a final packaging task. Do not assume that a resource is free to use, modify, redistribute, or monetize merely because it is publicly accessible, free of charge, open source, AI-generated, or available in a developer tool.

Maintain a resource and license inventory for software and non-software materials used or distributed by Commonplace. It must cover, as applicable: application code, shared libraries, SDKs, frameworks, plugins and build tools; fonts, icons, illustrations, photographs, audio, animations, templates and other visual or sound assets; datasets, sample content, documentation, code examples and copied snippets; APIs, hosted services, storage providers, AI models and model outputs where their terms affect use or distribution.

For each item, record its origin, exact name/version, rights holder where known, license or terms URL, date checked, intended use, modification and redistribution/commercial-use permissions, attribution and notice duties, source-disclosure or copyleft obligations, patent/trademark conditions, usage quotas, fees, and material restrictions. Record uncertainty instead of guessing.

Classify resources as:
1. **Allowed:** rights and obligations are clear and compatible with the intended use and distribution.
2. **Allowed with conditions:** usable only after specified attribution, notice, source offer, paid permission, scope restriction, or other obligations are met.
3. **Prohibited or unresolved:** do not include, ship, train on, or rely on the resource until the rights and terms are clarified.

Check direct and transitive software dependencies. Evaluate license compatibility with both open-source and commercial distribution scenarios, including whether distribution, modification, network use, patent terms, or source disclosure create obligations. Keep required LICENSE, NOTICE, attribution, copyright, and corresponding-source materials with releases. Generate and review a software bill of materials (SBOM) where practical.

Do not copy code, scrape data, use brand assets, or bundle fonts, media, or models based only on availability or attribution. Use official APIs and respect provider terms, rate limits, data-use restrictions and commercial-use rules. For AI-assisted code and generated assets, retain provenance and check applicable tool/model terms; review generated code like any other third-party contribution. Unknown or conflicting rights block distribution until reviewed.

The license selected for Commonplace's own code is a separate decision from the licenses of its dependencies and assets. Do not add a repository license, promise open-source distribution, or adopt a dual-license model without an explicit decision based on commercial, contribution, patent and compatibility implications. Seek qualified legal review for material ambiguity or high-risk distribution decisions.

## 14. Initial open decisions

Do not silently decide these during implementation:

1. **First desktop OS:** Windows or macOS. Desktop-first is confirmed, but the first desktop operating system remains undecided pending native architecture and feasibility prototypes.
2. **Full platform matrix:** confirm whether iOS, Android, macOS, Windows, Linux, and web are all intended targets, and in what order. Linux sync is conditional and may be dropped if its feasibility spike shows disproportionate complexity or maintenance; a local-only Linux app is a separate decision.
3. **First sync destination/provider behind PSC:** user-selected local/provider folder, Google Drive API, Apple-native iCloud document access, or another provider. PSC is the shared coordination/integration layer, not a substitute for choosing and validating the storage destination.
4. **Shared spaces:** read-only sharing, invite-based shared folders, asynchronous multi-writer edits, or real-time collaboration.
5. **End-to-end encryption:** whether content must be encrypted before upload, and how this affects search, thumbnails, group access, and account recovery.
6. **Markdown dialect:** CommonMark/GFM baseline, supported extensions, YAML frontmatter rules, internal links, and attachments.
7. **Mobile canvas:** full canvas, simplified canvas, or list-first mobile navigation with canvas as an alternate view.
8. **Shopping-list scope:** whether its first version is just a structured list or also supports store/location and price history.
9. **Business model and commercial viability:** compare paid app, one-time purchase, paid upgrade, subscription, add-ons, support and other suitable models; assess costs and obligations before selecting one.
10. **Commonplace and PSC licenses:** choose the license and distribution model for each project's own code only after reviewing business goals, contribution model, dependency compatibility and commercial implications.
11. **Allowed resource policy:** approve the inventory and rules for code, SDKs, APIs, fonts, icons, media, datasets, AI models, generated outputs and other third-party materials.
12. **PSC local execution and adapter model:** decide which coordination components belong in each app, which may run as user-controlled local components, and how platform adapters communicate without a central service.
13. **PSC capability scope:** decide which decentralized capabilities are in scope (for example, provider discovery, device metadata, synchronization status and event routing) and define their local/provider-based contracts.
14. **PSC content protection:** define encryption, metadata exposure, key recovery, provider/token ownership and revocation for user-selected storage paths. A hosted PSC relay or managed storage service is not an available option.

Record the answers in a decision log before implementing the affected architecture.

## 15. Definition of done for the pre-plan

This pre-plan is complete as a planning baseline only. It does not mean that the application framework, storage protocol, sharing model, or platform list has been selected or implemented. The next milestone is a focused architecture/design spike, not a large speculative code scaffold.
