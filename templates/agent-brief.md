# Template: an agent brief

The skeleton we use for any step in an agent workflow. It assumes the agent knows nothing that is not in the brief ([5.10](../docs/05-orchestrating-agents.md#510-brief-agents-like-peers-give-them-the-rules-and-the-facts)). It is written so the step can be interrupted and re-run cheaply ([5.6](../docs/05-orchestrating-agents.md#56-checkpoint-every-long-step-to-a-file-with-a-sentinel-last-line-and-check-the-file)).

---

```markdown
# <Step name> (<key>)

## Checkpoint
If <report path> exists and its last line is "REPORT-COMPLETE <key>", return its content and stop.
Otherwise keep running notes in <progress path> (reuse any an interrupted attempt left),
and write the final report to <report path>, ending with the line "REPORT-COMPLETE <key>".

## Goal
<What this step produces, and who will use it.>

## Already decided or ruled out
- <decision> (decided by <role>, <date>)
- <option we are not pursuing, and why>

## Facts you can rely on (do not re-derive)
- <ids, counts, dates, paths the orchestrator already established>

## Scope
- You may write only: <paths>
- You may read: <paths>
- Out of scope: <anything nearby the agent might wander into>

## Authority (only for steps that act outside the scratch folder)
AUTHORIZATION: <the standing instruction, quoted verbatim, with who said it and when>.
This allows: <exact list of outside actions>.
This does NOT allow: <e.g. merging, deploying, messaging anyone>.

## Rules
- <the read-only block, if read-only: see the adversarial verify template for the full list>
- Never print or copy a secret. Do not read these files raw: <list>.
- Run long commands detached with an exit-code file; do not wrap them in a short timeout.
- If anything here conflicts, follow the most specific and most recent instruction, and say so in your caveats.

## Return
Reply with only:
{ "reportPath": "<path>", "lastLine": "<the file's last line>", "summary": "<one or two lines>" }
The orchestrator checks the file, not your reply.

## Report shape
1. Caveats, breaches and exposures (read first)
2. Verdict / result
3. Detail
4. Limits / not done (footer template)
REPORT-COMPLETE <key>
```

---

## Notes for the orchestrator

- Pass the model tier and effort explicitly. Use the cheapest tier that gets the step right, and attach a mechanical check when the tier is small ([5.1](../docs/05-orchestrating-agents.md#51-give-each-step-the-cheapest-model-tier-that-still-gets-it-right)).
- Never trust the summary for anything that gates a merge or a release. Re-read the artifact ([5.14](../docs/05-orchestrating-agents.md#514-agents-never-gate-the-orchestrator-re-reads-the-artifact)).
- Read the caveats and "Limits / not done" sections before the verdict.
