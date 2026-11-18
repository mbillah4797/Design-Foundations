# Case study 2: Three stops that were the system working

A release process run by agents will stop. The question is whether it stops safely, says why, and is cheap to restart. These are three stops from our release log, told in the order a newcomer should read them.

---

## Stop 1: The build that aborted on known noise

**What happened.** The release owner session posted STARTING in the shared log and began a production build. About a minute in, the build aborted. The release kit's comparison step had found a handful of compiled bundles whose internal module ids had swapped places. This is a known class of build noise: identical code, numbered differently. The kit refuses it on purpose rather than waving it through.

Nothing was deployed. The kit restored the working tree to the release base and confirmed that production and its configuration were unchanged. The owner posted the abort, with its cause, in the log.

The owner re-ran the pre-release check and the build. The second attempt passed and shipped. Then, each as its own line in the log: a live sanity check, a walk through the product as a signed-in member, a walk as a signed-out visitor by the separate session that signs off visitor-visible output, and a post-check by the session that owns the data archive. Only then did the owner post DONE.

**What the rules did.**
- *Refuse anything not explained* ([4.16](../docs/04-gates-releases-and-reversibility.md#416-measure-your-build-noise-pin-it-and-abort-on-anything-unnamed)). The kit does not try to decide whether this particular swap is harmless. A known noise class still costs a rebuild, because "known" and "harmless in this instance" are different claims.
- *Fail closed with nothing written* ([3.18](../docs/03-evidence-and-verification.md#318-declare-the-failure-direction)).
- *One owner, STARTING and DONE, receipts re-read by the owner* ([4.12](../docs/04-gates-releases-and-reversibility.md#412-a-merge-does-not-deploy-releases-are-serial-owned-and-announced)).
- *Long steps detached, never under a short timeout* ([4.20](../docs/04-gates-releases-and-reversibility.md#420-run-long-steps-detached-with-an-exit-file-test-every-waiter-once-by-hand)).

**Lesson.** An abort that costs one rebuild is cheaper than a deploy that needs a rollback. If agents treat an abort as a failure to avoid, they will eventually find a way past it. Say plainly, in their instructions, that a clean abort is a correct outcome.

---

## Stop 2: Automatic merge inside an open release window

**What happened.** A different session, working on a small hardening fix, had armed the hosting platform's automatic merge on its pull request. Automatic merge fires the moment checks go green; it knows nothing about releases. It merged in the middle of an open release window and moved the main branch under the pinned build. The kit noticed main was no longer the pinned commit and aborted. Production was never touched.

**What the rules did.** The kit's refusal on any moved input ([4.13](../docs/04-gates-releases-and-reversibility.md#413-a-release-is-a-pinned-set-of-exact-inputs-and-it-refuses-on-any-mismatch)) turned a race into a stop. The release was rebuilt with that one commit named explicitly as "on main, not in this release". Another armed automatic merge was disarmed on request.

**New rules, written the same day.**
- Never arm automatic merge on the release repository. Merge by hand, in the release owner's order, and only when the log shows no STARTING without its DONE.
- At STARTING, the release owner lists any open pull requests with automatic merge armed and asks their owners to disarm.
- A commit that lands on main after the base is allowed only if it is named, and only while its single parent is the release base.

**Lesson.** Concurrency control that lives in a shared log only works if the automation that ignores the log is switched off.

---

## Stop 3: A correct release, rolled back

**What happened.** A release with several changes deployed. The post-deploy sanity check compared the page structure of one live item against baselines taken before the release. It failed, on that comparison only.

The release kit has one response to a failed sanity check: roll back first, diagnose after. Production went back to the previous deployment within minutes, and the rollback step confirmed that production was serving the target and that configuration was unchanged.

Diagnosis showed the code was correct. The kit's allowance for "new kinds of page element this release may add" had been measured on a test data set whose example item never rendered two kinds of element that real data does. The new code added them on real data; the allowance had never seen them.

The two kinds were added to the allowance, with tests frozen from the real readings (one that must pass, and a red twin that must fail). The second attempt was built from a cold cache and shipped.

**A sibling.** Another release was rolled back later by the same rule for a different reason: the live item's own content changed after the baselines were taken, so its structure legitimately differed. Again the code was fine. Again rolling back first was cheaper than arguing about whose fault it was while it was live. Baselines were re-taken on current data and the release shipped.

**What the rules did.**
- *A failed sanity check means rollback, named before the build* ([4.17](../docs/04-gates-releases-and-reversibility.md#417-name-the-rollback-before-the-build-a-failed-sanity-check-means-roll-back-first)).
- *A fixture is not the world* ([3.5](../docs/03-evidence-and-verification.md#35-a-fixture-is-not-the-world)). Allowances are now measured on a real-data page before the release, not only on test data.
- *Pin-fresh* ([4.14](../docs/04-gates-releases-and-reversibility.md#414-re-read-every-pin-that-follows-live-data-right-before-you-use-it)). Baselines and live checks are taken close together on the same data.

**Lesson.** Rolling back a correct release is a real cost: a rebuild and some time serving the previous version. We accept it as the price of a rule simple enough that an agent will follow it under pressure.

---

## What the three stops have in common

None of them needed a human to notice. Each stopped with nothing harmful written, said why in one line in the shared log, and restarted from a known state. Each one also produced a rule, written the same day, so the next stop of that kind is cheaper or does not happen.

The design goal is not a release process that never stops. It is one where every stop is safe, explained and boring.
