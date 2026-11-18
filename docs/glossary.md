# Glossary

Terms we use in this repository, in the sense we use them.

**Adversarial verify (refute pass).** A step run by a different agent from the builder, briefed only to find holes in the builder's work. Its report opens with caveats, then a verdict, then findings most severe first, each with a command that reproduces it.

**Build dark / dark path.** Code merged behind a switch that is off, so nothing anyone sees changes. "Dark equals today" is the claim that, with the switch off, the deployed output is identical to before. It has to be proven, not assumed.

**Census.** A script that measures facts about the live product (sizes, contrast, counts) so design arguments start from what is, not from what anyone likes.

**Contract (of a check).** A written statement of what "pass" covers and what it does not.

**Decide and tell.** An agent takes a reversible, recommended default so work does not wait, says exactly what it decided, and invites a "no".

**Decision class.** Domain, design, engineering or process. Each has one owner.

**Domain owner.** A person who owns what the business data means and what it is worth. In our case, non-technical executives.

**Fail closed / fail open.** Fail closed: when unsure, stop and change nothing (used for authority, spend, secrets, access and releases). Fail open, honestly: when a product feature cannot work, show that it is unavailable rather than hiding the gap or guessing.

**False green.** A passing result that does not mean what it says. Our register of these is the most useful document we keep.

**Floor.** A non-negotiable design rule with a named owner and source (for example an owner's ruling, honest data, accessibility). Changed only by a recorded human decision. Compare *taste rule*.

**Incident rule.** A process rule an agent writes after a failure, recorded as agent-decided and reported to the engineer, who can overturn it.

**Guidance (status).** What outside consensus becomes when recorded: useful, never binding by itself.

**Noise class.** A known, measured kind of difference between two builds of identical code (for example module ids swapping). Pinned so a release can tell expected noise from a real change.

**Orchestrator.** The session that launches agent steps and reads their results. It re-reads artifacts itself before anything gates a release.

**Pin / pin-fresh.** A pin is an exact recorded input to a release (a commit, a hash, a chosen page to check). Pin-fresh means re-reading every pin that follows live data right before using it.

**Red twin.** A planted defect, kept as a permanent test, that a check must fail on. It proves the check can detect what it exists to detect.

**Release owner.** The one session accountable for a production release: order, hold, STARTING, DONE, receipts.

**Reserved list.** The short list of actions that always need a human's explicit word, whatever other authority applies.

**Resolver.** A short step after a critic that re-checks the critic's refuted blockers, so a wrong refutation does not stand.

**Sentinel line.** A fixed last line in a report file (for example `REPORT-COMPLETE <key>`) that proves the report was finished, so a re-run can skip a finished step.

**Silence window.** The time (ours is six hours) after which a stated, reversible default on the engineer's own question may ship. A domain owner's silence is never agreement: at most, a reversible, lower-tier domain question takes its stated default after its own stated window, recorded as "defaulted".

**STARTING / DONE.** The two lines a release owner posts to the shared log around a release. While a STARTING has no DONE, nobody merges or installs.

**Taste rule.** A design preference. Tested by deliberately breaking it and looking at the result. Compare *floor*.

**Touch budget.** The cap on how often we contact a domain owner (for us, one batched message a week, blocking domain questions only).
