---
title: Dana - Initial Onboarding Walkthrough
subtitle: Heuristic walkthrough audit (predecessor to the cross-persona JTBD audit) - 2026-05-27
report_type: ux-audit
product: Dana
product_url: https://dana.aitomatic.com
---

# Dana - Initial Onboarding Walkthrough

- **Date:** 2026-05-27 14:08 (+07:00, Asia/Saigon)
- **Auditor lens:** UX-expert posing as new onboarding user
- **App version:** v0.2.114
- **Workspace:** Dana Enterprise → Dana Team (logged-in)
- **Screenshots dir:** `../dana-onboarding-ux-audit-screenshots/` (alongside this report)

## 1. Methodology

- **Type:** heuristic walkthrough (Nielsen 10 + NN/g first-use patterns).
- **Journey audited:** post-login Home → Your Agents → Create Agent → Curate Knowledge → Integrations (list + add) → Library → Help / Admin / Dark mode / User menu.
- **Caveat (account bias):** audit ran on a tainted account (2 pre-existing agents Alex/Tara, integration provisioned, "How it works" still visible). Empty-state / signup / email-verify / first-login findings are inferred from cues, not observed. Recommend a follow-up pass on a fresh signup before locking the roadmap.
- **Severity scale (NN/g):**
- S0 catastrophic - blocks task or causes data loss
- S1 major - measurable conversion / activation loss
- S2 moderate - friction / confusion, no blocker
- S3 minor - polish / nit
- **Scoring note:** severity assumes a representative new-onboarding user who has never seen Dana before. A power user mostly bypasses these.

## 2. TL;DR

Dana's create-and-curate path is **conceptually strong** (auto-greeting from Dana, multi-choice clarifiers, file-attached agent profile, sensible empty Library) but the **shell around it is rough**:

- Visual hierarchy on Home buries the activation message under 320px of dead space.
- The left navigation has **two sticky-popover bugs** that occlude content across navigations and don't recover when the theme changes.
- The Integrations gallery is **3 raw primitives**, not a curated catalog - new users land on jargon (MCP / OAuth / self-hosted) with no one-click path.
- The Curate workspace exposes the **internal filesystem** (`AGENT.md`, `ONTOLOGY.md`, `curate_outputs`) before the user has a chance to form a mental model of what an agent is.
- Dana's first turn in Curate is a **wall of markdown** with multi-choice questions rendered as plain text - no clickable options.

Fix-impact roughly: ship the popover dismissal + integration gallery + Home hierarchy first; the rest is incremental.

## 3. Findings (per step)

### 3.1 Home - `/#/dana-enterprise/dana-team`

Screenshot: `01-home-post-login.png` · annotated: `annotated-01-home.png`

| # | Sev | Heur | Observation | Recommendation |
| --- | --- | --- | --- | --- |
| F1 | S1 | A/V/FT | ~320px of empty whitespace above the "Your knowledge, always on." hero - first-paint feels broken / loading | Lift hero up; tighten top padding to ~64–96px; reserve the dead band for a stateful welcome strip (next step, % complete, or active agent CTA) |
| F2 | S1 | FT/V | "How it works" appears *below* Quick Access - new users meet content they can't yet act on (agents Alex/Tara) before they meet the instructions | For accounts with 0 agents, swap order: How-it-works above the fold, "Quick Access" hidden until ≥1 agent exists |
| F3 | S2 | M/C | Card labels are descriptive ("Your Agents - Manage and chat with your AI agents"), not action-driven. First-run user wants a verb, not a noun. | Replace "Your Agents" card with "Create your first agent →" for empty-state accounts; keep noun framing only after activation |
| F4 | S3 | A | Left rail truncates "Dana T..." / "Integrat..." / "Admini..." with ellipsis even though horizontal space exists | Either expand the rail by ~24px or drop the redundant label entirely (icons + tooltips suffice) |
| F5 | S3 | V | Dark-mode toggle uses moon-only icon - affordance is fine, but a tooltip would help | Add `title`/aria for sun/moon transition |


### 3.2 Your Agents list - `/#/dana-enterprise/agents`

Screenshot: `02-agents-list.png` / `02b-agents-list-clean.png` · annotated: `annotated-02-agents-sticky-flyout.png`

| # | Sev | Heur | Observation | Recommendation |
| --- | --- | --- | --- | --- |
| F6 | **S1** | E/V | **Sticky nav flyout** - hovering "Agents" reveals a submenu with Alex / Tara / + New Agent, but the submenu **persists after navigation and after clicking elsewhere**, occluding the Alex card on the agents page | Dismiss flyout on: route change, `mouseleave` of nav, click outside, `Escape`. Add a 200ms close delay only on the flyout itself |
| F7 | S2 | C/F | Three "+ New Agent" entry points visible at once (nav flyout, top-right header CTA, nothing on the agents-list canvas) - fine for power users, noisy for new ones | On empty list, replace flyout/header with one prominent canvas CTA; collapse to header-only once ≥1 agent exists |
| F8 | S2 | D/M | Agent cards show "by Nhi Dam" - for me as new user the author has no meaning. Tooltip / link / explanation absent. | Either suppress when author is the workspace creator, or surface as a small avatar with hover tooltip ("Created by Nhi Dam · Workspace admin") |
| F9 | S3 | F | "Import agent" lives both as a top-right secondary button **and** inside the Create modal (twice). Three entry points for the same low-frequency action. | Keep one entry in the New Agent modal only |


### 3.3 Create-Agent modal

Screenshot: `03-create-agent-modal.png`, `03b-create-agent-filled.png` · annotated: `annotated-03-create-agent.png`

| # | Sev | Heur | Observation | Recommendation |
| --- | --- | --- | --- | --- |
| F10 | S2 | C | Home onboarding promises **"Name your AI specialist and define its role *and industry*"** (Step 1 card), but the modal collects only a freeform name + freeform role/expertise. No industry field, no taxonomy. | Either drop "and industry" from the Home description, or add an Industry selector with sensible defaults (Semiconductors, Healthcare, Compliance, Finance…) - keep promise/payload consistent |
| F11 | S2 | R/V | After "Create Agent" the user is silently routed to a curate workspace - no "what happens next" framing in the modal. The 3-step Home card is the only signpost. | Add a one-line "Next: Dana will help you teach what they need to know" subtitle near the CTA, plus a mini stepper (1/3) in the modal header |
| F12 | S3 | E | Disabled "Create Agent" gives no inline hint about which field is required | Add "Both fields required" microcopy under the disabled button, or a per-field required marker |
| F13 | S3 | A | `Visibility: Private` button is a dropdown - there's no preview of the alternatives without clicking. For a one-option-most-of-the-time default this is fine, but if "Public/Team" exists, hint the selectable options inline | If only Private/Team, render as a 2-segment control (recognition > recall) |
| F14 | S3 | D | "Got a document describing the agent profile? Attach File" - fantastic affordance, but the format / what-it-does is unexplained. Will it skip the curation chat? Pre-fill ontology? | One-sentence helper: "We'll pre-fill the role and seed the ontology from this file." |


### 3.4 Curate workspace - `/#/.../agent/{uuid}/curate/conversation/{uuid}`

Screenshot: `04-curate-workspace.png`, `04b-curate-fullpage.png` · annotated: `annotated-04-curate.png`

| # | Sev | Heur | Observation | Recommendation |
| --- | --- | --- | --- | --- |
| F15 | **S1** | M | **File-tree pane exposes internal artifacts** (`AGENT.md`, `ONTOLOGY.md`, `curate_outputs/`) before the user knows what an ontology is. Leaky abstraction at the worst possible moment. | Hide the raw tree behind a "Files" tab for the first session; replace default-visible view with a human-readable summary card ("Aria - Role: …; Ontology: 0/4 sections drafted; Sources: 0"). Power-user toggle to reveal the markdown tree. |
| F16 | **S1** | R/F | Dana's first message contains 4 clarifying sections (Target Users / Scope / Depth / Source Materials) rendered as **plain markdown** with "Option A/B/C/D" multiple choice - but no actual selectors. User must hand-type their answers. Hick's law violated. | Render multi-choice options as clickable chips (`Option A`, `Option B` …). Save 60–90s per turn and reduce typo-driven dead-ends |
| F17 | S2 | V | "Use Aria" button is visible **before any curation has happened**. Clicking it at this stage would give an essentially untrained model. Risk of "this thing is broken" first-impression. | Disable "Use Aria" until ≥1 ontology section is drafted; show tooltip "Curate at least one knowledge section first" |
| F18 | S2 | R | URL is `/agent/dd5d0835-9a1a-462f-a439-9ba91902fbf7/curate/conversation/a4b19ecb…` - two opaque UUIDs. Not bookmarkable / shareable / memorable. | Use slugified agent name (`/agent/aria/curate/…`) with UUID retained as canonical alias |
| F19 | S2 | V | Working Panel on the right has 4 sub-tabs (Sources, Library, Uploaded - visible - plus more?), no empty-state guidance ("Add from Library" exists but no explanation what Library is here vs the main Library page) | Add one-line empty-state per tab, link "Open full Library →" |
| F20 | S3 | A | Top breadcrumb reads "Dana - Curating Aria" - fine, but no link back to Agents list; back-navigation relies on left rail | Make "Aria" a link to agent detail, "Dana" a link to home |


### 3.5 Integrations - list + add modal

Screenshots: `05-integrations.png`, `05b-new-integration-modal.png` · annotated: `annotated-05-integrations.png`

| # | Sev | Heur | Observation | Recommendation |
| --- | --- | --- | --- | --- |
| F21 | **S1** | M/FT | "Choose integration type" offers **3 raw primitives** (Database / MCP Server / Remote MCP Server (OAuth)) - no curated gallery of named apps (Notion, Linear, Slack, GitHub, Google Drive, Confluence…). New users land on jargon. Even though copy mentions "e.g., Notion, Linear" there's no one-click setup. | Lead with a tile gallery of branded integrations (10–20 popular MCPs / DB providers) → fall back to "Custom / Advanced" for the 3 primitives. Match the Claude / ChatGPT / Cursor integration UX pattern |
| F22 | S2 | M | Existing Jira integration shows `OAUTH · NOT SIGNED IN` badge + "Sign in to use" but no explanation of *what NOT-SIGNED-IN means* (admin added the connector, you the user still need to OAuth?) | Add inline microcopy: "Admin added this connector - sign in to grant your own access." Distinguish "workspace install" vs "personal auth" clearly |
| F23 | S2 | C | "Org wide" badge appears as a chip but is not interactive (looks clickable); also "Add to Agent" is disabled with no explanation | Disable visual chip styling for non-interactive labels; tooltip on disabled "Add to Agent" ("Sign in first") |
| F24 | S3 | D | Modal banner "Specify which integrations your team or organization can use." - passive voice / unclear actor. Is this you specifying, or the org admin? | Rewrite: "Add an integration your team can use." |


### 3.6 Library

Screenshot: `06-library.png`

| # | Sev | Heur | Observation | Recommendation |
| --- | --- | --- | --- | --- |
| F25 | S3 | + | **Positive:** Strong empty state - clear icon, "Your library is empty", supported-extensions table, file-size cap, "How Library works" link. NN/g-grade. | Keep as-is. Consider a sample-dataset CTA ("Try with example PDF") for the very-first-upload case |
| F26 | S3 | V | "Agent: All" filter visible but no "documents per agent" badge - unclear why a fresh user would scope by agent | Hide the agent filter until ≥1 doc exists |


### 3.7 Help / Admin / User menu / Dark mode

Screenshots: `07a-help-feedback.png`, `07b-administration.png`, `07c-user-menu.png`, `07d-dark-mode.png` · annotated: `annotated-07-user-menu-and-popovers.png`, `annotated-07d-dark-popover.png`

| # | Sev | Heur | Observation | Recommendation |
| --- | --- | --- | --- | --- |
| F27 | **S1** | E/U | **Popover stacking bug:** opening Help & feedback, then Administration, then User menu leaves **all three popovers open simultaneously**. Clicking elsewhere / pressing Escape does not dismiss them. | Single-popover policy: opening any popover closes the others; click-outside + Escape + route-change dismiss them. Tested via `body.click()` and Escape - neither worked. |
| F28 | **S1** | A11Y | In dark mode the popovers retain a light-style fill with near-black text → severe low-contrast (visually unreadable in screenshot `07d-dark-mode.png`). WCAG AA failure on `.popover-body` against dark surface | Audit `prefers-color-scheme: dark` tokens; popovers must consume the same `--surface-elevated` token as the rest of the dark theme |
| F29 | S2 | M/A | User menu shows email twice (as title and subtitle) - no display name, no avatar, no profile, no settings, no theme/locale, no API tokens | Add display name + workspace + role above email; collapse duplicate. Optional: "Account settings" link |
| F30 | S2 | F | Help & feedback menu has only: User Guide / Report a bug / Request a feature. No search, no in-app docs surface, no "Contact us" / "Status" link | Add a search-the-docs entry + status page link; keep the three explicit actions |
| F31 | S2 | C | Administration menu shows two scoped settings (Organization: Dana Enterprise / Team: Dana Team) - the active scope vs editable scope is unclear. Hovering an entry doesn't preview the page. | Indicate current scope inline (e.g., "Org Settings · Dana Enterprise (current)"); allow scope-pinning |
| F32 | S3 | V | Dark-mode toggle button label flips between "Dark" / "Light" but icon alone is non-obvious - fine for power users, mild friction for new | Add an aria-label that names the *next* state ("Switch to light mode") |


## 4. Top recommendations (do these first)

1. **Fix popover dismissal (F27)** - one line in the popover wrapper kills three bugs across Help/Admin/User-menu + the dark-mode contrast bug becomes less visible (S1 → S0 to fix).
2. **Hide the raw filesystem tree in Curate (F15)** - biggest mental-model trap. Replace with a human summary card; tree behind a "Files" tab.
3. **Render Dana's multi-choice questions as chips (F16)** - 60–90s saved per turn, fewer dead-ends. Hook into the existing markdown renderer to map `**Option X**` headings to selectable buttons.
4. **Integrations gallery (F21)** - even 10 curated MCPs (Notion / Linear / Slack / GitHub / Google Drive / Confluence / Jira / Atlassian / Salesforce / Asana) drop new-user activation friction substantially.
5. **Home hierarchy (F1+F2)** - lift hero, swap "How it works" to lead on empty accounts.
6. **Sticky nav flyout (F6)** - straightforward dismiss-on-route-change; high visibility regression.

## 5. Quick-win polish (≤1d each)

F4 (rail labels), F9 (duplicate Import agent), F12 (disabled-button microcopy), F18 (slug URLs), F20 (breadcrumb links), F22 (NOT-SIGNED-IN microcopy), F23 (chip styling), F24 (modal banner copy), F26 (agent filter hide), F29 (user-menu duplicate email), F32 (dark-mode aria-label).

## 6. What I did NOT audit (scope gaps)

- Signup, email verification, first-login (account is logged-in / tainted).
- Mobile / responsive (desktop Chrome only).
- Performance / load latency (no Lighthouse pass).
- Curate conversation end-to-end (stopped after Dana's first turn).
- "Use " runtime chat surface.
- Org / Team / Admin pages (only the menu was inspected, not the destinations).
- Error paths (bad input, slow network, cancel mid-flow) - out of "heuristic walkthrough" depth.

## 7. Unresolved questions

1. Is the "industry" field on Home Step 1 a stale promise or a removed feature? Affects F10 framing.
2. Does "Use Aria" before curation give a deliberately empty agent, or is it gated server-side? Affects F17 severity.
3. Is the Curate UI's `AGENT.md` / `ONTOLOGY.md` tree intended as an end-user surface (Cursor-style power UX) or an internal debug view that leaked? Affects F15 fix shape.
4. Are integrations org-admin-installed (Jira pattern observed) or self-serve per user? Affects F21 + F22 wording.
5. Is the user menu intentionally minimal because account settings live elsewhere, or is this an unfinished surface? Affects F29 priority.
6. What's the target new-user time-to-first-trained-agent? Without that target, severity weighting on F2 / F11 / F16 is a guess.
