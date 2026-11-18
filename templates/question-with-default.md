# Template: a question with a default

Use this for any question you need a person to answer. It is written so the person can decide from the top five lines alone, and so work can continue if they say nothing.

Rules for using it (from [chapter 6](../docs/06-communicating-with-non-technical-owners.md#64-a-question-carries-its-own-options-a-recommendation-and-what-happens-on-silence)):

- Classify the question first. Only domain questions go to domain owners; everything else goes to the engineer ([1.1](../docs/01-decision-rights-and-autonomy.md#11-sort-every-decision-into-a-class-and-give-each-class-one-owner)).
- Ask about meaning, never about gaps that are your job to fill ([6.2](../docs/06-communicating-with-non-technical-owners.md#62-ask-about-meaning-never-about-our-own-gaps)).
- No issue numbers, commit ids or jargon in anything the person reads. Assume they remember nothing about earlier threads.
- A default on silence is only allowed for reversible questions that touch nothing on the reserved list ([1.5](../docs/01-decision-rights-and-autonomy.md#15-keep-a-short-list-of-actions-that-always-need-a-humans-own-word)).

---

```markdown
## Q<n>. <The question, in one plain sentence>

**Our recommendation:** <option letter and name>, because <one line>.
**Needed by:** <date and time, with time zone> (<why that date>)
**If you say nothing:** <exactly what happens, and when>. <"This default covers building only" | "This default covers building and shipping">
**Decider:** <one named role>
**Tier:** <quick | standard | big>   (silence defaults apply to quick and standard only; big gets a second reader)

<details><summary>Today: what is true now</summary>

<Two or three sentences. Enough context that someone who has never seen this thread can follow.>

</details>

<details><summary>One real example</summary>

<A real, dated case. A screenshot if the decision is about how something looks.>

</details>

<details><summary>Options</summary>

**A. <name>** (Recommended)
- What it does: <...>
- What you would notice: <...>
- Size of effect: <Known | Estimated | Guess>: <...>

**B. <name>**
- What it does: <...>
- What you would notice: <...>
- Size of effect: <Known | Estimated | Guess>: <...>

**C. Not yet / I have a question**

</details>

<details><summary>The case against our pick</summary>

<The strongest honest argument for another option. What evidence would change our mind.>

</details>

<details><summary>If this turns out wrong</summary>

<How it is undone, how long that takes, who notices first.>

</details>

### Record (filled in when answered)
- Answer (verbatim): "<their exact words>"
- Who / when (to the minute, with zone) / channel / session that heard it: <...>
- Page version the answer applies to: <...>
- Status: <answered | defaulted at <time> | shipped by default at <time>, release record <...>, undo: <...>>
```

---

## The light form

For small, reversible calls where the full form is too heavy:

```markdown
I decided <X> so work would not wait, because <one line>. I read your earlier "<quote>" as covering it.
Say "no" to stop it; it stays built and switched off.
```
