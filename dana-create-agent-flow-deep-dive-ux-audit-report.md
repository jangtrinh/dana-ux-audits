---
title: Dana - Create-Agent Flow
subtitle: Deep-dive UX audit · v0.2.114 · UX scope only
report_type: ux-audit
product: Dana
product_url: https://dana.aitomatic.com
date: 2026-05-27 20:48 – 21:30 +07:00, Asia/Saigon
app_version: v0.2.114
workspace: Dana Enterprise → Dana Team (Admin role)
method: Single-flow heuristic deep-dive, 9 sub-scenarios. Entry points / modal init / help carousel /
  tab order / visibility dropdown / validation edges / attach file / cancel paths / happy path landing.
confidence: Medium - one auditor, no outside-user run. Findings are reproducible single-observation grade.
finding_counts:
  critical: 1
  major: 0
  moderate: 8
  minor: 5
  win: 6
  changed: 0
---

# Dana - Create-Agent Flow

## Summary - TL;DR

> **Approach:** Compressed nine sub-scenario walks into one summary plus the top wins and bugs that stand out across the whole flow.
> **Why:** Readers want the headline first. This page must point them to the few things that matter before they read the per-scenario detail.

Dana's create-agent flow is **mostly good** with strong helper content hidden behind a small affordance, but has two bugs that matter to first-time users: **silent data loss on click-outside** and a **form/Dana label mismatch** that mislabels the user's role text as "Industry."

#### UX wins

- **Help (?) panel** is a 3-slide carousel that explains cognitive ontology + sources + experts-reasoning. Best Dana onboarding content lives here.
- **Visibility dropdown** has icon + name + descriptive subtitle per option (Private / Team / Organization).
- **Sticky-flyout regression is fixed** - hovering the left rail Agents icon shows a one-click path to "+ New Agent" and dismisses cleanly on mouse-leave.
- **Adaptive Dana** - the role text is read for domain inference and clarifying questions adapt accordingly (UX-themed for UX-audit role).
- **Happy-path transition** - pre-rendered Curate surface with "Preparing your workspace..." loader sets correct expectation.

#### Top bugs to fix

- **Click outside silently discards typed data** - no confirm prompt, no draft preservation.
- **Form/Dana label mismatch** - form asks "What's its role and expertise?" but Dana interprets the input as *Industry* and defaults Role to "General Assistant."
- **Escape key does not close the modal** - violates standard dialog convention; click-outside is the only keyboard-free dismissal.
- **Tab order skips the disabled "Create Agent" CTA** - once enabled, keyboard users may not find the primary action via tab nav.
- **How-it-works Step 1 card looks clickable but isn't** - the most prominent "Create an Agent" visual on Home does nothing on click.
- **Help (?) panel is too easy to miss** - the strongest framing of Dana's value lives behind a 24px icon.
- **No validation feedback** for long names (86+ chars accepted, no counter), duplicate names (matches existing "Alex" passes pre-submit), or attach-file format/size limits.

### Scenario 1: Entry points to Create Agent

*Cataloged 4 distinct paths from Home to the Create Agent modal. The fastest is 1 click via a flyout most users won't discover; the most prominent visual affordance is broken.*

> **Why this matters:** Entry-point discovery friction starts before the modal opens. If the most prominent visual is a dead-click, the user's first impression is "is this broken?"

### How-it-works Step 1 looks like an entry point but isn't

**Severity:** Moderate  
**Confidence:** High

First-time SME user clicks the most prominent "Create an Agent" visual and nothing happens. Discovery friction; risk of "Is this app broken?" first impression.

**Recommendation:** Either (a) make Step 1 actually open the Create modal on click, or (b) visually de-emphasize the card framing so it doesn't read as an affordance (drop the bordered container, lighten the icon).

### Sticky-nav flyout regression is fixed

**Severity:** Win  
**Confidence:** High

The Agents hover flyout from prior audits no longer persists after mouse-leave. Clean dismiss.

### Scenario 2: Modal initial state

*Modal opens in place (URL unchanged), centered, with a dim background. Title and tagline frame Dana as differentiated.*

> **Why this matters:** First impressions of the form drive whether users start filling it. Auto-focus, clear labels, and a visible primary CTA reduce activation friction.

| Element | Behavior | Verdict |
| --- | --- | --- |
| Title: "Build an expert agent with Dana" | Frames the value clearly | Win |
| Tagline: "For domains where proprietary knowledge wins." | Differentiates from generic AI | Win |
| Auto-focus on name field | Cursor in field on open | Win |
| Name placeholder: "e.g. Maya" | Concise example | Win |
| Role placeholder: full sentence example | Models what good input looks like | Win |
| Role placeholder rotates between renders | 3+ unique examples observed (export-compliance / reliability engineer / medical-device QA / semiconductor process / etc.) | Minor |
| Visibility default: Private | Conservative default | Minor (see Scenario 5) |
| Disabled "Create Agent" with no microcopy | No hint about what's required to enable | Minor |
| "Import agent" link below the primary CTA | Unusual placement; visual weight competes with CTA | Minor |


### Role placeholder rotates between renders - intent unclear

**Severity:** Minor  
**Confidence:** Medium

Each modal open shows a different example role (compliance analyst, reliability engineer, medical-device QA, etc.). Probably intentional to signal "Dana works for any vertical" - but a user who notices the change between attempts might feel the form is shifting under them.

**Recommendation:** Decide explicitly: rotate (and add subtle "see more examples" affordance), or freeze one strong example.

### Scenario 3: Help (?) carousel - Dana's strongest value framing is hidden

*Clicking the small (?) top-right of the modal expands a side panel with a 3-slide carousel: "What sets Dana-built agents apart." This is the best onboarding content in the app - and most users will never see it.*

> **Why this matters:** The value framing of "what makes Dana different from ChatGPT" lives in the help carousel. If users never find it, they evaluate Dana against the wrong baseline.

| Slide | Title | Content |
| --- | --- | --- |
| 1 | Powered by a cognitive ontology | KNOWLEDGE (Concepts/Entities/Facts) + REASONING (Rules/Policies/Judgment) → "Cognitive ontology - Knows what it knows and how to think about it" |
| 2 | Your sources become its knowledge | YOUR SOURCES (Export Manual.pdf, EAR Regulations.pdf, Shipping SOP.docx) → KNOWLEDGE (ECCN classifications, License exceptions, Restricted destinations) |
| 3 | Your experts' reasoning, codified | Compliance expert quote → codified as `rules/encryption-classification.yaml` + `skills/dual-use-judgment.md` |


### Best onboarding content is hidden behind a 24px (?) icon

**Severity:** Moderate  
**Confidence:** High

The carousel is the strongest "why Dana not ChatGPT" framing in the product. For a technical-evaluator persona this is the "aha" moment. The small (?) trigger makes it easy to miss; a first-time SME may never discover it.

**Recommendation:** Either (a) auto-show the panel on first modal open for the session, (b) replace (?) with a labeled "How Dana works ·" button alongside the tagline, or (c) inline the cognitive-ontology framing as a banner at the top of the modal.

### Carousel content quality is excellent

**Severity:** Win  
**Confidence:** High

Visual storytelling with diagrams + named-example files + expert quote. Communicates Dana's differentiation in 3 slides without jargon dump.

### Pagination dots aren't clickable for direct slide nav

**Severity:** Minor  
**Confidence:** Medium

Only left/right arrows navigate. Direct dot-click is a common pattern and would be cheap to add.

### Scenario 4: Tab order & keyboard behavior

*Keyboard navigation has two violations of common dialog conventions: Escape doesn't close, and the primary CTA is skipped in tab order when disabled.*

> **Why this matters:** Keyboard nav is the test of whether the form is usable beyond mouse-first interactions. Tab order and Escape behavior are baseline-accessibility expectations.

**Observed tab order from initial focus:**

1. Name field (auto-focus on open)
2. Role textarea
3. Attach File button
4. Visibility dropdown
5. **(skip)** - Create Agent (disabled) is NOT in tab order
6. Import agent link
7. (Exits modal - focus goes off-screen)

### Escape key does NOT close the modal

**Severity:** Moderate  
**Confidence:** High

Pressed Escape from multiple focus states (textarea, dropdown, blank focus): modal stayed open. Standard dialog convention is Escape = close (or Escape = cancel). Click-outside DOES close (see Scenario 8), so the click and keyboard behaviors are inconsistent.

**Recommendation:** Implement Escape → close (and trigger the same confirm-discard prompt recommended for Scenario 8 when data is present).

### Disabled "Create Agent" CTA is skipped in tab order

**Severity:** Moderate  
**Confidence:** High

Tab from Visibility jumps to "Import agent" (a secondary tertiary action), skipping the primary CTA entirely. When fields fill and the button enables, keyboard users may continue tabbing past it without noticing the action is now available.

**Recommendation:** Keep the CTA in tab order even when disabled (use `aria-disabled` + `tabindex="0"` patterns rather than HTML `disabled`). Add a focus state that hints why it's disabled.

### Scenario 5: Visibility dropdown

*Three options, each with icon + name + descriptive subtitle. Strong recognition-over-recall design.*

> **Why this matters:** Visibility is the only required pre-submit decision. Defaults shape what users ship; option clarity shapes whether they ship the right thing.

| Option | Icon | Description |
| --- | --- | --- |
| Private (default) | 🔒 lock | "Only you can see this agent" |
| Team | 👥 people | "Visible to your team members" |
| Organization | 🏢 building | "Visible to everyone in the organization" |


### Each option has icon + name + "visible to whom" description

**Severity:** Win  
**Confidence:** High

No tooltip ambiguity. User sees who can access at a glance.

### Default "Private" may be wrong for team workspaces

**Severity:** Minor  
**Confidence:** Medium

In a workspace with shared agents (Alex, Tara by another team member), most newly created agents likely belong to the team. "Private" as default produces invisible-by-default behavior; user must remember to set Team/Org.

**Recommendation:** Default to "Team" for non-solo workspaces; keep "Private" as default only for personal accounts.

### Escape doesn't dismiss the open dropdown

**Severity:** Minor  
**Confidence:** Medium

Same pattern as the modal-level Escape issue. Click the dropdown header again or click outside (still within modal) to dismiss.

### Scenario 6: Validation edge cases

*Pre-submit validation is essentially absent. Long names, duplicate names, and edge-input cases all pass without warning.*

> **Why this matters:** Edge inputs (long names, duplicates, special chars) reveal whether the form respects users' time. Silent failures here become support tickets later.

| Input | Behavior observed | Verdict |
| --- | --- | --- |
| Empty (both fields) | CTA disabled, no microcopy | Minor |
| Name = 86 characters | Accepted; field scrolls horizontally; no warning or counter | Moderate |
| Name = "Alex" (existing agent name) | Accepted; CTA enables; no pre-submit duplicate warning | Moderate |
| Special chars / emoji / whitespace-only | Not tested in this session | - |


### Long agent names accepted with no warning or counter

**Severity:** Moderate  
**Confidence:** High

An 86-char name was accepted. May break Quick Access card layout, agent greeting copy, and the AGENT.md identity section. No counter shows N/100 (or whatever the limit is).

**Recommendation:** Define a max length (suggest 32-50 chars). Show "N/50" counter as user types past 80% of limit. Block input past max.

### Duplicate agent names pass pre-submit validation

**Severity:** Moderate  
**Confidence:** High

Typing "Alex" (matching the existing agent) does not produce a warning. The CTA enables and only post-submit feedback would tell the user the name is taken.

**Recommendation:** Async-validate name uniqueness on blur. Show inline "Name already taken - try another" hint near the field.

### Scenario 7: Attach File affordance

*A paperclip button next to "Got a document describing the agent profile?" Hover gives no tooltip; click opens a native OS file picker (invisible to automation in this session, so post-attach behavior not directly tested).*

> **Why this matters:** Attach File is the "skip the chat path" option. If its outcome is unexplained, users won't trust it - and the team loses an activation accelerant.

### No format / size / outcome explanation on hover or near the button

**Severity:** Moderate  
**Confidence:** High

Open questions for the user pre-click: What file types? What's the size limit? What does it actually do - pre-fill the role text? Skip the Curate chat? Seed the ontology? None of this is explained.

**Recommendation:** Add a one-sentence helper below the field: "We'll pre-fill the role and seed the ontology from this file. Supports PDF, DOCX, MD (max 10MB)."

### Visual weight competes with form fields

**Severity:** Minor  
**Confidence:** Medium

Attach File and Visibility share horizontal layout zone. Attach File is a tertiary action (optional helper) but visually equal to Visibility (required pre-submit decision).

**Recommendation:** Demote Attach File to a smaller inline link near the role textarea: "or attach a profile document". Reserve the prominent button styling for the primary path.

### Scenario 8: Cancel paths & data loss

*Three dismissal methods, three different behaviors. The most accidental-prone (click outside) silently destroys typed data.*

> **Why this matters:** Accidental dismissals are common in modals. How the form treats them defines whether users feel safe filling out long-form fields.

| Dismissal method | Closes modal? | Confirms before discard? |
| --- | --- | --- |
| X button (top-right) | Yes | No |
| Click outside modal | Yes | No - silent data loss |
| Escape key | No | n/a |


> 🚨 **Critical: click-outside discards typed data with no warning**
> Reproduced: typed *"My Important Agent Name"* + *"A detailed role description I spent 5 minutes writing."* → accidental click outside modal → modal closed silently → reopened → both fields blank.
> For a creative-input form where users invest thought into role descriptions (full sentences modeled by the placeholder), silent destruction on accidental click is a major risk.

### Click outside modal silently discards typed data - no confirm prompt, no draft preservation

**Severity:** Critical  
**Confidence:** High

**Recommendation:** Either (a) show a "Discard your draft?" confirm modal when fields are non-empty and user clicks outside, or (b) auto-preserve form state per-session so re-opening shows the last typed values. Ideally both: confirm + preserve.

### Scenario 9: Happy path & Curate landing

*After valid submit, the modal closes and routes to `/agent/<uuid>/curate`. Pre-rendered surface elements appear during a short "Preparing your workspace..." loader. Then Dana streams her first turn. The hand-off is fast and well-framed - but reveals a form/Dana label mapping bug.*

> **Why this matters:** The transition from form to Curate is where Dana hands off authority. A smooth hand-off shapes everything that comes after.

**Test input:**

```
Name:    AuditTestBot
Role:    A test agent for the UX audit of the create-agent flow.
Visibility: Private (default)
```

**What Dana wrote in her opening Activity block:**

```
The agent is described as:
- Name: AuditTestBot
- Role: General Assistant
- Industry: A test agent for the UX audit of the create-agent flow
```

> 🚨 **Form/Dana label mismatch - user's role becomes "Industry"**
> The form asks *"What's its role and expertise?"* and the user types role-text. Dana then writes the user's input as **Industry** and assigns **"General Assistant"** as the Role. The label the user filled does not match the label Dana uses downstream.
> This is a data-mapping bug, not a copy bug - the same field becomes a different conceptual thing depending on where you read it.

### Form label "role and expertise" is interpreted by Dana as "Industry"

**Severity:** Moderate  
**Confidence:** High

Either the field is mislabeled in the form, OR the form should collect both Role and Industry separately (matches the Home "Step 1" promise: "name your AI specialist and define its role and industry"). Today the user can't set Role explicitly - it defaults to "General Assistant" and their input fills the Industry slot.

**Recommendation:** Split into two fields: "Role" (e.g., "Solutions Architect") + "Industry / Domain" (e.g., "Embedded systems"). OR rename the single field to match downstream usage. OR rename Dana's downstream usage to match the form. The form copy and the agent spec must agree.

### Transition to Curate is fast & well-framed

**Severity:** Win  
**Confidence:** High

Surface elements (header, file tree pane, working panel, chat input) render immediately. "Preparing your workspace..." loader sets correct expectation while Dana initializes.

### Dana's first turn adapts to the agent's inferred domain

**Severity:** Win  
**Confidence:** High

The role text "UX audit of the create-agent flow" produced UX-themed clarifying questions ("WCAG, Nielsen's heuristics", "User research, wireframes, design specs"). Strong perceived-intelligence moment.

### Agent-removed toast on delete

**Severity:** Win  
**Confidence:** High

Green "Agent removed" toast appears at the bottom of Home after successful deletion. Closes prior gap where delete had no immediate feedback.

## Punch List - Top 7 fixes

> **Approach:** Ranked recommendations by severity x user-frequency x ease. Click-outside data loss (Critical) leads because it affects every user, every session.
> **Why:** Engineers need an ordered list, not a wishlist. Severity alone doesn't capture how many users hit the issue or how cheap the fix is.

Ranked by severity × user-frequency × ease.

1. **Confirm-before-discard on click-outside.** Critical Modal should not silently destroy typed data. Add a "Discard your draft?" prompt when fields are non-empty. Optionally auto-preserve per-session. *Cost: 0.5d.*
2. **Align form label with Dana's downstream usage.** Moderate Either split into "Role" + "Industry" fields, or rename the single field to match what Dana writes ("Industry / Domain"). Currently user's input gets mislabeled. *Cost: 0.5-1d depending on whether you split or rename.*
3. **Make Escape close the modal.** Moderate Standard dialog convention. Bundles with #1 (Escape triggers the discard-confirm if fields are dirty). *Cost: <0.5d.*
4. **Make "Step 1 - Create an Agent" on Home actually clickable.** Moderate The most prominent "Create an Agent" visual is dead. Either wire it to open the modal or de-emphasize the card framing. *Cost: <0.5d.*
5. **Surface the help carousel more prominently.** Moderate Best onboarding content is hidden behind a small (?). Auto-show for first-time users, or replace the icon with a labeled button alongside the tagline. *Cost: 0.5d.*
6. **Add validation feedback - long names, duplicate names, attach-file format hint.** Moderate Inline counter on name (after 80% of max), async duplicate check on blur, helper text for Attach File (formats + size + outcome). *Cost: 1d.*
7. **Keep "Create Agent" CTA in tab order when disabled.** Moderate Use `aria-disabled` + `tabindex="0"` instead of HTML `disabled` so keyboard users can find the primary action. *Cost: <0.5d.*

## Caveats - Confidence & caveats

### Method & bias

One auditor, one session, no outside-user test. Findings are reproducible single-observation grade. An unscripted run by someone unfamiliar with Dana would raise confidence on copy-discovery findings (especially the help carousel + "How it works Step 1" dead-click) since those depend on first-time perception.

### Surfaces not reached / not tested

- Special characters, emoji, RTL text, very short (1-char) names in name field
- Whitespace-only role text submission
- Network failure during create
- Slow network during create (does CTA show loading spinner?)
- Native file picker post-attach state (file selected → modal preview / removal)
- Visibility option behavior post-create (can you change it later?)
- "Import agent" link destination (clicked once, no visible change - opens file picker or modal?)
- Dark-mode rendering of modal
- Mobile / responsive layout

### Wall-clock

~42 minutes of browser work across 9 sub-scenarios. Roughly 4-5 minutes per scenario plus cleanup.

### Highest-confidence findings (least bias)

- Click-outside data loss - reproduced with verbatim typed string
- Escape doesn't close modal - tested from multiple focus states
- Form/Dana label mismatch - quoted directly from Dana's Activity block
- Long name + duplicate name pass pre-submit - demonstrated
- Sticky-flyout regression fixed - confirmed dismiss behavior

Dana create-agent flow UX audit · 2026-05-27
