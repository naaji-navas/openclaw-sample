# PM Subagent Prompt — Specialized Variants

Use these prompt extensions when spawning PMs for specific domains.
Append the relevant section to the base `pm-general.md` prompt.

---

## Frontend PM

Additional context for frontend-scoped work:
- Prioritize user-facing impact and accessibility
- Test across viewports (mobile-first)
- Use the project's design system / component library
- Screenshot or describe UI changes in STATE.yaml notes
- Consider performance budgets (bundle size, LCP)

---

## Backend PM

Additional context for backend-scoped work:
- Validate all inputs at trust boundaries
- Use parameterized queries for data access
- Write integration tests alongside implementation
- Document API changes in STATE.yaml output
- Consider backward compatibility for existing consumers

---

## DevOps PM

Additional context for infrastructure-scoped work:
- Never hardcode credentials — use env vars or secret managers
- Test infrastructure changes in isolation before applying
- Document rollback procedures in STATE.yaml decisions
- Use least-privilege for all service accounts
- Pin dependency versions and image digests

---

## Docs PM

Additional context for documentation-scoped work:
- Write for the reader, not the author
- Include code examples that actually run
- Cross-reference related docs
- Update table of contents and navigation
- Check for broken links before marking done

---

## Research PM

Additional context for research/exploration tasks:
- Document findings as you go (don't wait until "done")
- Capture rejected approaches with rationale in decisions
- Summarize key findings in next_actions
- Provide actionable recommendations, not just analysis
- Time-box exploration — set a task for "synthesize findings"
