# PLAN — hawks-and-doves

A map of **epochs**, not a task list. Each epoch says what it produces, what has
to be agreed before it starts, and what it deliberately leaves for later. Tasks
appear inside an epoch only once that epoch opens.

Status: `[ ]` not started · `[~]` in progress · `[x]` done and approved.

**Reading order for a new session:** `AI_INSTRUCTIONS.md` → `PROJECT_MEMORY.md` →
this file → `IDEAS.md`.

---

## The shape of the whole thing

```
E0  Foundation ................ architecture, layout, reproducibility contract
E1  The US trunk .............. corpus + market + four signals + the naive study
E2  Doing it properly ......... expectations, surprise, sign flip, regimes, validation
E3  The model zoo ............. more signals, new axes, the original methods
E4  Going wide ................ 25 central banks, transfer, spillovers
E5  Trading ................... the strategy branch, honestly evaluated
E6  Interpretability .......... attribution, text-change, linguistic infographics
E7  The site .................. static-first, then interaction
E8  Finish .................... methodology write-up, release, repository polish
```

Epochs 3 through 7 are **not strictly ordered**. Once the trunk (E0–E2) stands,
they attach independently and can be built in whatever order is most interesting
at the time. That is the point of the architecture, and it is the owner's stated
preference: keep the project alive by choosing the next branch, not by working
through a queue.

Cross-cutting throughout: the **method-critical studies** (`IDEAS.md` B8) —
hindsight contamination, power analysis, placebos. Each is small, each can be
slotted into any epoch, and each makes everything around it more credible.

---

## E0 — Foundation `[~]`

**Goal.** A repository where the first real notebook can be written without any
of it being thrown away later.

**Agreed before it starts** — these are the decisions currently on the table:

1. The layered store and its interfaces (`IDEAS.md` §1.1).
2. Folder layout, and the rule for where each kind of artefact goes.
3. How much logic lives in notebooks versus an importable package.
4. Storage format (Parquet, or DuckDB over Parquet).
5. The reproducibility contract: what "the owner can re-run this" means exactly —
   which cells, how long, what it costs, what needs a key.
6. Environment pinning, and whether the notebooks must run in Colab.

**Produces.** Folder skeleton; pinned environment; a first notebook that runs end
to end and produces an empty but correctly-shaped store; the repository standards
written down once so later epochs inherit them.

**Deliberately not here.** Any modelling, any data.

---

## E1 — The US trunk `[ ]`

**Goal.** The full pipeline working on the United States, at deliberately naive
settings, with four scoring methods side by side.

**Agreed before it starts.**

- Which documents are in scope for v1 (statements and minutes was the working
  answer; press conferences and speeches are E3/E4 material).
- How documents are sectioned and split into sentences — this is a modelling
  decision disguised as a cleaning decision.
- Which four scoring methods go in first.
- Which market series are loaded, and at which vintages.

**Produces.**

- FOMC statements and minutes, 2000 → present, committed as text with fetch date
  and content hash; refresh is incremental and idempotent.
- The market layer: yields across the curve, the GSW curve, the ACM term premium,
  the FRBSF surprise series, macro controls at ALFRED vintages.
- The signal table populated by four methods at once.
- The event table.
- **The naive study** — tone level against next-day yield change — run and
  reported honestly, including the near-certain finding that it barely works.

**Definition of done.** The owner re-runs the notebooks and gets the same corpus
and the same numbers. Tests cover the collector, the parser and the joins.

**Deliberately not here.** Expectations, surprise correction, regimes, any claim
that the result means anything.

---

## E2 — Doing it properly `[ ]`

**Goal.** Turn a naive correlation into a defensible measurement.

**Agreed before it starts.**

- **The primary hypothesis, nominated in advance.** Everything else is labelled
  exploratory. This is the difference between a study and a fishing expedition.
- How regimes are split — by rule, not by eye.
- Which expectation measures are built (`IDEAS.md` §1.3) and which is primary.
- Which sign-flip strategy is primary (`IDEAS.md` §1.4).

**Produces.** Expectation measures; the surprise-based target; the information
effect handled at least two ways; regime-split results; walk-forward validation;
the compulsory baselines; a power analysis; a placebo battery.

**Definition of done.** A result — positive or negative — with a confidence
interval, a stated sample, and a placebo pass. A well-measured null is a pass.

---

## E3 — The model zoo `[ ]`

**Goal.** Many methods, compared on the same footing; at least one of them ours.

Content: `IDEAS.md` B1. The headline candidates for something genuinely new are
**topic-conditional hawkishness** (S-e), **predicted-next-statement surprise**
(E-d), and the **conviction axis** treated as first-class rather than as noise.

**Agreed before it starts.** How much to spend on LLM annotation; whether the
headline model is chosen by performance or by interpretability.

**Definition of done.** A comparison table where the dictionary floor, the
published benchmark and our own methods are all present, and where a loss is
reported as clearly as a win.

---

## E4 — Going wide `[ ]`

**Goal.** From one bank to many. Content: `IDEAS.md` B4 and B5.

**Agreed before it starts.** The language strategy; which banks are in the first
cut; whether the WCB corpus is used as-is or supplemented.

**Watch.** WCB's per-bank coverage is uneven — the ECB starts only in 2015 there.
That gap has to be closed or acknowledged.

---

## E5 — Trading `[ ]`

**Goal.** The practical branch: is there a rule, and does it survive costs.
Content: `IDEAS.md` B6.

**Agreed before it starts.** What counts as success, agreed *before* seeing the
backtest — otherwise the threshold moves to wherever the result landed.

---

## E6 — Interpretability and the visual layer `[ ]`

**Goal.** Everything a reader can look at and understand. Content: `IDEAS.md` B7.

This epoch is unusually safe to bring forward: the text-change visualisation and
the linguistic infographics need only the corpus from E1, so they can be built
early if the project needs something visible and satisfying at that moment.

---

## E7 — The site `[ ]`

**Goal.** The interactive thing, deployed. Content: `IDEAS.md` B9.

**Agreed before it starts.** Static-first or live inference; hosting; what the
licences allow the site to serve.

**Non-negotiable.** An adversarial pass before it goes public — hostile input,
oversized input, broken data, and a check that nothing private or costly is
reachable. The security skill and a dedicated audit agent are used here.

---

## E8 — Finish `[ ]`

**Goal.** The repository reads as a finished product, not a workbench.

The methodology write-up; the README a stranger can follow; a release; the
platform surface tidied; `PLAN.md` folded into `PROJECT_MEMORY.md` and deleted.
`IDEAS.md` **stays** — it is the visible menu of what could come next.

---

## How agents are used

Authorised by the owner on 2026-07-27, at the agent's discretion.

| Where | How |
|---|---|
| **Discovery** | Parallel researchers on independent questions, at the start of an epoch. Used today for data sources, pricing and the method survey. |
| **Adversarial audit** | At every epoch close, several auditors with *different lenses* — correctness, data integrity, leakage, reproducibility — each blind to the others. This is where the most valuable defects get found. |
| **Code review** | Before anything substantial is merged. |
| **Long runs** | Scraping and model training go to the background so the conversation is never blocked. |
| **Never** | Conceptual decisions, the project's narrative, or anything where the owner's judgement is the input. |

**Lesson recorded 2026-07-27.** Parallel agents share the session's web-search
budget and its egress policy. A wide fan-out consumed the entire 200-search
allowance and left later agents unable to verify anything, and the environment
blocks direct access to almost every central bank and data-vendor domain. From
now on: cap the fan-out, give each agent an explicit budget, and tell it to report
what it *could not* verify rather than filling gaps from memory. The agents did
exactly that, and the honesty was worth more than a fuller-looking answer.

---

## What gets decided, and in what order

Ordered by how much depends on the answer.

**Blocking now (E0):**

1. The layered store and the folder layout.
2. Notebooks versus package — how much logic lives where.
3. The reproducibility contract, including whether Colab must work.

**Next (before E1):**

4. Documents in scope for v1; sectioning and sentence splitting.
5. The four first scoring methods.
6. Storage format.

**Before E2, and best decided early because they shape what E1 must record:**

7. The primary hypothesis.
8. The regime-splitting rule.
9. The primary expectation measure and the primary sign-flip strategy.

**Deferrable without cost:**

10. Language strategy for other banks (E4).
11. Trading success criteria (E5).
12. Hosting and the live-inference question (E7).
