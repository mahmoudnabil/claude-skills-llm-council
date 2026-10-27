# Mode: proposal (NSF, NIH, DoD)

Simulates a review panel and a program officer read of a proposal, specific
aims page, or a pre-proposal idea against a specific solicitation.

## Extra brief fields

- Sponsor and program, solicitation number and URL, deadline.
- **Solicitation extract** (from the downloaded `.md`; quote, do not
  paraphrase): program goals and research areas, eligibility, project
  types and award sizes, page limits, required sections and supplementary
  documents, solicitation-specific review criteria, and any "do not
  submit" or "will be returned without review" conditions.
- Review criteria for the sponsor (below) plus any solicitation-specific
  additions.
- Proposal files: for NSF typically `project_description.tex`,
  `summary.tex`, `references.bib`, and supporting docs under
  `_Supplementary_Docs/`. Name the files in scope.
- Team, institutions, and roles; prior funding and results from prior
  support if relevant.
- For an idea-stage council (no draft yet): the idea, the candidate
  solicitation(s), and what exists today.

### Sponsor review criteria

- **NSF:** Intellectual Merit and Broader Impacts, each judged on: potential
  to advance knowledge / benefit society; creative, original, or
  transformative concepts; well-reasoned, well-organized plan with a
  mechanism to assess success; qualified team; adequate resources.
  Panel ratings: Excellent, Very Good, Good, Fair, Poor. Also check the
  PAPPG requirements the solicitation invokes (Project Summary with
  separate Overview, Intellectual Merit, Broader Impacts statements; Data
  Management Plan; Mentoring Plan when postdocs or grad students are
  funded; Results from Prior NSF Support).
- **NIH:** Significance, Innovation, Approach (the dominant criterion),
  Investigators, Environment; overall impact score 1 (exceptional) to 9
  (poor). Check the Specific Aims page logic: gap, long-term goal,
  objective, central hypothesis, rationale, aims, expected outcomes.
  Use the simplified review framework factors if the FOA specifies them.
- **DoD:** alignment with the BAA or topic's stated technical objectives
  and evaluation criteria (usually technical merit, relevance to the
  mission, team qualifications, cost realism), transition path, and any
  security or export-control requirements stated in the call.

## The five lenses

**Seat 1, Panel Contrarian.** The panelist who writes the "Poor/Fair"
review. Weak or generic hypotheses, aims that depend on each other so one
failure sinks all, overreach for the budget and timeline, Broader Impacts
that are boilerplate, missing preliminary data, unclear evaluation.

**Seat 2, Feasibility Methodologist (First Principles).** Does the research
plan actually test the stated objectives? Are the methods, data access,
evaluation metrics, and success criteria concrete? Are risks named with
mitigations? Is the project the right size for the award?

**Seat 3, Program Officer (Expansionist).** Reads for fit and portfolio
value. Does this match the solicitation's priorities and language? Which
solicitation goals does it serve that the draft does not claim? What would
make the PO champion it? Flags anything that risks return without review.

**Seat 4, Outsider Panelist.** A panelist from an adjacent field, which is
common on NSF panels. Is the summary readable cold? Are the key terms
defined, the problem motivated in plain language, and the aims
distinguishable? Applies academic-writing-style structure rules.

**Seat 5, Executor (Compliance and Plan).** Builds the compliance checklist
from the solicitation (every required section, page and font limits,
supplementary documents, letters of collaboration format, budget caps) and
marks each present, missing, or at risk. Checks the timeline, personnel
effort, and management plan for realism against the deadline.

## Extra peer-review question

4. Which issue would most likely move the panel rating down a full level
   (for example Very Good to Good), and which single fix would move it up?

## Chairman output format

```
### Predicted Panel Outcome
[NSF: rating per criterion and overall (E/VG/G/F/P) with the likely
panel summary sentence. NIH: estimated impact score range. DoD: rating
against the call's criteria.]

### Solicitation Fit
[Priorities the proposal serves, priorities it misses, any
return-without-review risk.]

### Compliance Gaps
| Requirement (solicitation section) | Status | Fix |
|------------------------------------|--------|-----|

### Where the Council Agrees
...

### Strengths to Keep
[What the panel will likely praise; do not cut these.]

### Highest-Leverage Revision
[The single change most likely to raise the rating.]

[shared sections from SKILL.md: Clashes, Blind Spots, Action List, One Thing]
```
