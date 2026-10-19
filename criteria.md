# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy

Accuracy is at least 0.65 on the test posts, on all three seeds.

**Why this target:** Almost half of the comments I read were praise, so a
model that just guessed `praise` every time would already get close to 50%.
I want it clearly better than that, but I'm not expecting it to be great at
telling `reaction` and `argument` apart, since that was the hardest call for
me too.

---

## 2. F1 for the two big labels

`praise` has an F1 of at least 0.75 and `reaction` has an F1 of at least
0.60, on all three seeds.

**Why this target:** Praise comments use obvious words like "thank you" and
"great interview", so it should be the easiest label to get right.
`reaction` is harder because it sits in the middle. It can look like praise
when someone says the episode was great and terrifying, and it can look like
an argument when someone makes a point without anything specific behind it.

---

## 3. Is `argument` learned at all?

`argument` has an F1 of at least 0.40 on all three seeds.

**Why this target:** It's my rarest label. Only a few of the comments I read
were real arguments, so there won't be many in the test posts, and one
mistake will change its F1 a lot. That's why the target is lower. But I
still want proof the model actually learned what an argument looks like and
didn't just ignore the label.

---

## 4. Balance

`argument` makes up at least 20% of `labels.csv` (40 of 200), and no label is
above 55%.

**Why this target:** Arguments were rare in my first read, so if I just took
comments in order I'd end up with too few to test on. This means I'll have to
go looking for longer, reasoned comments on purpose. The cap is there because
praise is already about half, and if it grows the model can do well by mostly
guessing praise.

---

## 5. Confidence

Accuracy on the posts the model is most sure about is at least 0.15 higher
than on the posts it's least sure about (the most-sure and least-sure thirds
the notebook prints), on all three seeds.

**Why this target:** If I used this for real, I'd want it to handle the
obvious comments on its own and flag the unsure ones for me to check. That
only works if the model is actually right more often when it's sure.

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
