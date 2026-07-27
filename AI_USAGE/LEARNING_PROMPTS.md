# LEARNING PROMPTS

## What this file is for

The owner studies the subject in a **separate chat that cannot see this
repository**, in parallel with the build. The target is specific: by the end of
the project he should be able to **reconstruct the whole thing himself** — every
conceptual decision, why it was taken, what it rules out, and where it breaks.
Not "understand roughly what the code does": be able to defend it to a referee.

So this is not an introduction to fixed income. It assumes a competent reader with
a working base, and it asks only two kinds of question:

1. **Fundamental** — something the project's validity rests on, which cannot be
   taken on faith.
2. **Hard and technical** — the mechanism behind a step being built, at the depth
   needed to argue with it.

Anything that can be looked up in five minutes is deliberately absent.

**How to use it.** Each batch is tied to a step of the project. Take a batch when
that step starts, not before — the questions are much sharper when there is a real
decision behind them. Prompts are self-contained; that chat knows nothing about
this repository.

**Tail to append to any prompt whose answer comes back too smooth:**

> Tell me what is contested rather than presenting one view as settled. Give the
> strongest argument against the position you just took. Where the evidence is
> weak, say so. Use a real historical episode and real numbers.

---

## Batch A — before the first line of code

*Why these first: they determine whether the project is measuring anything at all.
Getting them wrong is not a bug that shows up in a test.*

**A1 · What is actually being identified.**
> In an event study of a central bank announcement, what is the object being
> identified, and under what assumptions? Walk me through why a 30-minute window
> around the release identifies a monetary policy shock, what exactly the
> identifying assumption is, and what breaks it. Then explain what changes if I
> use a daily window instead — not "it is noisier", but specifically what other
> shocks enter and how they bias the estimate.

**A2 · Why the level of hawkishness is close to meaningless.**
> Explain, mechanically, why the market barely moves when a central bank does
> exactly what was expected. Then explain what "the expectation" is a claim about:
> whose expectation, measured how, over what horizon. Compare fed funds futures,
> OIS, surveys of forecasters, and the previous statement itself as measures of it
> — what each one actually contains, and where each is wrong.

**A3 · The information effect, and whether it survived.**
> Explain Nakamura–Steinsson (2018) on the Fed information effect and why it makes
> the sign of a hawkish shock ambiguous. Then explain Bauer–Swanson (2023), who
> argue the effect is largely an artefact of the Fed responding to public
> information rather than revealing private information. Who is right, what would
> settle it, and what should someone building a text-based measure do about it in
> practice?

**A4 · What a null result would mean here.**
> I have roughly 210 central bank meetings and I want to know whether a text-based
> tone measure predicts bond yield moves. Given the volatility of daily 10-year
> yield changes, do a power calculation: what effect size is detectable at all at
> that sample size? Then tell me what R² would be plausible, what would be
> suspicious, and what I could honestly conclude from a null.

**A5 · Why almost every study like this is wrong.**
> Explain look-ahead bias, data snooping, backtest overfitting and multiple
> testing in the specific setting of predicting asset prices from text. What is
> walk-forward validation and why is a random train/test split invalid here? Cover
> the Bailey–López de Prado argument on the number of trials. Then: what would a
> referee attack first in a paper claiming that central bank tone predicts bond
> yields?

## Batch B — while the corpus and the signals are built

**B1 · Where the signal actually lives in the text.**
> FOMC statements are highly templated — most of the text repeats from meeting to
> meeting. Explain what this implies for measuring tone: why scoring the whole
> document tends to produce a near-constant, and what the alternatives are
> (differencing against the previous statement, sentence-level scoring with
> aggregation, topic-conditional scoring). What does each one lose?

**B2 · The models, and what they were actually trained to do.**
> Compare, for classifying central bank sentences as hawkish or dovish:
> FinBERT (Araci), FOMC-RoBERTa from the Trillion Dollar Words paper (ACL 2023),
> BART-large-MNLI used zero-shot, and a general LLM given instructions. For each:
> what corpus and what objective it was actually trained on, why that does or does
> not match this task, and its specific failure mode. Be concrete about why
> "financial sentiment" and "hawkish/dovish" are different axes.

**B3 · Small labelled samples.**
> I have about 2,400 human-labelled sentences and about 340,000 unlabelled ones.
> Compare full fine-tuning, frozen embeddings with a linear head, LoRA, and
> LLM-labelling followed by distillation into a small model. Which wins at this
> data scale, why, and what does each one overfit to? Include how I would tell
> overfitting from a real result given the sample size.

**B4 · Hindsight contamination.**
> A pretrained language model has seen text written after the events I am
> "predicting". Explain precisely how this contaminates a zero-shot score on a
> 2013 statement used to predict the 2013 market reaction. Is there any clean
> protocol? What would an experiment that *measures the size* of this
> contamination look like?

**B5 · Why dictionaries are still competitive.**
> Explain the Loughran–McDonald financial lexicon and the Apel–Blix Grimaldi
> hawkish/dovish dictionary — how they were built and what they assume. Why do
> dictionary methods remain hard to beat on central bank text specifically, and
> what exactly does a transformer add that word counts cannot?

## Batch C — while the studies are run

**C1 · Decomposing the yield move.**
> Decompose a 10-year yield into expected average short rates and a term premium.
> Explain the ACM and Kim–Wright models: what they assume, why their term premium
> estimates disagree, and how much weight a result should carry if it depends on
> which one I use. Then: what different text content should move which component,
> if the theory is right?

**C2 · Separating policy news from economic news, in practice.**
> Compare the identification strategies for separating a pure monetary policy
> shock from a central bank information shock: Jarociński–Karadi sign
> restrictions on stock co-movement, Bauer–Swanson orthogonalisation against
> pre-announcement information, and Cieslak–Schrimpf's three-way decomposition.
> For each: the intuition, the data it needs, and its weakest assumption.

**C3 · Delphic versus Odyssean.**
> Explain the distinction between Delphic forward guidance (a forecast about the
> economy) and Odyssean forward guidance (a commitment about policy), following
> Campbell et al. and Andrade–Ferroni. Why does the distinction matter for the
> sign of the market reaction? Is the difference visible in the *language* of a
> statement, and how would I test that claim?

**C4 · Statistical versus economic significance.**
> A model predicts the direction of the 10-year yield move on announcement days
> with 54% accuracy over 210 events. Work through whether that is worth anything:
> the confidence interval on 54% at n=210, transaction costs and bid-ask in
> Treasuries and Treasury futures, what Sharpe ratio it implies, and what could
> make an apparently significant result vanish out of sample.

**C5 · Regimes and structural breaks.**
> I want to split 2000–2026 into monetary policy regimes and report results
> separately. Explain the difference between choosing breaks by calendar, by
> policy state, and by a structural break test (Bai–Perron and successors). Why is
> choosing breaks after seeing the results a form of data snooping, and what is
> the defensible procedure?

## Batch D — cross-country

**D1 · Comparability across banks.**
> Compare how the Fed, ECB, Bank of Japan, Bank of England, Bank of Russia and
> Banco Central do Brasil communicate policy — what they publish, on what
> schedule, and how binding the language is. What is the closest functional
> equivalent of an FOMC statement at each, and where does the analogy break down
> badly enough that a single hawk-dove scale stops being comparable?

**D2 · Spillovers.**
> Explain the global financial cycle (Rey) and the channels through which US
> monetary policy transmits to other countries' bond markets. Why do
> emerging-market local-currency yields react more than Bunds? Decompose the EM
> reaction into a rate channel and a risk-premium channel, using the 2013 taper
> tantrum as the worked example.

**D3 · One scale, several languages.**
> I want a hawk-dove score comparable across statements written in English,
> Russian, Japanese and Portuguese. Compare multilingual encoders, machine
> translation into English, and the banks' own official English translations.
> What does each get wrong, and which bias is most dangerous for a *comparative*
> claim rather than a within-country one?

---

## Batches to come

Written when the corresponding step opens:

- interpretability for text models — attributions, probing, counterfactuals, and
  what an attribution does and does not license you to claim;
- QE/QT and balance-sheet communication as a separate signal from rates;
- backtesting discipline and the specific ways event-driven strategies lie;
- how an economics or finance paper is structured, and what referees attack first.
