# 6. Communicating with non-technical owners

The domain owners here are executives who know their market far better than any of us and have no interest in git. Their attention is the scarcest resource in the system. Every question we send them is a cost; every question we fail to send, when only they can answer it, is a worse one.

This chapter is how we ask the right people the right questions, how we record what they said, and how we keep their trust when we get something wrong.

---

### 6.1 Owners talk in plain English; their tools do the git

**Rule.** Anything an owner reads carries no issue numbers, commit ids, file paths or jargon. Their own tools do the version control and infrastructure for them. Engineering surfaces keep the exact identifiers; owner surfaces translate.

**Why.** An owner should never have to learn our tools to make a decision about their own business. When a page for an owner is generated from structured input, the generator can refuse ids, hashes, vendor names and bare percentages outright, so a slip cannot reach them.

---

### 6.2 Ask about meaning, never about our own gaps

**Rule.** Ask a domain owner only what the data *means*: which number to divide by, what counts as a fair comparison, what is strong. Never ask what to do when data is missing; sourcing and filling are our job. An unresolved data state becomes a flag the system clears by itself (retry, verify, fail closed on ambiguity, show what is still open). A manual fix is a stopgap beside the flag, not the design.

**Why.** Early documents described a mandatory input as a "trade-off" and asked an owner what to show when it was unknown. The engineer's answer: deciding what to do when we cannot do something is not the owner's job, unless we have first proven we cannot. Separately, a handful of items needed manual matching, and the engineer asked why that was manual at all when volume would grow.

**How to apply.**
1. Before drafting a question, ask: is this meaning, or is this our job?
2. If it is our job, build the fill (other sources first), the flag, the automatic retry and a visible count of what is open.
3. Rare cases that really need the owner go into a log, and reach him in the form he chose. Ours chose one weekly list of every case, unfiltered, over our recommended one-question-per-case. When we later capped how many questions reach an owner, we exempted this list, because filtering it would have undone his choice.

---

### 6.3 Respect a touch budget: a weekly batch, not a stream

**Rule.** At most one message per owner per week, for blocking domain questions only (three or fewer), each cleared by the engineer first. No "for your information" pages. A note goes only when the engineer starts it for one named change. Replies to the owner's own requests do not count. In an owner's channel: milestones on *their* items and answers to *their* questions. Engineering detail stays out.

**Why.** The owners run a whole company. There are many more moving parts in their week than our product. In a shared channel, our engineering confirmations were burying the answers they were looking for.

**How to apply.** If an owner asks for status, answer their own items first, then offer the full list. If an answer will take more than a couple of minutes, say at once what is running and what, if anything, is blocking.

**Status.** Adopted recently; the track record is short.

---

### 6.4 A question carries its own options, a recommendation, and what happens on silence

**Rule.** Every question has: the question in plain words; two to four options; exactly one marked recommended, with why; what happens if nobody answers; what each option does and what the person would notice; the size of each effect labelled Known, Estimated or Guess; and the case against our own pick. It stands alone, with a real dated example and a picture when the decision is about how something looks.

**Why.** The engineer told us never to assume he remembers pull request numbers or earlier threads. A question that needs its history re-explained gets a slow or wrong answer. The format was also designed against the usual failures of approval pages: one-click approve, options that are not real options, and false precision.

**How to apply.**
1. Five lines at the top: the question, our recommendation, why, needed by, if you say nothing. Keep each short.
2. Detail below, collapsed: today's context, one real example, the options, the case against our pick, what would change our mind, what we do if it turns out wrong.
3. One named decider.
4. Tier it (quick, standard, big). Silence defaults apply only to reversible, lower-tier questions and are recorded as "defaulted". A big one gets a second reader.
5. Save the answer with the version of the page it answered.

The [template](../templates/question-with-default.md) has the fields.

**Cost.** It is heavy for small calls. For those, the light form is a one-line default and "say no to stop it" ([1.7](01-decision-rights-and-autonomy.md#17-decide-and-tell-take-the-reversible-default-say-so-invite-a-no)).

---

### 6.5 One running question file; questions hang on the work they block

**Rule.** New questions go into one running file, not into every status update. Answered questions move to a "closed" section, and answered-but-not-yet-applied ones to their own section. Each question sits on the task it blocks, rather than becoming its own "decide X" task. Updates mention only what needs the person right now.

**Why.** Too many new questions in each update buried the one that mattered. We once had to remove over a dozen standalone "question" tasks from the work board, each of which duplicated a real task.

**Cost.** The file grows. It needs a short block at the top for the one thing with a deadline, and a "closed since you last looked" section.

---

### 6.6 Chat is not a record: write the answer down, verbatim, to the minute

**Rule.** A human's answer becomes a log entry the same day: their exact words, who, when (to the minute), which channel, which session heard it, and the exact question it answered. When a session relays the engineer's word into a formal record, the record says "relayed, not signed". Silence is never recorded as an owner's yes. When an answer was implied rather than explicit, the record says "implied, confirm".

**Why.** Chat transcripts are not backed up. An affirmation is only as good as the question it answered: if the question's premise turns out to be false, the answer does not carry over, so we re-ask. And a relayed word recorded as a signature is a small forgery, however well meant.

**How to apply.** When you ask for confirmation, quote exactly what you asked beside the answer. Records can be amended; they are never silently rewritten.

Held by a gate for formal records: our records repository refuses a record accepted after that rule began without a relay entry carrying the quote, the time and the channel. Older records were grandfathered.

---

### 6.7 Say any departure from an owner's ruling out loud

**Rule.** Not tested, done differently, deferred, or a modelling choice that narrows what the owner asked for: each is a departure. State it in plain words with the reason and the evidence. For a domain ruling, it goes to the owner (in the weekly batch). For design or engineering, it is the engineer's call: record it, do not send it. A silent gap is the one forbidden outcome.

**Why.** A planned "test everything" step was quietly dropped during a rush. The engineer's response was that we work toward the owner's goals with him, not around his instructions.

---

### 6.8 Volunteer corrections, in the place the reader saw the claim

**Rule.** When an earlier statement of yours turns out to be wrong, tell the person who relied on it, where they read it, with the evidence. Keep the old text and add a dated correction block so the audit trail survives. Record rejected and retracted findings, not only accepted ones.

**Why.** Trust in an agent-run system comes from the corrections more than from the successes. Examples from our log: a status document said no record had ever been accepted, when two had been (their acceptance went unannounced because a notification path had silently skipped); a dated correction was added with the evidence. A peer's wrong report of a failing check was retracted within the hour. An orchestrator told the engineer that it had missed a caveat in an agent's report: "the miss was mine". See [case study 4](../case-studies/04-the-caveat-at-the-bottom-of-a-green-report.md).

---

### 6.9 Owner-facing pages are perishable: re-check every live claim right before publishing

**Rule.** Immediately before merging or publishing anything an owner reads, re-check every claim about the live system.

**Why.** A "not yet updated" line nearly shipped twenty minutes after the update had happened. Owner-facing text describes a moving system; it goes stale faster than code.

---

### 6.10 Name the version behind every number you show

**Rule.** Any number shown to an owner names the model or data version that produced it.

**Why.** Learned models are refit. Without a version, an owner compares two numbers that came from different models and draws a conclusion neither supports.

---

### 6.11 Sample data on an owner's screen is visibly marked, or absent

**Rule.** Placeholder or sample values never appear on a surface an owner or customer reads unless they are visibly marked as samples. Before an owner reviews a page, hand over the list of sample slots.

**Why.** A page once showed an impossible percentage in a sample slot. Every check was green, because the slot was correctly marked as a sample *in the code*. On a live call, nobody could say what it meant: a placeholder had been read as a measurement. A marker that only the checks can read protects the checks, not the readers.

---

### 6.12 Keep one review page current

**Rule.** For design decisions, keep one review page, published where the decider can open it, with each new round added at the top and one line saying what changed. Every image opens full size. Test that it does before publishing.

**Why.** The decider reads dense screenshots and needs full-size detail. Pages he could not open were useless, and many separate pages were worse than one kept current.

---

## What this chapter does not claim

- That owners should be shielded from hard news. The opposite: the touch budget exists so the hard news is not lost among routine notes.
- That the question format is finished. It is the format we use; the people who receive it are still tuning it.
