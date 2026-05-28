---
title: How we audit Dana
subtitle: The approach behind every report in this collection - and why each choice matters.
report_type: how-we-audit
product: Dana
product_url: https://dana.aitomatic.com
---

# How we audit Dana

## 1. We organize by Job-to-be-Done, not by page

Each audit follows a user trying to get something done - "build my first agent," "wire an integration," "onboard my team," "inspect the ontology." The walkthrough crosses pages naturally; findings sit in the step where a real user encounters them.

## 2. We audit across multiple personas

For broad audits we walk four personas: a non-technical SME, a technical evaluator, an IT/workspace admin, and a data-scientist power user. Each persona starts with their own goal and tolerance for friction.

## 3. We use plain severity words, not codes

| Label | Means |
| --- | --- |
| Critical | Blocker, data loss, or security exposure the user encounters |
| Major | Measurable activation loss - the product is noticeably worse without fixing |
| Moderate | Friction or confusion that slows the user; not a blocker |
| Minor | Polish, nit, or small copy improvement |
| Win | Notable positive worth keeping (and replicating elsewhere) |


## 4. We label confidence on every finding

| Confidence | Means |
| --- | --- |
| High | Observed by 2+ personas independently, or has verbatim/screenshot evidence |
| Medium | Single-persona observation with an evidence anchor (screenshot, quote) |
| Low | Auditor inference without evidence anchor - excluded from recommendations |


## 5. We rank fixes by impact, not severity alone

The "Top 7 fixes" list in each report ranks recommendations by severity x persona breadth x ease (how cheap the fix is). The result is roughly "which fixes give the most reader-perceptible UX improvement per engineering hour."

## 6. We name our bias up front

Most audits in this collection were run by one auditor playing all personas, scripted, with knowledge of prior audits. That setup has confirmation bias built in. Every report has a "Confidence & caveats" section that lists:

- What the auditor likely confirmed because the script pointed there
- What surfaces weren't reached at all
- Which findings have the lowest bias contamination (typically the unexpected ones)
- Whether an outside-user unscripted run happened (the gold standard for headline confidence)

## 7. We separate UX from non-UX up front

The body of every report is UX-only: visual design, interaction, copy, IA, perceived intelligence, friction, accessibility, states, consistency. Non-UX observations that surface during the walkthrough (security exposures, backend data bugs, feature-capability notes) live in an Appendix with explicit "route to X team" callouts.

## 8. We audit live, not from spec

Every finding in these reports was observed by running Dana in a real browser session - not inferred from screenshots, docs, or the spec. Screenshots in each report come from those sessions.
