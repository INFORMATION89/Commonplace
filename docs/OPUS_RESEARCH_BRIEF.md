# Opus Research Brief — Commonplace Licensing and Automatic Updates

**Status:** Research assignment; no product or commercial decision is approved by this brief.  
**Requested model:** Opus (advanced independent research)  
**Project:** Commonplace  
**Related plan:** [PREPLAN.md](../PREPLAN.md), sections 5.1, 10, 12 and 14.

## Assignment

Conduct an evidence-based, independent investigation into Commonplace's proposed one-time, platform-by-platform licensing concept and the safest maintainable automatic-update approach for each target platform.

This is a research task, not an implementation task. Do not modify application code, introduce a payment provider, select a final price, or treat the current hypothesis as a requirement. Challenge the proposal where evidence suggests a better approach. Keep recommendations consistent with Commonplace's local-first, privacy-first, native-platform direction and PSC's permanently decentralized boundary.

Use current primary sources for store rules, SDKs, distribution mechanisms, and fees. Cite URLs, exact policy/document names, access dates, relevant sections, and note any regional differences or uncertainty. Separate verified facts, estimates, assumptions, and recommendations. Do not rely on stale pricing or policy summaries.

## Part A — Commercial model and licensing

### Owner-provided hypothesis to evaluate

- 14 days of free use before purchase.
- Approximately **US$2 one-time** for a base/limited license covering the Commonplace app and underlying Commonplace "system".
- Approximately **US$1 one-time** for each additional platform unlock.
- A complete package covering every supported platform should cost **no more than US$10 total**.
- The intended direction is one-time payment, not a recurring subscription.
- The figures are placeholders. USD is only a working assumption for research; validate target-market and regional price points.
- "Limited license" has not yet been defined. Do not invent its restrictions; compare clear interpretations and explain their consequences.

### Questions to answer

1. Is this structure understandable to ordinary users? Does it create confusing separation between a base/system license and platform unlocks?
2. What exactly could the base license include? Compare a few comprehensible entitlement structures, including whether it unlocks one platform, the core system only, or a limited mode. Identify what should remain usable after the trial expires.
3. How should the 14-day trial work? Compare trial start conditions, trial reset/abuse risks, device changes, reinstallations, offline use, and whether an account would be required. Preserve access to local notes and export regardless of trial or purchase state.
4. How should a platform unlock work across Windows, macOS, iOS, Android, and Linux (if supported)? Define what counts as a platform and how cross-device reinstalls, device replacements, refunds, and purchase restoration should behave.
5. Can purchases made on one platform unlock the corresponding app on another? Examine current Apple App Store and Google Play policies, Microsoft Store rules, direct distribution, and the requirements for cross-platform entitlements. Identify when store billing must be used, when access to an external purchase is permitted, and regional exceptions or programs. Do not assume a single external license key is permitted in every store.
6. What are realistic minimum price points and price tiers in target storefronts? Could US$1 and US$2 SKUs actually be offered in all regions? Compare localization and purchasing-power implications.
7. Estimate net revenue after store commissions, payment processing, refunds, chargebacks, taxes and support costs. Explain the economic limits of very low-priced one-time purchases.
8. Compare this proposal against simpler alternatives: one purchase per platform, one universal bundle, a base license plus one platform, paid major upgrades, or other suitable one-time structures. A subscription may be discussed as a comparator, not assumed to be desired.
9. How can entitlements be represented and verified without creating a mandatory Commonplace account, centralized content backend, or dependency on PSC hosting? Distinguish purchase validation from synchronization. Consider store receipts, signed local entitlements, restore purchases, and what must happen when offline.
10. Does the model create future obligations that could conflict with paid updates, maintenance, platform changes, or long-term support?

### Required output for Part A

- A concise comparison table of at least three viable commercial structures.
- A platform-by-platform purchase-policy matrix with source citations and regional caveats.
- A transparent example calculation of gross and net revenue at the proposed price points; label all assumptions.
- A recommended model, a confidence level, key risks, and open owner decisions.
- A clear statement on whether the US$10 full-package ceiling appears feasible, and what trade-offs it imposes.
- A proposal for what the "limited license" means, or a recommendation to remove that concept if it is too confusing.

Do not finalize pricing on the owner's behalf. Make a recommendation and explain the evidence.

## Part B — Automatic updates and release distribution

Research automatic update options across the intended native platform matrix:

- Windows (WinUI 3).
- macOS (SwiftUI).
- iOS (SwiftUI, if included).
- Android (Jetpack Compose).
- Linux (only if retained as a target; include major package distribution options such as Flatpak and Snap where relevant).

Investigate the full lifecycle, not merely a library that checks for a newer version.

### Release stages to cover

1. Local developer builds and CI artifacts.
2. Internal/private alpha distribution.
3. Closed/open beta distribution.
4. Public production releases.

### Questions to answer

1. What is the native or platform-approved update path for each OS and distribution channel?
2. Compare store-managed updates with direct distribution and dedicated updater frameworks. Include current candidate technologies such as MSIX/App Installer and Microsoft Store on Windows, Sparkle and Mac App Store distribution on macOS, Google Play update mechanisms on Android, App Store distribution on iOS, and Flatpak/Snap or other maintained approaches on Linux. These are candidates to verify, not predetermined choices.
3. Which paths allow background checking, downloading, staged installation, delta updates, user prompts, deferral, and critical updates?
4. What signing, notarization, package identity, certificates, update manifests, HTTPS, and cryptographic verification are required?
5. How should updates behave if the app is open, a document is being edited, local writes are pending, the device is offline, storage is low, or an update fails?
6. How are rollback, recovery, interrupted downloads, version compatibility, data/schema migrations, and release-channel changes handled?
7. Can the same release pipeline publish stable, beta, and internal channels without leaking test builds to production users?
8. What are the ongoing infrastructure, signing, developer-program, hosting, and maintenance costs?
9. Which mechanisms are unsuitable for a genuinely native app or would require a web/Electron shell? Do not recommend Electron or a web wrapper as an updater shortcut.
10. What is the best low-maintenance path for the first desktop app and how should it evolve as mobile and other desktop platforms are added?

### Required output for Part B

- A platform-by-platform matrix of recommended update mechanisms by release stage.
- A comparison of built-in store updates versus direct-download/updater-framework approaches.
- Signing and security requirements, operational costs, and known limitations.
- A recommended initial architecture and a migration path for additional platforms.
- A release workflow proposal that can be automated with CI/CD while retaining signing-key security and release approval.
- A short set of implementation spikes and acceptance criteria. Do not implement them.

## Part C — Final synthesis

Deliver:

1. Executive recommendation with the most important decisions first.
2. Separate findings for commercial licensing and update/distribution.
3. A risk register covering platform policy, pricing, low-revenue economics, account requirements, offline use, store dependency, signing, and update failures.
4. Decisions that require the product owner's approval versus routine technical choices.
5. A proposed order of investigation and validation. Avoid turning unknowns into assumptions.

## Constraints and principles

- Preserve local-first use, user ownership of Markdown files, and access to export.
- Never make local notes inaccessible because a trial expired or a purchase receipt cannot currently be verified.
- No mandatory subscription or account should be introduced by assumption.
- Do not add a Commonplace-operated content backend, hosted relay, or managed storage service. Licensing infrastructure, if any, must be assessed separately and justified without violating the approved PSC boundary.
- Keep AI-provider costs separate from app licensing.
- Prioritize platform-native distribution and update mechanisms.
- Updates must be signed and verifiable, resilient to interruption, recoverable, and safe for documents being edited.
- Report exact dates for policies and price/fee sources; these change and must be checked again before release.
- Do not produce detailed screen designs. The editor's floating Markdown formatting controls are a product requirement, but their exact UX remains open.
