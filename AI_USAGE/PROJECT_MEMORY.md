# PROJECT MEMORY — hawks-and-doves

> **State: DRAFT, pre-decision.** Written on 2026-07-27 from the owner's opening
> brief so that a new session can restore context. Sections marked *(open)* are
> not agreed yet — do not treat them as decisions. Rewrite this file properly at
> the end of the shaping conversation, per `AI_INSTRUCTIONS.md` §13.2 step 6.

## 1. What this project is and who it is for

Every central bank statement leans hawkish (tightening) or dovish (easing).
The project asks whether that tone, measured from the published text and combined
with recent rate and market dynamics, carries information about where government
bond yields go next.

Scope as stated by the owner:

- start with the **United States** (FOMC), then reuse the machinery for other
  central banks — euro area, Russia, Japan, Brazil, and emerging markets;
- collect central-bank communication *and* market data, including cross-country
  spillovers (does the Fed move Indian or European bonds?);
- train several models, both to compare them and to catch the effect in
  different ways; the model section is meant to be a first-class part of the work,
  not an afterthought;
- deliver a set of reproducible notebooks plus a methodology notebook, and an
  **interactive, visual** layer — at minimum a way for a reader to play against
  the model on real central-bank text; a deployed site is a later version;
- **open sources only**;
- built in **versions**, like successive revisions of a paper, each extending the
  previous baseline.

Purpose questions (`AI_INSTRUCTIONS.md` §11a) — *(open)*, not yet answered.
Working assumption until told otherwise: an educational-professional project
suitable for a CV or as material for an article.

## 2. Phases and current state

Nothing built. The shaping conversation is in progress: the owner has given the
opening brief; the agent has restated it, raised the methodological objections
that reshape the project, and put the open forks to the owner.

## 3. How to communicate

`AI_USAGE/AI_INSTRUCTIONS.md` governs. Project-specific notes:

- conversation with the owner in **Russian**; everything in the repository in
  **English**;
- the owner is learning macroeconomics, trading and deep learning in parallel, in
  a separate chat. On request, produce short thesis-style prompts for that chat
  and keep them in `AI_USAGE/LEARNING_PROMPTS.md`. That chat cannot see this
  repository, so every prompt must be self-contained;
- the owner explicitly asked to be challenged hard ("прожарь меня") — surface
  objections to the premise, not only to the implementation.

## 4. Repository map

| Path | What it is |
|---|---|
| `AI_USAGE/AI_INSTRUCTIONS.md` | portable contract for working with the owner |
| `AI_USAGE/PROJECT_MEMORY.md` | this file |
| `AI_USAGE/IDEAS_BACKLOG.md` | every idea raised and not yet decided |
| `AI_USAGE/LEARNING_PROMPTS.md` | prompts for the owner's parallel learning chat |
| `README.md` | project landing page — still a stub |
| `LICENSE` | MIT |

## 5. Locked technical decisions

None yet.

## 6. The owner's decision log

- **2026-07-27** — project opened. US first, then other central banks. Open
  sources only. Versioned like a paper. Interactive layer wanted, deployment
  deferred to a later version.

## 7. What is done

- Repository layout started: `AI_USAGE/` created, instructions moved into it per
  `AI_INSTRUCTIONS.md` §10.
- Background research on available free data and on the state of the literature,
  captured in `IDEAS_BACKLOG.md`.

## 8. What is next / open questions

The forks put to the owner, all open:

1. What exactly is being predicted — tone level, event-window yield change,
   surprise relative to market pricing, or a decomposed yield move.
2. Whether the primary claim is scientific (does the effect exist) or predictive
   (can it be traded), because the two demand different evaluation.
3. Daily data only, or the effort to get event-window resolution.
4. Whether to build on the existing labelled FOMC dataset or to label from
   scratch.
5. How far to widen the corpus beyond statements (minutes, press conferences,
   speeches, transcripts).
6. Cross-country language strategy.
7. Notebooks-only versus a package with thin notebooks.
8. Whether the agent may use parallel sub-agents in this environment.

## 9. Risks and maintenance

- **Sample size.** ~260 FOMC meetings since 1994 is a very small supervised
  dataset; naive fine-tuning will overfit. This is the binding constraint on the
  whole design.
- **The effect may be small or absent** at daily resolution. The project must be
  designed so that a well-measured null result is a publishable outcome, not a
  failure.
- **Hindsight contamination.** Pretrained models know how history turned out;
  any zero-shot result on historical text is in-sample by construction.
- **Source fragility.** Central-bank sites change layout and occasionally remove
  archives; the collected text corpus should be committed, not only the scraper.

## 10. Starter prompt for a new session

> Read `AI_USAGE/AI_INSTRUCTIONS.md` first — those are the rules for working with
> me and they outrank any default. Then `AI_USAGE/PROJECT_MEMORY.md`,
> `AI_USAGE/IDEAS_BACKLOG.md`, and `AI_USAGE/PLAN.md` if it exists. Talk to me in
> Russian; everything in the repository is in English. We work by the algorithm:
> directions first, then my view, then options, then code.
