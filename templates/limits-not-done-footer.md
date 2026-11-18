# Template: the "Limits / not done" footer

Every agent report ends with this section, and the most important items are repeated in a "Caveats, breaches and exposures (read first)" block at the top. The reader reads both before the verdict ([5.13](../docs/05-orchestrating-agents.md#513-agents-report-caveats-first-and-the-orchestrator-reads-them-in-full), [case study 4](../case-studies/04-the-caveat-at-the-bottom-of-a-green-report.md)).

It exists because agents put their most honest information at the bottom of their reports, and readers stop at the green headline.

---

```markdown
## Limits / not done

**Not done.** <What the brief asked for that this step did not do, and why.> ("None" if none.)

**Not checked.** <What was out of reach, skipped, sampled rather than swept, or checked only on test data.>

**Assumptions.** <What this result depends on that I did not verify. Guesses are labelled as guesses.>

**Breaches and exposures.** <Anything done outside the brief's rules, anything printed that should not have been, anything written outside scope. For each: what, what I checked afterwards, the result.> ("None" if none.)

**Secret scan.** <Which folders were scanned for which kinds of value, and the hit count.>

**Contract.** <What this result's "pass" covers, and what it does not.>

**What would change this result.** <The evidence that would reopen it.>

**Corrections.** <Anything in an earlier report of mine that this one shows was wrong.>
```

---

## For the reader

Before acting on any report, search it for: `limits`, `not done`, `breach`, `printed`, `exposed`, `secret`, `leak`. Read those sections in full. A security item goes to the human at once, and into the release record.
