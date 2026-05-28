---
title: Dana - Curate Workspace
subtitle: Deep-dive UX audit - v0.2.114 - UX scope only
report_type: ux-audit
product: Dana
product_url: https://dana.aitomatic.com
date: 2026-05-28 09:06 - 10:00 +07:00, Asia/Saigon
app_version: v0.2.114
workspace: Dana Enterprise -> Dana Team (Admin role)
method: Single-flow deep dive across 10 sub-scenarios, walked with a domain-specific test agent (reliability
  engineer, vibration analysis). Layout, first-turn, multi-turn, approval workflow, file tree, edit mode,
  working panel, activity reasoning, use-agent handoff, edge cases.
confidence: Medium - one auditor, reproducible single-observation grade. Findings supported by verbatim
  Dana output and screenshots.
finding_counts:
  critical: 1
  major: 1
  moderate: 3
  minor: 6
  win: 12
  changed: 0
---

# Dana - Curate Workspace

## TL;DR

> **Approach:** Distilled ten sub-scenario findings into the wins and bugs that most affect the curate-then-train loop.
> **Why:** The Curate workspace is where the user invests the most time. Stakeholders need to know what makes that investment pay off and what makes users walk away.

Curate is Dana's strongest surface in conceptual design and Dana's weakest surface in execution feedback. Dana's domain awareness, approval workflow, and write transparency are all best-in-class. But the gap between user approval and visible file output is a multi-minute silence with no granular progress, and the file tree does not auto-refresh when Dana finishes - both leave the user wondering if anything is happening.

#### UX wins

- **Domain-aware clarifying questions.** The role "reliability engineer interpreting vibration signatures" produced questions referencing ISO 10816, ISO 13373, and Mobius Institute - actual vibration-analysis standards.
- **Approval workflow is safe by default.** Dana writes "Awaiting your go-ahead" and waits for explicit consent before any file mutation.
- **Write transparency.** After mutations, Dana produces a "Files Created" table with file + description columns, plus "Knowledge Sources Referenced" external links.
- **Edit mode is polished.** Code/Rich Text split editor, Cmd+S save / Esc cancel shortcuts shown, Esc cancels cleanly.
- **One-click round-trip.** "Use Agent" -> runtime chat, "Curate with Dana" -> back to Curate. Easy to switch mid-flow.
- **Phased rollout proposals.** Dana proposes Phase 1 MVP first, with explicit Phase 2/3 extensions held in reserve. Scoped commitment.

#### Top bugs to fix

- **Critical: 5-minute silent execution between approval and file output.** Loader spinner is the only feedback. User has no idea if Dana is progressing or hung.
- **Major: File tree does NOT auto-refresh when Dana writes files.** User must click refresh to see what Dana created. No signal that "Files Created" content matches actual on-disk state.
- **Moderate: 7+ freeform questions on Dana's first turn.** Cognitive load remains high; no chips for categorical choices (e.g., depth level Basic/Intermediate/Advanced could be chips).
- **Moderate: Template grammar bug propagates into runtime greeting.** "I work across a reliability engineer that interprets vibration signatures..." reads broken (same root cause as agent-create flow).
- **Moderate: Agent cards expose creator email.** "by giang@aitomatic.us" visible on agent list cards.
- **Minor: Activity reasoning block expanded by default.** SME persona reads third-person meta-commentary and gets confused.

### Scenario 1: Layout & first-impression on landing

*3-column layout with pre-rendered surfaces during load. File tree (left), chat (center), Working Panel (right), top bar with agent identity + Use-Agent CTA + dark mode + avatar.*

> **Approach:** Created a test agent and observed the first paint of the Curate workspace before Dana started streaming.
> **Why:** First impressions of a long-form workspace shape user willingness to invest the next 10-30 minutes. Pre-rendered scaffolding signals "this is ready" even before content loads.

| Surface element | What's there | Verdict |
| --- | --- | --- |
| Top bar (left) | Dana avatar + green status dot + "Curating CurateAuditBot" subtitle + kebab menu | Win Clear identity |
| Top bar (right) | + (new chat), history icon, "Use CurateAuditBot" CTA, dark mode, avatar | Win Round-trip ready |
| Left pane | Header (ONTOLOGY + agent pill + refresh), search bar, Agent Profile link, folder tree (agents/, ontology/, curate_outputs/) | Win Pre-rendered before Dana streams |
| Center column | Dana streaming + "Preparing your workspace..." loader | Win Loader sets expectation |
| Right pane (Working Panel) | SOURCES header + search + Library / Uploaded tabs + empty state + "+ Add from Library" | Win Empty state copy clear |
| Chat input | "Type message, @ to tag files" placeholder + plus icon (attach?) + send button | Minor @ syntax discoverability |


### Green status dot next to Dana's avatar - meaning unclear

**Severity:** Minor  
**Confidence:** Medium

Dana isn't an integration that goes online or offline. The green dot reads "available" but conveys no actionable signal.

**Recommendation:** Either drop the dot, or use color to convey state that actually matters (busy / idle / error).

### @ syntax in chat input is undiscoverable

**Severity:** Minor  
**Confidence:** Medium

Placeholder says "Type message, @ to tag files". @ syntax to reference files is a power feature, but no autocomplete preview or tooltip explains what files can be tagged.

**Recommendation:** Show available tags in an inline autocomplete dropdown when user types @.

### Scenario 2: Dana's first turn structure

*Activity reasoning block exposed by default, followed by 4 categories of clarifying questions, then a preview of the roadmap coming next, with a closing call-to-action.*

> **Approach:** Read Dana's first turn verbatim and counted the question density. Compared the question vocabulary to the role text to verify domain adaptation.
> **Why:** Dana's first turn sets the curation contract. If it's domain-aware, the user trusts the loop. If it asks 12 generic questions, the user walks away. Question density is also a known friction source from prior audits.

For the test agent (a "reliability engineer interpreting vibration signatures on rotating equipment"), Dana produced these questions:

```
1. Users & Use Cases
   - Who will interact with this agent? (Level 1 maintenance technicians,
     Level 2 vibration analysts, reliability engineers, plant managers?)
   - What scenarios should it handle?

2. Scope of Equipment
   - What rotating equipment types? (pumps, motors, compressors, turbines,
     fans, gearboxes, centrifuges?)
   - Any specific industries to prioritize?

3. Depth of Knowledge
   - Target competency level?
     Basic: Recognize common fault patterns, suggest standard checks
     Intermediate: Diagnose using ISO standards, recommend corrective actions
     Advanced: Complex multi-fault analysis, bearing database lookups,
       resonance analysis

4. Knowledge Sources
   - Do you have existing materials I should incorporate?
   - Should I research public standards? (ISO 10816 vibration severity,
     ISO 13373 condition monitoring, Mobius Institute resources)
```

> ✅ **Domain awareness verified**
> References to ISO 10816, ISO 13373, and Mobius Institute are real vibration-analysis standards - Dana didn't fabricate them, she pulled from her general knowledge of the domain inferred from the role text. This is the strongest perceived-intelligence moment in the entire curate flow.

### Clarifying questions adapt to inferred domain with real-world vocabulary

**Severity:** Win  
**Confidence:** High

### 7+ freeform questions on the first turn - high cognitive load

**Severity:** Moderate  
**Confidence:** High

The Depth of Knowledge category is presented as Basic / Intermediate / Advanced - a categorical choice that would be a perfect chip. Same for Industries (power gen / oil & gas / chemical / HVAC).

**Recommendation:** Replace categorical questions with multi-select chips (Depth, Industry, Equipment types). Keep freeform only where the answer is genuinely open-ended (sources, scenarios).

### Activity reasoning block expanded by default

**Severity:** Minor  
**Confidence:** Medium

Block reads in third person ("The user wants me to curate..."). Power users may find useful; SME persona may find confusing or noisy.

**Recommendation:** Collapse Activity blocks by default with a subtle "Dana's reasoning" expandable.

### Scenario 3: Multi-turn conversation flow

*User reply -> Dana synthesizes a structured roadmap with code blocks, tables, and phased phases -> closes with specific actionable questions and an "Awaiting your go-ahead" pause.*

> **Approach:** Replied to Dana's first turn with a compressed paragraph covering all four categories. Observed the structure, depth, and approval pause of her response.
> **Why:** Dana's synthesis quality is the main perceived-value moment. The shape of her response - whether she over-asks, under-commits, or proposes a real plan - decides whether the user continues curating.

Submitted user reply: *"Maintenance technicians (mostly Level 1) and reliability engineers. Coverage: pumps and motors in oil and gas. Intermediate depth, using ISO 10816 thresholds. We have manuals and incident reports."*

Dana's response included:

- **Activity reasoning block** recapping the user's inputs as a 4-line structured summary, then announcing intent: "Now I should propose a roadmap and wait for their approval before writing files."
- **Curation Roadmap** section with Purpose Statement that synthesized all 4 inputs into one paragraph ("CurateAuditBot assists plant maintenance teams in diagnosing pump and motor health... bridges Level 1 technicians and reliability engineers, operating at ISO 10816-informed intermediate competency").
- **Ontology Skeleton** code block with a TREE structure (CONCEPTS / Equipment / Vibration / Condition with proper hierarchy and ISO 10816 zone references).
- **First Knowledge Sources to Ingest** table with Source / Extract / Output columns.
- **Phased Rollout** with named phases (MVP / Deepening / Integration) and bullet deliverables per phase.
- **My Questions Before We Begin** with 4 SPECIFIC actionable questions (not yes/no).
- **"Awaiting your go-ahead."**

### Roadmap synthesis quality is exceptional

**Severity:** Win  
**Confidence:** High

One brief reply produced a complete, coherent, domain-correct plan with file-tree preview, prioritized sources, and phased delivery. This is the strongest "Dana earns the user's trust" moment.

### Code blocks have copy + download affordances inline

**Severity:** Win  
**Confidence:** High

The ontology tree code block and any other generated code chunks each have icons in the top-right for copy and download. Lets the user grab pieces independently.

### Markdown render quality (tables, code, nested lists, headings)

**Severity:** Win  
**Confidence:** High

Tables render with proper headers and zebra stripes. Code blocks use monospace + scrollable horizontal overflow. Nested lists indent correctly.

### Scenario 4: File approval & write workflow

*Dana proposes structure -> user approves in chat -> Dana enters execution mode -> (long silent wait) -> Dana posts "Files Created" table with mutations + external knowledge sources referenced.*

> **Approach:** Approved Phase 1 with a specific reply ("Yes, proceed with Phase 1. Use public ISO 10816 summary. Focus on centrifugal pumps and AC induction motors."). Observed the gap between approval and visible file output.
> **Why:** The approval pattern is Dana's safety contract - users grant explicit consent before mutation. But the latency between approval and visible output is where users either lose patience or build trust. This is the critical UX gap.

> 🚨 **Critical: 5-minute silent execution**
> Time from approval ("Yes, proceed with Phase 1" at 09:50 AM) to file output ("Phase 1 ontology is complete" at 09:55 AM) was approximately 5 minutes. During this window, the chat showed only:
> - An Activity block listing 5 sub-steps
> - "I'll proceed with Phase 1. Let me create the ontology structure and fetch the ISO 10816 reference."
> - (40 seconds of silence)
> - "I have the information needed. Let me create the Phase 1 ontology files."
> - (More silence + a small spinner)
> No granular progress: no "Creating ontology/ONTOLOGY.md...", no "Writing rules/severity_classification.yaml...", no percentage, no countdown.

### Tool execution silent for minutes - no granular progress signal

**Severity:** Critical  
**Confidence:** High

For a 5-minute operation, the user cannot tell if Dana is working, hung, or about to error. The spinner is binary (on/off); there's no intermediate state.

**Recommendation:** Stream inline tool-use cards as Dana executes: "Reading ISO 10816 summary...", "Writing ontology/ONTOLOGY.md (1024 bytes)", "Writing rules/severity_classification.yaml". Make the file tree update in real time as files are created.

### "Files Created" table is gold-standard write transparency

**Severity:** Win  
**Confidence:** High

After execution, Dana posted:

| File | Description |
| --- | --- |
| `ONTOLOGY.md` | Core domain concepts: equipment hierarchy (pumps, motors, components), vibration characteristics, ISO 10816 condition zones, and relationships |
| `rules/severity_classification.yaml` | ISO 10816 machine classes (I-IV), velocity thresholds for Zones A-D, measurement parameters, trending rules |
| `rules/fault_patterns.yaml` | Frequency-to-fault correlations for imbalance, misalignment, looseness, bearing defects, cavitation, electrical faults |
| `skills/analyze_vibration.md` | Complete diagnostic procedure with decision tree, examples, and escalation triggers |


Plus a "Knowledge Sources Referenced" section with two external citations (ISO 10816 Vibration Severity Chart, Fluke: Diagnosing Imbalance/Misalignment/Looseness/Bearing Wear). This is best-in-class write transparency.

### Auto-proposes "Ready for Phase 2?" after Phase 1 completion

**Severity:** Win  
**Confidence:** High

Dana doesn't stall after writing files. She immediately proposes Phase 2 extensions and alternative paths ("if you have manuals to ingest, I can..."). Natural conversation continuation.

### Scenario 5: File tree exploration

*Folder tree on the left lists agents, ontology, and curate_outputs. Clicking a file opens a Preview pane. Refresh button and search field at the top.*

> **Approach:** Clicked into AGENT.md to inspect Preview pane structure. Manually triggered tree refresh after Dana wrote files. Tested folder expand / collapse.
> **Why:** The tree is Dana's commitment to "you can see everything." If the tree doesn't reflect actual state, the commitment breaks.

> 🚨 **Tree does NOT auto-refresh after Dana writes files**
> After Dana completed her file creation and posted the "Files Created" table, the file tree continued to show only the pre-existing entries (agents/curateauditbot/AGENT.md, ontology/ONTOLOGY.md, curate_outputs/). The new files (rules/severity_classification.yaml, rules/fault_patterns.yaml, skills/analyze_vibration.md) were NOT visible until I manually clicked the refresh button next to the ontology pane header.
> This breaks Dana's transparency contract: she says "Phase 1 ontology is complete. Here's what was created", but the tree disagrees until the user refreshes.

### File tree does not auto-refresh when Dana writes files

**Severity:** Major  
**Confidence:** High

**Recommendation:** Subscribe the tree to file-mutation events (or poll every 2s during streaming). The tree should update before or simultaneously with the "Files Created" message, not minutes later when the user happens to click refresh.

### Manual refresh gives a clear toast confirmation

**Severity:** Win  
**Confidence:** High

Clicking the refresh icon shows a green "Refreshed" toast at the bottom. Action acknowledged.

### Scenario 6: Edit mode round-trip

*Click a file -> Preview pane opens -> click Edit -> split editor (Code/Rich Text) with keyboard shortcuts and Save/Cancel buttons. Esc cancels cleanly.*

> **Approach:** Opened AGENT.md in Preview, toggled Edit mode, observed the split editor, then pressed Esc to verify cancellation. Did not save an actual edit to avoid corrupting the test agent's identity.
> **Why:** The edit affordance is what makes the file tree a power-user feature rather than a leak. If editing doesn't round-trip cleanly, the surface becomes a trap.

### Edit mode is a polished split editor with explicit shortcuts

**Severity:** Win  
**Confidence:** High

Header bar shows "52 lines · 2 pages | Preview | Edit | download | fullscreen | close X". Sub-header in Edit mode shows "Code | Rich Text" toggle and "Cmd+S to save, Esc to cancel" hint. Save and Cancel buttons accessible top-right of editor pane.

### Esc cleanly cancels edit mode

**Severity:** Win  
**Confidence:** High

Press Esc while in Edit mode returns to Preview view without prompting (no edits were made, so no discard-confirm needed). This is one of the few places in Dana where Esc actually works as expected.

Note: a full edit -> save -> verify-persistence round-trip was not exercised in this audit to avoid mutating the test agent's identity mid-session. Recommend a follow-up that types a deliberate marker, saves, refreshes, and verifies persistence + propagation to runtime.

### Scenario 7: Working Panel (Sources / Library / Uploaded)

*Right pane with two tabs: Library (workspace-level files the agent can draw from) and Uploaded (files attached directly in conversation). Both have clear empty-state copy.*

> **Approach:** Clicked both tabs to read empty-state copy and inspect the action affordances.
> **Why:** The Working Panel is where the user wires knowledge into the agent. Two tabs imply two distinct mental models (workspace-level vs conversation-level); the IA decision is consequential.

| Tab | Empty state copy | Bottom action |
| --- | --- | --- |
| Library | Book icon + "Library files your agent draws from." | + Add from Library |
| Uploaded | Paperclip icon + "Files attached in conversation will be shown here." | (none) |


### Clean IA separation between Library and Uploaded

**Severity:** Win  
**Confidence:** High

Two tabs encode two genuinely different concepts: workspace-level persistent files vs conversation-level transient attachments. The copy on each tab makes this clear without jargon.

### No affordance to upload directly from the Uploaded tab

**Severity:** Minor  
**Confidence:** Medium

The Uploaded tab tells you files will appear here when attached in conversation, but doesn't link to the chat-input attachment affordance. New users may stare at the empty state wondering how to attach.

**Recommendation:** Add "Use the + icon in the chat input to attach" link in the empty state, or render an upload zone directly in the tab.

### Scenario 8: Activity reasoning UX

*Dana exposes her step-by-step reasoning in a collapsible "Activity" block before her conversational response. Expanded by default. Persists in every turn.*

> **Approach:** Observed Activity block content across multiple turns. Compared the framing tone (third person) against the conversational message tone (first person).
> **Why:** Reasoning transparency is a major Dana differentiator vs ChatGPT, but transparency that's always-on becomes noise. The right default matters.

Activity block content samples observed in this audit:

```
The user wants me to curate an ontology for a new agent called
CurateAuditBot, which is a reliability engineer focused on vibration
analysis for rotating equipment in plant maintenance settings.

Before I proceed, I need to ask clarifying questions to understand:
1. Users
2. Depth
3. Scope
4. Sources
```

```
The user has approved Phase 1. I need to:
1. Create the directory structure for the ontology
2. Create ONTOLOGY.md with core domain concepts
3. Create rules/ directory with ISO 10816 severity classification rules
4. Create skills/ directory with the "Analyze vibration reading" skill
5. Fetch ISO 10816 public summary for the velocity thresholds
```

### Activity reasoning is substantive and useful for power users

**Severity:** Win  
**Confidence:** High

For a data-scientist or technical evaluator, seeing Dana's plan before she executes builds trust and lets them intervene if she's about to do the wrong thing.

### Third-person framing reads odd when expanded by default

**Severity:** Minor  
**Confidence:** Medium

"The user wants me to..." reads as if Dana is talking to a third party. For non-technical users this can feel impersonal or confusing ("why is she narrating my session?").

**Recommendation:** Reframe in first person addressing the user directly: "Got it. I'll start by asking you four questions about..." OR collapse Activity by default for non-power users.

### Scenario 9: Use Agent handoff from Curate

*"Use CurateAuditBot" button in top-right of Curate jumps to runtime chat. URL drops the conversation suffix. Left rail restores full set. "Curate with Dana" button enables round-trip.*

> **Approach:** Clicked "Use [Agent]" mid-curation. Observed URL transition, surface differences (file tree, working panel), greeting rendering, task chips, and the round-trip back to Curate.
> **Why:** The handoff is where Dana hands the user from "trainer" mode to "user" mode. The mode-switch needs to be one click in either direction without losing context.

| Element | Curate | Runtime (after Use Agent) |
| --- | --- | --- |
| URL | `/agent/<uuid>/curate/conversation/<uuid>` | `/agent/<uuid>` |
| Left rail | Reduced (Agents + Library + Integrations only) | Full (DE + Dana T... + Agents + Library + Integrations + Help + Admin) |
| File tree pane | Visible | Hidden |
| Working Panel | Visible | Collapsed (icon only) |
| Top-right CTA | "Use CurateAuditBot" | "Curate with Dana" |


### One-click round-trip in both directions

**Severity:** Win  
**Confidence:** High

"Use Agent" -> runtime, "Curate with Dana" -> back. The mode switch is visible and intentional. Users don't get lost between trainer mode and user mode.

### Template grammar bug propagates into the runtime greeting

**Severity:** Moderate  
**Confidence:** High

The runtime greeting reads:

```
Hi, I'm CurateAuditBot
I work across a reliability engineer that interprets vibration
signatures on rotating equipment, used by plant maintenance...
```

"I work across a reliability engineer..." is grammatically broken. The template concatenates "I work across" with the raw Industry text (which itself is mis-mapped from the form's role field, per the create-agent flow audit). Same root cause as the create-agent grammar bug.

**Recommendation:** Bundles with the create-agent flow fix (Top 7 #1 in that audit). Drop the "I work across" template entirely, or run the role/industry text through an NLU step to produce a grammatical sentence.

### Scenario 10: Long execution + edge observations

*Side observations during the 5-minute execution wait, the cleanup flow, and an unrelated finding on the agents list.*

> **Approach:** Used the wait time during Dana's long execution to probe peripheral surfaces (file tree state, refresh behavior). Cleaned up by deleting the test agent via the agent detail page.
> **Why:** The peripheral observations often surface issues that a focused walkthrough misses.

### Agent cards on the Agents list expose creator email

**Severity:** Moderate  
**Confidence:** Medium

The CurateAuditBot card on the Agents list showed "by giang@aitomatic.us" under the agent name. This exposes the creator's email to anyone viewing the agents list (including other workspace members and other teams' admins).

**Recommendation:** Show the creator's display name only (e.g., "by Giang Nguyen") and reserve the email for the agent detail page where it can be put behind a hover tooltip.

### Agent delete confirmation modal still reads "Delete?"

**Severity:** Minor  
**Confidence:** Medium

Same terse copy as the create-agent flow audit found. The kebab menu -> Remove agent path opens a "Remove agent" modal with body "Delete?" - no agent name, no consequence statement.

**Recommendation:** Same fix as create-agent flow Top 7 #3: adopt the integration-revoke modal pattern.

## Top 7 fixes

> **Approach:** Ranked recommendations by severity x how often the user hits the issue x cost to fix. The 5-minute silent execution and tree non-refresh affect every curate session, so they lead even though no single one is Critical-by-itself in the audit.
> **Why:** Engineering teams need ordered work, not a wishlist. Bundled fixes (where one change resolves multiple findings) get priority.

1. **Stream inline tool-use progress during execution.** Critical Replace the silent 5-minute spinner with streamed cards: "Reading ISO 10816 summary...", "Writing ontology/ONTOLOGY.md (1024 bytes)", "Writing rules/severity_classification.yaml". Each card appears as the operation starts. *Cost: 1-2d.*
2. **Auto-refresh the file tree when Dana writes files.** Major Subscribe the tree to file-mutation events (or poll every 2s during streaming). The tree should be consistent with the "Files Created" message at the moment it's posted, not minutes later. *Cost: 0.5-1d.*
3. **Replace categorical first-turn questions with chips.** Moderate Depth (Basic/Intermediate/Advanced), Industries (Power gen / Oil & gas / Chemical / HVAC), Equipment types (multi-select). Keep freeform only for truly open answers. *Cost: 1-2d.*
4. **Fix the "I work across" template grammar bug.** Moderate Same fix as create-agent flow audit Top 7 #1. Drop the template, or run role/industry text through NLU. One fix, multiple surfaces. *Cost: 0.5d.*
5. **Collapse Activity reasoning blocks by default for non-power users.** Minor Add user-preference toggle ("Show Dana's reasoning"). Default off. Reframe content in first person addressing the user. *Cost: 0.5-1d.*
6. **Mask creator email on agent list cards.** Moderate Show display name only; reserve email for detail-page hover tooltip. *Cost: 0.5d.*
7. **Add upload affordance to the Working Panel Uploaded tab.** Minor Render an upload zone in the empty state, OR link to the chat-input + icon. *Cost: 0.5d.*

## Confidence & caveats

### Method & bias

One auditor, one session, one test agent (vibration-analysis domain). Findings reproducible single-observation grade. An unscripted run by a real domain expert (e.g., a maintenance engineer) would raise confidence on Dana's domain-awareness claims and stress-test the approval workflow under more realistic question density.

### Not tested / not reached

- Edit mode save round-trip (intentional - did not want to corrupt the test agent mid-audit)
- Edit propagation to runtime (does an AGENT.md edit immediately affect runtime answers?)
- @-mention syntax in chat (placeholder shows it; no autocomplete tested)
- + Add from Library flow (workspace had empty Library)
- Attachment via chat-input + icon (not exercised)
- Multi-document concurrent edits (only AGENT.md viewed)
- Conversation history navigation (single conversation tested)
- Cancel during streaming (stop button exists in input but not exercised)
- Dark-mode rendering of Curate
- Long conversation (5+ turn) UI behavior - the 5-minute execution wait counts but is different from a long natural conversation

### Highest-confidence findings (least bias)

- 5-minute silent execution between approval and file output - timestamps verified (09:50 approve -> 09:55 complete)
- File tree non-refresh - reproduced by clicking refresh and seeing the new files appear only after manual action
- Domain-aware first turn - verbatim ISO 10816, ISO 13373, Mobius Institute references in Dana's output
- Approval workflow - Dana's verbatim Activity block: "Now I should propose a roadmap and wait for their approval before writing files"
- Template grammar bug propagation - same root cause as prior create-agent audit, observed in two more surfaces (AGENT.md content + runtime greeting)

Dana curate workspace UX audit · 2026-05-28
