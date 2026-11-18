# 2. Research and design without bias

Two problems sit under every design decision an agent helps make. First, agents are happy to build on assumptions, and the result looks finished whether or not the assumption was true. Second, the agent that helped write a design is the worst judge of it, and so is the team that wrote the design rules it follows.

This chapter is how we establish facts before building, and how we get honest judgement on work we are too close to see.

---

## Research before building

### 2.1 Establish the facts before you design

**Rule.** For anything non-trivial, read the actual code, documents and platform behaviour first, and write the findings down before design starts. Research and design are separate steps.

**Why.** Building on assumptions failed us repeatedly, and the result always looked finished. A guessed cause was written into a handoff and carried forward as fact ([2.4](#24-label-guesses-as-guesses-measure-before-you-write-by-design)). Gates that passed every local test hung on the real runner ([3.2](03-evidence-and-verification.md#32-verify-on-real-infrastructure-not-just-locally)). Each time the facts were cheap to get, and nobody had got them first.

**How to apply.**
1. Before a design, list what you do not know. Go and read the code or test the platform to answer it.
2. Write research output to a file. Name the sources each conclusion rests on, so a reviewer can re-open them.
3. Trace at least one complete user path end to end before calling a feature done. For work split across several builder agents, see [5.23](05-orchestrating-agents.md#523-after-a-fan-out-build-check-the-spec-against-the-union-of-the-briefs).

**When not.** Small reversible edits. And research that never ends is its own failure: cap it.

---

### 2.2 Re-open what a synthesis rests on

**Rule.** Before a synthesis step, re-read the underlying items behind each cited relation. Make the re-read a scripted step that runs before the critic, and measure the error rate of the evidence layer on a sample.

**Why.** In the pilot of a design-research library, re-reading the evidence behind cited relations found about half of them wrong, including cases that looked like agreement and were not. Separately, a gate had "passed" without ever checking that item ids were unique.

**How to apply.** Publish the measured error rate even when it misses the bar ("not met" is a result). Scale up only after the gate fixes land.

**Cost.** The re-read is slow and expensive, which is why we held the scale-up until the gate fixes had landed.

---

### 2.3 Outside advice is a lead, never a fact

**Rule.** Research pasted in from a chat assistant, an audit tool or an article is logged as a lead. Each claim is checked against a primary source or measured on the real thing, and marked agree, disagree or unverifiable. Only the verified set is acted on.

**Why.** We use outside research a lot, and it is often right. It is also confidently wrong in ways that are hard to see: in one outside audit, a cited standard turned out to be a different level of the standard than quoted, and an outside model's description of our own data invented a field that does not exist.

---

### 2.4 Label guesses as guesses; measure before you write "by design"

**Rule.** In any handoff, a suspected cause is labelled as a guess. Before closing an item as "by design" or "follow-up", measure it on real data and cite the count.

**Why.** A guessed cause ("these items lack a field, probably by design") was written into a handoff and carried forward as fact by the next reader. Tracing the code later showed none of them were by design.

---

### 2.5 Do not keep old prose beside fresh numbers

**Rule.** When the choice is between keeping an old written analysis and showing only fresh numbers, show the numbers. Any mechanism that carries prose forward needs a date and an age limit.

**Why.** A page once kept a weeks-old analysis that contradicted the live numbers on the same screen. That is worse than numbers alone: it teaches the reader to distrust both.

---

## Getting honest judgement

### 2.6 Get fresh eyes from other model families, blind, on pictures only

**Rule.** To judge whether a design is actually good, use reviewers that did not make it. Use model families other than the one that wrote most of the design. Give each a fresh session, screenshots of the real product, and one paragraph describing it. No repository, no design rules, no history.

**Why.** The engineer asked for a whole-product design audit and pointed out the trap himself: a team aligned with its own design principles will tend to confirm them. Most of the design had been written with one model family, so that family was left off the panel entirely.

**How to apply.**
1. Capture real screenshots: signed-out and signed-in, laptop and phone widths, first screen and full page.
2. Give each reviewer a different lens (for us: a creative director, a working user from the domain, an art director from a neighbouring field).
3. Forbid them from opening anything else on the machine.
4. Make them prove they can see the images first ("say what the biggest image shows"). Stop if they cannot.
5. Require a fixed report order so reports can be compared, and a fixed last line so an incomplete report is detectable.

The brief we use is in [templates/blind-panel-brief.md](../templates/blind-panel-brief.md).

**Cost.** Outside reviewers do not know your hard constraints, so their advice is distilled against them, never adopted wholesale (see 2.9 and 2.11). Panel reviewers' tastes overlap with each other, so their agreement counts for less than it looks, and their scores are opinions, not measurements. Each round costs real usage.

---

### 2.7 Ask for the blind spot directly

**Rule.** Every outside review brief includes four questions: a *template test* (which elements look like a stock pattern or an AI default?), a *distinctiveness test* (swap the logo: could this be any other product?), *what to remove*, and *the blind spot* (the most likely reason this team cannot see its own biggest problem).

**Why.** Asking a reviewer for opinions gets opinions. Asking it specifically to find what you cannot see turns it into a bias probe.

---

### 2.8 Take a census of facts first

**Rule.** Before opinions, measure what is measurable on the live product: sizes, contrast, counts, what appears where. Keep the output dated. When a rule is argued, argue from the census.

**Why.** A census separates "what is" from "what we like". It is the base the panel's opinions are judged against, and it ends arguments that taste cannot.

---

### 2.9 Separate floors from taste rules, and test taste rules by breaking them

**Rule.** List the non-negotiables (floors) separately from preferences (taste rules). Floors are things like the domain owner's rulings, honest data, readability and accessibility, and any explicit rule an owner has set. Each floor has an owner and a source. Taste rules are tested by building a variant that breaks each one and looking at the result.

**Why.** A design system mixes the two, and then defends its preferences with the authority of its floors. Breaking a taste rule on purpose is the cheapest way to learn whether it earns its place.

**How to apply.** A floor changes only by a recorded human decision. A taste rule can change on evidence. The [glossary](glossary.md) has both terms.

---

### 2.10 Rate the options blind, with the incumbent hidden among them

**Rule.** When comparing a new design against the current one, build every option on the same real data, strip the labels, randomise the order, and include today's version as one of the options. Keep the key somewhere the reviewers never see. Score the same dimensions every round.

**Why.** Comparing "new" against "current" in the open lets familiarity and sunk cost leak in. In our rounds the incumbent ranked last, twice.

**How to apply.** Apply the findings to the winning option, and record honestly that the revised option was not re-rated.

---

### 2.11 Consensus from outside is guidance, never a lock

**Rule.** When several outside sources agree, record the agreement as *guidance*: a status that can never become binding by itself. Count agreement in independent voices (a tool that wraps another vendor's model is the same voice as that model). Label it with its blind spots. A consensus that collides with an existing rule becomes a question for a human, never a change. Only a recorded human decision creates or changes a rule.

**Why.** Several outside systems audited the same product and mostly agreed. Their agreement was persuasive, and none of them knew our constraints, our data or the owners' rulings. Without this rule, a well-argued consensus would quietly have eroded earlier human decisions and our honesty rules.

---

### 2.12 Keep the disagreement; do not average it away

**Rule.** Where reviewers disagree, record the split and who was on each side. Disagreement shows where a human decision is needed.

**Why.** In our design panel, the two creative lenses picked one option and the domain-user lens picked another. Averaging would have produced a compromise nobody chose. Keeping the split led to a better answer: one treatment on large screens and a simpler one on phones, which the decider then approved.

---

### 2.13 The decider decides by looking at real examples

**Rule.** Panels inform. A person decides, by looking. Nothing ships until the decider has seen examples rendered on real data, at the sizes people will see them. His decision is recorded in his words, with the time and the place he said it.

**Why.** A description of a design is not the design. Screenshots on real data are where a decision stops being abstract.

**How to apply.** Show options on a test deployment or a review page, never production first. Keep one review page current rather than sending many (see [6.12](06-communicating-with-non-technical-owners.md#612-keep-one-review-page-current)).

---

### 2.14 Build variants over shared real data, on test deployments only

**Rule.** Keep alternative designs as a lab: each variant reads the same data through the same adapters, the current ruled design is the control variant, and the lab sits behind a server-side switch that is never on in production. Each variant notes which earlier decisions it would change.

**Why.** Arguing in the abstract wastes everyone's time. Once the data wiring is shared, an alternative design is cheap, and showing beats describing.

**Status.** We have used this shape for design rounds (several options on real data in a test rig). The full standing lab is a plan we are still building out.

---

### 2.15 Make a design move a language, not a one-off

**Rule.** When a new design move is adopted, write it as a small language: the idea in two sentences, one constant that every part derives from, numbered rules that can be tested, a table of parts, design tokens, and the accessibility floors inside the rules themselves.

**Why.** The engineer's instruction was that a new move must not be a one-off; it must join the system. A move without rules drifts the first time someone else applies it.

**Status.** Done once, as a draft. It is not yet live and not yet entered in our design library.

---

### 2.16 Make the agent critique its own plan against the default look before it builds

**Rule.** Ask a designing agent for a plan first, then a check of that plan against the looks AI tools tend to produce by default, then a revision note saying what changed and why. Only then code. After building, screenshot and critique again. State any requirement the agent's defaults would argue against.

**Why.** Agents converge on a recognisable default style. A plan that names that default and steps away from it on purpose is easier to judge than finished code that quietly fell into it.

---

### 2.17 Design the quiet states as part of the design

**Rule.** Empty, thin, uncertain and low-confidence states are designed and reviewed with the main design, not left to fall out of the code. Wording for uncertainty in domain data is agreed with the domain owner.

**Why.** We proposed a way of marking low-confidence values and asked a blind panel directly whether it helped. All three reviewers read it correctly, and all three said it hurt: it made a value look broken next to a normal one. We dropped it before it shipped. Asking was the point: we knew what the marking meant, so we could not judge how it read to anyone else.

**How to apply.** A section with nothing to show keeps its heading and one short line, with the reason behind a small info control. We first hid empty sections; silently missing parts looked like missing data, so that rule was superseded (and the old rule kept, marked superseded, so the history teaches).

---

### 2.18 Name the visible effect of every engine change

**Rule.** For each planned change to the data or engine, write down what it does on screen, and check that against the design rules.

**Why.** Engine work adds surface: labels, flags, warnings. Without this check, a product gets more correct and busier at the same time, and nobody decided that.

---

## Ship, converge, refine

### 2.19 Ship a first version, then converge

**Rule.** Once a plan is ordered, carry it through to a live first version and iterate there. Review loops converge: accept unless a review finds a real blocker; allow at most one small, blocker-only final fix round; everything else goes on a written follow-up list. Size the review bar to the cost of failure.

**Why.** Confirmation loops stalled the project. One workstream ran nine review-and-fix rounds where each fix produced new findings, all of them polish, until usage limits stopped the work. On another change, a couple of dozen external reviews ran before anyone re-read this rule. The engineer's word: it does not need to be perfect; we do not have the capacity to iterate forever.

**How to apply.**
1. What earns a fix round: a blocker, a correctness defect in what users see, or a red check caused by the change.
2. Everything else goes on the follow-up list.
3. If the worst case is small and capped, say so and propose accepting it before the second round, not the seventh.
4. Before a third review round, re-read this rule.

**When not.** Speed never means skipping a gate. Security, data deletion and money paths keep their blockers blocking.

The review-round mechanics are in [chapter 4](04-gates-releases-and-reversibility.md#46-review-loops-converge).

---

### 2.20 Do not launch incomplete work: fill it, swap it, or leave it out

**Rule.** Define a measurable completeness bar. For each gap, look for a real fill from another source first; never invent a value. If there is no honest fill, swap the item for a complete one or leave it out. Then fix the step in the pipeline that produced the gap, so the next item is complete.

**Why.** A page with blanks looks broken to a paying user. An item once had no score because two sources disagreed; it was swapped rather than shown blank. The follow-up was to trace which step left the gap and fix that step.

**When not.** This changes what you choose to show, never how a value is computed.

---

### 2.21 After launch, refine rather than build

**Rule.** After a launch, frame work as fixes, polish, reliability and data quality. Tier each item (must, should, optional, nice). A new feature is the exception, and says so when proposed.

**Why.** After launch the risk is scope creep. Value comes from making what exists trustworthy.

---

## What this chapter does not claim

- That blind panels replace a designer. They replace the team grading its own homework. A person still decides.
- That outside model families are unbiased. They have their own defaults. What makes them useful is that their biases are *different* from ours, and that we can see where they agree with each other and disagree with us. Their tastes also overlap with each other, so their agreement is weaker evidence than it looks.
- That a panel replaces real users. In the case we describe, the planned check with real users had not run when the change shipped.
