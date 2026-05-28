---
title: Dana Onboarding UX Audit
subtitle: Jobs-to-be-Done analysis · v0.2.114 · UX scope only
report_type: ux-audit
product: Dana
product_url: https://dana.aitomatic.com
date: 2026-05-27 (audit ran 14:52–15:24 +07:00, Asia/Saigon)
app_version: v0.2.114
workspace: Dana Enterprise → Dana Team (Admin role)
method: Heuristic walkthrough + scripted Jobs-to-be-Done across 4 personas (SME, technical evaluator,
  IT admin, data scientist)
confidence: Medium - one auditor played all 4 personas. Outside-user unscripted run not done; would raise
  confidence to High.
finding_counts:
  critical: 0
  major: 4
  moderate: 13
  minor: 6
  win: 10
  changed: 0
---

# Dana Onboarding UX Audit

## TL;DR - JTBD scorecard

### Three biggest UX wins

- **Ungrounded agent degrades gracefully.** Clicking "Use Agent" before any curation gives an honest gap acknowledgment + correct standard-industry guidance + a list of missing documents. Healthy behavior, not "this is broken" (JTBD 1 step 7).
- **Roadmap synthesis from minimal input.** One short SME reply produces a structured Purpose + Ontology + Source Priorities + File Tree in a single Dana turn (JTBD 1 step 6).
- **Integration revoke modal is best-in-class.** Name + 3-sentence consequence + type-to-confirm + verb-rich button. Other destructive flows in Dana should match this pattern (JTBD 2 step 6).

### Three biggest UX bugs to fix

- **Grammar template bug.** Agent description gets concatenated into greetings, task chips, and AGENT.md content as "I work across knows our manufacturing SOPs..." Three surfaces, one fix.
- **Destructive-action UX is wildly inconsistent.** Agent delete is a terse "Delete?", integration revoke is rich. Same product, opposite quality.
- **Curate file tree exposes raw markdown by default.** Intentional power-user UX for data scientists, mental-model trap for non-technical SMEs. Tier the visibility; don't remove it.

## Build my first expert agent

> **Approach:** Walked a non-technical SME from cold Home through create-agent, into Curate, answering Dana's first turn, then asking the agent a real domain question.
> **Why:** This is Dana's activation path. If a first-time domain expert can't reach a useful answer in 15 minutes, the product hasn't proved its value.

I want an agent that knows our SOPs. I'll create it, give it one document, answer whatever Dana asks, and then ask it a real question my team would ask.

## Wire a Notion integration to my agent

> **Approach:** Walked a technical evaluator through "+ New Integration," located the right MCP path, installed Notion, and probed the binding to an agent.
> **Why:** Integrations are how Dana clears the enterprise-ready bar for evaluators comparing it to ChatGPT or Claude. The Notion path tests the most common "I want my docs in the agent" workflow.

Show me you can do Notion. I want to install the integration, bind it to an agent, and ask the agent something only Notion would know.

## Onboard my team

> **Approach:** Walked an IT admin through Org Settings, Members, Roles & Permissions, Audit Log, and the invite flow (modal only, no invites sent).
> **Why:** Admins gate adoption. They need to understand scope (Org vs Team), the governance model, and the audit trail before they invite their team.

Before I invite my team, I want to understand what I'm signing up for: what's org-wide vs team-scoped, where do invitations and roles live, and what will a new user see on day one?

## Inspect & edit my agent's ontology

> **Approach:** Walked a data-scientist power user into Curate, opened AGENT.md and ONTOLOGY.md, tested Edit mode (Code/Rich Text split, Cmd+S), and observed adaptive clarifying questions.
> **Why:** The file tree is either a power-user feature or a leak. Running it as a DS settles which side it lives on - and shapes whether the recommendation is "remove" or "tier visibility."

I want to inspect the agent's underlying spec, edit the ontology directly, and verify changes propagate to the runtime.

## Top 7 UX fixes

> **Approach:** Ranked recommendations by severity x persona breadth x fix cost. A Moderate that affects all four personas can outrank a Critical that affects one.
> **Why:** This orders fixes by "biggest reader-perceptible UX improvement per engineering hour," not by severity alone. Engineering teams need to know what to do first.

Ranked by severity × persona breadth × ease. **All UX scope**; security / feature / data items live in the [Appendix](#appendix).

1. **Fix the "I work across" template grammar bug.** Moderate One template fix, three surfaces (agent greeting + task chip + AGENT.md content). Either drop the template, run role text through an NLU step, or constrain role input to a noun-phrase format. *Cost: ~0.5d.*
2. **Tier the Curate file-tree visibility.** Major Hide raw `AGENT.md`/`ONTOLOGY.md` tree behind "Advanced view" for non-technical users; keep full access for data-scientist personas. *Cost: 1–2d.*
3. **Standardize destructive-action confirmation UX.** Moderate Adopt the integration-revoke pattern (name + consequence + type-to-confirm + verb-rich button) for all destructive ops - agent delete first. *Cost: ~0.5d.*
4. **Explain the 2-tier install/auth model.** Moderate Inline microcopy on connector rows: *"An admin added this connector. Sign in to use it with your own data."* *Cost: <0.5d.*
5. **Fix the OAuth popup-blocker silent failure.** Moderate Visible feedback + same-tab fallback when popup is suppressed. *Cost: ~0.5d.*
6. **Reduce 8-question cognitive load in Curate intake.** Moderate Restore chips for categorical questions (Users / Sources), or use progressive disclosure (ask one category, get an answer, ask the next). *Cost: 1–2d.*
7. **Fix agent-delete copy + feedback.** Moderate Add agent name + consequence sentence + type-to-confirm + success toast. Bundles naturally with #3. *Cost: ~0.5d.*

## Confidence & caveats

### Method & bias

One auditor played all four personas. Findings drawn from scripted Jobs-to-be-Done walkthroughs combined with heuristic observation (Nielsen 10 + first-use patterns). **An unscripted run by someone who has never used Dana before would significantly raise confidence** - recommend scheduling one before locking the punch list.

### Highest-confidence findings (least bias contamination)

- **Template grammar bug** - reproduced across 3 surfaces independently; verbatim evidence captured.
- **Ungrounded-agent graceful degradation** - required actually running the agent, not just observing UI. Behaviour is the win.
- **Integration detail-panel binding** - required clicking into the side panel; not visible from list-level scan.
- **Dana's adaptive clarifying questions** - emerged from comparing JTBD 1 (manufacturing prompt) vs JTBD 4 (ontology-engineering prompt) responses.
- **Destructive-action UX inconsistency** - emerged from comparing JTBD 1 step 6 (agent delete) vs JTBD 2 step 6 (integration revoke) modals.

### Surfaces not reached (impact unknown)

Dark-mode entirely (tooltip, contrast, aria-labels) · Help & feedback menu · User menu · Popover stacking behavior · Sticky nav flyout · Breadcrumb back-navigation · Direct mobile / Safari · Signup & first-login flow (outside audit scope).

### Wall-clock by JTBD

| JTBD | Actual | Target | Notes |
| --- | --- | --- | --- |
| 1 · Build first agent | ~8 min | 15 min | Did not include document-upload step |
| 2 · Wire integration | ~5 min | 12 min | OAuth aborted at sign-in (popup blocked) |
| 3 · Onboard team | ~10 min | 10 min | Skipped Usage tab; did not send invites |
| 4 · Inspect ontology | ~6 min | 20 min | Did not run full edit→propagation test |


## Non-UX observations

Surfaced incidentally during the UX audit. Listed here so they aren't lost, but they do NOT drive UX recommendations.

> 🚨 **🚨 Security - route to security team**
> **Org Settings → AI Services → Environment Variables** displays API keys in **plaintext**. Example observed: `MOONSHOT_API_KEY=sk-RC3...EIl` (key masked here). No reveal-toggle, no masking, no write-only set pattern.
> **Risk:** any admin viewing this surface - or compromise of an admin account - leaks LLM provider keys.
> **Action:** route to security team; verify whether the visible key is real production (rotate if so); deploy masking + reveal-toggle UI fix.

### Backend data / consistency (route to backend)

- **Member count discrepancy.** Org Settings shows "Active Agents: 4" while the workspace shows 2. Counting-scope reconciliation needed.
- **Members tab dual-lists 2 users** (same email in BOTH "Pending Requests" and "Members" sections). Likely a query-scope bug.

### Feature capability inventory (informational, not UX)

- Org Settings has 8 sub-destinations (General, Members, Teams, Agents, Roles & Permissions, AI Services, Audit Log, Usage).
- Roles & Permissions exposes a 7-policy × 4-role matrix.
- "Manage own integrations → Admins only" - restrictive default. Policy decision, not UX.
- Audit Log captures timestamp + user + action + IP per event with drill-down.
- AI Runtime selectable per-org: Claude / Codex / Dana.
- Team-level override available on AI runtime.

### Reliability (root cause non-UX, symptom is)

- **Direct sub-route URL navigation unreliable.** Hash-router bug - route to frontend.

Dana onboarding UX audit · 2026-05-27
