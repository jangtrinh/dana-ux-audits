---
title: Dana - Accessibility Audit
subtitle: WCAG 2.1 AA scan across Shell, Library, Agents, Create-Agent modal, Curate - v0.2.119
report_type: a11y-audit
product: Dana
product_url: https://dana.aitomatic.com
date: Scanned 2026-05-28, every finding re-verified 2026-05-29 on v0.2.119
app_version: 'v0.2.119 (under active daily development: v0.2.114 -> 116 -> 119 across three days. Findings
  are a point-in-time snapshot.)'
workspace: Dana Enterprise -> Dana Team (Admin role)
method: axe-core 4.10.2 WCAG 2.1 AA + best-practice rules, injected via direct CDP into the controlled
  Chrome, plus targeted DOM/ARIA probes (keyboard operability, dialog role, label associations) and a
  live message-send test for status-message announcements. 5 surfaces. Every claim re-checked against
  live DOM on v0.2.119.
confidence: Medium
finding_counts:
  critical: 2
  major: 1
  moderate: 9
  minor: 1
  win: 6
  changed: 0
---

# Dana - Accessibility Audit

## TL;DR - How accessible is Dana?

Dana ships clean visual polish but assistive-technology support has structural gaps. A keyboard-only user cannot open an agent card. A screen reader gets no "dialog" announcement when the Create-Agent modal opens. Body text uses a semantic `text-tertiary` color that is **0.09 below** the WCAG 4.5:1 minimum, putting every agent card description, owner line, and tag below threshold.

None of these are visual-design failures. They are 4 to 8 lines of code each (correct ARIA roles, focused Tailwind color hex change, swap a `<div>` for a `<button>`). The fix surface is small. The current impact - keyboard and screen-reader users locked out of core flows - is large.

> 🚨 **Conformance verdict: Dana does not currently meet WCAG 2.1 Level A**
> Two findings fail Level A - the most basic tier, the floor beneath AA: **2.1.1 Keyboard** (agent cards are not keyboard-operable) and **4.1.2 Name, Role, Value** (the Create-Agent modal has no dialog role). A product that fails Level A cannot claim AA conformance and would fail a VPAT / Section 508 / EN 301 549 review - the bar enterprise and public-sector buyers gate procurement on. The good news: both Level A failures are small, localized code changes.

#### 3 biggest wins

- `<html lang="en">` set everywhere (WCAG 3.1.1)
- Icon-only buttons in the top bar carry proper `aria-label` (More options, History, Dark, Refresh tree)
- Curate's message input has `aria-label="Message input"` - screen reader announces it correctly

#### 3 biggest bugs

- **Agent cards are unreachable by keyboard** - rendered as plain `<div>` with no role, no tabindex (Critical, WCAG 2.1.1)
- **Create-Agent modal has no dialog semantics** - no `role="dialog"`, no `aria-modal`, no `aria-labelledby` (Critical, WCAG 4.1.2)
- **Screen readers are never told Dana replied** - the Curate conversation has no live region, so a blind user gets no announcement when an answer arrives (Major, WCAG 4.1.3). For a chat product this is close to unusable. Contrast also fails systemically (`text-tertiary` #787878 at 4.41:1) but is lower-impact.

## Scorecard - WCAG 2.1 AA per surface

> **Approach:** axe-core 4.10.2 injected via direct CDP into bb-browser's controlled Chrome, scanning each surface's rendered DOM. Rule set: wcag2a + wcag2aa + wcag21a + wcag21aa + best-practice.
> **Why:** Automated scanning catches roughly one third of WCAG issues but does so reliably and reproducibly. Numbers below are the lower bound. Manual SR + keyboard checks add the rest.

| Surface | Violations | Passes | Incomplete | Worst severity |
| --- | --- | --- | --- | --- |
| Library page | 3 | 32 | 1 | Moderate |
| Agents list | 4 | 27 | 1 | Critical |
| Create-Agent modal | 3 | 30 | 1 | Critical |
| Curate workspace | 4 | 23 | 2 | Major |


*Numbers are axe violations (rule groups failed), confirmed on v0.2.119. "Incomplete" means axe could not auto-decide and a human must judge. **The worst-severity column is driven by manual findings axe does not catch**: keyboard-unreachable cards (Agents, Critical), a modal with no dialog role (modal, Critical), and no live region for Dana's replies (Curate, Major). Library's worst is Moderate. The axe violation counts therefore understate real severity - automated rules cannot detect a missing live region or a non-keyboard-operable div. Severity uses this report's rubric (Critical / Major / Moderate / Minor), not axe's impact words.*

### Scenario 1: Shell - sidebar, top bar, theme toggle

*Persistent chrome around every page. Sidebar nav, top bar account / theme controls, version label. Same DOM on every surface, so every shell finding multiplies across the app.*

> **Approach:** Inspected DOM landmarks (header/nav/main/aside/footer presence) plus tabbed through visible focusable elements (21 found) and audited each top-bar icon button for accessible name.
> **Why:** Shell findings show up on every page. Fixing them once removes them from every surface scan.

### No `<main>` landmark on any surface; landmarks are inconsistent across routes

**Severity:** Moderate  
**Confidence:** High

Direct DOM check on v0.2.119: **no `<main>` element exists on any surface** (Library, Agents, Curate all return `main_el=0`). Axe fires `landmark-one-main` on every page. Screen reader users rely on a `main` landmark to skip past the chrome straight to content (D-shortcut in NVDA, rotor in VoiceOver); there is nothing to skip to.

The other landmarks are present but **inconsistent**: Library has a `<header>` and a `<nav>`; Agents has a `<header>` only; the Curate workspace route has **no landmark elements at all**. So the earlier impression of "no landmarks anywhere" is not quite right - the gap is a missing `main` everywhere plus a Curate route with zero landmarks.

Because content sits outside landmarks, axe also fires `region`: 10 unlandmarked blocks on Library, 7 on Agents, 10 on Curate. WCAG 1.3.1 (Info and Relationships) + best-practice.

**Recommendation:** Add a `<main>` wrapper around the content column on every route (including Curate). Make `<nav>` (sidebar) and `<header>` (top bar) consistent across all routes - Curate is missing both. No visual change; large SR benefit.

### Version label `v0.2.119` fails contrast at 2.75:1

**Severity:** Moderate  
**Confidence:** High

The bottom-left sidebar version label uses `text-gray-500` (#999999) on background `#fbfbfb` at 11px. Contrast ratio 2.75:1. WCAG 1.4.3 requires 4.5:1 for normal text. Re-confirmed on v0.2.119.

Not a high-priority surface (version label is rarely the thing a user is looking for), but it is a quick fix in the same Tailwind change that fixes the more important `text-tertiary` below.

**Recommendation:** Change `text-gray-500` to `text-gray-600` (#525252) for the version label specifically, or darken the Tailwind `gray.500` custom color to ~#6b7280 / 4.6:1.

### Text inside dropdowns and the workspace switcher fails contrast (only visible when expanded)

**Severity:** Moderate  
**Confidence:** Medium

When the bottom-left Help & Feedback menu is open, the "HELP & FEEDBACK" and "ADMINISTRATION" section headers use `text-gray-500` (#999999) on #f8f8f8 at 8.3pt - 2.68:1. The workspace switcher likewise renders the "Dana Enterprise / Dana Team" labels in a low gray (measured ~3.71:1 on v0.2.116; not reconfirmable on v0.2.119 with the switcher closed).

Marked Medium confidence because these elements are only rendered while their menu is expanded, so they did not appear in the closed-state v0.2.119 scan. The pattern - uppercase micro text in a too-light gray - is consistent with the always-visible failures above.

**Recommendation:** Either bold the `text-micro` uppercase menu headers (bold needs only 3:1) or darken menu/switcher text to `text-gray-700`.

### Top-bar icon buttons carry proper `aria-label`

**Severity:** Win  
**Confidence:** High

Every icon-only button in the top bar carries a meaningful `aria-label`: "More options", "New conversation", "History", "Dark" (theme toggle), "Refresh tree". The account avatar opens with proper button semantics.

This is the right pattern. Apply it to the agent cards (see Surface 3) and Dana hits a near-perfect icon-button score.

**Recommendation:** Keep doing this. Document the pattern in the engineering README so new icon buttons follow it.

### Scenario 2: Library page

*Document upload + listing. Currently empty in test workspace, so the scan focuses on the empty state, sidebar, and overlays.*

> **Approach:** Loaded Library at /dana-enterprise/library and ran axe with WCAG 2.1 AA + best-practice rules. Sampled the contrast failures by class to identify the root token.
> **Why:** Library is the second-most-visited surface and the simplest. If it has a11y debt, every other page inherits it.

### Empty-state hint text fails contrast at 4.41:1

**Severity:** Moderate  
**Confidence:** High

The "Your library is empty / Upload any of these formats" hint uses `text-tertiary` = #787878 on white. 4.41:1 is 0.09 below the 4.5:1 minimum. Same root token causes failures on every agent card in the next surface.

**Recommendation:** Darken the `text-tertiary` design token from #787878 to `#757575` (4.52:1) or `#737373` (4.74:1) - one config change ripples to roughly two dozen failing nodes app-wide.

### Supported-formats table uses #999999 text at 2.8-2.84:1

**Severity:** Moderate  
**Confidence:** High

The supported-formats table (TYPE / SUPPORTED EXTENSIONS column headers and the file-type rows) uses #999999 on white at 12px: column headers measure 2.8:1, body rows 2.84:1. WCAG 1.4.3 fails.

Structural credit: the table *does* carry `scope="col"` on its headers, so the table semantics are correct - only the text color fails.

**Recommendation:** Darken the table text from #999999 to at least `#767676` (4.5:1) or bold it. Keep the existing `scope="col"` markup.

### Scenario 3: Agents list - cards + + New Agent

*The agent picker surface. Each card represents an agent the user can open into Curate. The most-critical interaction surface in Dana for daily use.*

> **Approach:** Ran axe + manually inspected each card's tag/role/tabindex via JS. Tested whether keyboard Tab reaches and activates a card.
> **Why:** Cards are the primary call-to-action. If keyboard users cannot trigger them, the entire product is keyboard-inaccessible.

### Agent cards are `<div>` with no role, no tabindex, no keyboard handler

**Severity:** Critical  
**Confidence:** High

Each agent card (Alex, Tara) renders as:

```
<div class="relative h-full text-left p-4 rounded-md border border-gray-700 hover:bg-gray-700/20 ...">
  ...content...
</div>
```

No `role`, no `tabindex`, no `aria-label`. Verified via JS: `matches("a,button,[role=button],[role=link],[tabindex]") === false`.

A keyboard user cannot Tab to an agent card, cannot activate one with Enter or Space, and a screen reader announces it as plain text without role.

This is a **WCAG 2.1.1 (Keyboard) Level A** failure - one of the strictest. Every keyboard-only user is locked out of opening an agent.

**Recommendation:** Replace the wrapping `<div>` with `<button type="button">` (or a routing `<a href>` if click routes to a new URL like the screenshots show it does). The styling can stay identical via class. Adds zero visual change, removes a Level A blocker.

### Agent card body text uses `text-tertiary` #787878 - 4.41:1

**Severity:** Moderate  
**Confidence:** High

"Solutions Architect", "by Nhi Dam", and the role tag ("Embedded Systems Strategy") all use `text-tertiary`. 4.41:1 against white. Off by 0.09 from passing 4.5:1.

**Recommendation:** Same single fix as Library scenario - darken `text-tertiary` token by one stop.

### Heading hierarchy skips H2 - page has H1 then H3

**Severity:** Moderate  
**Confidence:** High

The Agents page heading order is: `H1: Agents` → `H3: Alex` → `H3: Tara`. There is no H2. Axe reports `heading-order: Heading order invalid`.

Screen reader users navigating by heading shortcut (1, 2, 3 keys in NVDA / rotor in VoiceOver) lose the page structure - they jump from page title straight into agent names without an intermediate section header.

**Recommendation:** Either promote each agent card name to `<h2>` (treating each agent as a section), OR add an invisible H2 like `<h2 class="sr-only">Your agents</h2>` between page title and the grid. Pick once and apply to Library + Curate pages too.

### "+ New Agent" and "Import agent" buttons have proper visible labels

**Severity:** Win  
**Confidence:** High

The two action buttons in the top-right both have visible text labels and proper `<button>` semantics. Keyboard Tab reaches them, Enter activates. Win - the buttons are not the problem, the cards are.

**Recommendation:** Keep. Use the same `<button>` pattern when fixing the agent cards.

### Scenario 4: Create-Agent modal

*The dialog that opens from + New Agent. Auditable in isolation because it overlays whatever page the user is on.*

> **Approach:** Opened the modal, walked the entire parent chain of the modal's H2 heading via JS looking for role/aria-modal/aria-label/aria-labelledby on ANY ancestor, and inspected every form field for a programmatic label.
> **Why:** Modals are the highest-risk a11y surface because they require focus trap + dialog announcement + close affordance. Getting any one wrong breaks SR users.

### Modal has NO `role="dialog"`, NO `aria-modal`, NO accessible name

**Severity:** Critical  
**Confidence:** High

Walked the entire parent chain from the modal's `<h2>Build an expert agent with Dana</h2>` up to `<body>` looking for ANY ARIA attributes. Result: **zero**. The modal is a styled positioned `<div>`.

Consequence: a screen reader user opening the modal hears nothing. There is no "Build an expert agent with Dana dialog" announcement. The user does not know a dialog opened. They cannot identify the dialog by name. They cannot tell that focus moved into a modal context (vs. just a panel).

WCAG 4.1.2 (Name, Role, Value) Level A failure. Plus WCAG 2.4.3 (Focus Order) risk - without `aria-modal=true`, focus can escape to the background page during Tab cycling.

**Focus management is a related consequence.** Because the modal uses no dialog primitive, focus-trap (keeping Tab inside the modal) and focus-restoration (returning focus to the `+ New Agent` trigger on close) are almost certainly not implemented - libraries like Radix provide these for free, hand-rolled overlays rarely do. Automated keyboard testing here was inconclusive (synthetic Escape events did not reliably close the modal across runs), so the exact focus behavior needs a manual keyboard pass - but the structural absence of a dialog primitive makes a focus-management gap the likely default.

**Recommendation:** Add three attributes to the modal container: `role="dialog"` + `aria-modal="true"` + `aria-labelledby="<id of the H2>"`. Better: migrate this one modal to a `Radix UI Dialog` or `Headless UI Dialog` primitive - that delivers the role, the focus trap, AND focus restoration on close in a single change.

### Modal form fields are properly labeled

**Severity:** Win  
**Confidence:** High

Both inputs in the modal have correct programmatic labels: the name field is `<input id="agent-name">` with `<label for="agent-name">What do you call it?</label>`, and the role field is `<textarea id="agent-description">` with `<label for="agent-description">What's its role and expertise?</label>`. A screen reader announces each field's purpose correctly.

So the modal's only structural a11y gap is the missing dialog role - the form inside it is built right.

**Recommendation:** Keep. Once the dialog role is added, this modal is in good shape.

### Modal scan inherits all systemic findings (contrast, heading order, region)

**Severity:** Moderate  
**Confidence:** High

Axe on the modal-open Agents page returns 8 contrast failures (vs 7 with the modal closed - the extra come from modal copy), 1 heading-order, 13 unlandmarked regions. Same root tokens as the rest of the app.

**Recommendation:** Fixing the global `text-tertiary` + landmark wrapping fixes the modal too. No modal-specific work for these.

### Scenario 5: Curate workspace

*Per-agent conversation surface. Suggested tasks, message input, send button, attach (+) button, history, theme toggle.*

> **Approach:** Navigated to /dana-enterprise/dana-team/agent/<id>, scanned with axe, then probed message input + icon buttons for accessible names via JS.
> **Why:** This is where users spend the most time. Every interactive control here needs SR + keyboard support or daily work is blocked.

### Screen readers are not notified when Dana replies (no live region)

**Severity:** Major  
**Confidence:** High

Tested directly on v0.2.119: sent the message "what is one key task you help with?", Dana returned a full answer, and the conversation surface contained **zero** live regions - no `aria-live`, no `role="log"`, no `role="status"`, no `aria-busy` anywhere in the DOM, before, during, or after the response.

Consequence: a blind user sends a message and hears nothing back. The reply renders silently. They have no signal that an answer arrived, that one is streaming, or that the agent is still working. They would have to blindly navigate the page hunting for new text. Combined with Dana's known long silent execution times, a screen-reader user often cannot tell whether the product is working at all.

This is WCAG 4.1.3 (Status Messages, Level AA). For a conversational AI product - where the entire value is the model's response - failing to announce that response is arguably the single most damaging a11y gap, even though axe does not flag it (axe cannot detect a region that simply does not exist).

**Recommendation:** Wrap the message list in a polite live region: `<div role="log" aria-live="polite" aria-relevant="additions">`. Announce in-progress state with `aria-busy="true"` on the container while streaming, and consider an `aria-live="assertive"` status node for 'Dana is thinking' / 'Response ready'. This single container fixes the core chat-accessibility gap.

### Message input is correctly labeled

**Severity:** Win  
**Confidence:** High

The primary message input is a `contenteditable` element carrying `aria-label="Message input"`. A screen reader announces it correctly. The send (`aria-label="Send message"`) and attach (`aria-label="Add files or library"`) buttons beside it are also labeled.

**Recommendation:** Keep. The label could be even better as `aria-label="Message Dana about Alex"` to give task context, but the current is WCAG-compliant.

### All icon-only buttons in the workspace are labeled

**Severity:** Win  
**Confidence:** High

Inspected every icon-only button on the Curate surface: "More options", "New conversation", "History", "Dark" (theme), "Refresh tree", "Add files or library", "Send message", "Toggle Working Panel". All carry a meaningful `aria-label`. Zero unlabeled icon buttons.

**Recommendation:** Keep. This is the icon-button pattern the agent cards (Surface 3) should adopt.

### Suggested-task chips are real buttons

**Severity:** Win  
**Confidence:** High

The three suggested-task chips below the agent greeting ("What does solutions Architect involve?", "Key challenges in embedded Systems Strategy", "Walk me through your workflow") are genuine `<button>` elements - keyboard reachable and activatable. Verified on v0.2.119: `matches("a,button,[role=button],[tabindex]") === true`.

This is the right pattern, and it makes the agent-card gap (Surface 3) more conspicuous: the chips on this screen are buttons, but the cards that open this screen are not.

**Recommendation:** Keep. Build the agent cards the same way these chips are built.

### Page has no H1 (axe: `page-has-heading-one`)

**Severity:** Moderate  
**Confidence:** High

The Curate workspace shows "Alex • Solutions Architect · Embedded Systems Strategy" at the top - but this is styled text, not an `<h1>`. Axe reports `page-has-heading-one: Page should contain a level-one heading`.

SR users navigating by heading lose the anchor for "what page am I on". The agent name should be the H1.

**Recommendation:** Wrap the agent name + role in an `<h1>`. Style can stay identical.

### "Hi, I'm Alex" greeting is plain text - not labeled as page heading

**Severity:** Minor  
**Confidence:** Medium

The greeting "Hi, I'm Alex" is the visual anchor of the conversation. It is currently a plain `<div>`. Should be `<h2>` (or absorbed into the H1 above as the visible page heading).

**Recommendation:** Mark as `<h2>`. Keeps heading hierarchy: H1 (agent name in top bar) → H2 (greeting in conversation) → future H3 (each Dana response).

## Top fixes - Grouped by effort, ordered by impact

Eight fixes clear every confirmed finding in this report. The first five are small, localized changes shippable in a single sprint (rough total: 2-3 dev-days); the last three are a markup-structure pass. The three highest-impact items - the two Level A blockers and the missing chat live region - are also among the smallest.

### Ship this sprint (small, localized)

1. **Make agent cards keyboard-accessible.** Replace each card's wrapping `<div>` with `<button>` or `<a>`. Critical (Level A). The suggested-task chips on the Curate screen are already buttons - copy that pattern.
2. **Migrate the Create-Agent modal to a dialog primitive.** A `Radix UI Dialog` / `Headless UI Dialog` delivers `role="dialog"` + `aria-modal` + accessible name + focus-trap + focus-restoration in one change. Critical (Level A)
3. **Wrap the conversation in a live region.** `<div role="log" aria-live="polite">` around the message list so screen readers announce Dana's replies. Major The core chat-accessibility fix; one container.
4. **Darken the `text-tertiary` design token** from #787878 to ~#757575. One Tailwind config line; fixes the dominant always-visible contrast failure app-wide. Moderate
5. **Darken the #999999 gray family** - sidebar version label (2.75:1), Library supported-formats table (2.8-2.84:1), dropdown / switcher text (2.68:1). Promote to `text-gray-600`/`700`. Moderate

### Structural (markup pass)

1. **Add a `<main>` landmark to every route** (including Curate, which has none) and make `<nav>` / `<header>` consistent across routes. Clears `landmark-one-main` everywhere and most "region" violations. Moderate
2. **Fix heading hierarchy.** Add an `<h1>` per page (Curate has none); stop the Agents page skipping H1 -> H3. Moderate
3. **Adopt the icon-button + label pattern everywhere.** The top bar already does this right - extend the same discipline to any new control so the win does not regress. Minor

## Confidence & caveats - What this audit did and did not cover

> ⚠️ **Method**
> Single auditor, automated scan (axe-core 4.10.2 WCAG 2.1 AA + best-practice) + DOM inspection via direct CDP into bb-browser's controlled Chrome. The bb-browser MCP stdio bridge was unreachable - a small Python CDP driver (`cdp_driver.py` in the plan dir) bypassed it. Wall-clock: ~60 min including tooling setup.

> ⚠️ **Bias**
> The auditor wrote earlier UX reports on Dana and went into this scan knowing the app well. Confirmation bias risk: focused on the screens already mapped. Surfaces NOT scanned in this pass: sign-in page (would require log-out), Admin / Settings pages, error states, file-upload modal, OAuth integration popups. A follow-up scan should cover these.

> ⚠️ **Tool limits**
> Automated tools catch ~30% of WCAG issues. The remaining ~70% require manual SR testing with VoiceOver (macOS), NVDA (Windows), or TalkBack (Android) AND a real keyboard-only walkthrough by a user who relies on assistive tech. This audit added targeted manual DOM/ARIA probes (keyboard operability, dialog role, live regions, label associations) on top of axe, but did not run a real assistive-technology pass. Findings here are the lower bound - real impact is likely worse, not better.

> **Confidence**
> Headline confidence: **Medium**. Specific findings:
> - **High confidence**: cross-source observations (axe + DOM inspection + visual screenshot all confirm), every one re-verified against live DOM on v0.2.119. Examples: agent cards are `<div>`, modal has no dialog role, modal form fields are labeled, suggested-task chips are buttons, contrast ratios are exact axe measurements. The live-region gap was tested end to end - a real message was sent, Dana replied, and zero live regions existed in the DOM.
> - **Medium confidence**: state-dependent observations not reproducible in the closed-state scan. Example: dropdown / workspace-switcher text contrast (only rendered while the menu is open; the ~3.71:1 switcher figure is from v0.2.116 and was not reconfirmable on v0.2.119). Modal Escape-to-close + focus-return could not be reliably tested via automation and need a manual keyboard pass.

### Surfaces NOT in this audit

- Sign-in / sign-out / SSO flows (would log out current session)
- Admin pages (Organization Settings, Team Settings, integration management)
- Error states and toast notifications
- File upload flow (drag-drop a11y, progress announcement)
- Dark mode (only inspected light mode contrast)
- Mobile viewport / touch a11y (desktop only)
- Reduced-motion compliance (animations not tested)
- Forms with validation errors (form-error announcement to SR)

### Criteria NOT yet tested (do not read "no finding" as "passes")

This pass covers only the criteria that axe plus targeted DOM probes can evaluate: roughly 1.3.1, 1.4.3, 2.1.1, 2.4.6, 4.1.2, and 4.1.3.

The rest of WCAG 2.1 AA was not evaluated and needs a manual pass. The criteria most likely to surface real issues for Dana:

| Criterion | Why it matters for Dana | Status |
| --- | --- | --- |
| 2.4.7 Focus Visible | Is there a visible focus ring when tabbing? Stylesheet has focus rules and no `outline:none`, so likely OK - but unconfirmed visually. | Probable pass, unconfirmed |
| 1.4.11 Non-text Contrast | Icon buttons, input borders, the send button against their backgrounds. | Not tested |
| 2.1.2 No Keyboard Trap | Once focus enters the modal or working panel, can it get out with the keyboard? | Not tested |
| 1.4.10 Reflow / 1.4.4 Resize | Does the layout survive 200% zoom / a 320px viewport without horizontal scroll? | Not tested |
| 1.4.1 Use of Color | Is any state (agent status dot, validation) conveyed by color alone? | Not tested |
| 2.4.3 Focus Order | Does Tab order match visual order, especially in/around the modal? | Not tested |
| 3.3.1 / 3.3.3 Error Identification | When the create-agent form rejects input, is the error announced to SR? | Not tested |
| 1.2.x Time-based Media | Any onboarding video / help carousel captions. | Not tested |


### Open questions

- Does Dana support a dark mode contrast check? Initial screenshots show theme toggle exists but this audit covered light mode only.
- How does the platform handle `prefers-reduced-motion`? Not tested.
- Are there any data tables or charts in admin pages that would need `<th scope>` / `aria-describedby`?

## Appendix - Raw axe-core output

Full JSON for each surface scan lives in the plan dir at `plans/260528-1911-dana-accessibility-audit/evidence/`. Two passes are kept: the original v0.2.116 scan and the v0.2.119 re-verification.

**v0.2.119 re-verification (current):**

- `axe-library-v119-clean.json` - 3 violations, 32 passes
- `axe-agents-v119-clean.json` - 4 violations, 27 passes
- `axe-curate-v119-clean.json` - 4 violations, 23 passes

**Original v0.2.116 scan:** `axe-library-baseline.json`, `axe-agents-clean.json`, `axe-create-modal.json`, `axe-curate-clean.json`.

The verification verdict (`verification-verdict.md`) records every claim re-checked on v0.2.119 - what held, what was corrected, what changed. The Phase 0 baseline file (`phase-00-baseline.md`) documents the toolchain validation.
