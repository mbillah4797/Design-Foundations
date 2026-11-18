# Case study 3: A design call made by blind panels, a census and looking

## The situation

The engineer asked for a design audit of the whole product, with a clear goal: make it impressive, not merely tidy. In the same message he named the trap. We had written design principles, and the agents helping with design were aligned with them. A team that grades its own work against its own rules will tend to find that it followed them.

Most of the existing design had been written with one model family. So the question was not "what do our agents think?" It was "how do we get a judgement that is not ours?"

## The process

**1. A blind panel from other model families.** Three reviewers, each from a model family other than the one that wrote the design, each in a fresh session. Each saw only screenshots of the real product (signed-out and signed-in, laptop and phone, first screen and full page) and one paragraph describing it. No repository, no design rules, no history. Each had a different lens: a creative director, a working user from the product's domain, and an art director from a neighbouring field. Each had to prove first that it could see the images. Each wrote its report in the same fixed order, ending with a fixed line so an incomplete report was obvious. The brief is in [templates/blind-panel-brief.md](../templates/blind-panel-brief.md). ([2.6](../docs/02-research-and-design-without-bias.md#26-get-fresh-eyes-from-other-model-families-blind-on-pictures-only))

The brief asked for opinions, but also for four specific probes: which elements look like a stock pattern or an AI default; whether the product would be recognisable with the logo swapped; what to remove; and the most likely reason this team cannot see its own biggest problem. ([2.7](../docs/02-research-and-design-without-bias.md#27-ask-for-the-blind-spot-directly))

**2. A census.** A script measured the live pages: sizes, contrast, counts. That gave the panel's opinions something factual to be checked against. ([2.8](../docs/02-research-and-design-without-bias.md#28-take-a-census-of-facts-first))

**3. Floors and taste rules, separated.** The non-negotiables stayed fixed: the domain owners' rulings, honest data, readability and accessibility, and an explicit typographic rule an owner had set. Everything else was a taste rule, open to being broken in a test rig to see what happened. ([2.9](../docs/02-research-and-design-without-bias.md#29-separate-floors-from-taste-rules-and-test-taste-rules-by-breaking-them))

**4. Options rated blind, incumbent hidden.** For the element under question, several alternatives were built on the same real data in a rig, and today's version was put among them. Labels were stripped and the order randomised; the key was kept where the panel could not see it. ([2.10](../docs/02-research-and-design-without-bias.md#210-rate-the-options-blind-with-the-incumbent-hidden-among-them))

## What came back

- All three reviewers ranked the incumbent last.
- All three, independently, proposed the same stronger idea for the product's signature.
- The two creative lenses picked one option. The domain-user lens picked a different one, and ranked the creative favourite last.

That disagreement was the most useful result. Averaging it would have produced a compromise nobody chose. Instead the recommendation split by device: the creative favourite on large screens, where there is room for it, and a simpler treatment on phones. ([2.12](../docs/02-research-and-design-without-bias.md#212-keep-the-disagreement-do-not-average-it-away))

## The decision

The recommendation went to the engineer as a review page with screenshots of each option on real data, at real sizes. He approved it in his own words, and those words were recorded with the time. ([2.13](../docs/02-research-and-design-without-bias.md#213-the-decider-decides-by-looking-at-real-examples))

The same process then ran on a second section. Again the incumbent ranked last with all three reviewers. This time we also asked them directly whether our proposed way of marking low-confidence values helped. All three read it correctly, and all three said it hurt: it made a value look broken next to a normal one. We dropped it before it shipped. The fix kept the value readable and moved the uncertainty into quieter styling with the domain owner's agreed wording underneath. It joined our existing rule for quiet states. ([2.17](../docs/02-research-and-design-without-bias.md#217-design-the-quiet-states-as-part-of-the-design))

## What happened next, and why it matters

Two more things came out of the audit that were not design taste at all.

First, while looking at the real pages, the team found two live faults, one of them in how the product marked stale information. They were traced to code, fixed, and proven fixed for signed-in members on the live site with a check that *could have failed*: the old rule and the new rule were run side by side on the same data, plus a control that had to trigger. "No stale marker seen" on its own would have proven nothing. One code path, and the signed-out view, had no live data that could tell the old rule from the new, so they stayed unproven, and the record says so.

Second, looking at the improved pages, the engineer noticed a problem the reviewers had only brushed against: it was hard to tell which section you were in and what your eyes should land on. He asked for a separate research pass on that question. It ran in a fresh session; nobody acted on it until its results were in; and its claims were then checked against real screenshots and measurements before any of them became work. ([2.3](../docs/02-research-and-design-without-bias.md#23-outside-advice-is-a-lead-never-a-fact))

That is the honest shape of a good design process. Panels catch what the team cannot see. A census keeps everyone honest about facts. And the person who decides, looking at real examples, still sees things nobody else did.

**The limit.** The planned check with real users of the product had not run when the change shipped; the decision rested on the panel, the census and the engineer's own look. And the panel's reviewers share more taste with each other than their different lenses suggest, so their agreement is weaker evidence than it looks. Their scores are opinions, not measurements.

## What a stranger can take from it

- Bias is handled by process, not by effort. Trying hard to be objective about your own work does not work. Removing your own voice from the judgement does.
- Hide the incumbent. Familiarity is the strongest bias in any redesign.
- Keep disagreement as data. It tells you where a human decision is actually needed.
- Panels inform; a person decides by looking at real examples on real data.
- Write down what the panel could not know (your floors), and never let their advice override them without a recorded decision.
