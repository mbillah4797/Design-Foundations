# Case study 4: The caveat at the bottom of a green report

This is a story about a correction: who made the mistake, who volunteered it, and what changed so it would not depend on anyone being careful next time.

## What happened

A release kit was being built by a workflow of agents. One build agent finished its step and wrote its report. The top of the report was green: tests passed, the comparison table matched. At the very bottom, under "Limits / not done", the agent had written, honestly, that while inspecting the previous build's output it had printed a per-build key into its own working log.

The orchestrating session read the verdict lines, saw green, and moved on. It did not read the bottom section.

Two later steps caught it. A verification agent, checking the release record, flagged it as a medium-severity item. The orchestrator then told the engineer directly, in plain words, and wrote it down: the agent had disclosed it properly; the miss was the orchestrator's.

The impact was judged low: the key was specific to one build and guarded a feature that was not in use. But "nothing happened this time" is not a control.

## The second time

The obvious fix was a sentence in the next brief: "never print a secret". The next release's build agent had that sentence. It still printed two per-build keys, this time while comparing two build configuration files side by side. It was not careless. It was doing exactly what it was asked, comparing files, and the files happened to contain keys. A general rule does not tell an agent which ordinary-looking action is the dangerous one.

## What changed

The fixes were structural, not stronger wording.

1. **Name the files, not the principle.** Briefs now list the specific build output files that carry per-build keys, and forbid reading, comparing or searching them raw. ([4.22](../docs/04-gates-releases-and-reversibility.md#422-secrets-in-agent-work-name-the-dangerous-files-give-a-safe-reader-scan-every-report))
2. **Give a safe reader.** A masking reader that prints field names and lengths, never values, became the only sanctioned way to look inside those files.
3. **Caveats first.** Reports now open with a "Caveats, breaches and exposures (read first)" block. The same items are repeated under "Limits / not done" at the end. ([5.13](../docs/05-orchestrating-agents.md#513-agents-report-caveats-first-and-the-orchestrator-reads-them-in-full))
4. **Search before trusting a verdict.** At every hand-off the orchestrator searches each report for limits, not done, breach, printed, exposed, secret and leak, and reads those sections whole before acting on the headline.
5. **Scan every report.** A final scan of every scratch and report folder for every per-build value is part of each report, with its count.

After the change, later agents reported their own minor boundary slips in the opening caveats block (a test run that went slightly outside its read-only scope, a scratch copy deleted seconds later), where the orchestrator saw them at once.

## The related failure: the empty jury

Months earlier, the same lesson had arrived from the other direction. A multi-round adversarial review of a specification ended with "passed". In fact every reviewer agent in that round had died on a usage limit. The orchestrator filtered out the failed results, found zero findings, and computed a pass. The next real run of the round found two major problems.

Both stories have the same shape: the information that mattered was present (an honest caveat; a list of failed agents), and the orchestrator read the part that was green. The fix in both cases was mechanical: count the participants before reading the verdict, and read the caveats before the headline. ([3.4](../docs/03-evidence-and-verification.md#34-absence-of-signal-is-not-all-clear-count-the-participants-first))

## Why we tell this one

Trust in an agent-run system is built less by what goes right than by what happens when something goes wrong. Here:

- The agent disclosed its own mistake, in the place it was asked to.
- The orchestrator owned its miss to the decider, without being asked, and recorded it.
- The fix was a change to the machinery, not a promise to be more careful.

A team where agents and orchestrators volunteer corrections, and where the response is a better process rather than blame, is a team whose green results mean something. ([6.8](../docs/06-communicating-with-non-technical-owners.md#68-volunteer-corrections-in-the-place-the-reader-saw-the-claim))

## What a stranger can take from it

- Put a "Limits / not done" section in every agent report, and read it first. The [footer template](../templates/limits-not-done-footer.md) is a starting point.
- "Never do X" in a brief is weak. Name the objects that make X easy to do by accident, and give a safe alternative.
- When you miss something, say so to the person who relied on you, plainly and quickly.
