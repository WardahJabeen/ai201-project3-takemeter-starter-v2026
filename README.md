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

**Community:** the public YouTube comment section of The Diary of a CEO's
interview with Jeffrey Ladish on AI risk
(https://www.youtube.com/watch?v=qDzg-xvkeXw). Comments are public, need no
login to read, and range from one-line jokes to multi-paragraph arguments.

**What the classifier does:** it sorts each comment into one of three labels.
`praise` is a comment about the show itself, `reaction` is a gut response to
AI risk, and `argument` is a claim about AI backed by something specific you
could point at. The useful part is finding the arguments, which are the
comments worth reading but are buried under jokes and thank-yous.

**Candidate distinctions from my first read (35 comments):**

1. **Some comments point at something specific from the video, most don't.**
   A timestamp ("1:23:23", "27:00") or a named event ("OpenAI's own spawned
   agents… took over its security system") versus a general "this is
   terrifying."
2. **Some give a reason for their claim, most just state it.** "We haven't
   even been able to align human leaders like Putin… yet we expect to align a
   superintelligence" connects a claim to support. "Only humanity would create
   something smarter than itself and then be dumb enough to think they could
   control it" is a claim on its own.
3. **Some are about the show, the rest are about AI.** Comments addressed to
   the host or guest ("Thank you both", "great respect to your guest") versus
   comments about AI itself. About half the comments I read were the first
   kind.
4. **Length.** The comments that give reasons run three or more sentences;
   most others are one or two. Easy to point at, but probably a symptom of
   distinction 2 rather than a distinction of its own.



---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `praise`

**Definition:** The comment evaluates the episode, the host or the guest
(thanks, compliments, "best interview yet") rather than talking about AI
itself.

**Example 1:**
> the best podcast on AI I have recently watched. on/not on this channel.
> thank you.

**Example 2:**
> My favorite guest on this subject yet. Easily understandable.
> Congratulations on a beautifully produced episode.

### `reaction`

**Definition:** A response to AI risk (fear, a joke, a quote or a bare claim)
that offers no concrete support for what it says.

**Example 1:**
> Only humanity would create something smarter than itself and then be dumb
> enough to think they could control it

**Example 2:**
> These bots have learned from humans very well. Cheating, stealing, lying.
> Only 25 mins in and this sounds like some insane fever dream.

### `argument`

**Definition:** A claim about AI or AI safety where a specific detail (a
named event, company, country, person, historical example, number or moment
from the video) is given as the reason the claim holds.

**Example 1:**
> The fact that OpenAIs own spawned agents ran amok and took over its
> security system is enough proof that these companies aren't capable of
> managing safety.

**Example 2:**
> It's terrifyingly profound when Steven points out at 1:23:23 that we
> haven't even been able to align human leaders like Putin or Kim Jong-un,
> yet we expect to somehow perfectly align a digital superintelligence.

### The hardest boundary

**Which two labels:** `reaction` and `argument`. The post that sat between
them:
> How can we expect alignment from models or agents when companies aren't
> aligned with what they SAY and what they ACTUALLY DO?

It makes a real point, but it never says which company or what they did.

**The decision rule I used every time:**
If the comment backs its claim about AI with something specific you could
point at (a named event, company, country, person, historical example, number
or timestamp) and that detail is the *reason* the claim holds, it's
`argument`, even if it's one sentence or phrased as a question. If the support
is only a general statement ("companies lie", "humans are greedy"), it's
`reaction`, however true it sounds. A timestamp or quote followed only by a
feeling ("At 41:10 he says nobody knows how to align these. That's
terrifying.") is also `reaction`. Fiction doesn't count as a named example:
a comment whose support is Terminator or The Matrix is `reaction`. So the
post above is `reaction`.

**Second rule, for `praise` vs `reaction`:** a comment about the host or guest
is `praise` only if it evaluates them ("great guest", "well explained"). If
it's about how the topic makes them or the commenter feel ("Watching Steven's
nervous self-soothing… is giving me anxiety"), it's `reaction`. A comment that
does both ("Great interview! … terrifying") is `praise` if it's addressed to
the show.



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** The public YouTube comment section of The
Diary of a CEO's interview with Jeffrey Ladish
(https://www.youtube.com/watch?v=qDzg-xvkeXw). I copied the comments by hand
and stripped usernames, dates, like counts and "Reply" lines. Comments are
kept whole; multi-line comments are joined onto one line.

I left out bot spam (promos for "Claudixum", a "Winner's Wealthgate" ad, and
a near-copy of the top comment), comments that weren't about the episode or AI
(an intro timestamp, a knee-surgery story, a request for PDF summaries),
non-English comments, and near-duplicates. My first 200 had only 25
arguments, so I went back for more and swapped out 13 of the shortest,
vaguest reactions ("We're doomed.", "If we die, we die.") to meet my own
balance criterion.

**How I labelled them:** I labelled the first 20 cold. The other 180 were pre-labelled by AI using my definitions and decision rules,
then I read every one and corrected argued a few of them.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `reaction` | 102 | 51% |
| `praise` | 58 | 29% |
| `argument` | 40 | 20% |
| **Total** | 200 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "I'd like to point out that we can't even get pets "aligned""
>
> *Could have been:* `reaction` or `argument`
>
> *I chose, because:* `reaction`. It's a comparison, like the nuclear-weapons
> one I counted as `argument`, so it made me stop. The difference is that
> nuclear weapons are a specific named example, while "pets" is a whole
> category. Nothing in it is something you could point at, so under my rule
> it's a bare claim.

**2.**
> *The post:* "My mind is blown..... spinning..... and so fucking grateful!!"
>
> *Could have been:* `praise` or `reaction`
>
> *I chose, because:* `praise`. "My mind is blown" on its own is a feeling,
> which would make it `reaction`. But "grateful" is thanks aimed at the show,
> and my rule says a comment that thanks or compliments the show is `praise`
> even when it also says how the topic made them feel.

**3.**
> *The post:* "The longer the interview gets, the more the guest is avoiding
> crucial issues that Steven is pointing out"
>
> *Could have been:* `praise` or `reaction`
>
> *I chose, because:* `reaction`. It evaluates the guest, which sounds like
> `praise`, but it's negative, and my `praise` definition only covers thanks
> and compliments. This one showed me a gap: criticism of the show has no
> label of its own, so it lands in `reaction`.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** <!-- epochs, learning rate, batch size, seed -->
3 epochs, learning rate 2e-5, batch size 16, seed 42, 128 tokens per post.
Trained on `cpu`.

**Anything I changed from the defaults, and why:** Nothing in the training
settings. I wanted a first run on the defaults before deciding what to change.
I did have to fix two things just to get it to run: Windows Smart App Control
blocked pyarrow 26.0.0, so I installed pyarrow 21.0.0, and an expired Hugging
Face login token stopped the model downloading.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->
Train 139, validation 31, test 30.

| Label | Test count |
|---|---|
| `praise` | 9 |
| `reaction` | 15 |
| `argument` | 6 |

`argument` has only 6 examples in the test split, under the notebook's
threshold of 8. One right or wrong guess moves its F1 a lot, so I expect it to
jump around between seeds next unit. On this first run the model got none of
them right (F1 0.000) and mostly guessed `reaction` (accuracy 0.533).


---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* After coming up with my own ideas for labels, I gave
  an AI tool the same comments and asked it for 2–4 labels, to see if it
  would split them the same way.
- *What came back:* Three labels that matched mine (`praise`, `reaction`,
  `argument`), plus an `off_topic` label. It put "Watching Steven's nervous
  self-soothing with his sleeves is giving me anxiety" in `off_topic`.
- *What I changed:* I kept the three that matched and looked into the one
  that didn't. I pushed back on that comment: it's anxiety about AI, picked up
  from the host, so it's `reaction`. That gave me my rule that a comment about
  the host or guest is only `praise` if it evaluates them. I didn't add
  `off_topic` as a label. Truly off-topic comments got swapped out of the
  dataset instead. I also had it write eight boundary posts to stress-test the
  definitions, which made me tighten `argument`: the specific detail has to
  be the reason the claim holds, and fiction like Terminator doesn't count.

**Moment 2**

- *What I asked for:* After I labelled the first 20 myself, I had the AI
  pre-label the other 180 using my definitions and decision rules.
- *What came back:* A full set of labels, with hard cases, possible spam and
  off-topic rows flagged.
- *What I changed:* I argued about the labels instead of accepting them. I
  disagreed on whether off-topic
  comments were just reactions, asked whether some were really spam or just
  mentioned the same product, and questioned borderline ones, like whether
  the "tactical bun" joke was really an indirect reaction. I swapped out the spam, duplicates, near-copies and
  non-English comments for real comments I hadn't used yet, and kept or
  changed each label based on my own decision rule.

**Pre-labelling disclosure:** Rows 22–201 were pre-labelled by AI (Claude)
from my definitions and decision rules, and are marked `pre-labelled` in the
`note` column. I read every one, questioned a few, and in the end kept the
labels as they are. The AI also suggested which
spam, off-topic and short-reaction rows to swap out; I made the final call on
each.

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
