# DATA SOURCES

Everything the project can be built from, with an honest verification status on
each entry. Compiled 2026-07-27.

**Verification legend**

| Mark | Meaning |
|---|---|
| ✅ | fetched and read directly during this research |
| 🔎 | returned by a search index with a matching page title — strong, but not a live fetch |
| ❔ | prior knowledge or a lead only; **must be checked before anyone writes a scraper against it** |

A ❔ is not a fact. Several of the sites below were redesigned in the last few
years, which is exactly when remembered URLs rot silently.

---

## 1. Ready-made text corpora — the highest-value finds

### 1.1 World Central Banks (WCB) ✅ — cloned and counted

`gtfintechlab/WorldCentralBanks` — <https://github.com/gtfintechlab/WorldCentralBanks>

The single most useful dataset found. Figures below are counted from the actual
files, not taken from the README.

- **25 central banks**, **343,209 sentences** in `sanitized_data/` (the "380k"
  figure is the advertised one), plus 487 MB of original PDFs in `raw_data/` and
  76 MB of cleaned markdown.
- **25,000 sentences expert-annotated**, 1,000 per bank, on **three axes**:
  stance (hawkish / dovish / neutral / **irrelevant**), temporal (forward-looking
  or not), and certainty (certain / uncertain).
- HuggingFace: `gtfintechlab/WCB_380k_sentences`,
  `gtfintechlab/all_annotated_sentences_25000`, per-bank datasets, and per-bank
  models `gtfintechlab/model_<bank>_stance_label`. **The paper's headline finding
  is that the aggregated cross-bank model beats the per-bank ones** — so prefer
  `model_WCB_stance_label`.
- Licence **CC-BY-NC-SA 4.0** — non-commercial, share-alike.
- Paper: Shah, Sukhani, Pardawala et al. (2025), "Words That Unite The World".

**Two gotchas, both confirmed by inspection:**

1. `final_data/` and `master_file_metadata.xlsx` are **Git LFS pointers**. A plain
   `git clone` silently yields 131-byte stubs. Use `git lfs pull`, or take the
   data from HuggingFace instead.
2. **Per-bank coverage is very uneven, and it bites our 2000-onward window.**
   Verified ranges: Bank of England 1997-2024 (69,171 sentences), **Fed 1996-2024
   (46,823)**, Bank of Japan 2006-2024, RBA 2006-2024, **ECB only 2015-2024**
   (21,044), **Bank of Canada only 2011-2024 (1,499)**. The ECB gap has to be
   topped up from the ECB's own speech download (§1.3) if the euro area is to
   reach back to 2000.

**Why it matters here:** the multi-country branch stops being a scraping project
and becomes a modelling project. And the three annotation axes match the project's
own intention to separate *direction* from *conviction* — that separation is now
available as supervised labels rather than something we would have to invent.

### 1.2 Trillion Dollar Words / FOMC ✅ — cloned and counted

`gtfintechlab/fomc-hawkish-dovish` — <https://github.com/gtfintechlab/fomc-hawkish-dovish>

The US-specific benchmark to beat. Also smaller than its publicity suggests.

- **2,379 labelled sentences** (train + test, seed 5768), 1996-2022. Balance:
  neutral 1,154 / dovish 625 / hawkish 600. A sub-sentence "split" variant has
  2,480.
- Splits provided for three seeds (5768, 78516, 944601) — use them, for
  comparability with the paper.
- Raw index: 1,034 speeches, 214 minutes events, 63 press conferences. The repo
  has been extended past the paper (`master_mm_final_Oct_2024.xlsx`).
- Model `gtfintechlab/FOMC-RoBERTa` (RoBERTa-large, 3-class).
- **Label mapping is counter-intuitive and easy to get backwards:
  `LABEL_0 = Dovish`, `LABEL_1 = Hawkish`, `LABEL_2 = Neutral`.**
- **Trap:** `data/annotated_data/*.xlsx` have their `label` column filled with
  `-`. The real labels live only in `training_data/test-and-training/`.
- Paper: <https://aclanthology.org/2023.acl-long.368/> (ACL 2023). CC BY-NC 4.0.

### 1.3 Speech corpora — broader than expected

| Item | What | Status |
|---|---|---|
| **CBS Dataset** (Romelli et al.) <br> <https://github.com/DRomelli/cbspeeches> | **35,487 speeches, 131 central banks, 1986-2023**, CSV. Built as BIS (18,045) **plus** scraping 143 bank websites (15,435) plus archive work (2,007) — roughly double BIS alone. 5,347 machine-translated from non-English. Free for academic/non-commercial use; cite Campiglio, Deyris, Romelli & Scalisi (2025), *EER* | ✅ repo loaded |
| **BIS bulk download** <br> <https://www.bis.org/cbspeeches/download.htm> | single ZIP, ~120 MB, full text of speeches since 1996. Programmatic access via BIS's own `gingado` library | 🔎 — size and date need a 30-second check |
| **ECB key speeches bulk CSV** <br> <https://www.ecb.europa.eu/press/key/html/downloads.en.html> | official ECB bulk download — **the natural fix for WCB's 2015 ECB start** | 🔎 |
| `sophia-jihye/bis_speeches_text_dataset` | **already-scraped text**, not just code: 1997 – Sept 2019, MIT licence | ✅ |
| `HanssonMagnus/scrape_bis` | Python, downloads and organises BIS PDFs, converts to text + JSON; ~8% of PDFs fail conversion; GPL-3.0 | ✅ |

Verdict: for raw multi-country breadth over 2000-today, **CBS Dataset first, BIS
bulk second** — not twenty individual scrapers.

### 1.4 Other corpora, scrapers and dictionaries

| Item | What | Status |
|---|---|---|
| `vtasca/fed-statement-scraping` | maintained scraper for FOMC statements + minutes, updated 2026-07-26 | ✅ |
| `souljourner/FOMC-Statements-Minutes-Scraper` | alternative implementation | 🔎 |
| `palewire/fed-dot-plot-scraper` | extracts the **individual dots**, not just medians; actively maintained | ✅ |
| **Loughran–McDonald master dictionary** | 86,486 words × 19 columns. Negative 2,355 · Positive 354 · Uncertainty 297 · Litigious 904 · Constraining 184. Obtainable without the website: `pip install pysentiment2`, then `pysentiment2/static/LM.csv` | ✅ |
| FRASER (St. Louis Fed) | historical Fed archive, OAI-PMH | 🔎 — for pre-2000 if the period widens |
| "Op-Fed" | claimed FOMC transcript stance annotations | ❌ **could not be confirmed to exist.** Surfaced in one search result and found nowhere else. Treat as unverified until seen firsthand |

**Not investigated** — the search budget ran out. Each needs checking and none
should be assumed available: Hansen–McMahon–Prat FOMC deliberation data (QJE
2018); Correa et al. financial-stability sentiment dictionary; the Picault–Renault
ECB lexicon; the **Apel–Blix Grimaldi hawk/dove dictionary** (important — it is
the closest thing to a purpose-built lexicon for this exact task);
Gorodnichenko–Pham–Talavera voice-of-monetary-policy tone data (AER 2023);
Ehrmann–Fratzscher and Bennani–Neuenkirch datasets; FOMC verbatim transcript
archives; multi-country corpora on Dataverse / Zenodo / openICPSR.

---

## 2. Monetary policy surprise series — free, and the project's best validation target

These already contain the 30-minute windows that would otherwise cost money.

| Source | What it gives | Status |
|---|---|---|
| **FRBSF U.S. Monetary Policy Event-Study Database** <br> <https://www.frbsf.org/research-and-insights/data-and-indicators/us-monetary-policy-event-study-database/> | data to compute **Gürkaynak–Sack–Swanson (2005)** target/path factors, plus R code implementing **Acosta et al. (2025)** | 🔎 |
| **FRBSF Monetary Policy Surprises (Bauer–Swanson)** <br> <https://www.frbsf.org/research-and-insights/data-and-indicators/monetary-policy-surprises/> | raw surprises (first PC of 30-min changes in money-market futures) **and** surprises orthogonalised against pre-announcement public information | 🔎 |
| **EA-MPD** (Altavilla et al. 2019) <br> <https://www.ecb.europa.eu/pub/pdf/annex/Dataset_EA-MPD.xlsx> | intraday euro-area asset price changes for the full history of Governing Council announcements, with a **30-min policy-release window and a separate 90-min press-conference window** | 🔎 |
| IMF WP 2024/224, "A New Dataset of High-Frequency Monetary Policy Shocks" | multi-country shocks | 🔎 — replication data location unconfirmed |
| Jarociński–Karadi | sign-restriction split into pure-policy vs central-bank-information shocks | 🔎 — no official landing page found; check the AEJ:Macro replication package on openICPSR |
| Nakamura–Steinsson | the original information-effect shocks | ❔ — not reached |

**Judgement call to carry forward:** use the **orthogonalised** Bauer–Swanson
series, not the raw one. The raw surprise absorbs the same predictable-information
component that a text score also picks up, so regressing one on the other
overstates the text's contribution.

The EA-MPD's split between the press release and the press conference is exactly
the decomposition a text model wants to be validated against — statement text
versus Q&A.

---

## 3. Market data — free

| Source | What | Status |
|---|---|---|
| FRED `DGS1MO…DGS30`, `DFII10`, `T10YIE`, `T10Y2Y` | daily constant-maturity Treasury yields, real yields, breakevens, slope | ❔ series IDs from prior knowledge, not re-verified this session |
| **ALFRED** via `mortada/fredapi` | **vintage** (as-of-date) macro data | ✅ repo verified, 1,642★, active. Essential: without vintages, macro controls leak revised data into a real-time study |
| Fed GSW zero-coupon yield curve | the research-standard daily curve | ❔ |
| NY Fed ACM term premium | daily decomposition into expectations and term premium | ❔ |
| `USTREASURY/YIELD` on Nasdaq Data Link | Treasury par curve, free | 🔎 daily only |
| **BIS CBPOL** <br> <https://www.bis.org/statistics/cbpol.htm> | policy rates for **40+ economies**, daily and monthly, most daily series post-1980, several back to 1946, weekly updates, single CSV | 🔎 — the best free consolidated policy-rate source |
| DBnomics | one free keyless API aggregating BIS / ECB / IMF / Eurostat / OECD | 🔎 — would collapse much of the ingestion work; clients verified (`dbnomics/rdbnomics` and a Python client) |
| SDMX tooling | `pandaSDMX`, `ondata/opensdmx`, `rsdmx` | ✅ repos verified — the practical toolchain for ECB/OECD/BIS endpoints |

## 4. Expectations data — free

| Source | What | Status |
|---|---|---|
| FRED `FEDTARMD` | FOMC SEP median fed funds projection, as a proper FRED release | 🔎 — cleanest machine-readable route to dot-plot medians |
| `palewire/fed-dot-plot-scraper` | the individual dots | ✅ repo verified |
| **ECB Survey of Professional Forecasters** <br> <https://www.ecb.europa.eu/stats/ecb_surveys/survey_of_professional_forecasters/html/all_data.en.html> | **full anonymised individual-forecaster microdata**, quarterly, since 1999Q1 | 🔎 — individual-level dispersion is a genuine measure of disagreement, not just a mean |
| Philadelphia Fed SPF | the US analogue, free microdata | 🔎 |

---

## 5. Intraday data — what it costs

The question was whether to buy 30-minute (or finer) data on Treasury futures back
to 2000, for ~210 FOMC windows.

| Vendor | Verdict | Status |
|---|---|---|
| **Barchart Premier** — $29.95/month | **Cheapest legitimate route.** 1-minute history **10 years back only**; 250 downloads/day; **max 10,000 records per request** (≈7 trading days of 1-min ZN per download). One month's subscription is enough to pull a decade of windows, then cancel. Check redistribution terms before publishing any of it. | 🔎 price confirmed via Barchart's own help page |
| **Tick Data (tickdata.com)** | Per-symbol-year pricing with custom date ranges, so a narrow slice is orderable — but there is a **$1,000 minimum order for new clients** ($500 returning), which a single ZN symbol-year would not reach. Critically: **quote (BBO) data for futures begins 4 Jan 2010**; before that, trades only. No published academic discount found. Actual per-symbol-year price is behind a JS store page and was not readable. | 🔎 |
| **Barchart OnDemand API** | Usage-based, **no published rate card**. A "$500/month" figure circulating on aggregator sites failed corroboration — do not plan on it. CME/CBOT exchange fees are billed on top. | 🔎 |
| **Nasdaq Data Link CHRIS** (free CME continuous futures) | **Dead.** CME data vanished from Quandl in Dec 2018; the database was flagged deprecated by 2024. Do not build on it. | ✅ confirmed via multiple maintained-repo issue threads |
| **Databento** (CME Globex MDP3) | Arbitrary start/end timestamps, genuinely byte-metered. History starts **2010-06-06** — it cannot reach 2000. **$125 new-account credit.** ~210 two-hour windows at `ohlcv-1m` is ~1 MB, at `ohlcv-1s` ~50 MB — small enough to fall inside the free credit at any plausible per-GB rate. Effectively free for the post-2010 half of the sample | 🔎 coverage start verified; per-GB rate not verified |
| FirstRate Data, Portara, CSI, Norgate, Polygon, IQFeed | the other vendors practitioners name for intraday futures | ❔ — **not priced this session** |
| Dukascopy | free tick data, but **bond CFDs, not exchange futures** — broker quotes, no exchange volume, unadjusted rollover jumps, broker session hours. Reconstructing yields from these is a material compromise | ❔ — not researched |
| LSEG Tick History, Bloomberg, WRDS | institutional; a university affiliation is the realistic route | ❔ |

**Recommendation: do not buy anything.** Two independent reasons.

1. The free surprise series in §2 already contain 30-minute windows around FOMC
   statement *and* minutes releases, including 2-year and 10-year Treasury yields,
   TIPS, money-market futures, OIS, equities and the dollar — computed by the
   people who defined the method, over a longer sample than 2000-2026.
2. **Buying early-sample data would make it worse, not better.** In 2000-2003 the
   liquid Treasury market was the CBOT pit, not Globex. Electronic ZN/ZB volume
   was thin, so a paid Globex feed yields noisy or empty 30-minute windows in
   precisely the years we would be paying to obtain. The published series handle
   this with pit time-and-sales and fed funds / Eurodollar futures.

If a specific gap survives all of that, the proportionate purchases are Databento
for post-2010 (likely $0 against the signup credit) or one month of Barchart
Premier at $29.95 — not a full-history subscription.

---

## 6. Per-bank source map

**Read the caveat.** This environment's egress policy blocked direct access to
every central bank domain, so **no HTTP status code, `robots.txt`, or render mode
below was tested**. Entries marked 🔎 appeared as real indexed pages with matching
titles; entries marked ❔ are recall and must be `curl -I`'d before any scraper is
written against them. A 403 seen in this session tells us nothing about a bank's
own bot protection — it was our proxy, not them.

### 6.1 Build-order summary

| Bank | URLs constructible from a date? | Statement text in the HTML body? | Archive depth | Doc RSS | Data API |
|---|---|---|---|---|---|
| **Fed** | **yes** — `monetary{YYYYMMDD}a.htm` | yes | statements 1994, historical to 1936 | yes 🔎 | FRED, DDP |
| **RBA** | yes (dates), release numbers need the index | yes | releases 1990, SMP 1997 | yes ❔ | CSV only |
| **BoC** | yes — `fad-press-release-{date}` | yes | MPR 1995 | WordPress feeds ❔ | **Valet**, free, no key |
| **ECB** | **no** — every post-2016 filename carries an unpredictable `~{hash}` | yes | **press conferences from June 1998** 🔎 | yes 🔎 | SDMX, free 🔎 |
| **BoE** | no — media slugs | summary yes, rest PDF | Inflation Report 1993, MPC 1997 | hub 🔎 | IADB CSV |
| **SNB** | partly — `pre_{YYYYMMDD}` | partial | ~1997 | unclear | data.snb.ch ❔ |
| **BoJ** | mostly — `k{YYMMDD}a.pdf` | **no — PDF only** | 1998 | unclear | CSV only |
| **CBRT** | yes — `ANO{YYYY}-{NN}` | yes (HTML since 2014) | HTML ≥2014, PDF before | no | EVDS 3 |

**Recommended build order:** Fed and RBA first (constructible URLs, text in the
body), then BoC, then ECB (deep and rich, but you must crawl listings to discover
the hashes), then BoE and SNB, and BoJ last, where every document is a PDF.

### 6.2 Per-bank notes

**US Federal Reserve** — the easiest target here. Calendar
`federalreserve.gov/monetarypolicy/fomccalendars.htm` 🔎 carries per-year links
back to the 1990s; statements follow `/newsevents/pressreleases/monetary{YYYYMMDD}a.htm`
🔎, which means URLs can be constructed from the meeting calendar alone. Per-year
static pages `/newsevents/pressreleases/{YYYY}-press.htm` 🔎. RSS hub
`/feeds/feeds.htm` 🔎, monetary policy feed `/feeds/press_monetary.xml` 🔎.
Also available: minutes, SEP (from Oct 2007), Beige Book (1996 on site, 1970 via
the Minneapolis Fed archive), press-conference transcripts (PDF, from April 2011),
verbatim transcripts on a five-year lag (PDF, from 1976), speeches from ~1996.
The filtered `/newsevents/pressreleases.htm` UI is JS-driven — use the per-year
pages instead.

**ECB** — deepest archive of the set: press conferences with the full Q&A in the
page body back to **9 June 1998** 🔎. Accounts of the Governing Council from
January 2015. The blocking problem is that every post-2016 filename contains an
unpredictable hash (`ecb.is260723~b6fadd48f4.en.html`), so URLs must be discovered
from listings, and the listings lazy-load on scroll. The known workaround is the
`index_include.en.html` fragment, which returns a whole year as static HTML ❔ —
test this first; if it works, the ECB becomes easy. RSS `/rss/press.xml` 🔎.
Statistics via SDMX at `data-api.ecb.europa.eu/service` 🔎.

**Bank of England** — the Monetary Policy Summary is published *bundled with the
minutes* in one document, a format dating from August 2015. Listing pages support
`?InfiniteScrolling=False&Page=N` to force server-rendered pagination ❔ — the key
trick. Bank Rate history as CSV via the IADB endpoint. PDF-heavy.

**Bank of Japan** — the defining obstacle: **statement text is not in the HTML,
only in the PDF**. Summary of Opinions exists only from January 2016. The
2022-23 site redesign moved URLs from `/en/announcements/release_{YYYY}/` to
`/en/mopo/` ❔. Press-conference translations lag by days.

**Swiss National Bank** — **publishes no minutes at all**, and only four decisions
a year. Structurally the thinnest member of the set. The 2023-24 redesign broke
the old `/mmr/reference/` URLs. `data.snb.ch` has a clean REST/CSV API ❔.

**Bank of Canada** — best API access of any bank here: the **Valet API**
(`bankofcanada.ca/valet/docs`, free, no key) ❔. The site runs WordPress, so
`/wp-json/wp/v2/posts` and `/feed/` are likely open — if so, that is the cleanest
document-listing route of any bank in this table. Test it early. Summary of
Deliberations only from February 2023.

**Reserve Bank of Australia** — after the Fed, the most scraper-friendly: clean
date-based `.html` URLs, per-year static index pages, full text in the body for
statements, minutes, the Statement on Monetary Policy and speeches. Minutes from
December 2007. Note the 2024 governance reform renamed the decision-maker to the
Monetary Policy Board and cut meetings to eight a year — titles and structure
change around February 2024.

**CBRT (Turkey)** — researched in unusual depth, and instructive about what these
sites are like. Runs IBM WebSphere Portal; **server-rendered, no JS** 🔎 (a
published R scraper parses the listings with plain `rvest`). Decisions and MPC
summaries are ordinary numbered press releases at
`/wps/wcm/connect/EN/TCMB+EN/Main+Menu/Announcements/Press+Releases/{YYYY}/ANO{YYYY}-{NN}`
🔎, and the year is a path segment. Two traps: **all press release types share one
sequential counter per year** — rate decisions, MPC summaries, crypto
announcements and research-award notices all draw from the same `ANO` sequence, so
document type must be filtered from the title, not the number; and **PDF URLs
contain a random cache token and can never be constructed** — always scrape the
anchor. HTML from 2014, PDF before. Statistics moved from EVDS 2 to **EVDS 3** in
January 2026; the old `evds2.tcmb.gov.tr/service/evds/` endpoint now returns an
HTML shell and the legacy Python packages are broken 🔎.

**Not researched at all:** Bank of Russia, Banco Central do Brasil, Reserve Bank
of India, Banco de México, People's Bank of China, Riksbank, Norges Bank, Bank of
Korea, Bank Indonesia, NBP, CNB, SARB. Leads only, all ❔: Banxico uses GUID-laden
URLs; BCB is an Angular SPA over `/api/servico/sitebcb/`; RBI uses `__VIEWSTATE`
postback listings; PBoC's English Monetary Policy Report lags badly; Norges Bank
publishes a "Committee's assessment" rather than minutes; SARB publishes no
minutes; CNB is likely the cleanest of the group.

**Shortcut that may make much of this moot:** the WCB corpus (§1.1) already
contains cleaned text for 25 banks with per-meeting metadata including release
dates and source links. For several banks, collecting from scratch may be
redundant work.

### 6.3 BIS as a cross-bank shortcut 🔎

The BIS *Central bankers' speeches* collection carries speeches and press
conference remarks from most of these banks **in clean English text** with stable
IDs — far more scrapable than the individual bank sites, and it solves the
translation problem for free. Pre-scraped corpora exist on GitHub
(`sophia-jihye/Speech_Transcripts_of_Central_Bankers`,
`fagan2888/bis_speeches_text_dataset`, `HanssonMagnus/bis-scraper`). For the
speech branch this is almost certainly the right source rather than 20 scrapers.
