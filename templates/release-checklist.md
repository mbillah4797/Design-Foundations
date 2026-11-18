# Template: a release checklist

For a production release run by one release owner, possibly an agent session. Scale it down for simpler projects: the items marked **(core)** are worth doing for any release; the rest earn their cost when releases are frequent and agents run them. Background is in [chapter 4](../docs/04-gates-releases-and-reversibility.md).

---

```markdown
# Release <name>, owner: <role>

## Before
- [ ] (core) Every change in this release is merged through the normal gates, with every reviewer finding on every commit ticked (fixed / answered / moot).
- [ ] (core) Rollback target named: <previous deployment>. Rollback command: <exact command>. What a rollback takes away: <...>.
- [ ] (core) What kind of release: <code only | configuration only | data migration | switch-on>.
- [ ] Switch-ons only: the human's verbatim go is quoted here, with date and channel: "<...>".
- [ ] Pin file written by script (never typed): base commit, change heads, tree hashes, sign-off files.
- [ ] Derived values re-derived and compared with the pin (switch names, route lists).
- [ ] Live-data pins re-read now (pin-fresh): <list>. Re-read again right before STARTING.
- [ ] Known build-noise variants carried forward from every earlier build.
- [ ] Open pull requests with automatic merge armed: <list>, owners asked to disarm.
- [ ] Heavy-job lock: no other heavy work queued by me.

## STARTING (post to the shared log)
- [ ] `<UTC time> <owner> -> all: <release> STARTING. Base <...>. Contains <...>. Kind <...>. Merge hold on until DONE.`

## Build
- [ ] (core) Build from a clean state (cold cache; old cache moved aside, not deleted).
- [ ] Long steps detached, each with an exit-code file. No timeout shorter than the step's known run time.
- [ ] Any unnamed difference in the build: ABORT with nothing deployed, post the cause, restore, re-run pre-check.
- [ ] (core) Inspect the artifact before deploying: expected builder, compiled code where compiled code belongs, feature marker present, no configuration value compiled to a placeholder.

## Deploy and verify
- [ ] (core) Deploy.
- [ ] (core) Check the feature in the rendered page, not the status code.
- [ ] Sanity check against baselines taken close to now, on the same data.
- [ ] (core) Sanity fails: ROLL BACK FIRST, confirm production serves the target and configuration is unchanged, diagnose after.
- [ ] Walk as each kind of user (signed out, signed in, paying), including at least one write action.
- [ ] Separate sign-off on visitor-visible output by the role that owns it.
- [ ] Read every verifier's caveats and "Limits / not done" before its verdict.

## DONE (post to the shared log)
- [ ] `<UTC time> <owner> -> all: <release> DONE. Live: <...>. Verified: <...>. NOT verified: <...>. Rollback: <...>. Merge hold lifted.`
- [ ] Release record written: what shipped, what was verified, what was not, any security item, any rule used (for example "shipped by default after the silence window").
- [ ] Approved-but-not-built list checked before calling anything finished.
- [ ] Any new noise variant or lesson added to the kit, with its test.
```
