# Dana Onboarding UX Test Plan - Industry Benchmark Assessment

**Date:** 2026-05-27 14:43  
**Predecessor:** ux-audit-260527-1408-dana-onboarding-walkthrough-report.md  
**Scope:** Evaluate v2 plan against Nielsen Norman Group, Maze, UserTesting, and published onboarding research standards.

---

## Executive Summary

v2 is a **pragmatic step up from v1** (adds personas, timing, reset protocol) but **falls below industry standard for defensible onboarding research**. It mixes heuristic rigor with task-based framing without committing to either. Missing: post-task perception scoring (SUS), moderated observation depth (video/transcript), and cross-persona synthesis methodology. **Top 2 gaps:** no SUS/SEQ scoring, no remote-moderated evidence rig.

---

## Benchmark Assessment by Dimension

### 1. Sample Size for Heuristic-Style Audits  
**Plan:** 1 auditor playing 4 personas (all same user account, admin context)  
**Industry Standard:** Nielsen recommends 3–5 independent evaluators for heuristic evaluation; task-based testing requires 8–12 diverse participants for qualitative onboarding (NN/G finding: 5 users finds ~85% of issues; beyond 5, diminishing returns)

**Score: BELOW**  
- **Fidelity gap:** Four personas played by one person in admin mode ≠ four independent users. Persona A (SME) using admin account is fundamentally different from a real non-technical first-time user.
- **Recommendation:** Recruit 4–5 external participants per primary persona (at minimum 2 per persona) to validate empty-state, first-login, and auth paths v1 couldn't reach.

---

### 2. Task-Based vs. Heuristic vs. Cognitive Walkthrough  
**Plan:** Heuristic + scripted JTBD (→ "Jobs to be Done") + personas = hybrid walkthrough  
**Industry Standard:** True task-based testing (NN/G): set task, observe *without leading*, measure completion and friction. Cognitive walkthrough: first-time-user mental model focus. Heuristic: evaluator-vs-principles, no real user.

**Score: AT (with caveats)**  
- v2 is **honest about being a heuristic walkthrough with JTBD framing** — that's better than v1's pure heuristic.
- **Gap:** Scripts ("build one agent in 15 min") are strawman targets, not validated baseline. Real task-based testing would randomize task order, suppress cues, measure actual completion rate.
- **Trade-off:** v2 trades task-based rigor (high friction, 12+ participants) for speed (4 personas, ~10h total). Acceptable for pre-launch validation if data is treated as *directional, not normative*.

---

### 3. Time-on-Task Metric Definition  
**Plan:** TTFA (Time to First Agent), TTFU (Time to First Useful Answer) mentioned; no measurement protocol  
**Industry Standard:** MeasuringU & Maze: clock starts at explicit task start state, ends at user declares task *complete* (post-task SUS measures confidence). Stopwatch ambiguity kills validity.

**Score: BELOW**  
- **Critical gap:** v2 template says "t+mm:ss from run start" but never defines:
  - When does TTFA clock *start*? At Home first paint? After modal dismissal? When Curate modal opens?
  - When does TTFA clock *stop*? First successful agent creation? Routing to Curate? User's subjective "ready to ask"?
  - TTFU: "first useful answer" is purely subjective — no rubric, no post-task question.
- **Recommendation:** Adopt post-task Single Ease Question (SEQ): "How easy was it to create your agent?" (1–7 scale). Add explicit timer gates in reset protocol.

---

### 4. Evidence Depth  
**Plan:** Screenshots + step timings + friction notes (minor/blocked). No video, no transcript, no SUS/SEQ, no heatmaps  
**Industry Standard:** Remote moderated (Zoom/Lookback): video capture + transcription + post-task SUS (10 questions) + per-task SEQ (1 question) + optional session replay heatmaps. Maze/UserTesting: automated video + analytics dashboard.

**Score: BELOW**  
- v2's template is **lightweight and reproducible** (good for multiple runs), but lacks **perceived usability signal**.
- **Missing:** Post-task questionnaire (no SUS, no SEQ, no NPS). Without this, "friction notes" are subjective; impossible to rank across personas.
- **Cost-benefit:** Video capture adds 2–3h per run but unlocks full quote-ability and team consensus. Maze remote testing would add platform cost (~$500–$2k/run) but auto-transcribes.
- **Minimum viable increase:** Add 1 post-task SEQ question per run (7-point): "How easy was it to get your agent ready to use?" Aggregate across personas.

---

### 5. Severity Rubric Consistency  
**Plan:** Adopts NN/g S0–S3 from v1; no explicit re-calibration in v2 phase files  
**Industry Standard:** NN/g scale (Jakob Nielsen) is industry-standard; requires *per-issue* scoring on **frequency × impact × persistence**. Most teams calibrate rubric post-data (e.g., "anything blocking >2 personas = S1").

**Score: AT**  
- v1's S0–S3 scale is defensible and sourced (Nielsen).
- **Risk:** v2 phase files don't re-apply rubric to *new findings* (phase-01 through phase-04 are still unwritten). Drift likely post-execution.
- **Recommendation:** Before phase-05 rollup, add a 2-step calibration:
  1. **Individual phase:** Mark every finding with (frequency: 1–4 personas, impact: data-loss/conversion/friction/polish, persistence: every run or edge-case).
  2. **Rollup synthesis:** Re-rank severity based on aggregate frequency (e.g., F27 popover bug blocks all 4 personas → stays S1).

---

### 6. Cross-Persona Rollup  
**Plan:** Phase-05 is listed as "Cross-persona rollup report" with no detail  
**Industry Standard:** Affinity mapping + frequency binning + impact scoring. Serious orgs (Stripe, Anthropic) run synthesis workshop: "What % of users hit this friction?"

**Score: BELOW**  
- Phase-05 file is not detailed; plan says "ranking by activation impact" but provides no synthesis method.
- **Missing:** Rollup should include:
  - Heatmap: which findings hit which personas (matrix)?
  - Frequency rollup: X of 4 personas hit friction F6, Y of 4 hit F27, etc.
  - Activation blocker ranking: does F6 (popover) block any persona's JTBD completion? Actual yes/no per persona.
  - New-finding dedup: did phase-02 + phase-03 discover the same integration issue twice?
- **Recommendation:** Use affinity board format (Miro/FigJam) with: Finding ID | Description | Personas affected | v1 vs v2 status | Activation impact | Severity.

---

## Top 2 Industry-Standard Moves Missing from v2

### 1. **Post-Task Usability Perception (SUS or SEQ)**  
**Why:** v2 captures friction *observations* but no *user sentiment*. A finding marked "minor friction" by the auditor might be "moderate frustration" to a real user. NN/g standard is post-test SUS (10 questions, 5 min) or per-task Single Ease Question (1 question, 10 sec). Maze/UserTesting auto-capture this.

**Cost:** +10 min per phase (4 × 10 min SEQ per phase). SEQ is 1 question: "How easy was it to complete your task?" (1–7).  
**Payoff:** Quantify activation barrier. If all 4 personas rate TTFA ≥6/7, F1 (Home hierarchy) is polish, not blocker. If 3/4 rate ≤4/7, it's S1.

### 2. **Remote-Moderated Observation (Video/Transcript) with Moderator Notes**  
**Why:** Single-evaluator scripts miss the *why* behind friction. Real moderated testing captures:
- Participant's think-aloud rationale ("I expected a button here").
- Implicit signals (hesitation, re-reading, scrolling up).
- Recovery patterns (how users self-correct after a wrong click).

v2's "friction: blocked" binary doesn't distinguish "user recovered in 5s" from "user gave up."

**Cost:** +5–8 h per run (Zoom recording + async transcript). Could defer to v3 if budget-constrained.  
**Payoff:** Video clips become "supporting evidence" for design critiques. Turns "F1 is S1" into "watch Persona A scroll past the hero 3 times in 90 seconds — hierarchy is broken."

---

## Severity Rubric State

v2's adoption of NN/g S0–S3 is **appropriate and defensible**. However, **consistency risk is HIGH** because:
- v1 was a single auditor; v2 has 4 parallel runs, each filling evidence template independently.
- Without *a priori* calibration (frequency thresholds, e.g., "3+ personas = auto-S1"), post-hoc ranking will diverge.

**Mitigation:** Draft phase-05 preamble with explicit rubric:
- **S0:** Blocks JTBD completion in all test personas (catastrophic).
- **S1:** Blocks JTBD completion in ≥2 personas, or impacts activation in ≥1 (major).
- **S2:** Causes friction (slow, confusing) but JTBD completion intact (moderate).
- **S3:** Cosmetic / accessibility gap (minor).

---

## Comparison to Industry Practices

| Dimension | NN/g / Maze | v2 Plan | Gap |
|-----------|---------|--------|-----|
| Heuristic evaluators | 3–5 independent | 1 person, 4 personas | Lacks independence |
| Task-based participants | 8–12 diverse | 4 personas (same acct) | Under-recruited, tainted account |
| Perception scoring | SUS (10q) or SEQ (1q) | None | **Missing** |
| Evidence depth | Video + transcript + replay | Screenshots + friction notes | Lightweight (acceptable for v2) |
| Severity rubric | Nielsen S0–S3, calibrated | Nielsen S0–S3, uncalibrated | Drift risk in parallel runs |
| Cross-persona synthesis | Affinity map + frequency | TBD in phase-05 | Unspecified |

---

## Unresolved Questions

1. **Is phase-05 "rollup" a written synthesis, or just a data dump?** Plan says "ranking by activation impact" but no format specified. Recommend: Affinity board or cross-tabulation matrix.
2. **Will v2 data be treated as *directional* (one-way learning) or *normative* (repeatable benchmark)?** If normative, real external participants are non-negotiable; if directional, v2 sample is acceptable.
3. **Does Dana have acceptance criteria for "onboarding fixed"?** v2 targets TTFA 12–20 min; is that industry baseline or aspirational?
4. **Post-v2, is a v3 planned covering mobile, Safari, error paths, and fresh signups?** (Currently out of scope.)

---

**Recommendation:** Proceed with v2 as *diagnostic heuristic + task-framing*, not as *normative task-based research*. Pre-phase-01, commit to: (a) post-phase-05 SUS/SEQ aggregation (10 min), (b) phase-05 affinity synthesis template (30 min). If budget allows, capture 1 Zoom recording per persona as supporting evidence (defer full moderation to v3).

