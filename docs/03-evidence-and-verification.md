# 3. Evidence and verification

The single most useful document we keep is a register of the ways our evidence lied to us. It has dozens of entries. Almost none of them are cases where a check was missing. They are cases where a check existed, ran, and said something true about the wrong thing.

This chapter is about what counts as evidence, how checks fail, and how to verify work when the builder, the checker and the reviewer may all be AI agents.

---

## What counts as evidence

### 3.1 Green is not working: look at the thing itself

**Rule.** A result counts only when someone has looked at the outcome itself: the built artifact, the stored row, the rendered page, the live response. Not the exit code, not "deployed", not "200 OK", not "merged".

**Why.** A deploy once built, reported ready and answered every request with 200 while a feature was silently off, because a build-time flag had been stored in a way that compiled to a placeholder. Another deploy went ready while a piece of middleware shipped as uncompiled source, and every matching route failed for minutes; a project setting had silently changed. A page walk once loaded every page and the author called the app working; every *write* action on those pages was broken.

**How to apply.**
1. After a deploy, check the feature in the rendered page, not the status code.
2. Before a deploy, inspect the build output: the expected builder, compiled code where compiled code belongs, a known marker string present.
3. Test writes, not just loads.
4. A search that finds nothing in a minified bundle is not proof of absence. Compare against the previous build.

---

### 3.2 Verify on real infrastructure, not just locally

**Rule.** Push to a branch, watch the real run on the real runner, and read its real log. Local green is a different environment.

**Why.** Our first gates passed every local test and then hung on the real CI runner because of input, flag and exit-code differences. A gate that read git history ran green for days on full local clones and died on its first CI run, which used a shallow checkout. A local walk over plain HTTP invented bugs that only exist without a secure context. A clean local server could not show the hosting platform's runtime settings, so every page failed on the real platform despite a perfect local score.

**How to apply.** Expect a gate's first real run to fail for environmental reasons. Reproduce the runner's conditions before pushing. Save a failed run's log before re-running it.

---

### 3.3 Prove a mechanism by firing its failure path once, for real

**Rule.** Before trusting an automated mechanism, exercise it end to end on real infrastructure, including the failure path: detect, block, report. A mechanism that has never fired is "unproven", not "working".

**Why.** We proved a conflict-detecting gate by filing a deliberate conflict on the real host and watching it block and open an issue. The other side: an alert hook placed in the wrong section of a service definition was silently ignored, so the alarm could never fire although the file looked right. A monitoring endpoint answered "OK" for a check that did not exist, so every job looked monitored while nothing was.

**How to apply.** Cause the failure on purpose. Let a monitor go red once. Read applied configuration from the running system, not from the file you wrote.

---

### 3.4 Absence of signal is not all-clear: count the participants first

**Rule.** Any check that aggregates results (reviewers, tests, probes, a jury of agents) first asserts that it received the expected number of results. Zero findings from zero reviewers is a failure.

**Why.** In one review round, every reviewer agent died on a usage limit. The orchestrator filtered out the failed ones, saw an empty list of findings, and computed "passed". The next real run of the same round found two major problems, one of which would have reached customers. Variants of the same failure: a parse error compared against a threshold and read as "all clear"; a test probe that planted nothing and so found nothing; a waiter whose condition could never be true, which looked exactly like "still waiting".

**How to apply.**
1. Assert `results received == results expected` before reading any content.
2. Read the errors before the result.
3. Comparison helpers fail closed on null, empty or unparseable input.
4. When you plant a defect to test a check, count the plant before you read the verdict.
5. Test a wait condition once by hand before relying on it.

---

### 3.5 A fixture is not the world

**Rule.** For any "X never appears" or "every Y is handled" assertion, confirm the test data actually contains an X, and a Y of every kind. Before a gate certifies a launch, run its real function over real, live-shaped data.

**Why.** A test asserting that a forbidden string could never reach an output passed while real cases got through: its fixture had an empty list. An acceptance tool was green on dozens of fixture tests and stopped on the first real data, which came from a different source. A gate replayed over all real stored cases went red on most of them; the fixture-only green had certified the fixture. A release allowance measured on a fixture world missed a kind of output that only real data produces, and a correct release was rolled back ([case study 2](../case-studies/02-three-stops-that-were-the-system-working.md)).

---

### 3.6 A check must measure what its sentence says

**Rule.** An assertion's sentence is the claim; its code is only evidence for it. For each assertion, write down a change that would satisfy the code while making the sentence false. If you can find one, the check is weaker than it reads.

**Why.** This is the most common failure in our register. A check that "the value is present" kept passing after the real claim ("the user sees the high end of the range") went false. A check that "the shared formatter is used" passed through the exact regression it was written to catch. A check over a collapsed section could never fail. A test environment without the real stylesheet made every size assertion meaningless. A source scan proved a piece of code *existed*, not that it was *reached*: deleting the place it rendered left almost every such check green.

**How to apply.**
1. Ask "what would pass this and still be wrong?"
2. Put guarantees on the built artifact, not only on source.
3. When every failure in a red run says "element missing", suspect a build or configuration mismatch before you suspect the code.
4. A gate is defined by the artifact it runs against *and* the configuration its name claims. Check both.

---

### 3.7 Red-first means reinstating the defect, plus a control that must not move

**Rule.** To prove a check works, reinstate the real defect and watch the check fail. Then add a near-miss case that must stay green. Correcting an expected value in a check proves only that the value changed.

**Why.** A fix that changed an expected number in a gate "went red against the old code" by construction, which proved nothing. Reverting the real fix reddened three of four new checks; the fourth, a control where two fields happened to be equal, correctly stayed green, and that is what made the three reds meaningful. Without the control, a wrong fix would have satisfied everything.

**How to apply.**
1. Keep a copy of every file before you change it.
2. The new test runs against the pre-fix copy and must fail, then against the fixed file and must pass.
3. Add a control that must not change.
4. Use the word "red-first" only when the red came first.

The [glossary](glossary.md) calls the planted-defect half of this pair a *red twin*.

---

### 3.8 Never weaken a check to get green, and never lose what a check stands on

**Rule.** A red result is information. Fix the code, or fix a wrong assertion with a stated reason and a reinstated-defect proof. Never delete, skip, loosen or re-point a check just to turn it green.

**Why.** A clean-up of "regenerable" build output deleted directories that release tests used as pinned evidence. Production builds are not byte-for-byte reproducible, so those bytes could not be rebuilt, and the obvious next step was to skip the failing tests. That would have turned a gate into a decoration.

**How to apply.** Keep pinned test evidence in a named folder that no clean-up touches. Search every test tree before deleting build output. If a shortcut would weaken a gate, say so plainly in your report and stop.

---

### 3.9 The only red is only the first red

**Rule.** A CI job that stops at its first failing step tells you nothing about the steps after it. Before pushing a fix, run every step of every job locally, in order, with the same flags. And read the workflow to confirm the check you rely on is actually wired into a blocking step.

**Why.** We repeatedly fixed one red, pushed, and paid for a full CI run only to meet the next red behind it. Separately, several gate scripts existed in a repository and were wired into nothing; only grepping the workflow file found them.

---

### 3.10 The tree you tested must be the tree that merges

**Rule.** Compare against the current remote main branch, not a local copy and not the branch's old base. Before installing, confirm the merged tree is the tree you tested.

**Why.** A proof that "switched-off code equals main" compared against a merge base while main had moved on several commits; a reviewer caught it. A fix that lived only in a working tree was reverted by the author's own checkout command, and the merge shipped without it. A pipe inside a chain of commands hid a failed merge, and the next step built over a conflicted tree.

**How to apply.** Diff against the remote. Do not pipe a step whose exit status matters. Verify *who* landed a merge, not just that the state says merged.

---

## Adversarial verification

### 3.11 Builders do not certify their own work

**Rule.** Every build step is followed by a separate verify step run by a different agent, briefed only to find holes. Its report opens with caveats, then a verdict, then findings most severe first, each with a command that reproduces it. Every critic is followed by a resolver that re-checks the findings it marked as blockers.

**Why.** An agent grading its own work shares its own blind spots. Verification turned out to be most of our agent work by volume, so we designed it deliberately rather than bolting it on.

**How to apply.**
1. Verify steps get read-only briefs ([template](../templates/adversarial-verify-brief.md)).
2. Confirming a code fix is a deterministic step: run the test, read the exit code. Not another model's opinion.
3. The verify report lists what it did not check.
4. Judge a verifier by how often it is wrong when it refutes, not by how much it finds.

---

### 3.12 Set the bar before the round; when findings do not fall, close the class

**Rule.** Before an adversarial round, write down what counts as a failure. Run refute passes until a pass finds nothing above the bar. If the number of findings does not fall, stop patching instances. Close the whole class instead: define exactly what the check accepts, measured from real outputs, and send everything else to a human or a stricter check. Write a contract that names what the check does not cover.

**Why.** A proof by reading source ("only the switch loads this code") went through six rounds, each finding a stranger disguise of the same hole. On a second gate, successive independent refute passes found new holes; the count fell, then rose again on the fourth pass. Patching each trick did not converge. What closed it was an exact grammar of the shapes real builds produce, with anything outside it refused, plus a written contract.

**Cost.** Days of agent effort per gate, and many review passes. Only justified for a gate that decides whether a release ships.

---

### 3.13 For high-stakes judgements, two independent implementations must both agree

**Rule.** Build two checks that share no parser, no author and no assumptions, and accept only when both say yes. "One said no" or "one could not judge" means no. An unknown shape means no.

**Why.** For one release we had to prove that a large change, shipped switched off, changed nothing people could see. One tool was general-purpose; the second was written separately, on a different parser with its own analysis. Each caught holes the other missed. Then a verify pass found two holes that slipped through *both*: one because both tools had copied the same pattern for one kind of code, one because neither read a rare way of writing module ids. Agreement cannot see a shared assumption.

**How to apply.**
1. Different authors, different libraries, and where possible a different model family.
2. The builder of the second tool does not adopt the first tool's newer version mid-step. When that happened to us, the builder stopped, recorded it, and left the decision to the owner.
3. List every assumption the two tools share, in writing. Those are not covered by their agreement.

See [case study 1](../case-studies/01-proving-nothing-visible-changed.md).

---

### 3.14 Every counterexample a refuter finds becomes a permanent test

**Rule.** When an independent refuter produces an input that fools your check, keep it forever as a paired fixture with its expected verdict. Your own check must catch it alone, with the other tool removed.

**Why.** Each refute pass we ran produced small synthetic cases that differed in behaviour but looked equal. Turning every one into a permanent test, pinned by checksum, means a later change cannot quietly reopen a hole someone already closed.

---

### 3.15 Use independent judges for judgement too, and judge the judges

**Rule.** When an automated model judges something, pin the model and the tool version, scope its evidence, run order-swapped twin verdicts, derive severity by rule from the pair of verdicts (never by the model), and give "the judge itself failed" its own exit code that can never read as a pass. Have it read the change before the author's description of it.

**Why.** An audit of our own gates found a blocking judge whose output was never validated and a model that was not pinned, so verdicts flapped night to night. A sweep that looked green had crashed.

---

## Stating what a result means

### 3.16 Every check states its contract

**Rule.** Every tool and gate says what "pass" covers and what it does not. Every proof says what was and was not measured. An allowance measured on one population says which.

**Why.** An equality tool whose "equal" meant "equal under these masks" was misread until a written contract named the classes it never examined, and pointed to the check that does examine each. Comments that cited a measurement once had their conclusion inverted by an edit while the evidence clause survived untouched.

---

### 3.17 Every audit gets a closing count; samples understate

**Rule.** An audit's defect list gets an owner and a closing count: flagged, fixed, kept with a reason. A property a script can check becomes a gate, not a sample. For any filter or mask, compare its output with the previous version on real data.

**Why.** An extraction audit passed its bars while dozens of flagged items stayed flagged. Two samples read almost perfect while the full sweep changed about a tenth of the results. A redaction tool passed hundreds of tests aimed at what it might *leak* and masked real data in thousands of files, because nobody had tested what it masked by mistake.

---

### 3.18 Declare the failure direction

**Rule.** For each component, say up front which way it fails. Authority, spend, secrets and entitlements fail closed. Product features degrade honestly, with a visible "unavailable" and the reason kept out of user text. A check that could not run reports "could not check" as its own state.

**Why.** Our release steps stop with nothing written whenever a pin is stale, a measure disagrees or a state is unknown. Two such stops in one day cost about half an hour and wrote nothing. The alternative was a wrong release.

---

### 3.19 A sealed test is looked at once

**Rule.** A final evaluation set is sealed before its labels exist. Each look is logged to a tamper-evident record. One look. If a sealed test has to be voided, the void is a recorded human decision, and any later number on those rows is reported as an ordinary unsealed read, never as blind.

**Why.** The tooling for one prospective test was never built, and the decider chose to train on those rows instead of waiting days. The record keeps the rest of the evaluation honest: nothing about those rows can be passed off as a blind result again.

---

### 3.20 Stamp times from the clock and re-read live values right before use

**Rule.** Run the clock command before writing any timestamp. Read live values (pins, counts, current selections) right before the step that uses them. In shared documents, prefer a command that reads a value to a pasted copy of it.

**Why.** Timestamps written from memory were wrong often enough to make a rule. A release kit pinned live data that the product moved between kit build and use, and the pre-release check stopped twice; each stop looked like a regression until the data history was read.

---

### 3.21 Make evidence reproducible

**Rule.** Record the exact inputs a result came from (hash them). Run long verification steps detached, with an exit-code file, never inside a timeout shorter than their known run time. Never trust a run that overlapped an edit of its own script. Write the runtime version beside every recorded run.

**Why.** A step wrapped in a too-short timeout was killed after doing most of its work, and the whole run had to be repeated. A job run by the system service manager used a different runtime version than the shell, and one test failed only there. A closing script without fail-on-error posted "done" after its failed step.

---

## What this chapter does not claim

- That more checks mean more safety. Every check above can lie. The rules are about making checks that can fail, and noticing when they did not.
- That adversarial verification is cheap. It was the largest share of our agent spend. We reserve the heaviest forms (two implementations, many refute passes) for gates that decide whether a release ships.
