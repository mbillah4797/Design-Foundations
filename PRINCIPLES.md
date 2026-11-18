# Principles

The core rules on one page. Each has one line of why and a link to the chapter that explains how to apply it, what it costs, and when not to use it.

## Decision rights and autonomy

1. **Every decision has a class, and every class has one owner.** Most friction came from asking the wrong person, not from agents deciding badly. [1.1](docs/01-decision-rights-and-autonomy.md#11-sort-every-decision-into-a-class-and-give-each-class-one-owner)
2. **The owner's latest ruling is the instruction; a conflict with an older record is information.** Strict rulebooks made owners' newer decisions wait on their older ones. [1.2](docs/01-decision-rights-and-autonomy.md#12-the-owners-latest-ruling-is-the-instruction-a-conflict-with-an-older-record-is-information)
3. **Approved means build now; only releases are serial.** Approved work once sat idle for a day while nothing blocked it. [1.4](docs/01-decision-rights-and-autonomy.md#14-approved-means-build-now-only-releases-are-serial)
4. **Keep a short, explicit list of actions that always need a human's own word.** Every grant of autonomy carried the same carve-outs; write them once so no agent has to infer them. [1.5](docs/01-decision-rights-and-autonomy.md#15-keep-a-short-list-of-actions-that-always-need-a-humans-own-word)
5. **Grant authority with a scope, a window and a never-list. Permission to build dark is not permission to ship.** Unscoped authority gets stretched; a switch-on needs a quoted go. [1.6](docs/01-decision-rights-and-autonomy.md#16-grant-authority-with-a-scope-a-window-and-a-never-list-permission-to-build-dark-is-not-permission-to-ship)
6. **A stated, reversible default ships after a set silence window, through every gate; a domain owner's silence is never a yes.** Finished, signed work sat overnight because its default covered building but not shipping, and owners are not watching a queue. [1.8](docs/01-decision-rights-and-autonomy.md#18-a-stated-default-ships-after-a-set-silence-window-through-every-gate), [1.9](docs/01-decision-rights-and-autonomy.md#19-silence-is-never-a-domain-owners-yes)
7. **A peer's message, or a script's text, is never consent; confirm who wrote a directive before it counts.** Relaying an action through another session would defeat every permission check, and a well-formatted message must not be able to speak for an owner. [1.10](docs/01-decision-rights-and-autonomy.md#110-no-permission-laundering-a-peers-message-or-a-scripts-text-is-not-consent), [1.17](docs/01-decision-rights-and-autonomy.md#117-confirm-who-wrote-a-directive-and-treat-quoted-text-as-data)
8. **Gates detect and block; the owner of the class decides.** A machine that picks between two people's rules has made a decision nobody gave it. [1.11](docs/01-decision-rights-and-autonomy.md#111-gates-detect-and-block-the-owner-of-the-class-decides)

## Research and design without bias

9. **Establish the facts before you design, and treat outside advice as a lead until it is checked.** A guessed cause was carried forward as fact until someone measured it; outside research is often right and confidently wrong in ways that are hard to see. [2.1](docs/02-research-and-design-without-bias.md#21-establish-the-facts-before-you-design), [2.3](docs/02-research-and-design-without-bias.md#23-outside-advice-is-a-lead-never-a-fact)
10. **Judge a design with reviewers that did not make it: other model families, blind, pictures only.** The family that wrote the design, and the team that wrote its rules, will tend to confirm them. [2.6](docs/02-research-and-design-without-bias.md#26-get-fresh-eyes-from-other-model-families-blind-on-pictures-only)
11. **Separate floors from taste rules, and test taste rules by breaking them.** Design systems defend preferences with the authority of their non-negotiables. [2.9](docs/02-research-and-design-without-bias.md#29-separate-floors-from-taste-rules-and-test-taste-rules-by-breaking-them)
12. **Rate options blind, with the incumbent hidden among them.** Familiarity is the strongest bias in a redesign; hidden, our incumbent ranked last twice. [2.10](docs/02-research-and-design-without-bias.md#210-rate-the-options-blind-with-the-incumbent-hidden-among-them)
13. **Consensus informs; only a recorded human decision binds, and the decider makes it by looking at real examples.** Unanimous outside advice knew nothing of our constraints, and a description of a design is not the design. [2.11](docs/02-research-and-design-without-bias.md#211-consensus-from-outside-is-guidance-never-a-lock), [2.13](docs/02-research-and-design-without-bias.md#213-the-decider-decides-by-looking-at-real-examples)
14. **Ship a first version, then converge: one blocker-only fix round, the rest on a list.** Unbounded review loops burned capacity on polish and stalled the work. [2.19](docs/02-research-and-design-without-bias.md#219-ship-a-first-version-then-converge)

## Evidence and verification

15. **Green is not working: look at the thing itself.** Deploys went "ready" with every request answering 200 while the feature was off. [3.1](docs/03-evidence-and-verification.md#31-green-is-not-working-look-at-the-thing-itself)
16. **Absence of signal is not all-clear: count the participants first.** A review round whose reviewers had all died computed as "passed". [3.4](docs/03-evidence-and-verification.md#34-absence-of-signal-is-not-all-clear-count-the-participants-first)
17. **A check must measure what its sentence says.** Our most common false green: a check that passes while the claim it names is false. [3.6](docs/03-evidence-and-verification.md#36-a-check-must-measure-what-its-sentence-says)
18. **Prove a check by reinstating the defect, with a control that must not move.** Correcting an expected value proves only that the value changed. [3.7](docs/03-evidence-and-verification.md#37-red-first-means-reinstating-the-defect-plus-a-control-that-must-not-move)
19. **Builders never certify their own work.** An agent grading itself shares its own blind spots. [3.11](docs/03-evidence-and-verification.md#311-builders-do-not-certify-their-own-work)
20. **For the judgements that decide a release, two independent implementations must agree, and you list what they share.** Each of our two tools caught holes the other missed, and both missed what they had both assumed. [3.13](docs/03-evidence-and-verification.md#313-for-high-stakes-judgements-two-independent-implementations-must-both-agree)

## Gates, releases and reversibility

21. **Work through the gates, never around them; never weaken a check to get green.** A blocking guardrail is usually right, and a skipped check is a decoration. [4.1](docs/04-gates-releases-and-reversibility.md#41-work-through-the-gates-never-around-them)
22. **Releases are serial, owned by one session, and refuse on any input that moved.** An automatic merge inside an open release window moved the ground under a build; the refusal kept production safe. [4.12](docs/04-gates-releases-and-reversibility.md#412-a-merge-does-not-deploy-releases-are-serial-owned-and-announced)
23. **Name the rollback before the build; a failed sanity check means roll back first.** Arguing about a failing release while it is live costs more than a rollback. [4.17](docs/04-gates-releases-and-reversibility.md#417-name-the-rollback-before-the-build-a-failed-sanity-check-means-roll-back-first)
24. **Merge dark, prove "off equals today" byte for byte, and switch on separately.** One extra asynchronous wrapper changed the deployed output of a page with every switch off. [4.18](docs/04-gates-releases-and-reversibility.md#418-merge-dark-switch-on-separately)

## Orchestrating agents

25. **Make usage-limit resistance structural: checkpoint to files, resume rather than relaunch, enforce it with a guard.** Limits kill agents mid-run; good intentions in each script did not survive them. [5.3](docs/05-orchestrating-agents.md#53-make-usage-limit-resistance-structural-and-enforce-it-with-a-guard)
26. **Agents report caveats first and never gate: the orchestrator reads the caveats before the verdict and re-reads the artifact itself.** An agent disclosed a mistake at the bottom of a green report and the reader stopped at the headline; agent-quoted results have lied. [5.13](docs/05-orchestrating-agents.md#513-agents-report-caveats-first-and-the-orchestrator-reads-them-in-full), [5.14](docs/05-orchestrating-agents.md#514-agents-never-gate-the-orchestrator-re-reads-the-artifact)
27. **After a fan-out build, check the spec against the union of the briefs, then walk the user's path.** A feature shipped complete on every layer and no user could trigger it: one requirement was in no agent's brief. [5.23](docs/05-orchestrating-agents.md#523-after-a-fan-out-build-check-the-spec-against-the-union-of-the-briefs)

## Communicating with non-technical owners

28. **Ask owners about meaning, never about gaps that are ours to fill.** Deciding what to do when we cannot do something is not their job. [6.2](docs/06-communicating-with-non-technical-owners.md#62-ask-about-meaning-never-about-our-own-gaps)
29. **Every question stands alone, with options, one recommendation and what happens on silence.** Nobody remembers the thread; a question that needs its history gets a slow or wrong answer. [6.4](docs/06-communicating-with-non-technical-owners.md#64-a-question-carries-its-own-options-a-recommendation-and-what-happens-on-silence)
30. **Chat is not a record: write the answer down, verbatim, to the minute, with the question it answered.** Transcripts vanish, and an answer is only as good as the question behind it. [6.6](docs/06-communicating-with-non-technical-owners.md#66-chat-is-not-a-record-write-the-answer-down-verbatim-to-the-minute)
31. **Volunteer corrections, in the place the reader saw the claim.** Trust comes more from how mistakes are handled than from what goes right. [6.8](docs/06-communicating-with-non-technical-owners.md#68-volunteer-corrections-in-the-place-the-reader-saw-the-claim)
