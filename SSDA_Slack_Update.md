# SSDA Slack update — Task 3 + serving architecture

Slack-ready drafts distilled from `SSDA_Task3_Summary_for_Daniel.md` and
`SSDA_Serving_Architecture_for_Daniel.md`. Formatting is Slack mrkdwn
(`*bold*`, `_italic_`, `` `code` ``, `<url|link text>`) — paste as-is.

Replace `@Daniel` with the real mention and the two `<link>` placeholders with
the doc URLs before posting.

---

## 1. Channel post (main message)

*SSDA update: entry segmentation (Task 3) and getting the corpus onto the site*

Two write-ups are ready. Short version here, detail in thread, full docs linked at the bottom.

*Task 3 — segmentation works; accuracy isn't certified yet.* New public repo `slavesocieties/ssda-nlp-tools`. It runs the entire corpus — 232 volumes, 62,209 pages, 175,917 entries — in about 30 seconds, with no crashes and no AI model involved, so it costs nothing to run. It finds 29/29 records across @Daniel's three manual examples (four formats, two languages) and 8/8 on the older paired examples, `partial` flags included.

Running clean at that scale is not the same as splitting correctly, and I don't want the speed numbers read as accuracy. The one corpus-wide check I have — registers that number their own entries in the margin — agrees only ~36% exactly, ~59% within one entry, on a thin and somewhat noisy slice of pages. Treat it as a rough floor rather than a grade. The part I do trust: the segmenter flags 5–6% of pages as low-confidence and sets them aside instead of emitting entries it isn't sure about.

*Extraction cost, and a correction.* Extracting the whole corpus projects to *~$216*, about $0.003 per image, against the $0.01 target and the $0.05 cap. An earlier draft of mine said *$52*. That was wrong — it used placeholder prices I hadn't checked against what the vendors actually charge. Method and conclusion are unchanged, the number was off by roughly 4x. If $52 reached anyone, please pass on the correction.

*Serving architecture — designed and tested, nothing deployed.* The recommendation is one PostgreSQL database and *no* separate search engine. That's the unconventional half, and the reason is specific to us: the hard part of searching this collection is scribal spelling variants (Gonzalez/Gonzales, Vives/Bibes, Matanzas/Matansas), we already have matching code tuned on real Spanish and Portuguese registers, and the off-the-shelf engines offer English surname matching instead. So the thing a search engine is *for* is already solved outside it. Facet counts get precomputed, since the corpus is frozen between releases. Roughly $244/mo for the pilot, $482/mo for the full collection, at AWS list prices. I've written down four measurable conditions that would make me add the search engine later; if one fires it's a bolt-on, not a rebuild.

Verified: schema, loader, and every query run against a real PostgreSQL server, 81 tests, which caught four bugs that reading the code had not. Not verified: the infrastructure definitions have never been run and nothing is deployed to AWS. The sub-100ms search target is a budget, not a measurement.

*What I need*

1. @Daniel — a few hours of a historian's time on segmentation gold. We have 8 hand-labeled entries against ~177,000 unlabeled ones. The labeling sheets are pre-filled from the segmenter's output, so it's correcting entries rather than writing them. This is the only thing blocking a defensible accuracy number, and it also tells us where to invest next.
2. Approval of the ~$216 extraction spend. Batches are already staged; sending is the only paid step, and nothing in the toolkit sends anything on its own.
3. Four serving decisions, in the doc: open access vs. restricted volumes, what SSDA promises about person URLs, faithful vs. normalized text on screen, and how the historical vocabulary is presented. The person-URL one is the real risk — more in thread.
4. Whenever there's bandwidth: 1,281 pages were never actually transcribed. They contain Archivault's own error text and are silently missing from everything downstream. Per-volume re-transcription worklists are already generated.

Docs: <link|Task 3 — segmentation and NLP tooling> · <link|Serving the SSDA corpus>

---

## 2. Thread reply — Task 3 detail

*Thread: Task 3 detail*

*What the segmenter actually does.* Not a find-and-split. It reads a page line by line and labels each line — start of a record, closing attestation, signature, margin note, junk — and only cuts when the structure says a new record began. That's what keeps a birth date mentioned mid-baptism from fracturing the entry. It also stitches records that run across a page break back together, marks a record whose ending never arrives as `partial`, and recognizes non-record pages (blank, index tables, failed transcriptions) instead of forcing them into entries.

*Your examples earned their keep.* `740018` alone exposed two real bugs: a year marker one transcriber writes as `demil`, which was silently swallowing whole records, and an over-eager `partial` flag. Both fixed, both now permanent regression tests. Testing on our own six cropped volumes caught a missing opener format entirely — a Cuban diocese that starts every record with "El día" — worth ~530 recovered entries in one volume.

*One thing worth knowing about the manual examples.* They're `segmentation + normalization` targets, not segmentation targets: the gold expands abbreviations (`R.do P.e` → `Reverendo Padre`), modernizes spelling, and restores characters Archivault never produced. That makes them excellent gold for the _normalizer_, but a splitter can't reproduce it and shouldn't try. So the 29/29 is *boundary* accuracy — did we find each record, in the right place — matched on proper names, which survive normalization. Text similarity against that gold would be scoring the wrong component. There's a question in `SSDA_checkin_questions_for_Daniel.md` §1 that needs a call from you.

*Two other findings.* The earlier LLM window-repair approach duplicates about 17% of records — its overlapping-window de-duplication misses records transcribed slightly differently in adjacent windows. Our segmenter avoids this by construction, and the QA tool flags the existing duplicates by matching on the person baptized or buried, so two genuinely different same-day baptisms don't get merged. Separately, `ethnicity`, `origin`, and `phenotype` are used inconsistently across runs — same value, different field depending on the run. Worth firming up in `instructions.json` before the big run.

*To run any of it:* Python 3.10+, no dependencies. `git clone`, then `python -m pytest tests -q` — 79 tests, under a second, no network. Everything in this message reproduces from a fresh clone.

---

## 3. Thread reply — serving architecture detail

*Thread: serving detail, and the one thing I'd flag hardest*

*Person URLs are the riskiest part of this design*, more than speed or cost. Our identity matching gets rerun every time we improve it, and clusters legitimately split and merge when it does. If the site hands out `/person/4172` and a scholar cites it, that URL can point at a *different human being* after the next rerun, silently. The mechanism is built: when two identities merge, the old link redirects. When one splits, I made it refuse to guess — the reader gets a page saying the identity was revised, with the candidates listed. The friendlier alternative is redirecting to the largest fragment, which quietly asserts something we don't know. My instinct is the honest version, but that's a scholarly-citation judgement, not an engineering one, so it's @Daniel's call.

*Two problems people usually mash together.* The page images are big — about a terabyte cropped — but simple: once written they never change, so they go in private storage behind a CDN, each file named after a fingerprint of its own contents, which means a URL once issued is correct forever and never needs clearing. The records — names, dates, relationships — are *small*: all the text in the entire collection is about 5 GB. Most mistakes in this kind of project come from designing as if the second problem were the big one.

*Where the money goes.* Three deliberate savings: no connection proxy (~$88/mo we don't need), a small cache node instead of the managed tier (~$50/mo), and one storage endpoint that removes the per-GB charge on bulk loads. One place I spent more on purpose — the cheap CDN tier excludes South America, and given where most of this archive's readers are, that seemed like the wrong economy.

*Two things that fell out of this.* The cropping pipeline already records exactly where each crop sits in the original scan, and we've been discarding that at the last step. Keeping it lets the site draw a box on the original photograph showing the passage a transcription came from, which makes machine transcription auditable by eye instead of taken on trust. And the 1,281 failed pages need to be visible *as failures* — if the site just omits them, a reader sees a volume that looks complete. An absence in an archive shouldn't look like an assertion that nothing was there. Same for the 59 duplicate and 321 empty entries: hidden from search, kept on record.

*Timeline if you agree:* about twelve weeks part-time in four stages — accounts and database, load the existing 5,228 records and prove we can swap a corpus release in and roll it back, move images behind the CDN, then build the search service and run the load test. Nothing is irreversible until the last stage, and the load test is early on purpose so it can change the plan rather than rubber-stamp it.

---

## 4. Short standalone version

Use this instead of §1 if the channel wants one compact message.

*SSDA update: segmentation (Task 3) + serving architecture* — two docs are up: <link|Task 3> · <link|Serving>

*Task 3.* `ssda-nlp-tools` segments the full corpus — 232 volumes, 62,209 pages, 175,917 entries — in ~30 seconds, deterministically and for $0. It gets 29/29 records on @Daniel's three manual examples across four formats and two languages. But corpus-wide accuracy is *not* certified: my only broad check agrees ~36% exactly / ~59% within one entry, on a thin slice, so read that as a rough floor. The segmenter flags 5–6% of pages as low-confidence rather than guessing, which is the behavior I'd want.

*Cost.* Full-corpus extraction projects to *~$216* (~$0.003/image) vs. the $0.01 target. This supersedes the *$52* I circulated earlier — that used placeholder prices I hadn't verified. Same method and conclusion, number was off ~4x.

*Serving.* One PostgreSQL, no separate search engine: the hard part here is scribal spelling variants and our own matching code already beats what the off-the-shelf engines offer for Spanish and Portuguese. ~$244/mo pilot, ~$482/mo full. Schema, loader, and queries are tested against a real Postgres (81 tests, four real bugs caught); the infrastructure has never been run and nothing is on AWS yet.

*Asks:* (1) a few hours of a historian's time to grow the segmentation gold set — 8 labeled entries vs. ~177,000 is what's blocking a real accuracy number, and the sheets are pre-filled; (2) approval of the ~$216 extraction spend; (3) four serving decisions in the doc, the sharp one being what we promise about person URLs, since a cited `/person/4172` can point at a different person after we rerun identity matching; (4) 1,281 pages that were never transcribed, worklists ready, whenever there's bandwidth.
