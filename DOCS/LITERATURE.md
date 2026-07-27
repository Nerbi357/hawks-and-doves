# LITERATURE MAP

State of the art at the intersection of NLP and monetary policy communication,
2023–2026, compiled 2026-07-27. Organised by what it changes for this project.

**Verification legend — read before citing anything.**

| Mark | Meaning |
|---|---|
| ✅ | title, venue and URL corroborated across independent search results |
| 🔎 | exists at that URL, but authors / date / numbers come from a snippet only |
| ❔ | model recall, **not verified** — a lead, not a citation |
| 💭 | our own judgement or inference, not a finding |

**No paper below was actually opened.** This environment blocked egress to arXiv,
SSRN, NBER, AEA, ACL Anthology, BIS, federalreserve.gov and frbsf.org, and the
session's search budget ran out. Roughly a third of the entries — everything 🔎 or
❔, **and every 2026 arXiv identifier** — rests on search metadata or recall.
Treat this as a well-organised reading list with confidence labels, not as a
bibliography. Verify before citing, especially the 2026 items.

---

## 1. The four papers to read before the framing is fixed

Each is a potential scoop on part of this project.

1. **"Decoding central bank communications with large language models"**, *J. Int.
   Fin. Markets, Inst. & Money* 109 (2026) 🔎 — 1-minute Treasury futures across
   statement, minutes and transcript. The LLM-derived dovish shock decomposes into
   **two opposing channels: views on the recent economy versus forward guidance.**
   The closest published thing to this project's core question.
2. **Jones (RBA), "Ornithologist"**, arXiv:2505.09083 ✅ — taxonomy-guided
   reasoning: walk the model through human-authored decision trees rather than
   asking for a label. Less supervision, less hallucination, more transparency;
   RBA scores predict the future cash-rate path. The closest 2025 competitor to
   our design.
3. **Handlan, "Text Shocks and Monetary Surprises"** ✅ — fine-tunes XLNet to map
   statement *text* → change in fed funds futures, then uses the fitted map to
   construct a text-shock series. Claims wording explains **~4× more futures
   variation than the rate decision itself**, on a sample the size of ours. This
   is the direct precedent for our "predicted next statement" idea (`IDEAS.md`
   E-d). Companion paper: *"FedSpeak Matters: Statement Similarity and Monetary
   Policy Expectations"*.
4. **Ehrmann & Talmi, "Starting from a blank page?"**, JME ~2020 ❔ — low semantic
   similarity to the previous statement raises market volatility. The closest
   prior for the novelty idea; verify first.

---

## 2. The dataset that removes our hardest constraint

**Acosta, Ajello, Bauer, Loria & Miranda-Agrippino (2025), "Financial Market
Effects of FOMC Communication: Evidence from a New Event-Study Database"** —
FRBSF WP 2025-30 ✅

The **U.S. Monetary Policy Event-Study Database** is public, regularly updated,
and covers **announcements, press conferences and minutes releases separately**,
with all instruments per event, timestamps, and event flags (such as whether an
SEP was released). Sourced from LSEG Tick History — **terminal-quality intraday
data without a terminal.** Ships R code to build surprises as the first principal
component of money-market futures.

Two verified findings from it that matter directly:

- large surprises have returned in recent years;
- **press conferences are now the main source of policy news.**

💭 This plus the SF Fed `MPS` / `MPS_ORTH` series is the backbone of our
identification, and it lets us run **separate text models per communication
tier** — which, combined with §3 below, is probably where the result lives.

**First action:** confirm the end date of `MPS_ORTH` and download the USMPD. The
whole identification strategy depends on it, and the page was blocked here.

---

## 3. The information effect — what is implementable for free

| Strategy | Core idea | Free? |
|---|---|---|
| **Bauer–Swanson orthogonalised surprise** ✅ (AER 113(3); NBER w29939) | orthogonalise the raw surprise against macro/financial data pre-dating the announcement; the residual is the true shock | ✅ download `MPS_ORTH` |
| **Jarociński–Karadi "poor man's" sign restriction** ✅ (AEJ:Macro 12(2)) | rates ↑ stocks ↓ → tightening shock; rates ↑ stocks ↑ → information shock | ✅ **daily** S&P sign + a published surprise series. 💭 the cheapest sign-flip control available — build it first |
| **GSS / Swanson factor rotation** ✅ | rotate announcement-window asset moves into target / path / LSAP factors | ✅ reconstruct from the USMPD |
| **Andrade–Ferroni Delphic vs Odyssean** ❔ | rates ↓ with inflation expectations ↑ = genuine commitment; rates ↓ with expectations ↓ = the bank revealed bad news | ✅ 💭 **approximable with free FRED breakevens** (`T5YIE`, `T5YIFR`, `T10YIE`) at daily frequency. An under-used axis |
| **Miranda-Agrippino–Ricco** ✅ | project surprises on central bank forecasts to get an informationally robust instrument | ⚠️ 💭 Greenbook/Tealbook are released on a **five-year lag** — cannot be run for the most recent five years |
| **Nakamura–Steinsson** ✅ (QJE 133(3)) | the canonical statement of the puzzle: after a tightening, growth forecasts also rise | ⚠️ replication data yes; live extension needs the USMPD |
| **Cieslak–Schrimpf** ✅ (JIE 118) | decompose into monetary / growth / risk-premium news via stock-bond co-movement plus monotonicity across maturities | ❌ needs intraday multi-maturity yields and equities |
| **Lewis (2025)** ✅ REStat 107(4) | heteroskedasticity-based decomposition | ❌ **explicitly detects weak identification in daily data** — intraday required |

**Two findings that reshape the study design:**

- **Cieslak–Schrimpf:** the non-monetary component is **more than half** of the
  reaction to **press conferences and minutes**, versus mostly monetary news for
  the decision itself. **The sign flip is concentrated in the communication
  window, not the decision.**
- **Hoesch, Rossi & Sekhposyan** ✅ (AEJ:Macro 15(3), *not* JMCB): the Fed's
  information advantage is **substantially weaker in recent years**. 💭 Direct
  implication: a pooled 2000-2026 regression is mis-specified. Split the sample
  or interact with time — which is what our regime work already does.

---

## 4. Models and annotation

- **Hansen & Kazinnik, "Can ChatGPT Decipher Fedspeak?"** SSRN 4399406 ✅ — the
  canonical reference; GPT classifies stance far closer to the human benchmark
  than dictionaries or BERT-family baselines.
- **Shah, Paturi & Chava, "Trillion Dollar Words"** ACL 2023 ✅ — the benchmark
  corpus and `FOMC-RoBERTa`. **Ships three seeds with separate splits — copy that
  protocol.**
- **Shah, Sukhani, Pardawala et al., "Words That Unite The World"** NeurIPS 2025,
  arXiv:2505.17048 ✅ — the WCB dataset. Notable method result: **putting the human
  annotation guide in-context closes most of the LLM-versus-encoder gap.**
- **Gambacorta et al., "CB-LMs"** BIS WP 1215 ✅ — **some domain-retrained encoders
  beat frontier generative LLMs at classifying FOMC stance.** The strongest
  "small domain model > big general model" result in this literature, and the
  reason not to enter the LLM-annotation race.
- **Peskoff et al.** Findings of EMNLP 2023 ✅ — the diversity of member views
  visible in transcripts is **almost entirely stripped out of the public
  statement**; statement-only sentiment systematically understates dissent.
- **Mantion et al.** SSRN 4769112 ✅ — a systematic **dovish tilt** in ChatGPT's
  Fedspeak labels, and corrections for it. The best single failure-mode citation.
- **Pfeifer & Marohl, "CentralBankRoBERTa"** ✅ — adds an *audience* axis
  (households / firms / financial sector / government / the bank itself).
- **Yao et al., "Interpreting Fedspeak with Confidence"** arXiv:2508.08001, AAAI
  2026 ✅ — uncertainty-aware decoding whose predicted uncertainty correlates with
  error rate, giving a usable **abstention signal**.
- **Gorodnichenko, Pham & Talavera, "The Voice of Monetary Policy"** AER 113(2)
  ✅ — vocal tone in press conferences moves equities after controlling for the
  action and the text; **bonds take few vocal cues.** 💭 Our upper bound on what
  text alone can explain.

---

## 5. Methods worth stealing

**Novelty and surprise.** *"Financial market reactions to the novelty of
information in FOMC minutes"*, Finance Research Letters (2026) 🔎 — paragraph-level
semantic distance from prior communications, split into *overall novelty* and
*novelty tilt*. Crucially: **overall novelty explains the magnitude of repricing
but not its sign; novelty tilt carries the direction.** 2-year yield volatility
about 3× normal on minutes days. Cheap baselines: cosine to the previous
statement; **conditional-minus-unconditional perplexity** — 💭 always difference
the two, because raw perplexity is confounded by boilerplate ratio and length,
and public models have memorised FOMC statements unevenly (worse for famous ones),
which is a real identification problem for post-2010 text.

**Topic-conditional sentiment.** The recipe exists: LDA topics with dictionary
tone *within* each topic, aggregated to a topic-weighted index (DSFE 2022 ✅);
and a *separate reaction function per topic* (2024 ✅). 💭 **Warning: BERTopic's
HDBSCAN will produce garbage on ~260 documents.** Run it at sentence level, or
better, skip unsupervised topics and use a fixed theory-driven topic set. Crossing
a fixed topic set with `FOMC-RoBERTa` gives topic-conditional hawkishness at
**zero labelling cost**.

**Uncertainty and hedging.** **Cieslak, Hansen, McMahon & Xiao, "Policymakers'
Uncertainty"** NBER w31849 ✅ — uncertainty is **type-specific** (inflation vs
growth vs financial), and inflation uncertainty tightens the stance beyond what
forecasts explain. NLP lineage: CoNLL-2010 hedge detection ✅, and speculation
*scope* resolution ✅ — the latter matters because "we do **not** expect inflation
to fall" is scored wrongly by any bag-of-hedge-words method. Free and immediate:
Loughran–McDonald's **Weak Modal / Strong Modal** lists, underused here.

**Readability and complexity.** Bulíř, Čihák & Jansen ✅ — but the honest finding
is that the clarity–volatility relation shows up mainly for the euro area
*pre-crisis* and **is not robust once the crisis unfolds**. Cite as the cautionary
result. More interesting: **Hayo et al.** (JIMF) ✅ — complex communication does not
kill the market reaction, it **displaces it into the Q&A**. And *"Divergent Market
Reactions to Abstract Language"* (AMJ) ✅ uses linguistic **abstraction** rather
than syllable counting, and finds core and periphery markets react in *opposite*
directions. 💭 Abstraction and mean token surprisal are both better motivated than
Flesch–Kincaid, which is a pure function of sentence length and syllables and is
inflated by jargon a bond desk parses fine.

**Attribution — a design decision, not a reporting choice.** Do **not** report raw
attention (Jain & Wallace 2019 vs Wiegreffe & Pinter 2019 ✅; Liu et al. ICML 2022
on faithfulness violation ✅). Use `ferret` (EACL 2023 demo) ✅, which wraps
Integrated Gradients, Gradient×Input, Partition SHAP and LIME **and scores them**
on comprehensiveness and sufficiency, so the most faithful method can be reported
rather than chosen arbitrarily.

💭 **The design point that matters most:** post-hoc attribution tells you which
sentence drove *the model's score*, not which sentence moved *the market*. If we
want the latter, use an **inherently interpretable architecture** — a shared
sentence encoder with learned non-negative aggregation weights, then regress
yields on the document score. The weights are then structurally part of the model,
the whole faithfulness debate is sidestepped, and at N≈260 the constraint doubles
as strong regularisation. This should be the default architecture for the
attribution viewer branch.

**Pairwise and ordinal scaling — the strongest novelty claim available.** SetFit
✅ turns N labels into O(N²) pairs; CORAL / CORN ✅ give rank-consistent ordinal
heads; `choix` ✅ gives Bradley–Terry latent scales from pairwise comparisons with
no text model at all.

💭 The insight: **you cannot get reliable absolute hawkishness labels from an
annotator, but you can reliably get "is A more hawkish than B?"** — and 260
documents yield ~33,000 candidate pairs. We are annotation-budget-starved, not
label-starved. A siamese encoder trained with a Bradley–Terry / RankNet loss gives
a scalar hawk-dove scale identified up to an affine transform, which is fine
because it is standardised before regression. **The survey found no verified paper
applying CORAL/CORN or Bradley–Terry to hawk-dove scaling.** If that holds, it is
our strongest methodological novelty claim — and it is formally identical to an
RLHF reward model, which makes it immediately legible to a modern referee.

**Small samples.** 💭 Reliability ordering at N≈260: (1) frozen strong embedding
model plus a ridge head — near-zero cost, hard to beat, the right baseline;
(2) SetFit at sentence level; (3) LoRA on the last few layers; (4) full
fine-tuning, **which should be reported with multi-seed error bars or not at
all.** At this sample size seed-to-seed spread will routinely exceed the gap
between methods — **a single-seed results table is the most likely single thing to
sink the project.**

**Omission.** No verified literature on omission detection for central bank text.
💭 Three implementable designs, in order of what to build first: (a) **standing-
language deletion tracking** — a `difflib` detector of phrases deleted relative to
the previous statement, weighted by how long the phrase had persisted; this is
exactly what practitioners watch ("considerable time", "patient"), costs a few
dozen lines, and is the highest expected-signal-per-line item in this document;
(b) predicted-minus-actual topic coverage, treating large negative residuals as
salient omissions; (c) **Tealbook Book B alternative draft statements** — the gold
standard, because the Fed itself wrote the counterfactual (Doh, Song & Yang, KC
Fed RWP 20-14 ✅).

**Label-free scaling.** The Wordfish / Wordscores lineage ❔ is the natural
baseline. 💭 Run it: either the first dimension correlates with hawkishness (free
validation) or it recovers crisis regime and time instead (clean motivation for
why supervised comparison-based scaling is necessary). Either outcome is a
paragraph worth writing.

---

## 6. Twelve under-explored angles

💭 **All of this section is judgement, not a finding.** The unifying observation:
the LLM-annotation race is crowded and we would not win it, but **almost nobody is
connecting the text side to the information-effect side**, and that junction is
wide open.

1. **Predict the sign flip from the text itself.** *The strongest idea here.* Use
   Jarociński–Karadi or Bauer–Swanson to label each historical event "monetary
   shock" or "information shock" — free, published, event-level. Then ask whether
   the statement text, known before the market reacts, **predicts which regime the
   event falls into.** This inverts the literature, which treats the information
   effect as a nuisance to be identified out of *market* data. New object, and
   directly tradable.
2. **Topic-conditional hawkishness mapped onto the yield-curve decomposition.**
   The sharpest available test, with a falsifiable structural prediction:
   hawkish-on-inflation should raise the 2-year and flatten 10y−2y;
   hawkish-on-growth should raise both. A single scalar destroys exactly this.
   Free data throughout.
3. **Novelty → volatility, tone → direction, as a two-equation system.** Extends
   rather than duplicates the FRL 2026 result. Also the best fallback if the
   direction result is null — and a null direction result is *expected*.
4. **Hindsight contamination measured on central bank text** (see §7).
5. **Committee dispersion from inter-meeting speeches.** Peskoff et al. showed the
   statement strips out the dissent visible in transcripts. Untested next step:
   score the dispersion of individual members' speeches between meetings, and test
   whether it predicts the dissent count at the next meeting and the size of the
   yield reaction. Speeches also solve the sample-size problem.
6. **Text → term premium versus expected path.** Which *words* move which
   component. ACM and Kim–Wright are free and daily; nobody found doing this at
   the level of text content rather than aggregate tone.
7. **Standing-language deletion as an event.** Highest signal-to-effort ratio in
   the whole list.
8. **Statement versus press conference as separate text channels.** Two verified
   findings collide productively — Cieslak–Schrimpf find non-monetary news
   dominates the press-conference window; Acosta et al. find press conferences are
   now the main source of policy news. So: **the prepared statement carries the
   policy signal and the press conference carries the information signal.**
   Directly testable, because the USMPD separates the windows. If true, it
   explains the sign flip in text terms, which nobody has done.
9. **Conviction-weighted hawkishness** — a hedged hawkish statement should move
   yields less than an unhedged one. Cleaner and more novel than tone or
   uncertainty alone; needs only free tools.
10. **Pairwise Bradley–Terry scaling** (§5).
11. **Cross-country transfer degradation as the measurement** — not "does it
    transfer" but *how much does it degrade and along which axis*. And separately:
    does the **tone→yield coefficient** transfer even where the tone *scale* does
    not? The second question is the interesting one, and nobody was found asking
    it.
12. **Power analysis and pre-registration as a first-class contribution.**

💭 **Two things to avoid:** another LLM-versus-FinBERT annotation benchmark
(crowded, and CB-LMs suggests a domain encoder would win anyway), and a
multi-agent FOMC simulator (four groups are already there, and the accuracy
figures being reported are almost certainly contaminated).

---

## 7. Hindsight contamination

- **Glasserman & Lin**, arXiv:2309.17322 ✅ — separates **two** channels: look-ahead
  bias (the model knows the returns that followed) and a **distraction effect**
  (general knowledge of the named entity interferes with measurement). Headline:
  **anonymised headlines outperform**, implying the distraction effect dominates —
  the opposite of the naive intuition.
- **Sarkar & Vafa**, SSRN 4754678 / ICML 2025 ✅ — tests keyed on events genuinely
  unpredictable given a prespecified information set.
- **Crane, Karra & Soto**, Fed Board FEDS 2025-044 ✅ — LLMs recall recent macro
  data precisely and degrade going back, with two systematic errors: smoothing
  across vintages (mixing first prints with revisions) and within vintages (mixing
  past and future reference periods). **On any given day the model believes it
  holds data not yet released.** 💭 The most directly damaging finding for us,
  because it contaminates any LLM-based reconstruction of what the FOMC knew at
  time *t*.
- 🔎 **Unverified, and the 2026 arXiv identifiers are the least trustworthy items
  in this document:** a "Lookahead Propensity" test (arXiv:2512.23847);
  "DatedGPT", twelve 1.3B models with explicit annual cutoffs 2013-2024
  (arXiv:2603.11838); "Look-Ahead-Bench" (arXiv:2601.13770); "MemGuard-Alpha"
  (arXiv:2603.26797, whose reported effect size is extreme enough to demand
  reading before citing).

💭 **An unresolved disagreement worth knowing:** Glasserman–Lin (anonymisation
helps, so look-ahead bias is second-order) points in a different direction from
the purpose-built-clean-model framing (bias is large enough to need dated models).
Resolving that tension is probably the most interesting contribution available in
this corner.

💭 **A protocol we can assemble now:**

- **(a)** date / entity / number masking as an ablation, reporting the change in
  the tone→yield coefficient as a *measured quantity*, not a robustness footnote;
- **(b)** an early-cutoff open-weights model (Pythia, or the original RoBERTa) as
  a clean comparison arm;
- **(c)** **forward time-stamped forecasts starting immediately** — the only fully
  airtight answer to contamination, and it costs nothing. Every meeting from today
  onward is genuinely clean out-of-sample, and it cannot be done retroactively;
- **(d)** report fine-tuned-encoder results separately from any frontier-LLM
  results, since a model fine-tuned on our labels has a much smaller contamination
  surface than a zero-shot frontier model.

💭 Also worth chasing: **`infini-gram`**, a public n-gram index over open training
corpora, which would let us check directly whether specific FOMC sentences are in
a model's training data — no GPU required.

---

## 8. Build this first

💭 Three things, roughly a day's work, that give a working baseline and the three
features most likely to survive, before any modelling investment:

1. `FOMC-RoBERTa` sentence scores;
2. standing-language deletion diffs;
3. cosine novelty against the previous statement.
