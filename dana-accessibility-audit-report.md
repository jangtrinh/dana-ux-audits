---
title: Dana - Accessibility Audit
subtitle: WCAG 2.1 AA scan across Shell, Library, Agents, Create-Agent modal, Curate - v0.2.116
report_type: a11y-audit
product: Dana
product_url: https://dana.aitomatic.com
date: 2026-05-28 19:11 - 20:30 +07:00, Asia/Saigon
app_version: v0.2.116
workspace: Dana Enterprise -> Dana Team (Admin role)
method: axe-core 4.10.2 WCAG 2.1 AA + best-practice rules, injected via direct CDP into bb-browser's controlled
  Chrome. DOM + ARIA inspection via custom CDP driver. 4 surfaces scanned.
confidence: Medium
finding_counts:
  critical: 2
  major: 1
  moderate: 10
  minor: 1
  win: 4
  changed: 0
---

# Dana - Accessibility Audit

## How accessible is Dana?

Dana ships clean visual polish but assistive-technology support has structural gaps. A keyboard-only user cannot open an agent card. A screen reader gets no "dialog" announcement when the Create-Agent modal opens. Body text uses a semantic `text-tertiary` color that is **0.09 below** the WCAG 4.5:1 minimum, putting every agent card description, owner line, and tag below threshold.

None of these are visual-design failures. They are 4 to 8 lines of code each (correct ARIA roles, focused Tailwind color hex change, swap a `<div>` for a `<button>`). The fix surface is small. The current impact - keyboard and screen-reader users locked out of core flows - is large.

#### 3 biggest wins

- `<html lang="en">` set everywhere (WCAG 3.1.1)
- Icon-only buttons in the top bar carry proper `aria-label` (More options, History, Dark, Refresh tree)
- Curate's message input has `aria-label="Message input"` - screen reader announces it correctly

#### 3 biggest bugs

- **Agent cards are unreachable by keyboard** - rendered as plain `<div>` with no role, no tabindex (Critical, WCAG 2.1.1)
- **Create-Agent modal has no dialog semantics** - no `role="dialog"`, no `aria-modal`, no `aria-labelledby` (Critical, WCAG 4.1.2)
- **Color contrast fails systemically** - `text-tertiary` #787878 at 4.41:1, `text-gray-500` #999999 at 2.75:1 (Serious, WCAG 1.4.3)

## WCAG 2.1 AA per surface

> **Approach:** axe-core 4.10.2 injected via direct CDP into bb-browser's controlled Chrome, scanning each surface's rendered DOM. Rule set: wcag2a + wcag2aa + wcag21a + wcag21aa + best-practice.
> **Why:** Automated scanning catches roughly one third of WCAG issues but does so reliably and reproducibly. Numbers below are the lower bound. Manual SR + keyboard checks add the rest.

| Surface | Violations | Passes | Incomplete | Worst severity |
| --- | --- | --- | --- | --- |
| Library page | 3 | 32 | 1 | Critical |
| Agents list | 4 | 27 | 1 | Critical |
| Create-Agent modal | 3 | 30 | 1 | Critical |
| Curate workspace | 4 | 23 | 2 | Serious |


*Numbers above are axe violations (rules failed). "Incomplete" means axe could not auto-decide and a human must judge. Critical severity is assigned per WCAG impact + breadth across surfaces, not by axe directly.*

### Scenario 1: Shell - sidebar, top bar, theme toggle

*Persistent chrome around every page. Sidebar nav, top bar account / theme controls, version label. Same DOM on every surface, so every shell finding multiplies across the app.*

> **Approach:** Inspected DOM landmarks (header/nav/main/aside/footer presence) plus tabbed through visible focusable elements (21 found) and audited each top-bar icon button for accessible name.
> **Why:** Shell findings show up on every page. Fixing them once removes them from every surface scan.

### No `<main>`, `<nav>`, or `<header>` landmarks

**Severity:** Moderate  
**Confidence:** High

The document has zero landmark elements. Screen reader users rely on landmarks to jump (D-shortcut in NVDA, rotor in VoiceOver) between header / nav / main / aside / footer. Without them every page navigation is read top-to-bottom.

Axe flags this as `landmark-one-main` + `region` (14 unlandmarked content blocks on Library, 7 on Agents, 10 on Curate). WCAG 1.3.1 (Info and Relationships) and best-practice 4.1.2.

```
// Current
<div class="flex flex-row">
  <div class="sidebar">...</div>
  <div class="main-content">...</div>
</div>

// Should be
<div class="flex flex-row">
  <nav aria-label="Primary">...</nav>
  <main>...</main>
</div>
```

**Recommendation:** Wrap sidebar in `<nav aria-label="Primary">`. Wrap central content area in `<main>`. Wrap top bar in `<header>`. No visual change, large SR benefit. ~3 line change in the app shell layout component.

### Version label `v0.2.116` fails contrast at 2.75:1

**Severity:** Moderate  
**Confidence:** High

The bottom-left version label uses `text-gray-500` (#999999) on background `#fbfbfb` at 11px. Contrast ratio 2.75:1. WCAG 1.4.3 requires 4.5:1 for normal text.

Not a high-priority surface (version label is rarely the thing a user is looking for), but it is a quick fix in the same Tailwind config change that fixes the more important `text-tertiary` below.

**Recommendation:** Change `text-gray-500` to `text-gray-600` (#525252) for the version label specifically, or darken the Tailwind `gray.500` custom color to ~#6b7280 / 4.6:1.

### Workspace labels in sidebar fail contrast at 3.71:1

**Severity:** Moderate  
**Confidence:** High

The sidebar shows the active workspace as "Dana Enterprise / Dana Team" in `text-gray-400` (#808080) on light background. 3.71:1. Below the 4.5:1 minimum for body text.

These labels matter - they tell the user which workspace and team they are acting in. A user with low vision who cannot read them may act in the wrong workspace.

**Recommendation:** Promote workspace name text from `text-gray-400` to `text-gray-600` (or `text-secondary`). Workspace context is identification, not decoration - it deserves passing contrast.

### Theme toggle and account avatar are properly labeled

**Severity:** Win  
**Confidence:** High

Theme toggle button carries `aria-label="Dark"` (and presumably "Light" in dark mode). Account avatar opens with proper button semantics. Top-bar icon-only buttons consistently carry `aria-label`: "More options", "New conversation", "History", "Refresh tree".

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

**Recommendation:** Darken the `text-tertiary` design token from #787878 to `#757575` (4.52:1) or `#737373` (4.74:1) - one config change ripples to ~30 failing nodes app-wide.

### Section headers in opened menus fail contrast at 2.68:1

**Severity:** Moderate  
**Confidence:** Medium

When the Help & Feedback menu opens (bottom-left in the Library screenshot), the "HELP & FEEDBACK" and "ADMINISTRATION" section headers use `text-gray-500` (#999999) on `#f8f8f8` at 8.3pt - 2.68:1. WCAG 1.4.3 fails by a wide margin.

This affects the Account dropdown header too (no header but the email line is similarly low). The pattern is "uppercase micro text labeled as section header" - applied to a too-light gray.

**Recommendation:** Either bold the `text-micro` uppercase headers (bold text needs only 3:1) or darken to `text-gray-700` for menu section headers.

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

> **Approach:** Opened the modal, walked the entire parent chain of the modal's H2 heading via JS looking for role/aria-modal/aria-label/aria-labelledby on ANY ancestor. Tested Escape close behavior + post-close focus return location.
> **Why:** Modals are the highest-risk a11y surface because they require focus trap + dialog announcement + close affordance. Getting any one wrong breaks SR users.

### Modal has NO `role="dialog"`, NO `aria-modal`, NO accessible name

**Severity:** Critical  
**Confidence:** High

Walked the entire parent chain from the modal's `<h2>Build an expert agent with Dana</h2>` up to `<body>` looking for ANY ARIA attributes. Result: **zero**. The modal is a styled positioned `<div>`.

Consequence: a screen reader user opening the modal hears nothing. There is no "Build an expert agent with Dana dialog" announcement. The user does not know a dialog opened. They cannot identify the dialog by name. They cannot tell that focus moved into a modal context (vs. just a panel).

WCAG 4.1.2 (Name, Role, Value) Level A failure. Plus WCAG 2.4.3 (Focus Order) risk - without `aria-modal=true`, focus can escape to the background page during Tab cycling, confusing the user about whether the modal is still open.

**Recommendation:** Add three attributes to the modal container: `role="dialog"` + `aria-modal="true"` + `aria-labelledby="<id of the H2>"`. Three lines. Industry-standard `Radix UI Dialog` or `Headless UI Dialog` primitive does this automatically - migrate this one modal to a primitive and the issue is solved structurally.

### Focus does NOT return to `+ New Agent` trigger after Escape closes modal

**Severity:** Major  
**Confidence:** Medium

Opened modal, dispatched real Escape via CDP, observed that the modal closed but `document.activeElement` remained on an INPUT (presumably one inside the now-closed modal, or the first focusable on the page).

Best practice: when a modal closes, focus must return to the element that opened it. Otherwise the keyboard user loses context and has to Tab from page-start to find their way back.

Confidence Medium because CDP key-event timing is occasionally flaky. Worth a human-driven verification.

**Recommendation:** On modal close, restore focus to the trigger element. Standard Radix / Headless UI dialog does this for free. If hand-rolling: save `document.activeElement` when opening, restore via `.focus()` on close.

### Modal scan inherits all systemic findings (contrast, heading order, region)

**Severity:** Moderate  
**Confidence:** High

Axe on the modal-open Agents page returns 8 contrast failures (vs 7 clean - the extra failures come from modal copy), 1 heading-order, 13 unlandmarked regions. Same root tokens as the rest of the app.

**Recommendation:** Fixing the global `text-tertiary` + landmark wrapping fixes the modal too. No modal-specific work for these.

### Scenario 5: Curate workspace

*Per-agent conversation surface. Suggested tasks, message input, send button, attach (+) button, history, theme toggle.*

> **Approach:** Navigated to /dana-enterprise/dana-team/agent/<id>, scanned with axe, then probed message input + icon buttons for accessible names via JS.
> **Why:** This is where users spend the most time. Every interactive control here needs SR + keyboard support or daily work is blocked.

### Message input has `aria-label="Message input"`

**Severity:** Win  
**Confidence:** High

The primary text input area carries an explicit `aria-label`. SR announces "Message input, edit text". User immediately knows what the field is for.

**Recommendation:** Keep. The string could be even better as `aria-label="Message Dana about Alex"` to give task context, but the current is WCAG-compliant.

### Top-bar icons all have `aria-label`

**Severity:** Win  
**Confidence:** High

Inspected the five icon-only buttons in the top bar: More options, New conversation, History, Dark (theme), Refresh tree. Every one has a meaningful `aria-label`. Zero icon-only buttons without names.

**Recommendation:** Keep. Document the pattern in the engineering README and apply it to the attach (+) button and send (^) button at the bottom (need to verify those - this audit did not isolate them).

### Suggested-task buttons ('What does solutions Architect involve?') - role unverified

**Severity:** Moderate  
**Confidence:** Medium

The 3 suggested task chips below the agent greeting look clickable. They have button-like styling. This audit did not deep-inspect their DOM semantics. If they are `<div>` like the agent cards, the same WCAG 2.1.1 blocker applies.

**Recommendation:** Verify these are `<button>` elements. If not, swap. Same fix pattern as agent cards.

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

## Ranked by severity x breadth x effort

1. **Make agent cards keyboard-accessible.** Replace each card's wrapping `<div>` with `<button>` or `<a>`. Critical Single-component change. Unblocks every keyboard user.
2. **Add dialog semantics to Create-Agent modal.** Three attributes: `role="dialog"` + `aria-modal="true"` + `aria-labelledby="<h2-id>"`. Better: migrate to Radix Dialog primitive. Critical
3. **Darken `text-tertiary` design token** from #787878 to ~#757575. One Tailwind config line. Fixes ~30 contrast failures across all surfaces in one commit. Major
4. **Add `<main>` + `<nav>` + `<header>` landmarks** to the app shell. ~5 line layout change. Removes 30+ "region" violations app-wide and gives SR users jump-anchors. Moderate
5. **Restore focus on modal close.** Save trigger, focus it after close. Built into modal primitives. Major
6. **Fix heading hierarchy.** Add `<h1>` for page title on every page. Demote or promote agent cards / greetings so hierarchy never skips. Moderate
7. **Darken `text-gray-500` for version label + section headers in menus.** Promote to `text-gray-700` on these specific elements. Moderate

## What this audit did and did not cover

> ⚠️ **Method**

> ⚠️ **Bias**

> ⚠️ **Tool limits**

> **Confidence**
> - **High confidence**: cross-source observations (axe + DOM inspection + visual screenshot all confirm). Examples: agent cards are `<div>`, modal has no role, contrast ratios are exact axe measurements.
> - **Medium confidence**: single-method observations. Example: focus-return on modal close test was CDP-only and known to have flaky timing.

### Surfaces NOT in this audit

- Sign-in / sign-out / SSO flows (would log out current session)
- Admin pages (Organization Settings, Team Settings, integration management)
- Error states and toast notifications
- File upload flow (drag-drop a11y, progress announcement)
- Dark mode (only inspected light mode contrast)
- Mobile viewport / touch a11y (desktop only)
- Reduced-motion compliance (animations not tested)
- Forms with validation errors (form-error announcement to SR)

### Open questions

- Does Dana support a dark mode contrast check? Initial screenshots show theme toggle exists but this audit covered light mode only.
- How does the platform handle `prefers-reduced-motion`? Not tested.
- Are there any data tables or charts in admin pages that would need `<th scope>` / `aria-describedby`?

## Raw axe-core output

Full JSON for each surface scan lives in the plan dir at `plans/260528-1911-dana-accessibility-audit/evidence/`:

- `axe-library-baseline.json` - 3 violations, 32 passes
- `axe-agents-clean.json` - 4 violations, 27 passes
- `axe-create-modal.json` - 3 violations, 30 passes
- `axe-curate-clean.json` - 4 violations, 23 passes

The Phase 0 baseline file (`phase-00-baseline.md`) documents the toolchain validation: how axe-core was injected, what was tested, and the go decision criteria.
