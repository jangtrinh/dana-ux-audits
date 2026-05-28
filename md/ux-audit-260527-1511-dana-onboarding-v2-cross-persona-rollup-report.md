# Dana Onboarding UX Audit - Cross-Persona Rollup

- **Date:** 2026-05-27 (audit ran 14:52-15:24 +07:00, Asia/Saigon)
- **App version:** v0.2.114
- **Workspace:** Dana Enterprise → Dana Team (Admin role)

## Scope

**UX audit only.** This report covers visual design, interaction patterns, copy / microcopy, information architecture, perceived intelligence, friction (cognitive load / step count / errors / recovery), accessibility, empty / error / loading states, and consistency.

Non-UX observations surfaced incidentally during the audit (security, backend data bugs, feature-capability inventory) are listed in [Appendix A](#appendix-a--non-ux-observations-out-of-scope) so they aren't lost, but they do not drive UX recommendations.

## 0. Confidence label (mandatory honesty addendum)

**Overall confidence: MEDIUM.** Outside-human Persona A2 did NOT run; all 4 persona phases were auditor-as-proxy with knowledge of v1 findings. this caps headline confidence. Specific finding labels:
- **High:** observed by ≥2 proxy personas with verbatim evidence (V2-N1 hero rotation, V2-N12 grammar template, F22 context)
- **Medium:** observed by 1 proxy persona with verbatim evidence (most v1 re-judgments, most V2-N findings)
- **Low:** auditor inference, no anchor (none — all UX findings have screenshot or quote anchor)

## 1. TL;DR (UX-only)

Dana shipped meaningful UX wins between v1 and v2:
1. **Roadmap synthesis quality jumped** — Dana produces structured purpose + ontology + priority table on minimal SME input (V2-N10). Perceived-intelligence win.
2. **Agent runtime degrades gracefully** when ungrounded — **F17 reversed**. Honest gap acknowledgment + correct standard-industry guidance. The "this feels broken" risk v1 predicted didn't materialize.
3. **Dana adapts clarifying questions to the inferred domain** — "ontology editing" prompt got OWL/RDF/Protégé questions, not generic ones (V2-N40).
4. **Curate file tree is intentional power-user UX** (rich Preview/Edit pane, Code/Rich Text toggle, Cmd+S) — **F15 nuanced**: power-user UX confirmed for DS persona; same surface still a mental-model trap for SME. Recommend tiered visibility.

Top UX bugs to fix:
- **V2-N12 (S2):** Template grammar — agent description concatenates into greetings and AGENT.md producing nonsense like "I work across knows our manufacturing SOPs..." Hits 3 surfaces from one bug.
- **V2-N20 (S2):** Destructive-action UX wildly inconsistent — agent delete is terse "Delete?", integration revoke is best-in-class type-to-confirm. Same product, opposite quality.
- **V2-N17 (S2):** OAuth "Sign in to use" click silently fails when popup is blocked. No visible error, no recovery path.
- **V2-N9 (S2):** F16's multi-choice chip problem is gone, replaced with 8 open-ended questions in Dana's first turn — still high cognitive load.

## 2. Findings matrix (UX only — rows: findings, columns: personas)

Legend per cell: severity per persona, or `n/a` if surface not reached. `+` = positive finding.

| # | Surface | A1 SME | B Tech | C Admin | D DS | Confidence |
|---|---|---|---|---|---|---|
| V2-N1 | Home hero copy rotates (5 variants seen) — consistency | S2 | S2 | S2 | S2 | High |
| V2-N2 | Agent delete confirmation "Delete?" — error prevention copy | S2 | n/a | n/a | S2 | High |
| V2-N3 | Agent delete — no immediate visual feedback | S2 | n/a | n/a | S2 | Medium |
| V2-N4 | Direct sub-route URL navigation unreliable — broken back/forward | S3 | S3 | S3 | S3 | High |
| V2-N5 | Auto-generated task chips echo description verbatim — copy | S2 | n/a | n/a | S2 | High |
| V2-N6 | "Upload once, use everywhere" banner (+) — discoverability | S3+ | n/a | n/a | S3+ | Medium |
| V2-N8 | Dana's "Activity" reasoning block exposed — transparency UX | S2 | n/a | n/a | S2+ | Medium |
| V2-N9 | F16 replacement — 8 open-ended questions, still high cognitive load | S2 | n/a | n/a | S2 | Medium |
| V2-N10 | Roadmap synthesis quality on minimal input (+) — perceived intelligence | S2+ | n/a | n/a | S2+ | High |
| V2-N11 | Roadmap previews planned file creation (+) — transparency | S3+ | n/a | n/a | S3+ | Medium |
| V2-N12 | Greeting / AGENT.md template grammar bug — copy | S2 | n/a | n/a | S2 | High |
| V2-N13 | Runtime latency reassurance copy (+) — visibility of system status | S3+ | n/a | n/a | n/a | Medium |
| V2-N14 | Chat input tab-completion suggestion (+) — efficiency | S3+ | n/a | n/a | n/a | Medium |
| V2-N15 | Agent list cards show expertise tags (+) — recognition | S3+ | S3+ | n/a | S3+ | High |
| V2-N16 | 2-tier install/auth model unexplained in copy | n/a | S2 | n/a | n/a | High |
| V2-N17 | OAuth "Sign in to use" silent failure (no visible error) | n/a | S2 | n/a | n/a | Medium |
| V2-N18 | Integration detail panel rich metadata + agent-binding inline (+) | n/a | S2+ | n/a | n/a | High |
| V2-N19 | Integration revoke type-to-confirm UX (+) — error prevention gold standard | n/a | S2+ | n/a | n/a | High |
| V2-N20 | Destructive-action UX inconsistency (agent terse vs integration rich) | S2 | S2 | n/a | S2 | High |
| V2-N21 | Integration revoke success toast (+) — visibility | n/a | S3+ | n/a | n/a | Medium |
| V2-N24 | Org "Auto-Join by Email Domain" callout — IA discoverability | n/a | n/a | S3 | n/a | Medium |
| V2-N25 | Pending-requests approval pattern (team-led invite governance) — IA | n/a | n/a | S2+ | n/a | High |
| V2-N26 | Owner/Admin/Member role chips — recognition | n/a | n/a | S3+ | n/a | High |
| V2-N28 | Invite modal single-email only (no bulk) — efficiency friction | n/a | n/a | S3 | n/a | Medium |
| V2-N29 | Invite "Add to teams" defaults unchecked — bad default UX | n/a | n/a | S3 | n/a | Medium |
| V2-N31 | "Experts" role appears in policies but not in invite role dropdown — consistency | n/a | n/a | S3 | n/a | Medium |
| V2-N34 | Audit Log action naming mixes formats (snake.case / Title Case) — consistency | n/a | n/a | S3 | n/a | Medium |
| V2-N37 | "Codex" label ambiguous (OpenAI Codex discontinued) — copy | n/a | n/a | S3 | n/a | Medium |
| V2-N39 | AGENT.md polished MD editor (Code/Rich split, Cmd+S) (+) — interaction quality | n/a | n/a | n/a | S2+ | High |
| V2-N40 | Dana's clarifying questions adapt to inferred domain (+) — perceived intelligence | n/a | n/a | n/a | S2+ | High |

**28 UX findings** (12 negative, 11 positive, 5 neutral consistency/IA).

Runtime-chat findings (Phase B + D collision rule): Phase 02 didn't reach runtime (OAuth blocked); Phase 01 A1 + Phase 04 both touched but tested different things (SME safety question vs DS ontology setup). No de-dup needed.

## 3. v1 findings reconciliation (5-way scale)

| v1 # | Status | Persona who judged | Note |
|---|---|---|---|
| F1 | partial | A1 | Hero copy refreshed + ROTATES (5 variants); layout dead-space unchanged |
| F2 | confirmed | A1 | "How it works" still below Quick Access |
| F3 | confirmed | A1 | Card labels still nouns |
| F4 | confirmed | Phase 00 | Left rail still truncates labels |
| F5 | n/a | — | Dark-mode tooltip — not tested |
| F6 | n/a | — | Sticky nav flyout — not tested |
| F7 | partial | A1 | 2 visible entry points to Create Agent, not 3 |
| F8 | n/a | — | "by Nhi Dam" — not specifically tested |
| F9 | n/a | — | Import agent dup entries — not tested |
| F10 | confirmed | A1 | Still no Industry field in create modal |
| F11 | confirmed | A1 | Silent transition into Curate |
| F12 | confirmed | A1 | Disabled "Create Agent" still no microcopy |
| F13 | confirmed | A1 | Visibility dropdown unchanged |
| F14 | confirmed | A1 | "Attach File" still unexplained |
| F15 | **changed** | A1 + D | File tree IS deliberate power-user UX — problem for SME, asset for DS. Recommend tiered visibility. |
| F16 | **changed** | A1 | Chips gone; replaced with 8 open-ended questions (V2-N9) |
| **F17** | **REVERSED** | A1 | Ungrounded "Use Agent" gives honest + useful answer. **Drop from punch list.** |
| F18 | confirmed | Phase 00 + A1 + D | UUIDs in URL persist |
| F19 | partial | A1 | Working Panel empty-state copy improved |
| F20 | n/a | — | Breadcrumb back-nav links — not tested |
| F21 | confirmed | Phase 00 + B | 3 raw primitives, no Notion tile |
| F22 | **partial+context** | Phase 00 + B | "NOT SIGNED IN" copy unchanged BUT V2-N16 reveals deliberate 2-tier model |
| F23 | **partial-reverse** | B | "Add to Agent" lives in integration detail panel with explicit agent checkboxes (V2-N18) |
| F24 | confirmed | Phase 00 + B | Modal banner copy unchanged |
| F25 | confirmed | Phase 00 | Library empty state strong (+ now mentions 100MB cap) |
| F26 | confirmed | Phase 00 | Agent filter visible on empty Library |
| F27–F30 | n/a | — | Help / popover stacking / dark-mode contrast / user menu — not re-tested |
| F31 | **partial-reverse** | C | Admin popover now shows scope under each option |
| F32 | n/a | — | Dark-mode aria-label — not tested |

## 4. Top 7 UX recommendations

Ranked by severity × persona breadth × ease. **All UX scope.**

1. **Fix template grammar bug (V2-N12, V2-N5)** — S2. "I work across <role text>" concatenation produces nonsense for most role inputs. Same bug in task chips and AGENT.md content. Single template fix, 3-surface payoff. Cost: 0.5d.
2. **Tiered Curate file-tree visibility (F15 nuanced)** — S1 for SME. Hide raw `AGENT.md`/`ONTOLOGY.md` tree behind "Advanced view" toggle by default; SME sees a "What Dana knows about your agent" summary card. DS keeps full power-user surface. Cost: 1–2d.
3. **Standardize destructive-action confirmation UX (V2-N20)** — S2. Agent delete should match integration revoke pattern (name + consequence + type-to-confirm). Currently same product, opposite quality. Cost: 0.5d.
4. **Explain the 2-tier install/auth model (V2-N16, F22 context)** — S2. Inline microcopy: "An admin added this connector. Sign in to use it with your own data." Closes F22 confusion without changing the model. Cost: <0.5d.
5. **Fix OAuth popup-blocker silent failure (V2-N17)** — S2. "Sign in to use" must give visible feedback (toast "Popup blocked — click here to open in this tab") when the popup is suppressed. Cost: 0.5d.
6. **Reduce 8-question cognitive load in Curate intake (V2-N9)** — S2. Either restore chip-style multi-select for categorical questions (Users / Sources), or progressive disclose (one at a time). Currently dumps all 8 at once. Cost: 1–2d.
7. **Fix agent-delete confirmation copy + feedback (V2-N2, V2-N3)** — S2. Title + agent name + consequence sentence + confirm name pattern. Add success toast. Cost: 0.5d. Bundles with #3.

## 5. Polish backlog (not promoted to top 7)

V2-N1 (hero rotation — pick one or document A/B intent), V2-N4 (sub-route URL routing), V2-N24 (auto-join callout positioning), V2-N28 (invite single-email — bulk paste), V2-N29 (teams default checked for current team), V2-N31 (Experts role discoverability in invite modal), V2-N34 (audit action naming convention pass), V2-N37 (Codex label rename), plus all positive findings already shipped (V2-N6, V2-N10, V2-N11, V2-N13, V2-N14, V2-N15, V2-N18, V2-N19, V2-N21, V2-N25, V2-N26, V2-N39, V2-N40).

## 6. Honesty addendum (mandatory)

### Findings the auditor expected to find AND found
All v1 confirmations (F2, F3, F4, F10–F14, F18, F21, F24, F25, F26). The scripted phases targeted these — finding them does not validate them, it validates the script. Confidence on these specific confirmations: Medium at best.

### Surfaces not reached (UX impact unknown)
- F5 dark-mode tooltip
- F6 sticky nav flyout
- F20 breadcrumb back-nav links
- F27 popover stacking
- F28 dark-mode popover contrast (entire dark-mode surface untested in v2)
- F29 user menu
- F30 Help & feedback menu
- F32 dark-mode aria-label

### Bias caveats
Same auditor wrote v1, wrote v2 plan, wrote v2 scripts, executed v2 personas. Confirmation bias on v1 re-judgments is unavoidable. UX findings with **least bias contamination** (and thus highest single-auditor confidence):
- **V2-N12** (template grammar bug) — first observed in v2; reproduced across 3 surfaces independently
- **F17 reversal** — required actually running the agent, not just observing UI
- **F23 partial-reverse** — required actually clicking into integration detail panel
- **V2-N40** (adaptive questions) — emerged from comparing Phase 01 (SME) vs Phase 04 (DS) Dana behavior
- **V2-N19/N20** (destructive-action UX gap) — emerged from comparing Phase 01 vs Phase 02 confirmation modals

### Phase 00 immutable-state baseline
- Alex + Tara agents persisted across all 4 phases (owned by Nhi Dam, not removed per safety)
- Admin-installed Jira integration persisted
- Workspace identity fixed (`Dana Enterprise → Dana Team`)
- Acting account is workspace admin (Personas A/B/D acting as non-admins)

### Outside-human A2 status
**Not run.** This was required for headline-confidence evidence. Without it, the v1 confirmations are script-targeted and the new findings are auditor-observed only. **Strongly recommend a 20-min unscripted outside-human run before locking the punch list.**

### Wall-clock summary
- Phase 00 dry-run: ~13 min (budget 30)
- Phase 01 A1: ~8 min (budget 15)
- Phase 02: ~5 min (budget 12)
- Phase 03: ~10 min (budget 10) — capped
- Phase 04: ~6 min (budget 20)
- Phase 05 write-up: ~25 min including this UX re-scope

## 7. Unresolved UX questions

1. **Hero rotation:** 5 variants seen in one session. A/B test or random selection? Affects F1 framing — should v3 fix the test seed or capture all variants?
2. **V2-N12 template bug:** Reproducible only with role text starting with verbs ("Knows..." / "Generic..."), or universal? Worth a 2-min repro suite before fix.
3. **V2-N17 OAuth popup:** Is this only an automation artifact, or also affects real users on popup-blocker browsers (Safari, Firefox strict mode)? Recommend manual test on Safari.
4. **V2-N40 adaptive questions:** Driven by an LLM call per agent description? If so, what's the latency budget when scaling Curate intake?
5. **F17 reversal scope:** Auditor's manufacturing SOP question was empathetic; would the graceful degradation hold for adversarial questions, niche domains, or non-English inputs? Targeted follow-up needed.
6. **Outside-human A2:** Still not scheduled. Without it, this report's headline confidence is capped at Medium. Blocking question: is there anyone at Aitomatic who has never logged into Dana and can spare 20 min?

---

## Appendix A — Non-UX observations (out of scope)

Surfaced incidentally during the audit. Listed here so they aren't lost, but they do NOT drive UX recommendations and are NOT included in the top-7 punch list. Route each to the appropriate non-UX owner.

### 🚨 Security
- **V2-N35 (S0 if production):** Org Settings → AI Services → Environment Variables field displays API keys **in plaintext** (e.g., `MOONSHOT_API_KEY=sk-RC3...EIl`, masked here). No reveal-toggle, no masking, no write-only pattern. Admin compromise → key exfiltration. **Action: route to security team for immediate review.** Recommendation: mask by default + "Reveal" toggle + verify whether visible key is real production (rotate if so).

### Backend data / consistency
- **V2-N23:** Org Settings → General shows "Active Agents: 4" while Dana Team workspace shows only 2 (Alex + Tara). Count reconciliation likely a counting-scope bug (org vs workspace). Route to backend.
- **V2-N27:** Members tab dual-lists 2 users (same email appears in BOTH "Pending Requests" and "Members" sections). Likely a query-scope bug. Route to backend.

### Feature capability inventory (not UX judgments — feature presence notes)
These describe Dana's capability surface area; UX of the screens themselves IS captured in the matrix above where applicable. Listing the *capability existence* separately so product / marketing can use it without conflating with UX recommendations.

- **V2-N22:** Org Settings has 8 sub-destinations (General, Members, Teams, Agents, Roles & Permissions, AI Services, Audit Log, Usage). v1 audit had not surfaced this breadth.
- **V2-N30:** Roles & Permissions exposes a 7-policy × 4-role matrix (Agent Management × Integrations × own/teammates/any × Everyone/Members/Experts/Admins). Enterprise-grade RBAC.
- **V2-N32:** "Manage own integrations → Admins only" — restrictive default. Policy decision, not UX.
- **V2-N33:** Audit Log captures timestamp + user + action + IP per event with drill-down. Compliance-grade.
- **V2-N36:** AI Runtime selectable: Claude / Codex / Dana (STARAgent direct API). Per-org LLM backend choice.
- **V2-N38:** Team-level override on AI runtime — multi-tier config.

### Reliability (borderline UX, but root cause is non-UX)
- **V2-N17** is in the main UX matrix because the user-facing symptom (silent failure) is UX. The root cause (popup blocker bypass requires user gesture, automation can't provide it) is a browser-platform constraint.
- **V2-N4** (sub-route URL navigation unreliable) is in the main UX matrix as a broken-nav user-facing symptom. Root cause likely hash-router bug — route to frontend.
