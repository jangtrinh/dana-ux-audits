# Dana UX Audits

A collection of UX + accessibility audit reports for [Dana](https://dana.aitomatic.com) (Aitomatic) at v0.2.114 / v0.2.116.

**Live site:** https://jangtrinh.github.io/dana-ux-audits/

## What's here

Each audit report is paired: an HTML page for reading in browser, and a Markdown file (same basename, `.md`) for downloading and feeding into LLMs.

- `index.html` - hub page with cards for every audit
- `dana-ux-audits-methodology.html` - how the audits are conducted, and why
- `dana-onboarding-v2-ux-audit-report.html` - cross-persona Jobs-to-be-Done audit (4 personas)
- `dana-create-agent-flow-deep-dive-ux-audit-report.html` - deep dive on the create-agent modal (9 sub-scenarios)
- `dana-curate-workspace-deep-dive-ux-audit-report.html` - deep dive on Curate workspace (10 sub-scenarios)
- `dana-accessibility-audit-report.html` - WCAG 2.1 AA scan across shell + library + agents + create-agent modal + curate
- `dana-plan-benchmark-assessment.html` - methodology benchmark vs Nielsen Norman / Maze / UserTesting

## Markdown sources (AI-friendly)

Every HTML report has a paired `.md` file with the same basename. Click the "Download markdown" button at the top of any report to grab it.

The Markdown files are structured for LLM consumption:
- YAML frontmatter with title, date, version, scope, method, confidence, and finding counts by severity
- Per-finding blocks with explicit `Severity`, `Confidence`, `Recommendation` labels
- Clean tables, fenced code blocks, no HTML chrome

Useful for: pasting into a chat to ask follow-up questions, building a knowledge base, or generating a roadmap.

## Method

Job-to-be-Done framing, severity words (Critical / Major / Moderate / Minor / Win), confidence labels on every finding, explicit bias caveats, UX-only scope with non-UX observations in an Appendix. Full method: `dana-ux-audits-methodology.html`.
