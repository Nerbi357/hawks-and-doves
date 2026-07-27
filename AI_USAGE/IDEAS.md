# IDEAS — hawks-and-doves

The project's menu. Two tiers, deliberately kept apart (`AI_INSTRUCTIONS.md` §5a):

- **Part 1 — Now.** Things that belong in the next three to five steps. Raised in
  conversation as they come up, decided quickly, moved into `PLAN.md`.
- **Part 2 — Branches.** Idea-level extensions. Presented at the close of a phase
  or a branch, never mid-work. This is the part that keeps the project alive.

Status: `[?]` open · `[>]` proposed for a named branch · `[x]` decided, see
`PROJECT_MEMORY.md` · `[-]` set aside, with the reason.

---

# Part 1 — Now

## 1.1 The layered store — the thing that makes everything else cheap

Six layers with stable interfaces. A new idea attaches at exactly one layer and
touches nothing else. This is the whole answer to "every step must help the next".

| Layer | Holds | A new branch here means |
|---|---|---|
| `raw` | documents exactly as published, plus source, hash, fetch date | a new bank, a new document type |
| `text` | cleaned, sectioned, sentence-split | a new segmentation or cleaning rule |
| `signal` | **`(doc_id, sentence_id, method_id, axis, value)`** | **a new scoring method — rows, not code** |
| `market` | **`(date, instrument, field, value)`** long format | a new instrument, country or control |
| `event` | the join: document ↔ windows ↔ market moves at several horizons | a new horizon or window definition |
| `study` | a declarative config → a standardised result record | **a new hypothesis — a config file, not code** |

The two rows in bold are load-bearing. Because `signal` is long and keyed by
`method_id` and `axis`, the eleventh model is an INSERT. Because a study is a
config naming which signals, which target, which sample and which validation, the
fortieth hypothesis is a config file. Everything downstream — notebooks, figures,
the site — reads from `study` results and never from a model directly.

`[?]` **Open:** storage format. Parquet on disk with a thin loader is the obvious
default; DuckDB over the same Parquet adds SQL for free.

## 1.2 First five steps

1. **Fix the store, the folder layout and the reproducibility contract.** Nothing
   else starts first.
2. **US corpus collector** — statements and minutes, 2000 → rolling present, with
   fetch date and content hash recorded and the raw HTML kept. Refresh must be
   incremental and idempotent, so re-running in six months extends the study
   rather than rebuilding it.
3. **Market layer** — FRED through ALFRED vintages, the GSW curve, the ACM term
   premium, and the FRBSF surprise series loaded as first-class instruments.
4. **Signal layer with four methods at once**, not one: a dictionary baseline,
   FinBERT, FOMC-RoBERTa, and zero-shot NLI. Four columns in one table from day
   one — that is what proves the architecture works.
5. **The deliberately naive study** — tone level against next-day yield change,
   full sample, no surprise correction. Run it, show that it fails or barely
   works, and use the failure to motivate everything after it.

`[>]` **1.2a** Step 5 doubles as the first interactive artefact: the naive result
plotted beside the corrected one is the clearest illustration of "priced in".

## 1.3 Measuring what was expected — five ways, all cheap

The naive frame fails because the statement is largely anticipated, so the
expectation must be measured. Do several; they disagree, and the disagreement is
itself informative.

- `[>]` **E-a. Previous statement as the expectation.** The text diff. Free,
  immediate, a surprisingly strong benchmark.
- `[>]` **E-b. Market-implied path.** Fed funds futures / OIS before versus after.
  The standard.
- `[>]` **E-c. Published surprise series.** FRBSF / Bauer–Swanson orthogonalised
  series as ground truth for the aggregate surprise.
- `[>]` **E-d. Predicted next statement (novel).** Train a model to predict the
  *text* of the next statement from everything known before it — prior statements,
  intervening speeches, macro releases. The surprise is then the distance between
  predicted and actual text. The project's own contribution to the measurement
  problem, and it needs no market data at all.
- `[?]` **E-e. Survey expectations** — Philadelphia Fed SPF and ECB SPF microdata,
  and the *dispersion* across forecasters as a measure of prior disagreement.

## 1.4 The sign-flip problem — six angles, one of them ours

A hawkish surprise can raise or lower long yields depending on whether it reads as
"tighter policy" or "the economy is stronger than we thought".

- `[>]` **S-a. Sign restrictions on stock co-movement** (Jarociński–Karadi). Yields
  and equities moving together → information shock; opposite → policy shock. Free.
- `[>]` **S-b. Orthogonalisation against pre-announcement public information**
  (Bauer–Swanson). Already computed in the free series.
- `[?]` **S-c. Three-way decomposition** into monetary, growth and risk-premium
  news (Cieslak–Schrimpf).
- `[?]` **S-d. Delphic versus Odyssean forward guidance** (Andrade–Ferroni) — a
  forecast about the economy versus a commitment about policy. The distinction is
  *linguistic*, which makes it unusually well suited to a text model.
- `[>>]` **S-e. Topic-conditional hawkishness — the project's best original
  angle.** The text usually says *why*. "Inflation remains elevated" and "economic
  activity has strengthened" are both hawkish, through different channels. Scoring
  hawkishness *per topic* separates the policy channel from the information channel
  **from the text alone**, with no market sign restrictions. If it works it is a
  genuine contribution; if it fails the failure is interesting. It is also the
  strongest argument for why this project should be built on text rather than
  prices.
- `[?]` **S-f. Sidestep it** — predict the magnitude of the move rather than its
  direction. Volatility is far more predictable than sign, and under-studied here.
- `[>>]` **S-g. Predict the sign flip itself, from the text.** Sharper than S-e and
  it subsumes it. Jarociński–Karadi and Bauer–Swanson already label each historical
  event as a *monetary* shock or an *information* shock — free, published,
  event-level data. So instead of treating the information effect as a nuisance to
  be identified out of market data, **ask whether the statement text, known before
  the market reacts, predicts which regime the event will fall into.** That
  inverts the entire literature, produces a new object, and is directly tradable.
  Nothing in the survey does this. Build S-e as the mechanism and S-g as the claim.
- `[>]` **S-h.** A second, independent reason to expect the flip, now testable:
  Cieslak–Schrimpf find non-monetary news dominates the **press-conference**
  window while the decision itself is mostly monetary news, and Acosta et al.
  (2025) find press conferences are now the *main* source of policy news. So the
  hypothesis is that **the prepared statement carries the policy signal and the
  press conference carries the information signal.** The FRBSF event-study
  database separates those windows, so this is directly testable.

## 1.5 Regimes

`[x]` Results reported per regime as well as pooled. Candidate breaks: 2000-2007
(conventional policy), 2008-2015 (ZLB and QE), 2015-2019 (normalisation),
2020-2021 (COVID), 2022-2024 (the fastest hiking cycle in forty years), 2025 on.

`[?]` Decide the breaks by rule rather than by eye — a structural break test, or
splits defined by policy state (at the ZLB or not, hiking or cutting) rather than
by calendar. Calendar splits chosen after seeing the results are a form of
snooping, and a referee will say so.

## 1.6 Start immediately — these cannot be done retroactively

- `[>>]` **Time-stamped forward forecasts.** From today onward, record a dated,
  committed prediction before every FOMC meeting. This is the *only* fully airtight
  answer to hindsight contamination: every meeting from now on is genuinely clean
  out-of-sample, and no backtest can match it. It costs almost nothing, it cannot
  be reconstructed later, and by the time the project is written up it is a real
  out-of-sample panel. **Set this up in E0, before anything else is modelled.**
- `[>]` **Snapshot the sources now.** Central bank sites have been redesigned
  repeatedly; archives disappear quietly. Collect and commit the raw text early
  even if the modelling is months away.

## 1.7 Cheapest features with the best odds

Roughly a day's work, and they give a working baseline plus the three features
most likely to survive, before any modelling investment:

1. `FOMC-RoBERTa` sentence scores;
2. **standing-language deletion diffs** — a `difflib` detector of phrases dropped
   relative to the previous statement, weighted by how long each had persisted.
   This is literally what practitioners watch ("considerable time", "patient"),
   there is no verified literature on it, and it is a few dozen lines;
3. cosine novelty against the previous statement.

## 1.8 Two rules that come from the sample size

- `[!]` **Multi-seed error bars, always.** At N≈260 the seed-to-seed spread will
  routinely exceed the gap between methods. A single-seed results table is the
  single most likely thing to sink the project. Trillion Dollar Words ships three
  seeds with separate splits — copy that protocol exactly.
- `[>]` **Method ordering by reliability at this N:** frozen strong embeddings plus
  a ridge head first (near-zero cost, hard to beat, the right baseline), then
  SetFit at sentence level, then LoRA on the last layers, and only then full
  fine-tuning — reported with error bars or not at all.

## 1.9 Notebooks, reproducibility, and where the logic lives

`[x]` The repository is a **chain of checkpoints**: after every meaningful step,
the owner must be able to reproduce the result from the notebooks and the
committed material alone.

`[?]` **Open, and it needs a decision:** how much logic sits in the notebooks
themselves versus in an importable package. Both extremes have a real cost, and
the trade-off was put to the owner explicitly. Whatever is chosen, three things
hold: the notebooks are the executable path, every artefact lands in a declared
folder, and the environment is pinned.

---

# Part 2 — Branches

Each branch attaches at one layer of §1.1 and can be built, finished and written
up on its own. None blocks another.

## B1 · Signals — the model zoo
*Attaches at: `signal`.*

- `[>]` Dictionary floor: Loughran–McDonald, Apel–Blix Grimaldi, Picault–Renault.
  If a transformer cannot beat these, that is the headline, not a footnote.
- `[>]` FOMC-RoBERTa (the published benchmark), FinBERT (a deliberate
  task-mismatch, kept to show that domain-task alignment matters), zero-shot NLI.
- `[?]` Fine-tuned DeBERTa-v3 on the labelled sets; **ordinal rather than
  three-class**, since hawk-dove is a continuum.
- `[>>]` **Pairwise Bradley–Terry scaling — the strongest novelty claim available.**
  Nobody can give a reliable *absolute* hawkishness label, but anyone can reliably
  answer "is A more hawkish than B?" — and 260 documents yield ~33,000 candidate
  pairs. We are annotation-budget-starved, not label-starved. A siamese encoder
  trained with a Bradley–Terry / RankNet loss yields a scalar hawk-dove scale
  identified up to an affine transform, which is fine because it is standardised
  before regression. The survey found **no verified paper applying CORAL/CORN or
  Bradley–Terry to hawk-dove scaling**. It is also formally identical to an RLHF
  reward model, which makes it legible to any modern referee. Tools exist:
  `choix`, `coral-pytorch`, sentence-transformers with `CoSENTLoss`.
- `[>]` **Inherently interpretable architecture instead of post-hoc attribution.**
  A shared sentence encoder with learned **non-negative aggregation weights**, then
  regress yields on the document score. Post-hoc attribution only tells you what
  drove *the model's score*, not what moved *the market*; this design makes the
  weights structurally part of the model, sidesteps the whole attention-faithfulness
  argument, and at N≈260 the constraint doubles as strong regularisation. Should be
  the default for the attribution viewer.
- `[-]` **Another LLM-versus-FinBERT annotation benchmark.** Crowded, and BIS
  WP 1215 finds domain-retrained encoders beating frontier LLMs at FOMC stance
  anyway. Also `[-]` a multi-agent FOMC simulator — four groups are already there
  and the reported accuracies are almost certainly contaminated.
- `[?]` LLM as annotator over the full corpus, distilled into a small model. The
  comparison of its labels against the human ones is a publishable result itself.
- `[?]` Contrastive/siamese model on `(previous statement, current statement)`
  pairs, learning a representation of *change in stance* directly.
- `[?]` Long-document encoders for minutes and transcripts versus chunk-and-pool.
- `[?]` A second axis throughout — **conviction/uncertainty** — and a third,
  **forward-looking or not**. The WCB corpus supplies supervised labels for both.
- `[?]` Topic-conditional scoring (see S-e): inflation, labour, growth, financial
  stability, balance sheet.

## B2 · Corpus width
*Attaches at: `raw`.*

- `[>]` Statements → minutes → press-conference transcripts → speeches →
  testimony → Beige Book → five-year-lagged verbatim transcripts.
- `[>]` The CBS Dataset (35k speeches, 131 banks) or the BIS bulk ZIP as a single
  multi-bank English source, rather than twenty scrapers.
- `[?]` **What a statement does *not* say.** Removed sentences, dropped phrases,
  abandoned forward guidance. Deletions may carry more information than additions,
  and almost nobody models them.
- `[?]` Dissents, vote splits and the identity of dissenters as structured
  features — free, strong, and a sanity check on the text model.

## B3 · Targets
*Attaches at: `market` and `event`.*

- `[>]` 2y / 10y / 30y separately. The policy-expectations channel predicts the
  effect decays along the curve — a clean falsifiable test.
- `[>]` Curve slope; breakeven inflation versus real yields; the ACM split into
  expectations and term premium.
- `[?]` Realised and implied volatility (MOVE) as targets in their own right.
- `[?]` The published surprise series as a target — if the text predicts the
  measured shock, that is a stronger claim than predicting a daily return.

## B4 · Cross-country
*Attaches at: `raw` and `market`.*

- `[>]` The WCB corpus makes 25 banks available immediately, with labels. Note the
  uneven coverage: the ECB starts only in 2015 there and must be topped up.
- `[?]` Language strategy: native-language multilingual encoder, official English
  translation, or machine translation. Each biases differently.
- `[?]` Does a hawk-dove model trained on the Fed **transfer** to other banks
  without retraining? Clean, well-defined, under-answered. Note that WCB's own
  finding — the aggregated model beats per-bank models — is a partial answer worth
  testing against.
- `[?]` Per-bank calibration: communication cultures differ enough (consensus
  voice, committee plus press conference, governor-centric, deliberately opaque)
  that one scale may not be comparable across banks.

## B5 · Spillovers
*Attaches at: `study`.*

- `[?]` Fed hawkish surprise → global long yields; EM local-currency yields react
  more than DM. Decompose into a rate channel and a risk-premium channel.
- `[?]` Does a local bank's own tone matter *after* controlling for the Fed?
- `[?]` Asymmetry: do dovish surprises move markets more than hawkish ones?
- `[?]` State dependence: same text, different effect at the ZLB, in a hiking
  cycle, in a cutting cycle.
- `[?]` Do speeches between meetings predict the *next statement's* tone? A
  text-to-text task with an order of magnitude more data than text-to-market.
- `[?]` Tone dispersion across committee speakers as a predictor of dissent and of
  the next meeting's surprise.

## B6 · Trading strategy
*Attaches at: `study`.*

- `[>]` Event-window rule with realistic transaction costs, a Sharpe, a drawdown
  and a confidence interval. With ~210 events the interval will be wide — say so
  rather than reporting a point estimate.
- `[?]` Position sizing on model confidence rather than a binary signal.
- `[?]` Cross-sectional version: trade the *relative* move across countries on a
  Fed event, which is far less demanding than predicting a level.
- `[?]` Honest failure modes as part of the deliverable: what would have to be
  true for this to work, and is it.

## B7 · Interpretability and interaction
*Attaches at: presentation.*

- `[>]` **Sentence attribution viewer** — a real statement, each sentence heat-
  coloured by contribution, beside the actual market move. Claims nothing, shows
  everything. The most defensible demo.
- `[>]` **Guess the signal** — the reader plays against the model on real
  excerpts, then sees what yields actually did. Fully precomputable.
- `[>]` **Text-change visualisation** (owner's idea) — how much each statement
  changed from the last, over 26 years, with changed words highlighted. Doubles as
  the visual proof of why the diff matters.
- `[>]` **Linguistic and grammatical infographics** (owner's idea) — length,
  readability, hedging density, sentence complexity, vocabulary turnover, tense
  and modality over time. Cheap to compute, genuinely interesting, and independent
  of whether the main effect exists.
- `[?]` **Counterfactual editing** — swap "patient" for "vigilant" and watch the
  score move. Teaches more than any chart.
- `[?]` **Be the central banker** — the user writes a statement, the model scores
  it. Great hook; must be framed as "this is what the model learned", never as a
  forecast, and it is adversarially fragile.
- `[?]` Timeline: tone index versus policy rate versus 2y and 10y, 2000 → now,
  with annotated episodes — taper tantrum, the 2018 pivot, COVID, 2022.
- `[?]` Country comparison view once B4 exists.

## B8 · Method-critical studies
*Small, self-contained, each a complete result on its own, each strengthening the
main line rather than competing with it.*

- `[?]` **Hindsight contamination, measured.** Score the same statements with
  models of different pretraining cutoffs and watch how "predictive" accuracy
  varies with how much of the future the model has seen. Almost nobody quantifies
  this; it is cheap, original, and it makes every other result more credible.
- `[?]` **Statistical power up front.** Given observed yield-change volatility and
  ~210 events, what effect size is detectable at all? Publishing this *before* the
  results is a strong signal of seriousness.
- `[?]` **Placebo battery.** Shuffled dates, non-FOMC days, a non-monetary corpus.
  If the pipeline still finds an effect, it does not work.
- `[?]` **Human versus LLM versus dictionary labels** on the same sentences: where
  do they disagree, and does the disagreement predict anything?
- `[?]` **Does the annotation scheme matter?** Three-class versus ordinal versus
  WCB's four-class scheme including "irrelevant".

## B9 · The site
*Attaches at: presentation.*

- `[>]` Static-first: precomputed JSON on a static host. No server, no cost, no
  attack surface. Everything in B7 except live inference works this way.
- `[?]` A live inference endpoint only if "be the central banker" graduates, and
  only with rate limiting, input length caps and an adversarial pass first.
- `[!]` **Licence constraint.** Both labelled corpora are CC-BY-NC; WCB adds
  share-alike. Redistribution terms must be checked before the site serves any of
  their text, and share-alike may reach derived outputs.

---

# Part 3 — Things that must not be forgotten

- `[!]` **Publication time versus event time.** Minutes are published three weeks
  after the meeting they describe. Using the meeting date is a look-ahead bug, and
  an easy one to ship.
- `[!]` **Revised macro data.** Use ALFRED vintages, or the study regresses text
  scores on numbers that did not exist at the time.
- `[!]` **Label leakage.** If a hawk-dove label is derived from the market
  reaction, regressing the market reaction on it is circular.
- `[!]` **Random splits.** Walk-forward only. The single most common way projects
  of this kind produce a beautiful, meaningless number.
- `[!]` **Multiple testing.** Dozens of hypotheses × several models × several
  horizons guarantees false positives. Nominate the primary hypothesis in advance
  and label everything else exploratory.
- `[!]` **Source fragility.** Commit the collected text, not only the scraper.
  Several of these sites were redesigned in the last three years.
- `[!]` **Breadth becoming sprawl.** Many branches is the goal; many *unfinished*
  branches is the failure mode. Every branch gets a definition of done before it
  starts.
- `[!]` **Shared budgets when fanning out agents.** On 2026-07-27 a parallel
  research fan-out consumed the session's entire web-search allowance and left
  later agents unable to verify anything. Cap the fan-out and give each agent an
  explicit budget.

# Part 4 — Considered and set aside

- `[-]` **Buying intraday Treasury data now.** The free surprise series already
  contain the 30-minute windows for these exact events; and in 2000-2003 the
  liquid market was the CBOT pit rather than Globex, so purchased electronic data
  would be *worse* than the published series in precisely the years we would be
  paying for. See `DOCS/DATA_SOURCES.md` §5.
- `[-]` **Nasdaq Data Link CHRIS** as a free futures source — dead since 2018.
- `[?]` Deferred, worth revisiting: options-implied distributions from SOFR
  options; press-conference audio (tone of voice, hesitation — real literature
  exists); non-central-bank texts such as IMF and BIS reports; extending the
  sample before 2000 via FRASER.
