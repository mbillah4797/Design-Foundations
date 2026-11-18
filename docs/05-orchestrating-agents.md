# 5. Orchestrating agents

Most of the work here is done by agent workflows: a script launches many agent steps, each with a brief, and an orchestrating session reads their results. This chapter covers how we split work, survive usage limits, brief agents, check what they return, and let several long-running sessions work alongside each other without stepping on each other.

Some of the mechanics are specific to the tools we use (Claude Code workflows and subagents, plus builder and reviewer tools from other vendors). The rules underneath should carry to any agent framework.

---

## Cost and capacity

### 5.1 Give each step the cheapest model tier that still gets it right

**Rule.** Every step gets the smallest model tier that does it correctly. The smallest tier only handles work over a known set of items (counted by a script before launch: copy with file and line, convert, classify into a fixed list), and always with a mechanical check (the count matches, every id is covered). It never does open-ended searches, commits, security work or anything a stakeholder reads. A middle tier does tracing, derivations and research legwork that a top-tier agent then fact-checks. The top tier does building, fixing, review, verification, synthesis and anything security-related or stakeholder-facing. Counts and single facts are a shell command, not an agent.

**Why.** Capacity, not just money, was the binding limit. A measurement window showed almost every workflow message ran on the top tier, and agents that inherited the highest effort setting appeared to cost noticeably more than the same work at the default (a first measurement, confounded by the kind of step; still to be re-measured). Every agent's first call carries a large fixed context, so spawning one to count lines is waste. Outside benchmarks report the smallest tier as worse on omissions and false "done", which is why its steps are fenced by checks.

**How to apply.** Pass the model and effort explicitly to every subagent; built-in agent types bring their own defaults. Do not run routing experiments on steps that gate a release or feed a trusted dataset. The quality risk is a check that passes wrongly, not the cost.

---

### 5.2 A failed check escalates one tier; a null or a limit message stops the run

**Rule.** Every agent call goes through one wrapper. If the result fails its check or looks like a refusal, the step re-runs one tier up with the failure reason in the prompt. A null result or a usage-limit message throws and ends the run as "needs resume". It is never treated as an answer.

**Why.** An early run cached a usage-limit message as if it were a result and handed it to the next step. Escalating on a limit just drives the next tier into the same wall.

**How to apply.** Match limit text only in short replies, so a report that quotes a limit message does not trip it. A step that fails on every tier says so and points at the check or the prompt; resuming would only replay the same failure. When you wrap an existing call by find-and-replace, make sure the wrapper itself still calls the real function (ours once recursed into itself).

---

### 5.3 Make usage-limit resistance structural, and enforce it with a guard

**Rule.** Every workflow script starts from one shared, versioned preamble (the wrapper, model constants, a read-only rule block, a resume rule block, a checkpoint block). A pre-launch hook refuses any script without it, any bare agent call, any parallel step whose results are not checked for nulls, and any launch by name that the tool would resolve to a different script. A post-launch hook registers every run.

**Why.** A usage limit kills agents mid-run. The run then sits with no result and nothing resumes it. The engineer did not want to keep repeating the same work every time the session and its workflows hit the limit together. Good intentions in each script did not hold; a guard did.

**How to apply.** Test the guard. It took several rounds before ours checked the script the tool would actually run rather than the one named. A guard that itself fails should prompt with a fix hint, not silently allow or silently block.

Held by a gate: the hook refuses non-compliant scripts.

---

## Surviving interruption

### 5.4 Resume, never relaunch, and resume with identical arguments

**Rule.** After a limit, wait for the reset, then resume the same run by its id with the exact saved launch arguments.

**Why.** Finished steps replay from cache only if everything before them is byte-identical. Resuming without arguments once returned an error instead of work. A run resumed straight back into the same limit only repeated work.

**How to apply.** Save the launch arguments when a run is registered. Put values that enter prompts (such as today's date) into the script as literal constants, so a resume stays identical. After a limit, report "wait until (time)", not "resume now".

---

### 5.5 Do not resume a parallel fan-out; recompute what is missing

**Rule.** A resume replays an unbroken prefix of calls in order. In a parallel fan-out, finished steps return instantly, the dispatch order shifts, and the prefix breaks after the first item. So for a parallel run that stopped on a limit, compute what is still missing from all the run records and launch a fresh, narrow run for just that.

**Why.** In several large batched runs, only the first batch replayed on resume; the readers for every later batch ran again, and again on the second resume. That was dozens of top-tier agents wasted.

**How to apply.** Mark the old run superseded so nothing resumes it automatically. Give reader steps a check that names the missing ids, so one dropped id costs one retry rather than a failed batch.

---

### 5.6 Checkpoint every long step to a file with a sentinel last line, and check the file

**Rule.** Each long step keeps running notes in a progress file and writes its final report to a file whose last line is a fixed sentinel. If the report already exists with the sentinel, a re-run returns it and stops. The orchestrator checks the *file's* last line; the step returns a small structured answer (path, last line, short summary).

**Why.** A resume re-runs every step after the first one that is not cached, so cheap-to-repeat steps keep a limit from costing more than one step. Checking the returned text instead of the file failed badly: in one run, many middle-tier steps returned a summary instead of the report (the file on disk was complete), failed their check, re-ran on the top tier, and one failure there ended the run.

**How to apply.** Put the sentinel and the progress-notes instruction in every brief ([template](../templates/agent-brief.md)).

---

### 5.7 Keep steps small; run anything long detached

**Rule.** Plan, build, each review round, each test run and each verify pass are separate steps with their own report files. Anything that may run longer than a few minutes runs detached with a log and an exit-code file; the step returns the log path, and the orchestrator does the waiting.

**Why.** One big agent loses all its in-flight work on a limit. The agent prompt cache is short-lived, and a large share of our cache writes turned out to be rewrites from agents idling on long commands. A release step wrapped in a too-short timeout was killed after most of its work and had to be repeated.

---

### 5.8 Watch your own runs, and believe the real state over your records

**Rule.** While a workflow is live, the session that launched it runs a recurring watchdog that classifies each run (running, stalled, waiting on a limit, needs resume, finished), resumes the ones that need it, and leaves alone the ones waiting on a limit.

**Why.** A resume only works from the session that launched the run, so each session needs its own watchdog. Once, our watchdog still listed two runs as running while the workflow view showed them paused on a usage limit. Trusting the records would have left them stuck.

---

### 5.9 After any resume or restart, list scheduled jobs and cancel the superseded ones

**Rule.** A resumed session keeps its scheduled prompts, and they fire. The first action after a resume is to list them and cancel any a newer decision superseded. Background watchers get a time limit and an exit condition. "Nothing of mine is still running" is a process check, not a memory.

**Why.** A scheduled prompt fired minutes after the human had closed the thing it was for. It was caught and skipped, but it could have spent capacity or written to a stakeholder's document. Several background watchers outlived their pull requests by days with no owner.

**Also.** Never block a session on an interactive question while scheduled jobs or workflows depend on it. One pending question froze automated lane management for most of a day. Ask in plain text and keep working.

---

## Briefing and checking agents

### 5.10 Brief agents like peers: give them the rules and the facts

**Rule.** The brief carries everything the step needs: the goal and who it is for; what is already decided or ruled out; the exact rules for this step (read-only, resume, scope, authority); where to write and the sentinel; the return format, caveats first. Facts the orchestrator can derive (ids, counts, dates) go into the brief rather than "go and find them".

**Why.** Agents inherit the instruction files their launching session loaded at its start. Long-lived sessions carrying large instruction files paid several times more per first call than fresh ones. We cut the shared file hard and moved step-specific rules into briefs.

---

### 5.11 A read-only brief lists what is forbidden, and the orchestrator checks afterwards

**Rule.** "Read-only" is not enough. The brief names: no git writes (not even fetch); no bytecode caches; probes only in a scratch copy; never open a live database file (copy it first); no service ports; never write to stakeholder-owned or system locations; never delete a file you did not create; never print a secret. After the run, scan for any new caches or fetch records and disclose what you find.

**Why.** Agents told only "read-only" ran `git fetch` in a shared repository and wrote caches into a frozen evidence folder. Another removed a shared database copy a different session depended on.

---

### 5.12 Never edit what is running

**Rule.** A shell reads a running script by byte offset, so editing it in place makes the running process execute half-lines or skip commands. Stop it by exact process id, edit, restart; or copy to a new versioned name. Never trust a run that overlapped an edit. For workflow scripts, edit only steps that have not started, and keep earlier prompts byte-identical so a resume still replays them.

**Why.** Two long loops and a running test were ruined in one day by in-place edits. A run that overlapped an edit is a possible false green.

---

### 5.13 Agents report caveats first, and the orchestrator reads them in full

**Rule.** Every report opens with "Caveats, breaches and exposures (read first)", then the verdict, then findings. At every hand-off the orchestrator searches each report for limits, not done, breach, printed, exposed, secret and leak, and reads those sections in full before acting on the verdict. A security item goes to the human at once.

**Why.** A build agent honestly reported, at the bottom of its report, that it had printed a per-build key into its log. The orchestrator read the green headline and moved on. A later verify step caught it, and the orchestrator told the engineer: the miss was the orchestrator's, not the agent's. See [case study 4](../case-studies/04-the-caveat-at-the-bottom-of-a-green-report.md) and the [footer template](../templates/limits-not-done-footer.md).

---

### 5.14 Agents never gate: the orchestrator re-reads the artifact

**Rule.** A step that gates a merge, a release, an install or a write into a trusted dataset never trusts an agent's quoted result. The orchestrator re-reads the artifact itself. Confirming a fix is a deterministic step: run it, read the exit code and the result file. The model is never the guard of production.

**Why.** Agent-quoted results have lied. An assistant wrote its "done" sentinel before it pushed, with many files still uncommitted. A closing script without fail-on-error posted "done" after a failed step. A document was rewritten after its "done" signal, so the reviewer's copy matched only an earlier moment.

**How to apply.** Before acting on "done", compare the remote head with the commit the report names and check the working tree. Name the exact snapshot a verdict covers.

---

### 5.15 Shape verification deliberately

**Rule.** Refuters take a small batch of findings each (we use at most eight) and return, for each, an id, a verdict, the evidence, whether they reproduced it, and the command. A refuted blocker gets a short resolver re-check. A second verifier only for blockers, and only with a different lens. Never two agents on the same question. After three fix-and-review rounds with majors still open, change approach or go to the human.

**Why.** Verification was most of our workflow steps and over half the cost. Measured, batched refutes found far more than one-claim-at-a-time refutes, and review-style lenses more again. A fixed shape keeps the largest cost bounded. See [3.11](03-evidence-and-verification.md#311-builders-do-not-certify-their-own-work) and [3.12](03-evidence-and-verification.md#312-set-the-bar-before-the-round-when-findings-do-not-fall-close-the-class).

---

### 5.16 Builder agents run under a scope contract with a log and a control file

**Rule.** When an agent builds (rather than reviews), it runs under a contract: an allowed-file list, a time budget, a cap on changed lines, a one-line action log written at least every few minutes, a control file it reads before each log line (STOP or NARROW), a stop after the same failure three times, and a scope check at the end. A watcher prints one line per builder and flags only the abnormal: stalled, out of scope, too large, looping, dead.

**Why.** One builder tool tended to wander into extra work and get stuck building. Reading every line would cancel the speed, so we check a log and act only on flags. Headless builder runs that read standard input hung for many minutes until input was closed. A fleet of builders on the fastest setting at the highest effort burned through a large share of a weekly quota in hours, so the default became standard speed with escalation per job.

---

## Sessions working together

### 5.17 Talk through an append-only log, read from a saved line

**Rule.** Sessions talk through a shared, append-only log with a fixed line format (time, sender, recipients, message). Each reader keeps a saved line number and reads everything after it, treating every line not signed by itself as a peer post, whatever the signature. Signal files that others watch are written atomically: write a temporary name, then rename.

**Why.** A session filtered the log for "lines newer than my last post" and missed a stakeholder's assignment for about two hours; the human had to get a peer to relay it. Two signal files landed empty because of a shell redirection mistake; a watcher that sees a half-written file is worse than one that sees none. A waiter fired early because a hold message *quoted* the trigger word.

**How to apply.** Match an outcome word only at the start of a message and only from the role that owns it. Keep a periodic backstop whenever a step waits on a peer. Put long content in a file and post its path.

---

### 5.18 Hand findings to the owner with the severity call explicit

**Rule.** Sessions may sweep code they do not own and hand findings to its owner, with severity stated (latent or live, and what would have to change for it to arrive), verified from the real state before relaying. Say plainly when you might be over-reading. Offer options (fold it into your change, I take it after yours lands, file it) and let the owner pick. Record findings durably, including rejections and retractions.

**Why.** In one night, the four best defects anyone found came from three or four sessions sweeping code outside their own areas. They had nothing to defend, and because they could not just fix it they had to make the case in writing, which forced the severity question. A wrong report from a peer was retracted within the hour, and recording the retraction was as useful as a finding: a rejected finding that is not written down looks the same as one nobody read.

---

### 5.19 Give the always-loaded instruction file a byte budget

**Rule.** The instruction file every session and subagent loads has a hard size budget, exactly one "now" block that is replaced rather than stacked, names and routes rather than values that move, and pointers to the one owning document for each fact.

**Why.** Before we cut it, the shared file cost on the order of a hundred thousand tokens per session, paid by every agent. Values that move without an edit (counts, commit ids, test totals) go stale; a command that reads them does not.

---

### 5.20 One accountable release owner across many agents

**Rule.** One named role owns releases: it sets the order, places and lifts the merge hold, posts STARTING and DONE, and re-reads every receipt itself. A separate named role signs off the exact bytes visitors will see. Everyone announces merges and installs in the shared log.

**Why.** Many agents can build in parallel. Only one can be accountable for what reaches users at a given moment. See [4.12](04-gates-releases-and-reversibility.md#412-a-merge-does-not-deploy-releases-are-serial-owned-and-announced).

---

### 5.21 Build the next release while the current one finishes, with placeholders that fail closed

**Rule.** While release N is still in progress, the kit for N+1 can be built (no heavy lock, no build), with every value that depends on N's outcome marked PENDING. A PENDING value blocks the release until a script derives it after N is done.

**Why.** It lets parallel agents prepare while the serial release proceeds, without any risk that a guessed value ships.

---

### 5.22 When instructions conflict, follow the primary record and say so

**Practice.** An agent given contradictory instructions re-reads the primary record of the authority (not the relayed copy), follows the newest explicit instruction, states the conflict and its resolution in its caveats, and leaves anything downstream fail-closed.

**Why.** We saw an agent do exactly this: its brief said a set of approvals stayed pending while an override said to record the human's answer once a dependency was built. It re-read the human's answer in the decision log, disclosed that the authority had reached it by relay, and left the next pin PENDING so nothing could release on its reading alone. This is an observed behaviour we want, not yet a written rule.

---

## Checking a build split across agents

### 5.23 After a fan-out build, check the spec against the union of the briefs

**Rule.** When one specification is split into briefs for several builder agents, give every requirement an id and check that each id appears in at least one brief, before the build starts and again before calling it done. Then walk the user's path end to end, naming each control the user touches.

**Why.** A feature once shipped complete on every layer (guard, refusals, wiring, record, hundreds of green checks) and no user could trigger it. One requirement was in the specification and in none of the briefs. Every agent did its part well, and no gate could have caught it, because nothing that was built was wrong. That is the characteristic failure of splitting work across agents: each part looking finished is exactly what hides the missing one.

**Cost.** Minutes, once requirements have ids. Give them ids.

---

## What this chapter does not claim

- That these numbers (eight findings per refuter, three rounds, a few minutes before detaching) are right for you. They are what we chose; the comparison that would tune them has not run yet. Measure yours.
- That the guard and the preamble remove the need for judgement. They make the common failures impossible. The uncommon ones still need an orchestrator who reads.
