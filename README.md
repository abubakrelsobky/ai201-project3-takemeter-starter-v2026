# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter reads text posts from Hacker News: mostly "Ask HN" and "Show HN"
posts where people ask for advice, share projects, or describe a problem. It
sorts each post into one of two labels: `Numeric` if the title or body
contains at least one digit (a price, a count, a date, a version number,
anything), or `Qualitative` if it contains none. The dataset is 205 recent
posts pulled from the public Hacker News API, and the classifier is a
fine-tuned DistilBERT trained on my labels.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `No Digits`

**Definition:**

The post does not have any numbers in it

**Example 1:**
> "Show HN: The Handsome Boy

Photographs with atmospheric themes
Films and videos, including those about bike tours and fitness training
Grow Diaries, i.e., documented planting and cultivation projects
A playlist of songs that go well with the pictures or that I listen to often."


**Example 2:**
> "Google Meet Enshittification

Google Meet Android app meeting join now claims to need Notification Permissions for incoming and outgoing calls.

This claim is false. There's no such requirement. And if there were, it would be needless and wholly unreasonable."


### `Contains Digit`

**Definition:**
The post has at least one numebr in it.

**Example 1:**
> "Ask HN: What is/was your strategy which landed you a JOB in this market?

I am a 2yoe+ software engineer working at oracle, with going layoffs I am bit insecure about the job, and wanted to know in this market what do you guys suggest for switching to another job ?"


**Example 2:**
> "Ask HN: My Gemini bill shot up suddenly to 2500 USD in one day

our usage of gemini is very limited. using 2.5 flash or similar models. at a hackathon in bay area i used the api key in my google app script. from that day my  usage overshot to insane amounts.Google started charging y card every 30 mins or so. Google support said they will help significantly waive this off but they havent been swift. any one else experienced anything similar?"


### The hardest boundary

**Which two labels:** It is quite clear cut to distinguish them

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even if the tone is heated." -->
The rule it to see whther there are any digits. If there is any single digit, regardless of what it is, we label it 'Contains Digit.'

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard cases. -->

**Where the posts came from:**
The posts were collected from Hacker News using its public Firebase REST API, pulling directly from official community discussion stories and feeds.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
I used AI to generate a script to label them.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
|  |  |  |
|  |  |  |
|  |  |  |
| **Total** |  | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have 
been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**2.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**3.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* Feedback from Claude on my first acceptance criterion,
  "The F1 score for both labels should be 0.99."
- *What came back:* 0.99 couldn't be defended. With only about 9
  `Qualitative` posts in the test split, one wrong prediction is enough to fall
  below it. It also said one number for both labels ignored that `Numeric` has
  more than twice as many examples.
- *What I changed:* I set separate targets, F1 ≥ 0.85 for `Numeric` and
  ≥ 0.70 for `Qualitative`. I also made the criterion say "on the held-out test
  set, in all three seeds."

**Moment 2**

- *What I asked for:* Feedback on my consistency criterion, "Two readers reading
  the same taxonomy definitions should label both groups at least 85% of the
  time accurately."
- *What came back:* "Accurately" was the wrong word, since agreement between
  two readers has no right answer. "Two readers" also didn't name a real check.
  The actual check is `agreement.py` on the staff's 30 posts, under the staff's
  three labels. And the staff note says agreement in the 70s is normal on those
  posts.
- *What I changed:* I rewrote it as "my labels match the staff labels on at
  least 80% of posts (at least 24 of 30)," and gave the staff's 70s figure as
  the reason for the number.

**Pre-labelling disclosure:**

185 of the 205 posts were pre-labelled by my collection script, `get_data.py`.
The script gives a post `Numeric` if its title or body contains any digit and
`Qualitative` otherwise. No AI model did any labelling. I then read every
pre-labelled post and corrected the label wherever the digit rule got it wrong.
These rows are marked `pre-labeled (script)` in the `note` column. The other 20
posts I labelled cold, by hand, before looking at the script's label; these are
marked `cold`.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
