# 4. Gates, releases and reversibility

Letting agents release to production is only reasonable if three things are true: every change goes through the same gates whoever wrote it, a release refuses to proceed when anything it depends on has moved, and every release can be undone in one step that was named before it shipped.

This chapter is the machinery behind those three. Some of it is heavy. We say where it is only worth it for frequent, agent-run releases.

---

## Pull requests, review and merges

### 4.1 Work through the gates, never around them

**Rule.** Every change goes through the same pull request and required-check path, including an agent's own small fixes and bookkeeping. When a guardrail blocks you, change your approach. If the only way forward is to loosen a gate, say so and stop.

**Why.** A guardrail that blocks is usually right. Once, a direct push to an unprotected branch made a whole queue of pull requests show as merged at the same moment. The badge recorded that the commits were reachable, not that anyone had approved them. Branch protection went on before the next wave.

**How to apply.** Gates apply to agents and humans alike. Verify *who* landed a merge, not just that it says merged. A broken gate is fixed by its own reviewed change, never by disabling it in passing.

---

### 4.2 When self-approval is impossible, use a stated substitute gate

**Rule.** If a required human review cannot be satisfied for agent-authored changes, replace it with a stated substitute gate that needs two proofs: an adversarial review with no open major finding, and the full test suite passing on the *merged* tree, not just the branch.

**Why.** Our one-review rule could not be met for agent-authored changes, so the engineer accepted this substitute. Testing the merged result matters because defects that exist only in the merge are real and recurring.

**How to apply.** Merge with one plain command, not a chain. Install only after the merged tree matches the tested one. Report every such merge.

---

### 4.3 Before merging, read every reviewer finding on every commit

**Rule.** Wait for the reviewer's completion signal for the final commit. Then list every inline finding across all commits, tick each one as fixed, answered, or outdated and moot, and only then merge. Top-severity findings block. "Reviewer hit a limit" or "review failed" means not reviewed.

**Why.** Our in-house checks kept signing off minutes before the outside reviewer posted, so blocking findings merged unread, more than once. Later, a clean summary on the final commit hid an unfixed blocking finding on an earlier one. The review tool re-attaches unchanged-line comments to each new commit, so filtering by the current commit shows old, answered findings and hides outdated, unfixed ones.

**How to apply.** Paginate. Filter "new this round" by each finding's *original* commit. Look once more at comments created after the merge.

---

### 4.4 Write the severity scale where the reviewer reads it automatically

**Rule.** Define severity levels once, in the instruction file at the repository root that the automated reviewer reads. Give each rule one severity and one owning document. Add a test that the file's references stay true.

**Why.** The automated reviewer had no settings we could tune; its instruction file was the only lever. A single written scale (a secret in the code or deleting received data is the top level; breaking a repository rule is the next; a missing test or a stale fact is the third) made triage mechanical and consistent across builders and reviewers.

---

### 4.5 The review gate comes from a different model family, and reviews statically

**Rule.** The reviewer that gates a merge is from a different model family than the builder. A same-family reviewer adds evidence but never counts as the gate. If the primary reviewer is unavailable, two other non-builder families stand in, and a severe finding from either blocks. External reviewers read code; they do not run builds, servers, browsers or test suites.

**Why.** A reviewer from the builder's own family is not a second pair of eyes. Separately, an outside reviewer briefed only to "run the hermetic tests" ran a full production build outside our shared-resource lock and stalled the machine for hours. Outside tools do not know local conventions.

**How to apply.** A claim that needs a build is written "needs a build" for the orchestrator to run. High-risk changes (money paths, access control, security, installs) get an extra, deeper review in a fresh session. Do not use reviewer features that push fixes outside the normal flow.

---

### 4.6 Review loops converge

**Rule.** A fix round is earned only by a blocker, a correctness defect in what users see, or a red check caused by the change. Allow at most one blocker-only final round; everything else goes to a follow-up list. A review loop that still has major findings after three rounds changes approach or goes to the decider. Never merge with a major finding open. A finding whose premise needs a state measured as absent on the real system becomes a follow-up only after the measurement is cited.

**Why.** See [2.19](02-research-and-design-without-bias.md#219-ship-a-first-version-then-converge): unbounded loops stalled work and burned capacity on polish. Very long specification documents also failed to converge, so the stop is a fixed number of rounds with the remainder handed over in writing.

**When not.** Security, data deletion and money paths keep their blockers blocking.

---

### 4.7 Open code changes as drafts; run the expensive checks once, when ready

**Rule.** Pull requests that change code open as drafts. Local checks and review rounds happen while draft. Mark ready once, when the diff is final; that runs the full battery. Convert back to draft before pushing a fix. On a draft, the required check fails fast with "mark ready". It never skips and never passes, so branch protection is unchanged.

**Why.** Most of our paid CI minutes were testing commits that never merged. Running the full battery only when ready roughly halved the expected cost without removing the independent gate.

**How to apply.** One trap: pushing and marking ready in the same second can cancel one run and make the other see a draft, so no battery runs at all. Push, wait for that run to start, then mark ready.

**When not.** Only worth it when CI is metered and expensive.

Held by a gate for the draft clause: a draft's required check fails fast. Opening as a draft, and converting back before pushing a fix, are habits: nothing blocks a session that opens a ready pull request.

---

### 4.8 Save a failed run's log before re-running; never re-run a green run

**Rule.** A re-run overwrites the evidence of the first failure and costs minutes. Save the log first. Never re-run a run that passed. A failure with zero steps across every repository at once is a platform or billing problem, not your code.

**Why.** When an organisation's CI budget ran out, every run in every repository failed in seconds with no steps executed. It looked like a code break until someone noticed that nothing had run anywhere.

---

### 4.9 A repository whose CI has not run reads exactly like a green one

**Rule.** "CI green" only covers steps that are wired and reached. Before calling a change green, read the workflow for the gate you are relying on. After a long CI silence, treat a repository as unverified.

**Why.** One repository went days with no CI run at all and quietly drifted. Gate scripts existed that no workflow called.

---

### 4.10 Shared-clone hygiene when several sessions push to the same repositories

**Rule.** Compare against the remote main branch. One worktree per task. Stage explicit paths and read the commit's file list before pushing. After any tool that rewrites files, account for every line of `git status`. Check open pull requests before taking an id, file name or branch name.

**Why.** Compared against a stale local main, a one-commit change showed over a hundred files. A broad `git add` on a shared clone committed a peer's half-finished edits. Two sessions took the same record id because main did not have it yet. A fresh worktree with no dependencies installed made gates fail to run, which looked like a pass.

---

### 4.11 Stacked pull requests: retarget the children before deleting the base branch

**Rule.** Before merging a pull request that others are stacked on, point each child at main. Never delete a head branch until the merge has completed.

**Why.** Deleting a merged branch closed a child pull request stacked on it, and a closed pull request's base can no longer be edited. It looked like routine noise while work was quietly lost.

---

## The release path

### 4.12 A merge does not deploy; releases are serial, owned and announced

**Rule.** Production releases are built one at a time by a single *release owner* session from a pinned commit. The owner posts STARTING to a shared log before the build and DONE after verification. While a STARTING has no DONE, nobody merges, installs or builds anything that touches the release. Automatic merge is never armed on the release repository.

**Why.** Another session armed automatic merge on a small fix. It merged the moment its checks went green, inside an open release window, moved main under the build, and the release aborted. Production was untouched. Automatic merge has no idea a release is in progress. See [case study 2](../case-studies/02-three-stops-that-were-the-system-working.md).

**How to apply.**
1. One append-only log, one line per event, a fixed format: time, sender, recipients, message.
2. STARTING names the base, the pinned commits and what kind of release it is (code only, configuration, data migration). DONE names what is live, what was verified, and what was *not* verified.
3. At STARTING the owner lists any open pull requests with automatic merge armed and asks their owners to disarm.
4. Readers read the log from a saved line number, not by time (see [5.17](05-orchestrating-agents.md#517-talk-through-an-append-only-log-read-from-a-saved-line)).

**When not.** A single-writer, single-deploy project does not need this.

---

### 4.13 A release is a pinned set of exact inputs, and it refuses on any mismatch

**Rule.** Record the release's base commit, each change's head, tree hashes and sign-off files in a pin file written by a script from records, never typed. Re-derive anything derivable (switch names, route lists) and compare it to the pin. On any difference, refuse and say which input moved.

**Why.** Typed values and stale pins were repeated sources of red in early release kits. Refusing, rather than warning, is what makes an agent-run release safe: the agent cannot talk itself past a mismatch.

**How to apply.** Keep each pin check as a test with a red twin that must refuse. Tests are release-relative: none may pin the previous release's state.

**Cost.** A real kit with hundreds of files. Only worth it where a wrong release is expensive and agents run it.

---

### 4.14 Re-read every pin that follows live data right before you use it

**Rule.** List the pins that read live data. Re-read them immediately before the pre-release check and again before STARTING. If one moved, stop with nothing written and re-pin.

**Why.** A kit pinned which item and which page to check. The product's own content loop changed both between kit build and check, so the pre-check stopped twice. Each stop wrote nothing and cost minutes; each looked like a site regression until the data history showed otherwise.

---

### 4.15 Inspect the artifact before every deploy

**Rule.** Before deploying, assert that the build output names the expected framework builder, that compiled code is compiled, and that a feature's marker is present in the built files. After pulling environment settings, check none of them compiled to a placeholder. After deploying, check the feature in the rendered page.

**Why.** See [3.1](03-evidence-and-verification.md#31-green-is-not-working-look-at-the-thing-itself). Neither of our two worst deploy failures showed anything wrong in the build or deploy output.

---

### 4.16 Measure your build noise, pin it, and abort on anything unnamed

**Rule.** If production builds are not byte-stable, measure the variation. Build from a cold cache (move the cache aside; keep it as data). Pin every known noise variant any earlier build produced, with both hashes, and carry them forward even when the latest measurement did not see them. Read every unexplained difference by its bytes before allowing it. Anything unnamed aborts the release safely.

**Why.** Successive builds of identical code shuffled module ids and order. A warm cache made it worse; a cold one helped but did not remove it. Once the kit dropped a known variant because a new measurement missed it, and the next real build landed on exactly that variant and aborted. Never auto-accept order-only changes: a masked id can hide a swapped dependency.

**Cost.** Minutes per noise round; an abort can cost half an hour. Worth it because an abort is cheaper than a rollback.

---

### 4.17 Name the rollback before the build; a failed sanity check means roll back first

**Rule.** Every release record names its rollback target (the previous deployment), the exact command, and what a rollback would take away. If the post-deploy sanity check fails, roll back first and diagnose after. The rollback step also verifies that production now serves the target and that configuration is unchanged.

**Why.** Two releases were rolled back this way. One was correct code whose allowance had been measured on test data; the other was correct code whose page content had changed after the baselines were taken. Both shipped on a second attempt. Arguing about whose fault a failed check is, while it is live, is more expensive than a rollback.

**How to apply.** For a switch-on, the rollback may be "unset the switch, then roll back the code": the reverse of the roll-out order.

---

### 4.18 Merge dark, switch on separately

**Rule.** Code merged behind an unset switch must behave exactly as the old code. A dark path adds no extra await, no wrapper, no extra asynchronous step, even one that returns nothing. Before merging, prove the dark build's output equals main's with an order-sensitive comparison of two real builds, normalising only build-specific names and per-request random values. Ship dark code as a code-only release, then switch features on with configuration-only redeploys, each its own small reversible release.

**Why.** A change passed its own byte-for-byte tests, CI and review, yet a cross-build check found its page streaming rows in a different order with every switch off. An extra asynchronous wrapper had delayed one render by a single tick. Same page content, different deployed bytes. It would have shipped while claiming "switched off equals today".

**How to apply.** Name the order of switches (one only with or after another). A switch-on needs the human's word ([1.6](01-decision-rights-and-autonomy.md#16-grant-authority-with-a-scope-a-window-and-a-never-list-permission-to-build-dark-is-not-permission-to-ship)).

---

### 4.19 One heavy job at a time, behind one lock, across all sessions

**Rule.** Installs, full test suites, builds, browser batteries and large data copies run under a single shared lock. Use one long wait, not short retries. Never leave a long-lived child process holding the lock. An orchestrator does not run its own heavy work while a release needs the lock.

**Why.** Several parallel heavy jobs on top of CI once exhausted memory and invalidated every CI step in that window. A script left a child running that held the lock after the script exited. A short retry loop kept rejoining the back of a queue and never got a turn.

**How to apply.** When a release or install must run, it may pause the queued waiters, run, and resume them, with a deadline, telling the waiters' owners. Diagnose resource pressure from the system's own pressure and kernel signals before blaming memory.

---

### 4.20 Run long steps detached, with an exit file; test every waiter once by hand

**Rule.** Launch long steps detached, each writing its exit code to its own file, and wait on that file. Never wrap a step in a timeout shorter than its known run time. Before arming a background waiter, run its condition once by hand. Never use a process-matching pattern that also matches your own command line.

**Why.** A waiter's command errored silently and spun, so a verifier's pass sat unseen for hours. A waiter matched its own command line and never saw the job end, holding a release freeze long after the checks had passed.

---

## Data, secrets and records

### 4.21 Keep every piece of data; check test fixtures before any deletion

**Rule.** Never design a path that deletes, prunes, samples away or overwrites data without history. Move build caches aside rather than deleting them. Before any clean-up, search every test and kit for the paths it would remove. To move data off a primary store: copy, prove the copy in the off-machine backup, and only then clear.

**Why.** Data from outside sources often cannot be fetched again. And a "safe" clean-up once deleted the pinned evidence two release tests depended on (see [3.8](03-evidence-and-verification.md#38-never-weaken-a-check-to-get-green-and-never-lose-what-a-check-stands-on)).

**Exception.** Credentials are not data to keep. Scan them out.

---

### 4.22 Secrets in agent work: name the dangerous files, give a safe reader, scan every report

**Rule.** A leak check prints the names and locations of what it finds, never the value. Credentials go to child processes through the environment, a file descriptor or a locked-down file, never the command line. Agent briefs name the specific files that hold secrets and forbid reading them raw; a masking reader that prints field names and lengths is the only sanctioned path. Every agent report is secret-scanned before it is shared. A secret pasted into a chat is treated as leaked and rotated; redaction is not rotation.

**Why.** A build agent noted, at the bottom of its report, that it had printed a per-build key into its own log. The next agent, whose brief said "never print a secret", printed two more while comparing build configuration files. A general rule was not enough; naming the files and providing a safe reader was. See [case study 4](../case-studies/04-the-caveat-at-the-bottom-of-a-green-report.md).

---

### 4.23 Every change to how we work lands with its decision-log entry

**Rule.** Changes to settings, hooks, runbooks, install and deploy recipes, and gates each carry a dated entry in the decision log, in the same pull request: what was decided, who decided it (quoted), why, what enforces it, and what it supersedes. Shared operational documents give volatile facts as commands that read them, not pasted values.

**Why.** Chat transcripts are not a record; they are not backed up. The log survives the loss of the machine. The "enforced by" field is the honest part: it tells the next reader whether a rule is a check or a hope. The [decision-record template](../templates/decision-record.md) has the fields.

---

### 4.24 Temporary exceptions have an exit condition

**Rule.** An exception to a rule names its scope, a named approval for each use, and the condition that retires it. An exception that only an agent has approved says so until a human confirms it. It is retired explicitly.

**Why.** On one launch day, the orchestrating session proposed an exception (builds outside the shared lock) and approved each use itself in the release record while the engineer's confirmation was still pending. The next release went back to the locked path, and the exception was later marked retired. Unretired exceptions become the rule, and an exception only an agent approved should never look like one a human did.

---

## What this chapter does not claim

- That you need a release kit. Most projects need branch protection, a named rollback and an artifact check. The pinned-inputs kit is for frequent releases run by agents on a surface where a mistake reaches paying users.
- That safe aborts are free. They cost reruns. We count that as the price of never shipping something we could not explain.
