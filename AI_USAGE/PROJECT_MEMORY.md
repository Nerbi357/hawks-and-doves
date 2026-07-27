# PROJECT MEMORY — hawks-and-doves

> **State: shaping.** The purpose and the working principles are settled; the
> architecture and the first phase are still being agreed. Sections marked
> *(open)* are not decided — do not treat them as decisions.

## 1. What this project is and who it is for

Every central bank statement leans hawkish (tightening) or dovish (easing). The
project asks whether that tone, measured from the published text and combined with
market dynamics, carries information about where government bond yields go next —
and then tries to turn the answer into something usable.

**Why this project** (owner, 2026-07-27): a personal project serving several
purposes at once — the basis of a research paper, a substantial portfolio piece
demonstrating many methods, and a way for the owner to learn the whole subject by
building it. In parallel he studies the material himself and must end up able to
reconstruct every conceptual decision unaided.

**Who will see it:** professors and teachers, colleagues, friends, interviewers,
and — for the interactive part — the general public.

**What "this went well" looks like** (owner's words, condensed): a large, varied
project that realises many directions and formats; that applies many methods and
explains both the reasons and the results; that squeezes the maximum information
and insight out of the idea and the data; whose structure not only carries the
branches already built but makes new ones cheap to add; and that ends in an
interactive site with experiments and visualisation that a visitor can poke at.
Classical methods are baselines and benchmarks — the project must also propose
new and interesting solutions. Above all: **the owner must find it interesting to
work on and be genuinely proud of it.**

Scope as stated:

- start with the **United States** (FOMC), then reuse the machinery for other
  central banks — euro area, Russia, Japan, Brazil, emerging markets;
- period **2000 → today**, with "today" refreshable: re-running the pipeline in
  six months must extend the study, not break it;
- both a **scientific** result (does the effect exist, how big, how stable) and a
  **practical** one — an actual trading strategy is an explicit goal, as one
  branch among several;
- **open sources only**, except where the owner decides to buy data deliberately;
- built in **versions**, like successive revisions of a paper.

## 2. Working principles specific to this project

These follow from `AI_INSTRUCTIONS.md` §5b, and the owner restated them on
2026-07-27 in stronger form:

- **Breadth before depth.** A strong trunk with many neatly finished branches, not
  one deep result. Ten methods that do not combine are ten small studies.
- **Every step must help the next one.** The architecture is judged by whether a
  new method, data source, country, model or output can be attached later without
  rewriting what exists.
- **Everything considered gets written down**, including what is deliberately not
  being done now, so the project always has a visible menu of next moves.
- **A failed baseline is a result.** Proving that the obvious approach does not
  work is part of the deliverable, not an embarrassment.
- **The project must stay alive** — every phase ends with new branches proposed.

## 3. How to communicate

`AI_USAGE/AI_INSTRUCTIONS.md` governs. Project-specific notes:

- conversation with the owner in **Russian**; everything in the repository in
  **English**;
- the owner runs a separate learning chat that cannot see this repository; feed it
  through `AI_USAGE/LEARNING_PROMPTS.md`, targeting only the fundamental and the
  hard-technical (see §2 of the instructions);
- the owner asked to be challenged hard — object to the premise, not only to the
  implementation;
- sub-agents are authorised: use them freely for breadth of research and for
  adversarial audit.

## 4. Repository map

| Path | What it is |
|---|---|
| `AI_USAGE/AI_INSTRUCTIONS.md` | portable contract for working with the owner |
| `AI_USAGE/PROJECT_MEMORY.md` | this file |
| `AI_USAGE/IDEAS.md` | the project's menu — actionable now, and idea-level branches |
| `AI_USAGE/LEARNING_PROMPTS.md` | prompts for the owner's parallel learning chat |
| `.claude/skills/` | curated working methods from `addyosmani/agent-skills` |
| `.claude/agents/`, `.claude/references/` | reviewer/tester/security personas and checklists |
| `README.md` | project landing page — still a stub |
| `LICENSE` | MIT |

## 5. Locked technical decisions

| Decision | Reason |
|---|---|
| Study period 2000 → rolling present | enough regimes to compare; pre-2000 statements are shorter and structurally different |
| Results reported per regime as well as pooled | the owner expects the relationship to differ across 2000-08, 2008-15, 2015-19, COVID, 2022+ |
| Build on existing labelled corpora rather than only labelling from scratch | reproducible, comparable to published benchmarks, and leaves room for our own labelling as a separate branch |
| Curated subset of `addyosmani/agent-skills` installed, not all 24 | the excluded ones are web-frontend and shipping concerns not yet relevant; add them when the site branch opens |

## 6. The owner's decision log

- **2026-07-27** — project opened. US first, then other central banks. Open
  sources only. Versioned like a paper.
- **2026-07-27** — purpose, audience and success criteria recorded in §1.
- **2026-07-27** — breadth-first working principle adopted for this and all future
  projects; written into `AI_INSTRUCTIONS.md` §5b.
- **2026-07-27** — a trading strategy is in scope as a branch, not merely a
  robustness check.
- **2026-07-27** — period fixed at 2000 → rolling present; regime-split analysis
  required alongside the full-sample view.
- **2026-07-27** — "do all the variants": where several methods exist for the same
  step, implement several and compare, rather than choosing one up front.
- **2026-07-27** — sub-agents authorised for research and audit at the agent's
  discretion.
- **2026-07-27** — curated 14 of the 24 `addyosmani/agent-skills`; the owner named
  planning, idea generation, risk play-out, code testing and site security as the
  ones that matter.
- **2026-07-27** — file naming follows the owner's plain words (`IDEAS.md`).
- **2026-07-27** — **the repository is a chain of checkpoints.** After every
  meaningful step the owner must be able to reproduce the result from the
  notebooks and committed material alone, on his own machine. Where an artefact
  belongs is a question to ask, not to guess.
- **2026-07-27** — the project has two standing goals beyond the research itself:
  continuously improving `AI_INSTRUCTIONS.md`, and building out a reusable roster
  of agent roles and orchestration patterns inside it, so nothing has to be
  pointed at from an external library each session.
- **2026-07-27** — do not buy intraday market data. The free published surprise
  series already contain the 30-minute windows, and purchased Globex data would be
  *worse* than them for 2000-2003, when the liquid market was the CBOT pit.

## 7. What is done

- `AI_USAGE/` created; instructions moved into it and extended with §5b and the
  learning-channel rule.
- Curated agent skills installed under `.claude/`.
- Background research started on: intraday data pricing, the full map of free
  central bank text sources, and the 2023-26 state of the art in central bank NLP.
- **Key find (verified 2026-07-27):** the *World Central Banks* corpus
  (`gtfintechlab/WorldCentralBanks`) — 25 central banks, 1996-2024, ~380k
  sentences, 25,000 expert-annotated on three axes: stance
  (hawkish/dovish/neutral/irrelevant), forward-looking or not, and certain or
  uncertain. CC-BY-NC-SA 4.0. This makes the multi-country branch tractable much
  earlier than assumed, and its three-axis labelling matches the project's own
  intention to separate direction from conviction.

## 8. What is next / open questions

*(open)* The architecture — the trunk and the set of branches, and which branch is
built first — is the subject of the current exchange. Also open: how the market's
pre-meeting expectation is measured; whether to buy intraday data; the code
structure (package plus thin notebooks versus notebooks alone).

## 9. Risks and maintenance

- **Sample size.** ~210 FOMC meetings in 2000-2026. Small for anything supervised
  at the document level; the design must push work down to the sentence level and
  outwards to other banks and document types.
- **The effect may be small or absent** at daily resolution. The project must be
  built so a well-measured null is publishable.
- **Hindsight contamination.** Pretrained models know how history turned out; any
  zero-shot result on historical text is in-sample by construction.
- **Source fragility.** Central bank sites change layout and remove archives; the
  collected text corpus must be committed, not only the scraper.
- **Licences.** The two best labelled corpora are CC-BY-NC — non-commercial only.
  Fine for this project; must be stated, and it constrains what the public site
  may serve.
- **Breadth becoming sprawl.** The stated goal is many branches; the matching risk
  is that none is finished. Every branch needs a definition of done before it
  starts.

## 10. Starter prompt for a new session

> Read `AI_USAGE/AI_INSTRUCTIONS.md` first — those are the rules for working with
> me and they outrank any default. Then `AI_USAGE/PROJECT_MEMORY.md`,
> `AI_USAGE/IDEAS.md`, and `AI_USAGE/PLAN.md` if it exists. Talk to me in Russian;
> everything in the repository is in English. We work by the algorithm: directions
> first, then my view, then options, then code. Go wide before deep.
