# Template: an adversarial verify brief

For a step whose only job is to find holes in work another agent built. Run it as a separate step, by a different agent, ideally a different model family from the builder ([3.11](../docs/03-evidence-and-verification.md#311-builders-do-not-certify-their-own-work)).

Fill the angle-bracket parts. Delete nothing from the rules block: each line is there because an agent once did the thing it forbids.

---

```markdown
# Verify: <what is being verified>

## Your job
Find holes in <the artifact>. You are not here to approve it. Assume it is wrong somewhere and find where.
The builder's claim is: "<the exact claim, e.g. 'with every switch off, the output equals main's'>".

## The bar (set before you start)
A finding counts as FAIL only if: <e.g. "it shows the artifact is weaker than the last good version for a case real inputs can produce, or it is a regression">.
Anything else is a LIMIT: record it, with evidence, but it does not fail the step.

## What you have
- The artifact: <path or description>
- The claim's evidence so far: <paths>
- Facts already established (do not re-derive): <ids, counts, dates>

## Rules (read-only)
- Do not change, commit, push or fetch anything in any repository.
- Work only on copies, in <scratch folder>. Never delete or move a file you did not create.
- Do not open live database files; copy them first.
- Do not start servers, open ports, or run builds or full test suites unless this brief says so.
- Never print, copy or paste a secret. Do not read these files raw: <list the specific files that hold keys>. Use <the masking reader> instead.
- If you break any of these rules by accident, say so in the first section of your report.

## How to work
- Re-run every finding yourself before reporting it.
- For each finding give a command that reproduces it.
- Prefer small synthetic counterexamples: a minimal input that fools the check.
- If you find the same kind of hole more than twice, say so: it may be a class, not an instance.
- Ask of any agreement between two checks: what do both assume?

## Report (write to <path>; last line exactly: REPORT-COMPLETE <key>)
1. **Caveats, breaches and exposures (read first).** Anything you could not do, did beyond scope, or printed by mistake. "None" if none.
2. **Verdict.** One of: HOLDS | HOLES | COULD NOT CHECK.
3. **Findings, most severe first.** For each, at most eight:
   - id, severity (HIGH / MEDIUM / LOW), FAIL or LIMIT
   - what is wrong, in one line
   - evidence, and whether you reproduced it (yes / no)
   - the reproducing command
   - the smallest fix you can see, and what it would cost on real inputs
4. **What I did not check.** Be specific.
5. Limits / not done (see the footer template).

REPORT-COMPLETE <key>
```

---

## After the verify step

- Follow it with a short **resolver** step that re-checks every finding marked HIGH, so a wrong refutation does not stand.
- Every reproduced counterexample becomes a permanent test with its expected verdict ([3.14](../docs/03-evidence-and-verification.md#314-every-counterexample-a-refuter-finds-becomes-a-permanent-test)).
- Confirming a fix is deterministic: run the test, read the exit code. Not another opinion.
- If three rounds still leave major findings, change the approach or take it to the decider ([5.15](../docs/05-orchestrating-agents.md#515-shape-verification-deliberately)).
