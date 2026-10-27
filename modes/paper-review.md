# Mode: review (mock peer review)

Simulates the review a paper will get at its target venue, before
submission. Scope can be the full paper or named sections.

## Extra brief fields

- Venue and track (e.g. IEEE TIFS regular paper, ICC 2027 symposium)
- Review form, if known (scores, criteria, accept thresholds)
- Page limit and current page count (build the paper if the count is unknown
  and a build script exists; otherwise ask)
- Claimed contributions, copied from the paper
- Scope: which sections the council must read; the rest is context only

## The five lenses

**Seat 1, Reviewer 2 (Contrarian).** Assumes the paper has a fatal flaw and
hunts for it. Overclaims vs evidence, missing or weak baselines, threat
model or assumptions that make the result trivial, results that do not
support the abstract. Writes the review that gets the paper rejected, so the
author can fix it first.

**Seat 2, Methodologist (First Principles).** Ignores the story and checks
whether the method and evaluation actually answer the research question.
Definitions and assumptions, threat model and system boundaries, data
provenance, train/validation/test splits and leakage, metrics vs claims,
statistical reporting (variance, seeds, confidence intervals, significance),
ablations, complexity, reproducibility details (hyperparameters, code,
datasets).

**Seat 3, Area Chair (Expansionist).** Judges significance and positioning,
the way an AE or AC writes the meta-review. Is the novelty clear against the
closest recent work? Is the contribution bigger than the authors frame it,
or smaller? What would move this from borderline to accept? What would a
strong related-work reviewer say is missing? Flags missing recent work by
topic; names specific papers only when certain they exist.

**Seat 4, Outsider (non-specialist reader).** A competent researcher from an
adjacent field with no context. Catches the curse of knowledge: terms used
before definition, forward references, overviews that do not tell the whole
story, notation that changes, figures that need the text to parse, acronyms,
paragraphs that do not connect. Applies the structure rules of
academic-writing-style.

**Seat 5, Revision Editor (Executor).** Turns critique into a revision plan
under the page limit and deadline. What to cut, merge, or move to an
appendix to make room. Which fixes are one-hour fixes vs new experiments.
Consolidated contributions (3 to 4 items, each with a number). Overuse of
numbered display equations that could go inline. Output is a ranked list
with an estimated cost per item.

## Extra peer-review question

4. Which [Major] finding, if left unfixed, is most likely to cause a reject
   at this venue?

## Chairman output format

```
### Predicted Outcome
[Score or decision on the venue's scale, e.g. "Major revision" or
"Weak reject, 2.5/5", with one-sentence justification.]

### Where the Council Agrees
[High-confidence findings raised independently by 2+ seats.]

### Major Issues (by section)
**Sec. I Introduction**
1. [Issue] [Location] [What would resolve it]
**Sec. IV Evaluation**
...

### Minor Issues (by section)
[Same structure, brief.]

### Top 3 Changes That Move the Decision
1. ...
2. ...
3. ...

[shared sections from SKILL.md: Clashes, Blind Spots, Action List, One Thing]
```
