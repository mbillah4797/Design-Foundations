# Case study 1: Proving that nothing visible changed

## The situation

A large set of new product features had been built behind switches, all of them off, on one large pull request waiting to merge. The release that would carry them to production made one claim: *with every switch off, nothing a visitor, a free account or a paying member sees changes.* Hundreds of files had moved. The build tool had reshuffled module ids and grouping across the whole bundle, as it does when this much code changes.

The claim was easy to make and hard to prove. A byte-for-byte comparison of the two builds was useless: almost every compiled file differed, for reasons that had nothing to do with behaviour. The question became: can we show that every difference is one that cannot change what anyone sees, and refuse the release if we cannot?

## What we already knew, from earlier releases

- **A dark path can change bytes even when it does nothing.** On an earlier change, an extra asynchronous wrapper that always returned nothing had delayed one render by a single tick, and the page streamed its rows in a different order. Same visible content, different deployed output. So "the switch is off" proves nothing by itself; the comparison must be order-sensitive. ([4.18](../docs/04-gates-releases-and-reversibility.md#418-merge-dark-switch-on-separately))
- **Builds are noisy.** Identical code builds to slightly different output. Known noise is measured and pinned; anything unnamed aborts. ([4.16](../docs/04-gates-releases-and-reversibility.md#416-measure-your-build-noise-pin-it-and-abort-on-anything-unnamed))

## What the rules did

**1. Two independent tools, and both must agree.** One comparison tool, written by one session, judged whether each pair of compiled modules was equivalent. A second, written separately on a different parser with its own analysis, judged the same pairs. A module counted as unchanged only if both said so. "One said different", "one could not judge" and "unknown shape" all meant *refuse*. ([3.13](../docs/03-evidence-and-verification.md#313-for-high-stakes-judgements-two-independent-implementations-must-both-agree))

**2. Repeated refute passes by a different agent.** A refuter's only job was to build tiny synthetic pairs of builds that behave differently but look equal, and see whether the tools were fooled. They were, repeatedly. Each pass found new classes of hole. The count fell, then rose again on the fourth pass. Patching each trick was not converging. ([3.12](../docs/03-evidence-and-verification.md#312-set-the-bar-before-the-round-when-findings-do-not-fall-close-the-class))

**3. Closing classes instead of instances.** What worked was to stop asking "is this trick handled?" and define exactly which shapes of change the tools may accept, measured from real builds. Anything outside that grammar is not judged equal; it goes to a human or fails. A written contract named what "equal" does not cover, and which other check covers each gap.

**4. Every counterexample became a permanent test.** Every pair a refuter produced was kept, pinned by checksum, with its expected verdict. The rule for closing a hole: the second tool must catch the pair *by itself*, with the first tool removed. Each fix was shown to miss the pair on a saved copy of the pre-fix tool and catch it on the fixed one. ([3.14](../docs/03-evidence-and-verification.md#314-every-counterexample-a-refuter-finds-becomes-a-permanent-test), [3.7](../docs/03-evidence-and-verification.md#37-red-first-means-reinstating-the-defect-plus-a-control-that-must-not-move))

**5. Finding what both tools assumed.** A separate verify pass found two holes that slipped through *both* tools: one because both had copied the same pattern for one kind of generated code, one because neither read a rare way of writing module ids. Both were latent. Agreement between two tools cannot see an assumption they share. The fix was made in both, and measured to cost nothing on the real build. The lesson went into the contract: list the shared assumptions.

**6. A stop rule when the ground moved.** Mid-step, the author of the first tool released a newer version. The builder of the second tool did not adopt it on the spot. It recorded that a newer version existed, left it unread, and left the decision to pin it to the release owner.

**7. Gate decisions were asked with a recommendation, never a default, and recorded verbatim.** Some judgements were not purely technical: for example, whether the new module-level gate could take over from the older file-by-file checks for this one release. Each went into the running question file with options and a recommendation. None carried a silence default: a change to a gate is on the reserved list ([1.5](../docs/01-decision-rights-and-autonomy.md#15-keep-a-short-list-of-actions-that-always-need-a-humans-own-word)), so nothing released until the engineer answered. The release configuration was built so that the switch letting the new gate decide a release *refuses to turn* unless the configuration carries the engineer's quoted words and the time he said them. Until then, the older file-by-file checks stay in charge, and the new gate only reports. ([6.4](../docs/06-communicating-with-non-technical-owners.md#64-a-question-carries-its-own-options-a-recommendation-and-what-happens-on-silence), [6.6](../docs/06-communicating-with-non-technical-owners.md#66-chat-is-not-a-record-write-the-answer-down-verbatim-to-the-minute))

**8. Caveats first.** Build and fix reports, and later every verify report, opened with a "caveats, breaches and exposures" block, and the orchestrator read those before the verdicts. ([5.13](../docs/05-orchestrating-agents.md#513-agents-report-caveats-first-and-the-orchestrator-reads-them-in-full))

## The outcome

At the time of writing, this release has not shipped. The gate still fails the trial on a small set of named cases, each traced to a cause and either awaiting a human ruling or a fix.

We count that as the method working. The claim "nothing visible changed" was never argued into production. Every round made the gate stricter and the remaining gaps more precisely named. Nothing released while the evidence and the claim disagreed, and when it does ship, the record will show exactly what was proven, by which two tools, and what was ruled on by whom.

## What a stranger can take from it

- If a release claim is important, make two independent mechanisms prove it, and write down what they both assume.
- Run refuters until findings fall. If they do not fall, change the method, not the patch.
- Keep every counterexample forever.
- Put the human's decision into the machinery: a switch that refuses without the quoted words cannot be flipped by an agent that misread a conversation.
- It is expensive. We would not do this for a release whose output is meant to change. We did it because the whole point of building dark is that users see nothing until someone decides they should.
