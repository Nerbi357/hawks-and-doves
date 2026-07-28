# INTERACTIVE BRIEF

A self-contained statement of what the interactive layer of this project has to
be. Written to be **pasted into a chat that has never seen this repository**, and
to serve as the standing brief for any session that builds part of it.

Two ways to use it: paste it and add *"design this"* to get proposals, or paste it
and add *"attack this — what will make it boring, wrong, or unfinished?"* to get a
critique. The critique mode is usually more valuable once ideas already exist.

---

## The prompt

> I am building an open research project called **hawks-and-doves**. It measures
> how hawkish (tightening) or dovish (easing) central bank statements are, using
> NLP, and studies whether that tone carries information about where government
> bond yields go next. It starts with the US Federal Reserve and extends to other
> central banks. Only free and open data. Built in versions, like successive
> revisions of a paper.
>
> The research half is under control. What I need designed is the **interactive
> layer** — and I care about it as much as about the results.
>
> **The bar.** It has to read as a **finished product**, not as the output of a
> notebook. Someone who lands on it knowing nothing about me should understand
> within twenty seconds what it shows, and immediately want to touch something. It
> should be the kind of thing a person sends to a friend, not the kind of thing
> they politely close. If it looks like a dashboard someone built for themselves,
> it has failed.
>
> **It has to work for four audiences at the same time**, and I will not trade one
> away for another:
>
> - a **professor or referee**, who must see that the method is sound and the
>   claims are careful;
> - a **hiring manager**, who has ninety seconds and wants evidence of real skill;
> - a **colleague**, who wants to argue with a specific choice I made;
> - a **curious stranger with no economics**, who should still leave having
>   learned one true, surprising thing.
>
> **It has to be genuinely interesting, not merely informative.** The test I apply:
> does a person *do* something, get a result they did not expect, and want another
> go? A chart that is merely correct fails this test. Interaction that changes what
> someone believes passes it.
>
> **Honesty is a hard constraint, not a disclaimer.** The model's output is a
> measurement of language, not a forecast of markets. Anything that lets a visitor
> type text and receive a number must make it unmistakable that this shows *what
> the model learned*, never *what will happen*. The sample is small — roughly 210
> meetings since 2000 — and the honest presentation of that uncertainty should be
> part of the design, not hidden by it. A demo that quietly implies prediction
> would be worse than no demo.
>
> **Constraints.** Free hosting, no paid services. Static-first if possible:
> precompute everything and serve JSON, so there is no server to attack and no bill
> to pay. Some of the labelled data I use is licensed non-commercial with
> share-alike, so redistributing raw text needs checking. It must work on a phone.
> It must not break when a data source changes.
>
> **Ideas I already have**, offered so you can improve, combine, or reject them —
> not as a specification:
>
> - a **sentence attribution viewer**: a real statement with each sentence
>   heat-coloured by how much it drove the score, shown next to what the market
>   actually did that day;
> - **guess the signal**: the visitor reads a real excerpt, guesses hawkish or
>   dovish, then sees the model's answer *and* the actual yield move, with a
>   running score against the model;
> - **how much did the text change?** — a visualisation of how much each statement
>   differs from the previous one across 26 years, with the changed words
>   highlighted, since the changes are where the news actually is;
> - **linguistic infographics** — length, readability, hedging, sentence
>   complexity, vocabulary turnover, tense and modality over time;
> - **counterfactual editing** — swap "patient" for "vigilant" and watch the score
>   move;
> - **be the central banker** — the visitor writes a statement and the model scores
>   it;
> - a **timeline** of tone against the policy rate and the 2-year and 10-year
>   yields, annotated with the episodes that people remember;
> - a **country comparison** once more central banks are in.
>
> **What I want from you.**
>
> 1. Tell me which of the above are actually good, which are worse than they sound,
>    and why. Be blunt; I would rather cut three than ship eight mediocre ones.
> 2. Propose things I have not thought of — including formats that are not a web
>    page, if something else would land better.
> 3. For the strongest ideas, describe what the visitor *does*, what they *see*,
>    and what they *understand afterwards that they did not before*.
> 4. Tell me what the single strongest first thing to build is, and what makes it
>    strong.
> 5. Name the ways this ends up looking amateurish, and what specifically prevents
>    each one.
>
> Do not give me a mock-up or a wireframe. Give me precise descriptions I can argue
> with, ordered by how much they would improve the project.

---

## Notes for whoever holds this brief

- **The success criterion the owner stated**: he must be genuinely proud of it and
  want to keep working on it. A technically correct but boring interactive layer is
  a failed one.
- **Cheapest strong start**: the text-change visualisation and the linguistic
  infographics need only the document corpus — no model, no market data, no
  results. They can therefore be built very early, and they are the natural answer
  when the project needs something visible and satisfying before the research is
  finished.
- **The most defensible piece** is the attribution viewer: it claims nothing and
  shows everything. Build it on an inherently interpretable architecture — a shared
  sentence encoder with learned non-negative aggregation weights — so the
  highlighted contributions are structurally part of the model rather than a
  post-hoc explanation that a reviewer can dispute.
- **The riskiest piece** is "be the central banker": the best hook and the easiest
  to misread as a forecast, and it is adversarially fragile — visitors will type
  nonsense, abuse, and very long inputs. It needs input caps, rate limiting and a
  hostile-input pass before it goes public.
