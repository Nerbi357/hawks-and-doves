# IDEAS BACKLOG — hawks-and-doves

Everything raised but not yet decided. Nothing here is agreed work. The owner
decides what graduates into `PLAN.md`.

Status codes: `[?]` open question for the owner · `[>]` proposed for a version ·
`[x]` decided (see `PROJECT_MEMORY.md`) · `[-]` rejected, with the reason.

---

## 1. Framing — what the project is actually measuring

The naive frame ("hawkish text → yields up") is not directly testable. Four
reframings, from weakest to strongest:

- `[>]` **F1. Tone level → yield level/change.** Simplest, almost certainly
  finds nothing beyond a spurious trend correlation. Useful only as a
  deliberately-shown failure baseline.
- `[>]` **F2. Tone → yield change in an event window** (release day, or
  release-day close minus previous close). The honest minimum viable version.
- `[>]` **F3. Text-implied surprise → yield change.** Model predicts the
  *deviation* of the statement from what was expected, given (a) the previous
  statement and (b) the market's pre-meeting pricing. This is what the
  literature actually finds effects for.
- `[>]` **F4. Text → decomposed yield move.** Split the move into policy-path
  expectations vs term premium (ACM), or 2y (policy) vs 10y-2y (growth/term),
  or real vs breakeven inflation (TIPS). Different text content should map to
  different components — this is where a paper-level contribution lives.
- `[?]` **F5. Text → the CB's own next action** (next meeting's rate decision),
  not the market. Cleaner label, no market microstructure, but the market has
  already priced most of it, so "beating the market" is the real bar.
- `[?]` **F6. Text → volatility / uncertainty** rather than direction. Direction
  is nearly unpredictable; *magnitude* of the move is much more predictable.
  Under-explored and a strong fallback if direction fails.

## 2. The sample-size problem (the binding constraint of the whole project)

FOMC: 8 meetings/year. 1994–2026 ≈ 260 statements. Any per-meeting-labelled
supervised task has ~260 rows. Directions to escape it:

- `[>]` **S1. Sentence-level modelling.** ~40k sentences instead of ~260 docs.
  Model scores sentences, document score is an aggregation. Gives
  interpretability for free (which sentence moved the score).
- `[>]` **S2. Widen the document set:** statements + minutes + press-conference
  transcripts + speeches + testimony + Beige Book + FOMC transcripts (5-year
  lag). Speeches alone are hundreds per year.
- `[>]` **S3. Pool across central banks** — a single multilingual model trained
  on 15 CBs sees thousands of events. Requires comparability work (§4).
- `[>]` **S4. Frozen encoder + tiny head.** Do not fine-tune 110M parameters on
  260 examples. Embeddings + regularised linear/GBM head.
- `[?]` **S5. LLM as weak annotator → distillation.** Label 40k sentences with a
  strong LLM, train a small model on the labels. Cheap, scalable, but the
  labels inherit the LLM's hindsight (see L1).
- `[?]` **S6. Data augmentation by paraphrase** of central-bank sentences.
  Risky: the signal in this domain lives in exact word choice.
- `[?]` **S7. Power analysis up front.** With N events and observed yield
  volatility, what R² is even detectable? Publishing this is a differentiator —
  almost no student project does it.

## 3. Text representation

- `[>]` **T1. Diff against the previous statement.** FOMC statements are
  templated; 80–95% of the text repeats. The *changed* sentences are the news.
  Feed only the diff, or feed both and let the model see the delta.
- `[>]` **T2. Section-aware parsing.** Statement ≠ minutes ≠ press conference.
  Within minutes: "Participants' views" vs "Committee policy action" vs staff
  review. Q&A in a press conference is unscripted and much noisier than the
  prepared statement — likely a different signal.
- `[>]` **T3. Dissent counts and vote splits** as structured features — free,
  strong, and a natural sanity check on the text model.
- `[?]` **T4. Forward-guidance phrase tracking** ("patient", "measured pace",
  "for some time", "data dependent", "well anchored") as an explicit lexicon
  with dated introduction/removal.
- `[?]` **T5. Hedging / uncertainty language** as a separate axis from
  hawk-dove. Two-dimensional score: direction × conviction.
- `[?]` **T6. Length, readability, jargon density** — measurable and known to
  correlate with market reaction.
- `[?]` **T7. Topic decomposition** (inflation / labour / growth / financial
  stability / balance sheet) and a hawkishness score *per topic*. "Hawkish on
  inflation, dovish on growth" is a real and common state that a single scalar
  destroys.

## 4. Cross-country comparability

- `[?]` **C1. Which document is the FOMC statement's equivalent** at each bank?
  ECB: monetary policy decision + press conference + accounts. BoJ: statement +
  Outlook Report + Summary of Opinions. CBR: press release + governor's
  statement + Monetary Policy Report. BCB: Copom statement + minutes. Not
  interchangeable — needs an explicit mapping table.
- `[?]` **C2. Language strategy.** (a) native-language multilingual model, (b)
  official English translation where the bank publishes one (CBR, BCB, BoJ,
  ECB all do), (c) machine-translate everything to English. Each biases
  differently; (b) is safest but the translation is written *after* the fact and
  may smooth tone.
- `[?]` **C3. Regime differences.** ZIRP/QE-era language is not comparable to
  inflation-targeting-EM language. Consider country-specific calibration or a
  country fixed effect.
- `[?]` **C4. Non-comparable communication cultures**: Fed is consensus-voice,
  ECB is committee-with-press-conference, CBR is governor-centric, BoJ is
  deliberately opaque. May need a per-bank "hawkishness scale" normalisation.

## 5. Market data and targets

- `[>]` **M1. US yields:** FRED (`DGS2`, `DGS10`, `DGS30`, `T10Y2Y`,
  `T10YIE`, `DFII10`), Treasury par curve, **GSW zero-coupon curve** (Fed,
  daily, the research standard), **ACM term premium** (NY Fed).
- `[>]` **M2. Published policy-surprise series as validation targets:**
  SF Fed *U.S. Monetary Policy Event-Study Database* / Bauer–Swanson MPS
  (30-minute windows, free). If the text model correlates with these, the text
  is genuinely carrying policy news — a much stronger claim than daily returns.
- `[>]` **M3. EA-MPD** (Altavilla et al., ECB) — intraday ECB event windows,
  free. Makes the euro area the second-best-instrumented bank after the US.
- `[?]` **M4. Intraday US Treasury data is not free.** Options: accept daily
  resolution, or use free intraday proxies (TLT/IEF ETF minute bars from a
  free provider) — a proxy with basis risk that must be stated.
- `[>]` **M5. Controls / co-drivers:** CPI & NFP release surprises, VIX/MOVE,
  DXY, oil, term-premium level, prior-day yield change, curve slope, QT/QE
  announcements, auction calendar, fiscal news.
- `[?]` **M6. Cross-market targets for spillover tests:** Bund, JGB, Gilt, OFZ,
  India 10y, Brazil NTN-B/DI, Mexico M-bonos, EMBI spread, local-currency vs
  hard-currency split.
- `[?]` **M7. Fed funds futures / OIS** to construct the pre-meeting expectation
  — free at daily frequency via CME FedWatch-style data or FRED OIS series.

## 6. Hypotheses to test (spillovers and interactions)

- `[?]` **H1.** Fed hawkish surprise raises global long yields; the effect on EM
  local yields is larger than on DM.
- `[?]` **H2.** The effect on EM decomposes into a rate channel and a risk-premium
  channel; hawkish Fed → EM spreads widen (risk-off) even when local policy is
  unchanged.
- `[?]` **H3.** A local CB's own tone has explanatory power *after* controlling
  for the Fed — i.e. domestic communication is not just an echo.
- `[?]` **H4.** Asymmetry: dovish surprises move markets more than hawkish ones
  (or the reverse) — test explicitly rather than assuming linearity.
- `[?]` **H5.** State dependence: the same text has a different effect at the
  zero lower bound, in a hiking cycle, and in a cutting cycle.
- `[?]` **H6.** The "information effect": a hawkish surprise that also signals a
  strong economy raises yields *and* equities; a pure policy shock moves them
  oppositely. Sign of the text effect should depend on which regime.
- `[?]` **H7.** Minutes matter less than statements because 3 weeks of news have
  intervened — measurable, and a good honest negative result.
- `[?]` **H8.** Press-conference Q&A adds information beyond the statement
  (Powell has moved markets in Q&A repeatedly).
- `[?]` **H9.** Speeches between meetings predict the *next* statement's tone —
  a text-to-text task with far more data than text-to-market.
- `[?]` **H10.** Tone dispersion across speakers (hawks vs doves on the
  committee) predicts dissent and the surprise at the next meeting.
- `[?]` **H11.** Time-of-day / calendar effects: 14:00 statement vs 14:30 press
  conference contribute different amounts.
- `[?]` **H12.** Effect decays across the curve: strongest at 2y, weaker at 10y,
  weakest at 30y — a clean falsifiable prediction of the policy-expectations
  channel.

## 7. Models

- `[>]` **N1. Floor baselines, non-neural:** Loughran–McDonald finance lexicon,
  Apel–Blix Grimaldi hawk/dove dictionary, Picault–Renault (ECB), plus simple
  word-count deltas. If a transformer cannot beat these, say so loudly.
- `[>]` **N2. FOMC-RoBERTa / `gtfintechlab/fomc_communication`** (Trillion
  Dollar Words, ACL 2023): ~2.5k human-labelled hawk/dove/neutral FOMC
  sentences and a released fine-tuned model. This is the strongest available
  starting point and the benchmark to beat. Licence: CC BY-NC 4.0 —
  non-commercial, fine for this project, must be stated.
- `[?]` **N3. FinBERT (Araci)** — trained for *financial news sentiment*
  (positive/negative), not hawk/dove. Cheap to include, but conceptually a
  mismatch; use it to demonstrate that domain-task alignment matters.
- `[?]` **N4. BART-large-MNLI zero-shot** — no training required, good as a
  "what do you get for free" baseline; sensitive to label wording, so the
  hypothesis template becomes a hyperparameter.
- `[?]` **N5. Fine-tuned DeBERTa-v3 / RoBERTa** on the labelled sentence set.
- `[?]` **N6. Long-document encoders** (Longformer, ModernBERT) for minutes and
  transcripts, vs chunk-and-pool.
- `[?]` **N7. Multilingual encoders** for §4: XLM-R, LaBSE, multilingual-E5,
  or per-language models.
- `[?]` **N8. LLM with structured output** as an annotator and as a competitor
  (score each sentence on hawk-dove and on conviction). Also the natural engine
  for the interactive demo.
- `[?]` **N9. Fusion head:** text embedding + macro/market features →
  gradient boosting vs a small MLP. Expect GBM to win on tabular; the neural
  part earns its place in the *encoder*, not the head.
- `[?]` **N10. Sequence models over the meeting series** (the path of tone over
  time as a signal): a small temporal model on top of per-meeting scores.
- `[?]` **N11. Contrastive/siamese training** on statement pairs
  (previous, current) to learn a "change in stance" representation directly.
- `[?]` **N12. Ordinal rather than 3-class** objective — hawk/dove is a
  continuum; ordinal regression respects it.

## 8. Evaluation discipline (where most projects like this die)

- `[>]` **E1. Walk-forward / expanding-window CV only.** Random splits leak the
  future and will produce a beautiful, meaningless number.
- `[>]` **E2. Compulsory baselines:** random walk / zero-change, AR(1) on yield
  changes, market-implied expectation, lexicon score, and "previous statement's
  score".
- `[>]` **E3. Economic evaluation:** a simple event-window trading rule with
  realistic transaction costs, reported with a Sharpe and a drawdown — not just
  accuracy. And a confidence interval, because with ~260 events, everything is
  noisy.
- `[>]` **E4. Multiple-testing control.** Dozens of hypotheses × several models
  × several horizons = guaranteed false positives. Pre-register the primary
  hypothesis; treat the rest as exploratory and say so.
- `[?]` **E5. Placebo tests:** shuffle dates, use non-FOMC days, use a
  non-monetary text corpus. If the model still "works", it does not work.
- `[?]` **E6. Regime split:** pre-2008 / ZLB / 2015–2019 / COVID / 2022–2024
  hiking / after. Report per-regime, expect instability, and say so.

## 9. Leakage and honesty risks

- `[!]` **L1. Hindsight in pretrained models.** Any modern LLM knows what
  happened after every FOMC meeting in the sample. Zero-shot "prediction" on
  2022 text is not prediction. Mitigations: use encoder models with a known
  pretraining cutoff, report the cutoff, and treat any LLM-based result as
  in-sample by construction.
- `[!]` **L2. Label leakage.** If hawk/dove labels are derived from the market
  reaction, then regressing the market reaction on them is circular.
- `[!]` **L3. Publication-time vs event-time.** Minutes are published 3 weeks
  after the meeting they describe; using the meeting date is a look-ahead bug.
- `[!]` **L4. Revised macro data.** FRED series are revised. Use ALFRED
  vintages for anything used as a real-time feature.
- `[!]` **L5. Survivorship/format drift** in scraped documents (HTML layout
  changed several times; pre-2006 statements have a different structure).

## 10. Interactive layer (v2+)

- `[>]` **I1. "Guess the signal"** — user reads a real excerpt, guesses
  hawk/dove, sees the model's answer *and* what yields actually did. Scoreboard
  vs the model. Cheap, genuinely fun, no inference server needed if
  precomputed.
- `[>]` **I2. Sentence attribution viewer** — a real statement with each
  sentence heat-coloured by its contribution, next to the actual market move.
  The most defensible demo: it shows what the model learned, claims nothing.
- `[?]` **I3. "Be the central banker"** — user writes a statement, model scores
  it and shows the implied yield move. Great hook, but must be framed as
  *"this is the model's mapping"*, never as a forecast. Also adversarially
  fragile (users will type nonsense).
- `[?]` **I4. Counterfactual word editing** — swap "patient" for "vigilant" and
  watch the score move. Teaches the reader more than any chart.
- `[?]` **I5. Timeline visualisation** — tone index vs policy rate vs 2y/10y,
  1994→now, with annotated episodes (taper tantrum, 2013; pivot, 2018;
  COVID; 2022 hikes).
- `[?]` **I6. Country comparison view** — same tone index across 10 CBs.
- `[?]` **I7. Static-first hosting** (GitHub Pages + precomputed JSON) so v1
  needs no server; a live model endpoint only in a later version.

## 11. Deliverables and repository shape

- `[?]` **D1. Notebook set** (collection → processing → modelling → analysis)
  vs `src/` package + thin notebooks. Recommendation: package + thin notebooks;
  notebooks that contain logic become unmaintainable by v2.
- `[?]` **D2. Methodology notebook** as the owner described — decisions, models,
  reasons. Effectively the paper.
- `[?]` **D3. Data snapshots committed** (small, dated) vs rebuild-on-demand.
  Recommendation: commit the *text* corpus (small, and the source may change or
  vanish) and rebuild market data from APIs.
- `[?]` **D4. Versioning as "editions"** — v1, v2, v3 as tagged releases with a
  changelog, mirroring how a paper gets revised.

## 12. Raised but out of scope so far

- `[?]` Options-implied distributions (SOFR options) as a richer expectation
  measure.
- `[?]` Central-bank speeches → FX, equities, credit — the same machinery,
  different target.
- `[?]` Non-CB texts: IMF/BIS reports, minutes of other committees.
- `[?]` Audio/video of press conferences (tone of voice, hesitation) — real
  literature exists; far beyond v1.
