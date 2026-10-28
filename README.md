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

**Moment 3 (unit 6)**

- *What I asked for:* I had the AI (Claude) go through `agreement_results.json`
  and my three-seed results, sort my staff-set disagreements into the three
  piles, and help draft the verdicts, diagnoses and run logs in this README.
- *What came back:* It caught that 8 of my 15 "disagreements" were me writing
  `hot-take` instead of `hot_take`, so my real rate was 23 / 30, not 15 / 30.
  It found a pattern in the 7 real ones: I treated any number as evidence and
  missed numbers that were doing real work. On the model side, it pointed out
  that every seed predicts `reaction` for almost every post, which matches
  under-training rather than inconsistent labels. It also ran the
  Milestone 5 re-run for me.
- *What I changed:* I fixed the label spelling myself and re-ran the
  agreement check. I picked more epochs as my one change because the
  diagnosis pointed at training. I reveiwed the diagnoses a bit before submitting.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.333 | 0.533 | +0.200 |
| Macro F1 | 0.270 | 0.294 | +0.024 |
| F1 — `argument` | 0.333 | 0.000 | -0.333 |
| F1 — `praise` | 0.000 | 0.200 | +0.200 |
| F1 — `reaction` | 0.476 | 0.682 | +0.206 |

Baseline is `facebook/bart-large-mnli`, zero-shot, using my definitions from
`label_definitions.txt`, on the same 30 posts in `test_split.csv`.

I also ran it once with bare label names instead of my definitions. It scored
**0.667** accuracy (`praise` 0.947, `reaction` 0.688, `argument` 0.000), so my
definitions were worth **-0.333**. They made the baseline worse, not better.

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
I don't think my trained model will beat the baseline, considering
`argument` got 0 and `praise` got fairly low. I understand why `reaction` and
`argument` were hard to tell apart, but I'm surprised `praise` got so low. I
had high hopes for it, since I think praise is pretty easy to spot and its
definition is straightforward.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
My trained model beat the baseline, but only barely did better than doing
nothing clever: half my test posts are `reaction`, so always guessing
`reaction` scores 0.500, and my model scored 0.533. Fine-tuning didn't add
much. It mostly learned to guess my biggest label.

The definitions I wrote made the baseline more confused, not less. Without
them, using just the label names, it scored 0.667 and found almost every
`praise` comment (0.947). With them, it scored 0.333 and found none. My
definitions work for me as a labeller, but they're a bad prompt for this
model.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | at least 0.65 | 0.533 | 0.500 | 0.500 | MISSED |
| 2. F1 for `praise` / `reaction` | at least 0.75 / 0.60 | 0.200 / 0.682 | 0.000 / 0.667 | 0.000 / 0.667 | MISSED (`reaction` met, `praise` missed) |
| 3. F1 for `argument` | at least 0.40 | 0.000 | 0.000 | 0.000 | MISSED |
| 4. Balance | `argument` at least 20%, no label above 55% | 20% / 51% | 20% / 51% | 20% / 51% | MET |
| 5. Confidence gap (most-sure third minus least-sure third) | at least 0.15 | 0.40 | 0.50 | 0.50 | MET |

Trained on `cpu`. Accuracy moved only 0.033 across the three seeds, but that
stability is misleading: on seeds 7 and 2024 the model called every single
test post `reaction`, which is exactly the 0.500 you get from always guessing
my biggest label. It's stable because it learned to guess one label, not
because it learned all three. Criterion 4 is about the dataset, so it's the
same on every seed.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42:

| true \ predicted | praise | reaction | argument |
|---|---|---|---|
| **praise** | 1 | 8 | 0 |
| **reaction** | 0 | 15 | 0 |
| **argument** | 0 | 6 | 0 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
8 real `praise` posts were called `reaction` and none went the other way, so
the model hasn't learned the `praise`/`reaction` boundary at all. It pulls
almost everything into `reaction`, including all 6 arguments.



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | at least 0.65 | MISSED | 0.533 / 0.500 / 0.500. None of the three seeds reached 0.65; the best was 0.12 short |
| 2 | F1 for `praise` / `reaction` | at least 0.75 / 0.60 | MISSED | `reaction` held on all three (0.682 / 0.667 / 0.667), but `praise` was 0.200 / 0.000 / 0.000, and the criterion needs both |
| 3 | F1 for `argument` | at least 0.40 | MISSED | 0.000 on every seed. The model never predicted `argument` once |
| 4 | Balance | `argument` at least 20%, no label above 55% | MET | `labels.csv` has 40 / 200 `argument` (exactly 20%) and 102 / 200 `reaction` (51%). It's a property of the data, so it's the same on every seed |
| 5 | Confidence gap | at least 0.15 | MET | 0.40 / 0.50 / 0.50, so it held on all three. But see the note under the diagnoses: this pass is hollow |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Misses 1, 2 and 3: the model collapsed to `reaction`.** On seeds 7 and
2024 it called every test post `reaction` (29 of 30 on seed 42). That's why
accuracy sits at 0.500, which is `reaction`'s share of the test split, and why
`praise` and `argument` score 0.000.

This is the model, not my labels. A boundary I applied inconsistently would
spread errors across one pair of labels, but here nothing is predicted except
`reaction`, and even `praise`, my easiest label, fails. My agreement rate with
staff (23 / 30, 76.7%) is in the normal range, so there's no sign I apply
rules inconsistently.

Cause: the model can't reach the task with this much training. At 3 epochs
it gets only about 33 gradient steps, too few to move off the majority class.
The fix I'll try is more epochs, changing nothing else.

**Criterion 5** is MET but hollow: with one predicted label, the gap doesn't
show the model can tell the labels apart. I'll recheck it after the fix.



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 23 / 30 = 76.7%

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
> *The post:* "Times are down about 3% since the shoe rules changed, so obviously the shoes are the whole story and the sport has a problem."
>
> *Staff said / I said:* `hot_take` / `analysis`
>
> *My call, and why:* Staff is right. This is almost Rule 1's own example: "so obviously" shows the conclusion came first, and the 3% is decoration.
>
> *Which pile:* (a)

**2.**
> *The post:* "Checked the entry lists. 41% of this year's field have never run the distance before, against 26% five years ago. That's why the finish-line photos look different, not because standards dropped."
>
> *Staff said / I said:* `analysis` / `hot_take`
>
> *My call, and why:* Staff is right. "That's why" makes the numbers the reason for the conclusion, so the fact is load-bearing (Rule 1).
>
> *Which pile:* (a)

**3.**
> *The post:* "Everyone blames the hill at 18 miles. I split the field into people who ran the first half under 1:45 and over, and the slowdown after 18 was almost identical for both groups. If the hill were the problem the fast starters would fall apart harder."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right. Nothing just happened to the writer and there's no feeling. A named comparison (under / over 1:45) is used to knock down a claim (Rule 3).
>
> *Which pile:* (a)

**4.**
> *The post:* "Got up at 5, drove ninety minutes, and it's cancelled. Standing in the car park in the rain reading the email."
>
> *Staff said / I said:* `reaction` / `analysis`
>
> *My call, and why:* Staff is right. It has times in it, but `analysis` has to make an argument, and this post doesn't. It's what just happened and how it felt.
>
> *Which pile:* (a)

**5.**
> *The post:* "First run back after eight weeks out. Two miles, slower than my warm-up used to be, and I don't care even slightly."
>
> *Staff said / I said:* `reaction` / `analysis`
>
> *My call, and why:* Staff is right. The numbers describe the run rather than support a claim, and "I don't care even slightly" is pure feeling.
>
> *Which pile:* (a)

**6.**
> *The post:* "Ran the same loop in 4C and in 24C within a fortnight, same effort by heart rate. 90 seconds slower in the heat over six miles. People keep saying heat costs you 'a bit' and it's more than that."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right that it isn't `reaction`, because it argues against a claim. But the rules never say whether a self-reported, one-runner measurement counts as "checkable". Nobody else can check this run. If it counts, almost any personal post with a number in it becomes `analysis`.
>
> *Which pile:* (b)

**7.**
> *The post:* "The club needs to stop pretending the Tuesday session is for everyone. It isn't and it never has been."
>
> *Staff said / I said:* `hot_take` / `reaction`
>
> *My call, and why:* I'd argue it. Rule 2 says `hot_take` asserts something general "about the world", and this is about one club the writer belongs to, which is personal and local. It's a weak case, though: `reaction` needs something that just happened, and nothing has.
>
> *Which pile:* (c)

**What the pattern in my disagreements tells me:**
Five of my seven are pile (a), and they follow one pattern. In 1, 4 and 5 I
treated any number as evidence. In 2 and 3 I missed numbers that were doing
real work. Saying it plainly: my weak spot is deciding whether a fact is
load-bearing. The one real gap in the rules is #6, about what "checkable" means
for personal data, and #7 is my only arguable win.

---

## The Improvement

**What I changed:** The learning rate, from 2e-5 to 5e-5. Nothing else:
same labels, same 3 epochs, same batch size, same three seeds.

**Which diagnosis pointed at it:** Misses 1, 2 and 3. The model predicted
`reaction` for almost every post, which looked like under-training (only about
33 gradient steps), not inconsistent labels. A higher learning rate makes each
of those steps bigger without adding training time.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | at least 0.65 | 0.800 | 0.767 | pending | pending |
| 2. F1 for `praise` / `reaction` | at least 0.75 / 0.60 | pending | pending | pending | pending |
| 3. F1 for `argument` | at least 0.40 | pending | pending | pending | pending |
| 4. Balance | `argument` at least 20%, no label above 55% | 20% / 51% | 20% / 51% | 20% / 51% | MET (labels unchanged) |
| 5. Confidence gap (most-sure third minus least-sure third) | at least 0.15 | pending | pending | pending | pending |

**Did it help, and how do I know:**
Yes, so far. On the two seeds finished, accuracy went from 0.533 to 0.800 and
from 0.500 to 0.767, and macro F1 roughly tripled (0.294 to 0.611, 0.222 to
0.584). That's well past the 0.500 you get by always guessing `reaction`, so
the model is no longer stuck on one label.

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

Criterion 1 (accuracy) looks fixed: 0.800 and 0.767 on the two finished seeds.
Per-label F1 for the after-run is still pending, so criteria 2, 3 and 5 aren't
confirmed yet. Macro F1 of about 0.6 next to 0.8 accuracy suggests one label
is still weak, most likely `argument`.

- **If `argument` (criterion 3) is still under 0.40:** I'd add about 30 more
  `argument` posts. It has only 40 examples, so just 6 land in each test
  split, and one wrong call moves its F1 a lot.
- **If `praise` (criterion 2) is still under 0.75:** I'd check whether the
  misses go to `reaction` and reread my `praise` / `reaction` rows for that
  boundary, since a comment that praises the host and reacts to the topic can
  go either way.
- **Why I stopped:** The assignment allows one change, and I ran out of time to
  test a second one separately.

**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->
I meant my labels to capture what a comment is *doing*: thanking, reacting,
or arguing a point. Before the fix, the matrix shows the model learned only
"most comments are `reaction`": all 9 `praise` and all 6 `argument` posts went
into the `reaction` column on seeds 7 and 2024, so it never got far enough to
learn the differences I labelled for.



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
