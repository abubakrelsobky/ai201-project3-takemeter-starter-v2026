# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of practice.

An acceptance criterion names a number. _"The model is accurate"_ is an opinion. _"Every label has an F1 of at least 0.60 on the held-out set"_ is a criterion.

Under each, write a sentence or two on **why that number**. A reason that says something about your data or your taxonomy earns credit — _"I picked 0.60 F1 for `reaction` because it's my smallest label and I only have about 50 examples of it"_. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area                  | A question it could answer                                            |
| --------------------- | --------------------------------------------------------------------- |
| Overall accuracy      | How often does it need to be right to be worth using?                 |
| Per-label performance | Is one label allowed to be much worse than the others?                |
| Balance               | How lopsided can your label counts get before it's a problem?         |
| Consistency           | If someone else labelled the same posts, how often should you agree?  |
| Confidence            | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50 examples, about 8 land in the test split. An F1 computed on 8 examples moves a lot between seeds. A target for that label should be looser than one for your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**. Pick with that in mind — it is stricter than it first sounds.

---

## 1.

<!-- Your criterion. It must name a number. -->

On the held-out test set, the F1 score for `Numeric` (contains a digit) is at
least 0.85 and the F1 score for `Qualitative` (no digits) is at least 0.70, in
all three seeds.

**Why this target:**

My taxonomy has a hard line between the labels: a post either contains a digit
or it doesn't. So I expect the model to separate them well, which is why both
targets are high. `Numeric` gets the higher target because it has 143 examples
against 57 for `Qualitative`. Only about 8–9 `Qualitative` posts land in the
test split, so a single mistake can move its F1 by close to 0.1. Its target
needs that extra room.

---

## 2.

<!-- Your criterion. -->

On the 30-post staff agreement check (`agreement.py`), my labels match the
staff labels on at least 80% of posts (at least 24 of 30).

**Why this target:**

The staff note says these 30 posts were chosen partly because they are
ambiguous, and that agreement in the 70s is normal. I set the target just above
that range because I'm used to labelling against a strict rule, so I expect to
apply someone else's written definitions slightly more consistently than
average. 24 of 30 still allows 6 disagreements on the genuinely hard posts.

---

## 3.

<!-- Your criterion. -->

In `labels.csv`, no label makes up more than 69% of posts, and `Qualitative` has at least 57 examples.

**Why this target:**

`Qualitative` is my smallest label, and with a 15% test split, 60 examples puts only about 9 of them in the test set. Any fewer and its F1 in criterion 1 would rest on so few posts that one mistake decides the verdict. The 69% cap keeps
the model from scoring well by mostly guessing `Numeric`: at my current 71.5%, that lazy guess alone already gets 0.715 accuracy.

---

## 4.

<!-- Your criterion. -->

On the held-out test set, the trained model's macro F1 is at least 0.10 higher than the zero-shot baseline's (`baseline.py`), in all three seeds.

**Why this target:**

The zero-shot model only gets my one-line definitions and judges posts by meaning, while my labels depend on a surface detail: whether a digit appears. Fine-tuning on 200 labelled posts should teach that rule far better than a definition can. If training can't beat the baseline by at least 0.10, it hasn't
earned the extra time and data. I use macro F1 so the gain has to show up in `Qualitative` too, not just in my larger `Numeric` label.

---

## 5.

<!-- Your criterion. -->

On the held-out test set, the model's most confident third of predictions is at least 0.20 more accurate than its least confident third, in all three seeds (at least 2 more right out of the 10 posts in each third).

**Why this target:**

Most of my posts are easy calls: a digit is either there or it isn't. The posts where the model should hesitate are my edge cases, like number words
("a couple of miles") or digits that aren't really numbers. If its confidence means anything, its mistakes should pile up in the unsure third. Each third is only about 10 posts, so 0.20 is the smallest gap that's more than a single post's luck.

---

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
