# Commonplace — Product and Technical Research

**Research date:** 2026-11-26  
**Status:** Source-backed initial research, not a complete competitive or legal audit.

## 1. Executive summary

Commonplace should not be a Craft clone or an Obsidian clone.

Its product thesis is the intersection of:

- Obsidian's transparent, portable Markdown files and Live Preview editing.
- Craft's cohesive document experience, tags, tasks, daily planning, collections, sharing, offline sync, and external AI integrations.
- Konode's user-controlled storage connections, least-privilege provider access, local credential handling, and explicit sync behavior.
- A distinctive desktop canvas organized around note cards, groups, and stacks, paired with a conventional list view.
- Native applications with restrained tactile interactions rather than one shared web UI inside native wrappers.

The central architecture distinction is to keep **note content**, **workspace layout**, **sync transport**, **shared-group permissions**, and **AI access** as separate systems with documented contracts.

## 2. Craft feature comparison

Primary references:
- [Craft product overview](https://www.craft.do/)
- [Craft writing experience](https://www.craft.do/write)
- [Craft planning and tasks](https://www.craft.do/plan)
- [Craft pricing and plan comparison](https://www.craft.do/pricing)
- [Craft Calendar help](https://support.craft.do/hc/en-us/articles/6778959743005-Event-Notes)
- [Craft MCP guide](https://www.craft.do/imagine/guide/mcp)
- [Craft MCP for Claude](https://www.craft.do/imagine/guide/mcp/claude)
- [Craft MCP for ChatGPT](https://www.craft.do/imagine/guide/mcp/chatgpt)
- [Craft MCP v2 and BYOK announcement](https://www.craft.do/blog/craft-update-3-4-4)
- [Craft whiteboard engineering article](https://www.craft.do/blog/thinking-outside-of-the-wkwebview)

Craft's current public feature set goes beyond formatted notes. It includes docs, tasks, calendar, daily notes, whiteboards, hierarchical tags, Collections, offline editing, sync, shared spaces, publishing, exports, AI, API, and MCP connectivity. Its plan table distinguishes personal content/storage limits from collaboration/shared-space capabilities. Exact plan limits and pricing can change and should be checked again before a commercial comparison.

| Craft capability | What Craft offers (source-backed) | Commonplace recommendation |
|---|---|---|
| Rich document editor | Structured, block-based documents with text, checklists, images, embeds, and styling | **Core.** Make Markdown the canonical durable format; provide live formatting while typing. Do not reproduce a proprietary block document as the source of truth. |
| Markdown | Markdown import/export exists, but Craft's core editor is block-based | **Differentiator.** Commonplace should use ordinary Markdown files as the source of truth, not merely support Markdown as an export format. |
| Tasks | Tasks can live inside documents, have dates, deadlines, reminders, and repeats | **MVP-lite / early follow-up.** Begin with Markdown-compatible checkboxes and simple date/time reminders. Avoid a complex project-management task engine. |
| Calendar / daily notes | Calendar brings daily notes, dated tasks, and synced events together; its documented calendar integration is Apple Calendar-specific | **Later.** Daily notes and dated reminders are useful, but a full calendar is not required for the first MVP. |
| Tags | Inline hierarchical tags with filtered views | **Core.** Tags should be visible to people and represented in portable, documented metadata so local search and explicitly authorized tools can use them. |
| Collections | Organized structured collections with table/gallery views, filtering, grouping, and sorting | **Selective.** Commonplace's canvas groups and list views should cover the first organization needs. Do not build a database builder in the MVP. |
| Whiteboards | Spatial board editing; Craft's engineering article describes a tldraw-based implementation | **Differentiate.** Commonplace's primary desktop canvas is a workspace for actual notes, not simply a separate free-form drawing board. Prototype note-card virtualization, zoom, grouping, and file/layout separation. |
| Offline editing | Craft advertises offline editing across supported experiences, including recent Web/Windows support | **Required.** Offline is not premium-only infrastructure; users must create, search, and edit local Markdown notes offline. |
| Sync | Fast cross-device sync is a core capability | **Required after local MVP.** Define conflict-safe sync rather than promising speed before data safety. |
| Sharing | Link sharing and permissioned shared spaces, with plan-dependent limits | **Core direction, phased.** Start with explicitly invited small groups; define roles, revocation, conflicts, and visibility. Do not make private notes implicitly shared. |
| Publishing | Public pages with controls such as password/expiry/indexing depending on plan | **Not MVP.** Export/share a note intentionally first; public publishing is a later product decision. |
| AI assistant | AI may summarize, translate, edit and organize content; Craft also describes Apple on-device models and bring-your-own-key provider connections | **Optional.** No AI dependency in core workflows. Start with controlled read-only retrieval and confirmed writes, with transparent provider and cost controls. |
| External AI tools | Craft exposes MCP connections to Claude, ChatGPT and other clients; clients can read, search, and update authorized content | **Important compatibility goal.** Provide documented, narrow MCP tools over user-authorized notes. Tags improve discoverability but do not grant access. |
| Export | Craft lists Markdown, PDF, Word and PNG export (formats vary by plan) | **Required.** Markdown and attachments must remain independently usable and exportable at all times. |

### Lessons from Craft

1. **A polished writing experience is a system**, not only a Markdown parser. Editing, selection, formatting, embeds, navigation, focus, keyboard shortcuts, and offline behavior must be designed together.
2. **Tasks should remain near their context.** A simple reminder attached to a note is more aligned with Commonplace than a separate project-management subsystem.
3. **Tags need useful views.** The value of tagging comes from filtering and grouping, not merely displaying colored labels.
4. **MCP should be designed as a first-class integration surface**, but with narrower permissions and lower default exposure than a full workspace connector.
5. **Whiteboards are technically expensive.** Craft's engineering article describes a tldraw-based whiteboard and platform-specific engineering to address native focus, text selection, trackpad inertia, zoom shortcuts, and context-menu behavior. Commonplace should prototype its canvas early and validate the native experience rather than assuming a cross-platform canvas library will feel native.
6. **Premium products can mix paid plans with optional AI provider choices.** Keep the product's business model and AI provider model separate.

## 3. Obsidian comparison

Primary references:
- [How Obsidian stores data](https://obsidian.md/help/data-storage)
- [Editing and reading modes / Live Preview](https://obsidian.md/help/edit-and-read)
- [Import Markdown files](https://obsidian.md/help/import/markdown)

Obsidian stores notes as Markdown-formatted plain text in a folder called a vault. Users can edit those files in external editors, and Obsidian refreshes when files change. Its default Live Preview renders most formatting inline and reveals underlying Markdown when the cursor enters formatted content.

### Lessons to adopt

- The folder of Markdown files is the durable content layer.
- A user can choose where the content lives.
- External edits must be detected and reconciled.
- Live Preview should be the default editing mode.
- Markdown import/export is not sufficient: files must remain ordinary Markdown at rest.
- Configuration and workspace state must not be confused with note content.

Obsidian's extensibility and plugin ecosystem are powerful but can create compatibility and maintenance complexity. Commonplace should start with a smaller, deliberate set of native capabilities rather than requiring plugins to complete basic workflows.

## 4. Konode comparison: user-owned storage and synchronization

Primary references:
- [Konode repository](https://github.com/konabe-studio/konode)
- [Konode README](https://github.com/konabe-studio/konode/blob/main/README.md)
- [Konode getting started](https://github.com/konabe-studio/konode/blob/main/GETTING_STARTED.md)
- [Konode privacy policy](https://github.com/konabe-studio/konode/blob/main/PRIVACY.md)
- [Konode roadmap](https://github.com/konabe-studio/konode/blob/main/ROADMAP.md)
- [Google Drive backend implementation](https://github.com/konabe-studio/konode/blob/main/src/lib/backends/gdrive-backend.ts)
- [WebDAV backend implementation](https://github.com/konabe-studio/konode/blob/main/src/lib/backends/webdav-backend.ts)

Konode is a browser-data synchronization utility, not a note editor or shared document workspace. Its architecture is still a useful reference for provider adapters and user-controlled storage.

### Observed design patterns

- The user chooses one storage backend for a sync group.
- Current backends include Google Drive, GitHub, and WebDAV.
- Google Drive access uses the narrow drive.file scope and stores sync files in an app-created folder.
- Credentials are stored in device-local extension storage and are not sent to a Konode server.
- Its sync engine writes per-device files and discovers/pulls peer files on a schedule; ordinary storage backends do not provide the push channel its design needs.
- Optional end-to-end encryption encrypts sync payloads before upload; it is not silently enabled.
- Sync status, retries, conflict handling, and deletion protection are treated as real product behavior, not implementation details.
- Konode's own documentation distinguishes CloudKit from a direct iCloud Drive connector: CloudKit is an Apple-managed app container, not general access to user-visible Drive files.

### What Commonplace should learn

- Isolate each provider behind an adapter contract.
- Use narrow OAuth scopes and show users what the provider can access.
- Store credentials in platform-secure credential facilities where available.
- Make disconnect and provider permission revocation understandable.
- Handle interrupted writes, stale reads, duplicates, retries, deletions, and multiple devices explicitly.
- Keep local data useful even when sync is unavailable.

### What Commonplace should not copy blindly

- A per-device whole-document JSON packet is not an adequate collaboration protocol for frequently edited Markdown notes.
- A scheduled pull model may be acceptable for a first personal sync implementation, but it is not real-time collaborative editing.
- One provider for every sync group may not fit users who want different storage for different workspaces.
- End-to-end encryption affects search, sharing, indexing, recovery, and account loss. It needs an explicit architecture decision.
- Konode's browser-extension credential storage is not a model for native apps; use Keychain, Android Keystore-backed storage, Windows Credential Manager/DPAPI, or platform-supported secure storage after platform-specific review.

### Provider-specific cautions

**Google Drive:** drive.file is not unrestricted read access to a user's whole Drive. Validate the user-selection and app-created-file flows needed for ordinary Markdown files, OAuth refresh, revisions, and conflicts.

**iCloud Drive:** use Apple-native file/document access to user-visible files where possible. Do not conflate this with CloudKit. The two have different ownership, discoverability, portability, and sharing behavior.

**WebDAV:** potentially broad compatibility, but server behavior varies. Validate TLS, app passwords, ETags, redirects, partial writes, and server-side revisions.

**Filesystem providers:** user-selected folders are a strong portability-first experiment, but cloud-backed folders may have online-only placeholders, delayed hydration, file coordination, and OS-specific behavior.

## 5. Feature priority proposal

This is a recommended planning classification, not a final approved scope.

### Foundation / MVP

- Native first-platform shell.
- Markdown files as canonical note content.
- Live Preview-like Markdown editor with round-trip preservation.
- Local-first persistence and offline editing.
- Create, edit, rename, move, archive, and delete notes.
- Tags and basic metadata.
- Fast local text search.
- Desktop canvas with pan/zoom and note cards.
- Groups and basic user-arranged organization.
- List view with tag/date/device sorting and grouping.
- Keyboard shortcuts and a command palette / quick search.
- Export, backup, and recovery basics.

### Early follow-up

- First provider adapter and conflict-safe device sync.
- Pinned tabs/notes.
- Device-origin indicator and creation/editing device history.
- Date/time reminders.
- Small-group invited sharing with explicit roles.
- Attachments and portable asset folders.
- Shopping list as a specialized, exportable note type.

### Later / validated by demand

- Location-based reminders.
- Multiple cloud providers and provider-specific optimization.
- Advanced sharing and richer group activity.
- MCP server and Claude compatibility.
- More advanced shopping lists with store/location and price history.
- On-device AI tag suggestions.
- Semantic/AI search and summaries.
- Public publishing, template marketplace, or sophisticated Collections if there is clear demand.

### Do not build in the first MVP

- Real-time multiplayer co-editing.
- A generalized database/relations builder.
- A large plugin marketplace.
- AI that silently reads all notes or rewrites them.
- A proprietary cloud lock-in that makes Markdown files secondary.
- A universal UI toolkit to force all platforms into identical rendering.
- A custom replacement for every native control.

## 6. Technical risks to prototype early

| Risk | Why it matters | Smallest useful experiment |
|---|---|---|
| Markdown live editor | Rich inline editing can damage source or selection behavior | Prototype parse/render/edit/save round-trips for nested lists, links, code, tables, and unknown syntax |
| Canvas performance | Pan/zoom, spatial hit-testing, large groups, and rich note cards can be expensive | Prototype hundreds/thousands of lightweight note cards, virtualization, drag/zoom, and keyboard navigation on target desktop |
| Native canvas behavior | Trackpad inertia, text selection, context menus, focus, zoom shortcuts differ by OS | Native prototype on first target; compare with any candidate cross-platform engine |
| Provider sync | Cloud file APIs differ and may return stale metadata or support different revision semantics | Two-device spike with offline edits, concurrent changes, retry, deletion and restore |
| E2EE and sharing | Sharing keys and search/indexing are more complex when content is encrypted | Threat model and key-sharing prototype before choosing a sync architecture |
| Multi-platform native development | Different UI stacks increase engineering and testing cost | Choose one first platform and define shared data/behavior contracts before broad scaffolding |
| AI cost and privacy | Large context and repeated provider calls create cost and exposure | Retrieval-limited proof of concept with read-only scope, token budget, and provider disclosure |

## 7. Product and monetization posture

Commonplace may remain a personal utility, become a paid app, or eventually support paid tiers/services. No pricing or business model has been selected.

Preserve these options:

- One-time paid app or paid platform-specific purchase.
- Paid upgrades or optional feature unlocks.
- Subscription only where ongoing service costs or continuing value justify it.
- Paid shared-space or hosted-sync capabilities if those introduce real recurring infrastructure costs.
- Bring-your-own-storage and bring-your-own-AI-provider options to avoid making the app's operating costs depend on the developer paying for every user's provider usage.

Do not assume that the app must be free or freemium. Equally, do not build subscription enforcement, payment systems, a proprietary backend, or artificial feature restrictions before there is a product and cost rationale.

**Non-negotiable even if paid:** Markdown content remains exportable; users can retrieve their own files; privacy is not traded for advertising; paid status must not silently authorize AI processing; and users must understand what happens to their data if they stop paying.

## 8. Sources and research limits

This is a source-backed product/technical comparison, not a complete code audit of Craft, Obsidian, or Konode. Craft's marketing and pricing pages describe current product capabilities but are not a substitute for testing the apps. Konode's source code demonstrates one implementation and its own design constraints, not a universal best practice.

Recheck third-party features, APIs, prices, OAuth scopes, terms, and platform capabilities before implementation because they can change.


## 9. Native editor foundations: platform-specific findings

The editor is one of the highest-risk parts of the product. The official platform documentation points to useful native foundations, but none of the simple high-level controls should be assumed to provide the complete Obsidian-style Live Preview behavior without custom work.

| Platform | Documented native foundation | Preliminary implication |
|---|---|---|
| iOS | UIKit UITextView / TextKit; SwiftUI TextEditor | UITextView supports multiline text, custom styling, and attributed text. SwiftUI TextEditor is convenient for long-form text but should not be assumed to solve complex inline Markdown editing. |
| macOS | AppKit NSTextView / TextKit | NSTextView supports rich text, attachments, input management, key bindings, selection, and text interaction. Strong candidate for a desktop editor prototype. |
| Android | Jetpack Compose state-based TextField and TextFieldState; compare EditText if required | Compose exposes text, selection, buffer editing, and input/output transformations. A Live Preview editor still needs custom range mapping and IME/selection validation. |
| Windows | WinUI 3 native text controls | The correct control must be selected through a prototype that tests inline formatting, selection, undo/redo, large notes, and keyboard behavior. |
| Linux | Not yet selected | Research the target desktop environment and native text stack after the first platform is chosen. |

A key implementation constraint: do not repeatedly serialize a rich-text document back to Markdown on every keystroke. Preserve the Markdown source as the durable document and maintain a stable mapping between source ranges and rendered decorations. Unsupported syntax and unknown frontmatter must survive an edit/save cycle.

### Editor proof-of-concept checklist

- Nested headings, emphasis, links, blockquotes, code, lists, task lists, tables, and attachments.
- Caret movement into and out of formatted ranges.
- Selection, copy/paste, undo/redo, and keyboard shortcuts.
- IME composition and input methods.
- Large-document performance.
- External file modification and refresh.
- Unknown syntax/frontmatter round-trip preservation.
- Accessibility and screen-reader behavior.

References:
- [Apple UITextView](https://developer.apple.com/documentation/uikit/uitextview/)
- [Apple NSTextView](https://developer.apple.com/documentation/appkit/nstextview)
- [SwiftUI TextEditor](https://developer.apple.com/documentation/swiftui/texteditor)
- [Jetpack Compose text input](https://developer.android.com/develop/ui/compose/text/user-input)

## 10. Native canvas primitives and implications

Official platform guidance confirms that pan/zoom is possible natively, but the primitives and interaction behavior differ:

- **Android:** Compose's transformable gesture modifier detects pan/zoom/rotation. The application still owns the transform state and applies the transform to content.
- **Windows:** WinUI ScrollView and ScrollViewer support touch/pen panning, optical zoom, configurable zoom bounds, and programmatic zoom.
- **Apple:** SwiftUI ScrollView supports horizontal/vertical scrolling but does not itself provide zooming. A canvas will need a lower-level native view or a carefully implemented transform layer; validate trackpad, pointer, and touch behavior separately.

This strengthens the recommendation to implement a shared canvas behavior contract but native platform surfaces. Do not assume the same gesture implementation will feel correct on mouse, trackpad, pen, and touch.

References:
- [Compose pan/zoom/rotation gestures](https://developer.android.com/develop/ui/compose/touch-input/pointer-input/multi-touch)
- [WinUI scroll, pan, and zoom](https://learn.microsoft.com/en-us/windows/apps/develop/ui/controls/scroll-controls)
- [SwiftUI ScrollView](https://developer.apple.com/documentation/swiftui/scrollview)

## 11. Provider API findings: important distinctions

### Google Drive

Google's OAuth scope guide describes drive.file as permission to create files and modify files that the user opens with the app or shares with it through Google Picker/file picker. It is not unrestricted access to all Drive files.

The Drive API provides a changes feed with page tokens. It also supports changes.watch notifications; notifications tell the app that the change log may have advanced, after which the app fetches the actual changes. Rate-limit and transient errors should use bounded exponential backoff.

This means Konode's periodic polling is a design choice suited to its no-server extension model, not proof that Google Drive has no change notification capability. A direct client-only Commonplace architecture must still evaluate how it receives notifications and whether a webhook endpoint/server would be required for the watch channel.

References:
- [Google Drive API scopes](https://developers.google.com/workspace/drive/api/guides/api-specific-auth)
- [Retrieve Drive changes and notifications](https://developers.google.com/workspace/drive/api/guides/manage-changes)
- [Drive API error handling](https://developers.google.com/workspace/drive/api/guides/handle-errors)

### iCloud Drive and CloudKit

These must remain separate options:

- FileManager and Apple's document APIs work with iCloud-based documents and user-visible file workflows.
- Apple's document browser can open files in iCloud Drive and supported third-party file providers.
- File Provider is for an app/provider that manages and syncs remote documents exposed to other apps.
- CloudKit stores app data in an app's iCloud containers. CKSyncEngine can manage record synchronization, but its automatic schedule is system-dependent and application code must still handle some record conflicts.

Do not promise a single "iCloud API" that works like Google Drive across iOS, Android, macOS, Windows, and Linux. Apple-native iCloud document access is a platform capability, not a universal provider abstraction.

References:
- [Apple FileManager and iCloud documents](https://developer.apple.com/documentation/foundation/filemanager)
- [Apple document browser](https://developer.apple.com/documentation/uikit/building-an-app-with-a-document-browser)
- [Apple File Provider](https://developer.apple.com/documentation/fileprovider)
- [Apple CloudKit](https://developer.apple.com/documentation/cloudkit)
- [Apple CKSyncEngine](https://developer.apple.com/documentation/cloudkit/cksyncengine-5sie5)

## 12. Craft AI/MCP findings

Craft's own product documentation confirms an official MCP endpoint at https://mcp.craft.do/my/mcp. Its setup flow uses OAuth and asks the user to approve a Craft space; connected clients can read, search, and update authorized documents.

Craft also announced MCP v2, bring-your-own-key provider connections, and on-device Apple Foundation Models support in its June 2026 product update. The update explicitly identifies reduced MCP token use as a goal. This is useful precedent for Commonplace's AI economy: smaller tool descriptions, search-first retrieval, scoped context, BYOK, and local inference where available.

Avoid confusing Craft Docs with Craft.io: the latter is a separate product with its own MCP server.

References:
- [Craft MCP guide](https://www.craft.do/imagine/guide/mcp)
- [Craft MCP for ChatGPT](https://www.craft.do/imagine/guide/mcp/chatgpt)
- [Craft June 2026 update: BYOK and MCP v2](https://www.craft.do/blog/craft-update-3-4-4)

## 13. Motion-UI repository verification

The current INFORMATION89/Motion-UI README identifies the project as a Jetpack Compose library, targeting Android 10/API 29 and using JDK 21. It separates Motion Core from an optional Motion UI layer. The README states that the current artifact does not support iOS or desktop targets (Windows, macOS, Linux). It also says Motion UI currently has an app-configurable Material 3 theme/token foundation and that component surfaces are being delivered in later stages.

Recommendation:
- Use Motion on Android only after a small integration prototype.
- Pin a tested commit/submodule revision.
- Use native platform motion APIs elsewhere.
- Keep Commonplace's product components and branding out of Motion.
- Do not choose Commonplace's application architecture based on Motion's current platform reach.

Reference:
- [INFORMATION89/Motion-UI README](https://github.com/INFORMATION89/Motion-UI/blob/main/README.md)

## 14. Recommended proof-of-concept order

1. **Markdown Live Preview editor** on the first chosen platform. Validate text fidelity, caret behavior, IME, undo/redo, accessibility, and large notes.
2. **Local Markdown file model.** Validate IDs, frontmatter, attachments, import/export, external edits, and recovery.
3. **Desktop canvas prototype.** Validate pan/zoom, anchor behavior, groups/stacks, keyboard interaction, virtualization, and 500–1,000 synthetic cards.
4. **Two-device sync.** Use one provider and test offline concurrent edits, partial writes, retries, deletion, restore, and account disconnect.
5. **Motion/tactility prototype.** Test a small set of interactions on real devices and compare native platform equivalents.
6. **Read-only MCP prototype.** Expose only selected test notes; validate OAuth, scope, source references, revocation, context limits, and write confirmation.

Do not implement all providers or all platforms before these core risks are understood.

