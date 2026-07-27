# LEARNING PROMPTS

Prompts the owner pastes into a *separate* chat to learn the domain in parallel
with the build. They are deliberately self-contained: that chat has no access to
this repository.

Each prompt is written to produce an explanation, not code. Suggested order is
top to bottom; each batch is roughly one sitting.

Convention for every prompt in this file — append if the answer comes back too
shallow or too academic:

> Explain at the level of someone who is technically strong but new to fixed
> income. Use concrete numbers and a real historical episode. Tell me what is
> contested rather than presenting one view as settled. End with the three
> things practitioners get wrong about this.

---

## Batch 1 — the instrument and the market

**1.1 What a bond yield actually is.**
> Explain the relationship between a bond's price, its coupon, and its yield to
> maturity. Why do price and yield move in opposite directions, and what is
> duration? Show the arithmetic on a 10-year Treasury when the yield moves from
> 4.00% to 4.25%.

**1.2 The yield curve as a set of expectations.**
> Decompose a 10-year government bond yield into: expected average short rate,
> expected inflation, and term premium. Explain the expectations hypothesis and
> why it fails empirically. What does the 2y-10y slope tell you that the 10y
> level does not?

**1.3 What the central bank actually controls.**
> A central bank sets an overnight rate. Explain the transmission chain from
> that overnight rate to a 10-year yield, and why the long end can move in the
> *opposite* direction to a policy decision.

**1.4 Real vs nominal.**
> Explain TIPS, breakeven inflation, and how to separate a move in nominal
> yields into a real-rate move and an inflation-expectations move. When is a
> hawkish central bank bullish for real rates but bearish for breakevens?

## Batch 2 — how policy communication moves markets

**2.1 Why the decision itself is usually a non-event.**
> Explain why financial markets barely react to a central bank decision that was
> fully expected, and what "priced in" means mechanically. How do fed funds
> futures and OIS encode the market's expected policy path?

**2.2 Monetary policy surprises and high-frequency identification.**
> Explain the Kuttner (2001) and Gürkaynak–Sack–Swanson (2005) approach to
> measuring monetary policy shocks in a 30-minute window around an announcement.
> What are the "target factor" and the "path factor"? Why is a narrow window
> essential for identification?

**2.3 Forward guidance.**
> What is forward guidance, what forms has it taken (calendar-based,
> state-contingent, qualitative), and why does the exact wording matter enough
> that markets move on a single word change?

**2.4 The information effect — why the sign can flip.**
> Explain Nakamura–Steinsson (2018) and the "Fed information effect": why a
> hawkish surprise can *lower* long yields or *raise* equities. How do
> researchers try to separate a pure policy shock from a central-bank
> information shock? Is the information effect now considered real or an
> artefact (Bauer–Swanson 2023)?

**2.5 Hawkish and dovish, precisely.**
> Define hawkish and dovish beyond "tightening/easing". Give ten real sentences
> from Fed statements and classify each, explaining what specifically makes it
> hawkish or dovish. Include at least two that are genuinely ambiguous and say
> why.

## Batch 3 — event studies and the statistics of small samples

**3.1 The event study.**
> Explain the event-study methodology in finance: event window, estimation
> window, abnormal returns, and how significance is tested. What breaks when
> events are clustered in time or overlap?

**3.2 Statistical power with ~250 events.**
> I have roughly 250 monetary policy meetings and want to know whether a text
> signal predicts the next-day change in the 10-year yield. Walk me through a
> power calculation: given the daily volatility of yield changes, what effect
> size could I actually detect at 250 observations? What R² would be plausible
> versus suspicious?

**3.3 How financial ML studies fool themselves.**
> Explain look-ahead bias, survivorship bias, data snooping, and multiple-testing
> in the context of financial prediction. What is walk-forward validation and
> why is a random train/test split invalid for time series? Include the
> "backtest overfitting" argument (Bailey, López de Prado).

**3.4 Economic vs statistical significance.**
> A model predicts the direction of daily 10-year yield moves with 54% accuracy.
> Explain how to judge whether that is worth anything: transaction costs,
> bid-ask on Treasuries, Sharpe ratio, and the confidence interval on 54% given
> 250 observations.

## Batch 4 — NLP for this task

**4.1 Encoder models, plainly.**
> Explain what BERT-style encoder models do, what a fine-tuned classification
> head is, and how this differs from a generative LLM. Why do encoders remain
> the default for sentence classification on small labelled datasets?

**4.2 The candidate models for this project.**
> Compare FinBERT (Araci), FOMC-RoBERTa (Trillion Dollar Words, ACL 2023),
> BART-large-MNLI zero-shot, and a general LLM prompted with instructions, for
> the specific task of classifying central-bank sentences as hawkish/dovish/
> neutral. What was each actually trained on, and where does each break?

**4.3 Why zero-shot on historical text is not prediction.**
> Explain why using a modern LLM to score a 2013 Fed statement and then
> "predicting" the 2013 market reaction is contaminated. What is the pretraining
> cutoff argument, and what designs avoid the problem?

**4.4 Small-data strategy.**
> I have ~2,500 human-labelled sentences and ~40,000 unlabelled ones. Compare:
> full fine-tuning, frozen embeddings plus a linear head, LoRA, and
> LLM-labelling followed by distillation. Which wins at this data scale and why?

**4.5 Dictionaries before neural networks.**
> Explain the Loughran–McDonald financial lexicon and the Apel–Blix Grimaldi
> hawkish/dovish dictionary. Why do dictionary methods remain competitive on
> central-bank text, and what exactly does a transformer add?

## Batch 5 — comparing central banks

**5.1 Communication regimes.**
> Compare how the Fed, ECB, Bank of Japan, Bank of England, Bank of Russia, and
> Banco Central do Brasil communicate policy: what documents they publish, on
> what schedule, and how binding the language is. What is the closest equivalent
> of an "FOMC statement" at each?

**5.2 Global spillovers.**
> Explain the "global financial cycle" (Rey) and how US monetary policy
> transmits to other countries' bond markets. Why do emerging-market local-currency
> yields react more than German Bunds? What happened in the 2013 taper tantrum?

**5.3 Cross-language sentiment.**
> If I want one hawk/dove score comparable across statements written in English,
> Russian, Japanese, and Portuguese, what are my options and what does each get
> wrong? Cover multilingual encoders, machine translation, and official
> translations published by the banks themselves.

---

## Batches to come

Written on request as the project reaches each area:

- term-premium models (ACM, Kim–Wright) and what they can and cannot say;
- QE/QT announcements and balance-sheet communication;
- interpretability for text models (attributions, probing, counterfactuals);
- how a finance/economics paper is structured and what referees attack.
