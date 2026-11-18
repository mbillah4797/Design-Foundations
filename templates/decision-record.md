# Template: a decision record

One entry per decision about how we work, in a single append-only log. A change to how we work lands with its entry in the same pull request ([4.23](../docs/04-gates-releases-and-reversibility.md#423-every-change-to-how-we-work-lands-with-its-decision-log-entry)).

The fields that matter most are **Decided by / quote** (so nobody has to remember what was said) and **Enforced by** (so the next reader knows whether this is a check or a hope).

---

```markdown
### <ID> · <The decision, as one sentence a newcomer can act on>

- **Date:** <YYYY-MM-DD> (<when it was said, to the minute if known, with zone; when this entry was written>)
- **Status:** <proposed | accepted | live | retired | superseded>
- **Class:** <domain | design | engineering | process>   (a mixed record takes its strictest class)
- **Decision:** <What is now true. Specific enough to check. Include what it does NOT cover.>
- **Decided by / quote:** <role>, <date/time>, <channel>, verbatim: "<their words>". In answer to: "<the exact question asked>".
  <If relayed by a session: "relayed by <session role>, not signed".> <If implied: "implied, confirm".>
  <If an agent wrote it after a failure: "agent-recorded (incident rule), reported to <role> at <time>; stands until overturned".>
- **Why:** <The incident, measurement or reasoning that led here. One or two sentences.>
- **Enforced by:** <a named check or gate | doc only | habit>
- **Recorded in:** <where else this lives, if anywhere>
- **Amends:** <earlier entry, or none>
- **Not covered:** <what this deliberately leaves alone>
- **Supersedes / Superseded by:** <entries, or none>
- **Evidence:** <SOURCED (where) | INFERENCE (from what)>
```

---

## Notes

- Never rewrite an entry silently. Amend it with a dated note, or supersede it with a new one.
- A waiver is its own entry: one-time or standing, with the reason and the rollback trigger ([1.12](../docs/01-decision-rights-and-autonomy.md#112-waivers-are-written-scoped-and-reversible)).
- When decision rights move, the entry says what moved, what did not, and why ([1.13](../docs/01-decision-rights-and-autonomy.md#113-when-decision-rights-move-write-what-moved-and-what-did-not)).
- A domain owner's silence is never recorded as their agreement.
