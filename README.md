# Design Foundations

Your design vision needs your input to build, but here's a nice foundation to build that on.

How we make design and engineering decisions we can trust when AI agents do most of the designing, building, checking and releasing.

This is not a style guide. It holds no palettes, fonts, layouts or chart styles. It is about the rules underneath: who decides what, what counts as evidence, how a release earns the right to go out, and how a team of agents is run so their work can be believed.

## The thesis

Agents are fluent. Fluency is not confidence.

We got to confident, mostly autonomous decisions through three things, none of which is the agents' own skill:

1. **Decision rights.** Every decision has a class and every class has one owner. Agents act freely inside the room the owners leave them, take stated defaults when the owner is silent, and never touch a short list of things reserved for a human's explicit word.
2. **Evidence.** A claim counts only when someone has looked at the thing itself, with a check that could have failed. Builders never certify their own work. A green result is a question, not an answer.
3. **Reversible gates.** Work goes through the same gates whoever wrote it. Anything that changes what people see can be undone in one step, and the undo is named before the change ships.

When those three hold, an agent's decision can be trusted for the same reason a colleague's can: not because it sounds right, but because it was made by the right party, checked by someone else, and can be taken back.

## Who this is for

Engineers and designers who work with AI agents and want to let them decide more without losing control. It was written by a very small team: one engineer who owns design and engineering, two non-technical executives who own the business domain, and many agent sessions from more than one model family. Most of it should carry to larger teams. Some of it (the release discipline, for example) is only worth its cost when releases are frequent and agents run them.

Every rule here was paid for. Each one has a "why" that comes from something that went wrong, or nearly did, or from a decision recorded at the time. Where a rule is only a written convention, or still a draft, we say so.

## Map of the repo

| Path | What it holds |
|---|---|
| [PRINCIPLES.md](PRINCIPLES.md) | The core rules on one page, each with a one-line why and a link to its chapter. Start here. |
| [docs/01-decision-rights-and-autonomy.md](docs/01-decision-rights-and-autonomy.md) | Who decides what, how much agents may decide alone, defaults, the reserved list, standing authority. |
| [docs/02-research-and-design-without-bias.md](docs/02-research-and-design-without-bias.md) | Research before building, blind panels, floors versus taste rules, consensus as guidance, deciding by looking. |
| [docs/03-evidence-and-verification.md](docs/03-evidence-and-verification.md) | What counts as evidence, how checks lie, refute passes, independent implementations, red twins. |
| [docs/04-gates-releases-and-reversibility.md](docs/04-gates-releases-and-reversibility.md) | Pull requests and review, serial releases, pinned inputs, safe aborts, rollback, building dark. |
| [docs/05-orchestrating-agents.md](docs/05-orchestrating-agents.md) | Model tiers, usage limits, checkpoints, briefs, watchdogs, agents talking to each other. |
| [docs/06-communicating-with-non-technical-owners.md](docs/06-communicating-with-non-technical-owners.md) | Asking the right people the right questions, recording answers, volunteering corrections. |
| [docs/glossary.md](docs/glossary.md) | The terms we use (dark build, red twin, refute pass, release owner, floor, and so on). |
| [case-studies/](case-studies/) | Four anonymised stories that show the rules working together. |
| [templates/](templates/) | Fill-in forms: a question with a default, a decision record, briefs for agents and reviewers, a release checklist, a report footer. |

## How to adopt it piecemeal

You do not need all of it. Pick the problem you have.

- **"Agents keep asking us things, or we keep waiting on each other."** Start with decision classes and the reserved list (chapter 1), and the [question-with-default template](templates/question-with-default.md).
- **"Our checks pass and things still break."** Read chapter 3, then add one thing: for every new check, show it failing on the defect it exists to catch.
- **"We cannot tell whether our own design is any good."** Run one blind panel ([template](templates/blind-panel-brief.md)) from chapter 2 before your next redesign.
- **"Agent runs die halfway and we lose the work."** Chapter 5, the checkpoint and resume rules. They cost a few lines per brief.
- **"A release went out that should not have."** Chapter 4: name the rollback before the build, and make the release refuse on any input that moved.
- **"Our executives are overwhelmed or confused by us."** Chapter 6.

A reasonable first week: decision classes, a reserved list, the question template, and the "Limits / not done" footer on every agent report. Those four are cheap to adopt and address the failures we hit most.

## What this is not

- Not a framework you install. There is no code here.
- Not a claim that agents need no humans. The opposite: it is about spending human attention only where it matters, and making sure it is never skipped there.
- Not finished. We keep a dated decision log and change these rules when the evidence says to.

## License

Everything here is released under the [MIT License](LICENSE). The files in [templates/](templates/) are meant to be copied, changed and reused.
