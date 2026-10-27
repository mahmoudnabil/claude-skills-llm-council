---
name: llm-council
description: >-
  Mahmoud Mahmoud's academic LLM Council: five advisors on five different
  models independently critique a paper, rebuttal, research decision, grant
  proposal, or prose; they peer-review each other anonymously; a chairman
  synthesizes a verdict. Modes: review, rebuttal, decide, proposal, style.
  MANDATORY TRIGGERS: /llm-council, 'council this', 'run the council',
  'mock review', 'Reviewer 2 this', 'stress-test my contributions',
  'pressure-test this paper', 'panel review', 'NSF solicitation fit',
  'specific aims review', 'plan the rebuttal', 'which venue'. STRONG TRIGGERS
  (with a real paper or proposal at stake): 'is this ready to submit',
  'how will reviewers see this', 'which experiments should I add',
  'does this fit the solicitation'. Do NOT trigger for single factual
  questions, quick wording fixes, or plain section reviews (use
  academic-writing-style or review-paper for those).
---

# LLM Council (Academic)

One model gives one read of a paper or proposal. The council gives five
independent reads from five different models, each forced into a different
reviewing lens, then has them grade each other anonymously, then a chairman
writes the verdict: where they agree, where they clash, what only peer
review caught, and what to change first.

Adapted from Andrej Karpathy's LLM Council (different models, anonymous peer
review, chairman synthesis) via `aiwithremy/claude-skills-llm-council`.

---

## Model roster (edit here)

| Seat | Default lens | Model slug |
|------|--------------|------------|
| 1 | Contrarian / Reviewer 2 | `gpt-5.6-sol-medium` |
| 2 | First Principles / Methodologist | `claude-opus-5-5-high` |
| 3 | Expansionist / Area Chair | `gemini-4-argon-medium` |
| 4 | Outsider / non-specialist reader | `grok-4.7-high` |
| 5 | Executor / Revision Editor | `composer-2.5` |
| Chair | Chairman synthesis | `claude-opus-5-5-high` |

- Launch every seat with the `Task` tool, `subagent_type: generalPurpose`,
  and the `model` slug from this table.
- **Fallback:** if a slug is rejected, retry that seat once with
  `model: inherit` and list the substitution under "Council notes" in the
  verdict. Never silently drop a seat.
- If the user names models for a run ("use only Claude", "put GPT on
  Reviewer 2"), follow that for the run only; do not edit this table
  unless asked.

---

## Modes

| Mode | Use for | Mode file |
|------|---------|-----------|
| `review` | Mock peer review of a full paper or section before submission | `modes/paper-review.md` |
| `rebuttal` | Planning responses to real reviewer comments | `modes/rebuttal.md` |
| `decide` | Venue, framing, contribution set, which experiments to add | `modes/decision.md` |
| `proposal` | NSF (incl. solicitation fit), NIH, DoD proposals and specific aims | `modes/proposal.md` |
| `style` | Prose-level critique against academic-writing-style | `modes/style.md` |

**Invocation:** `/llm-council <mode> <target> [notes]`, for example
`/llm-council review 031`, `/llm-council rebuttal 028 reviews.pdf`,
`/llm-council proposal 036 26-512`, `/llm-council style 031 Sec. III`.

**Mode inference** when no mode is given:
- Reviewer comments pasted or attached -> `rebuttal`
- NSF solicitation number (`NN-NNN`), NSF/NIH/DoD URL, or a proposal ID -> `proposal`
- A choice between options (venues, framings, experiments) -> `decide`
- A request about wording, flow, or clarity only -> `style`
- A paper ID, `.tex` file, or section -> `review`

If the mode is still ambiguous, ask one question with `AskQuestion`, then proceed.

Read the selected mode file in full before step 2. It defines the five
lenses, extra brief fields, mode-specific peer-review questions, and the
chairman output format.

---

## Step 1: resolve the target and gather context

Spend at most ~60 seconds here. The goal is a brief that lets every advisor
give specific, anchored critique instead of generic advice.

**A. Resolve the target**

```bash
# Paper by ID (003. Papers) -> paper_dir, main_tex
python3 "$HOME/.cursor/skills/review-paper/scripts/resolve_paper.py" 031 --json

# Proposal by ID (002. Proposals) -> folder
python3 "$HOME/mmahmoud-skills/lib/proposal_resolve.py" 036
```

Both read `PAPERS_DIR` / `PROPOSALS_DIR` from
`~/mmahmoud-skills/config/locations.env`. A direct file path also works.
For a paper, list the `\input`/`\include` files under `main_tex` so
advisors get the section files, not only the wrapper.

**B. Always-loaded rule sources** (pass paths to every advisor; quote the
relevant parts in the brief):
- `~/.cursor/skills/academic-writing-style/SKILL.md` (language, structure, checklist)
- `~/.cursor/skills/review-paper/review.md`, section "WHAT TO CRITIQUE"

**C. Mode-specific sources**
- Reviews for `rebuttal`: pasted text, or files in the paper folder
  (look for `review*`, `*reviews*`, `decision*`, `.pdf`/`.txt`/`.md`).
- NSF solicitation for `proposal`: if the user gives a number, check the
  proposal folder and `_Supplementary_Docs/` for an existing solicitation
  `.md`/`.pdf` first. Otherwise download (needs network permission):

  ```bash
  S="$HOME/.cursor/skills/nsf-research-automator"
  python3 "$S/scripts/nsf_downloader.py" 26-512 --dir "$S/workspace"
  ```

  The downloader writes the PDF and a Markdown conversion. If only a PDF
  exists, convert it with
  `"$HOME/.cursor/skills/pdf-to-markdown/scripts/pdf_to_md.sh" <pdf>`.
- Venue facts (page limit, format, deadline, review form): from the user,
  the paper folder (`README`, call for papers, template class), or ask.
  Never guess a page limit.
- Prior council transcripts in `<target_dir>/council/` (avoid re-litigating
  settled points; note what changed since).

**D. Write the framed brief.** Neutral, no opinion, no steering:

1. Mode and the exact question for the council
2. Target: title, venue or program, scope (whole paper or which sections),
   absolute file paths advisors must read
3. Constraints: page limit, deadline, word limits, required sections
4. Mode-specific fields (from the mode file)
5. What is at stake (submission, resubmission, panel, rebuttal window)

If the request is too vague to brief ("council my paper" with no ID),
ask one clarifying question, then proceed. Keep the brief for the transcript.

---

## Step 2: convene the council (5 seats in parallel)

Send all five `Task` calls in **one message** so responses stay
independent. Each advisor gets the lens from the mode file, the brief, and
this template:

```
You are Seat [N], [Lens name], on an academic LLM Council convened by
Mahmoud Mahmoud (Assistant Professor, University of Alabama).

Your lens: [lens description from the mode file]

READ-ONLY: read the files listed below. Do not edit, create, or delete any
file. Do not run builds or git commands.

Rule sources you must apply:
- ~/.cursor/skills/academic-writing-style/SKILL.md
- ~/.cursor/skills/review-paper/review.md (section "WHAT TO CRITIQUE")

The council brief:
---
[framed brief, including file paths]
---

Respond only from your lens. Be direct and specific; do not balance or
hedge. Other seats cover the other angles.

Rules:
- Anchor every point to a location: section number or title, \label,
  paragraph ("Sec. III-B, para 2"), equation, table, figure, or reviewer
  comment ID.
- Quote at most one short phrase per point when it pins the location.
- Never invent citations, data, results, or reviewer statements. If a claim
  needs support, say what support is missing.
- Quantify when the text gives numbers; flag unquantified claims.
- No em dashes.

Length: 200-400 words. No preamble. Return a numbered list of findings,
most severe first, each tagged [Major] or [Minor].
```

---

## Step 3: anonymous peer review (5 seats in parallel)

1. Shuffle the five responses and label them Response A to E. Record the
   mapping; do not reveal it to reviewers.
2. Launch five reviewers in one message, one per roster model.
3. **Each reviewer sees only the four responses not written by its own
   model** (keep the global A to E letters; omit its own). This prevents a
   model from grading its own output.

```
You are a peer reviewer on an academic LLM Council. Advisors independently
answered this brief:
---
[framed brief]
---

You may read the files named in the brief to verify claims. READ-ONLY.

Anonymized responses:
**Response [X]:** [text]
... (four responses)

Answer, referencing responses by letter:
1. Which response is strongest, and why? Pick one.
2. Which response has the biggest blind spot or a factual error about the
   manuscript? Verify against the files when the claim is checkable.
3. What did all responses miss that the council should consider?
[mode-specific peer-review question from the mode file, if any]

Under 250 words. Direct. No em dashes.
```

---

## Step 4: chairman synthesis (1 seat)

Launch one `Task` with the Chair model. It receives the brief, all five
responses **de-anonymized** (seat and lens names), the A to E mapping, and
all five peer reviews.

```
You are the Chairman of an academic LLM Council. Synthesize five advisor
responses and five anonymous peer reviews into one verdict for Mahmoud.

Brief:
---
[framed brief]
---

ADVISOR RESPONSES:
**Seat 1, [lens] ([model]):** [response]
... (all five)

ANONYMIZATION MAP: [A = Seat k, ...]

PEER REVIEWS:
[all five]

Rules:
- Side with a minority seat when its reasoning and evidence are stronger;
  say so explicitly.
- Drop findings that peer review showed were wrong about the manuscript.
- Every action item names a location and a concrete change.
- Never invent citations, data, or results. No em dashes.

Produce the verdict in exactly the format defined below.
[chairman output format from the mode file]
```

Every mode's format ends with these shared sections:

```
### Where the Council Clashes
[Real disagreements, both sides, why reasonable reviewers differ.]

### Blind Spots Caught in Peer Review
[Points that only surfaced in step 3.]

### Action List
1. [Location] [Change] [Why] (ranked by impact on the outcome)
...

### The One Thing to Do First
[One concrete step.]
```

---

## Step 5: present the verdict in chat

Present the chairman's verdict in chat as Markdown under
`## Council Verdict: <mode>, <short target>`. No HTML, no generated report
files. Add a short "Council notes" line at the end: models used, any
fallback substitutions, files read.

## Step 6: edit gate

The council observes; it does not edit. After the verdict, **stop**. Do not
modify any `.tex`, `.bib`, or proposal file until Mahmoud approves items
from the Action List (e.g. "green", "green on 1-4"). Then apply edits
following academic-writing-style (direct, clean replacements; build-check
and page count after edits). For `review-paper` style highlighted notes,
hand off to the `review-paper` skill instead.

## Step 7: save the transcript (only when asked)

Write `<target_dir>/council/council-<mode>-<YYYY-MM-DD>.md` containing the
brief, the five responses with seat, lens, and model, the A to E mapping,
the five peer reviews, and the verdict. If there is no target folder, ask
where to save.

---

## Example (review mode, abbreviated, illustrative content)

**User:** `/llm-council review 0NN Sec. I only, TIFS, 14 pages double column`

- **Seat 1, Reviewer 2:** [Major] Sec. I para 3 claims the detector is
  "robust to adaptive attackers" but Sec. V evaluates only non-adaptive
  perturbations; the claim is unsupported.
- **Seat 2, Methodologist:** [Major] Contribution 2 reports 97.1% accuracy
  without stating whether the train/test split is by device or by session.
- **Seat 3, Area Chair:** [Major] The gap statement positions against
  2019-2021 work; two 2024 TIFS papers on the same threat model are absent,
  which a TIFS AE will check first.
- **Seat 4, Outsider:** [Minor] "fingerprint drift" is used in para 2 and
  defined only in Sec. III-A.
- **Seat 5, Revision Editor:** [Major] Collapse contributions 3 and 4 into
  one item; this frees ~6 lines for the missing threat-model paragraph.

**Peer review** caught that Seat 3 named no specific missing papers, so the
chairman turns that point into "search and confirm before citing" rather
than inventing citations.

**Verdict, one thing first:** narrow the "adaptive attackers" claim in
Sec. I para 3 to the evaluated threat model, or add the adaptive evaluation.

---

## Notes

- **Parallel always.** Sequential launches let earlier answers bleed into
  later ones.
- **Anonymize always** for peer review, and never let a model review its own
  response.
- **The chairman can overrule the majority** when the dissent is better
  argued.
- **Do not council trivial requests.** A single wording fix or a factual
  question gets a direct answer.
- **Cost:** a full run is 11 sub-agent calls. For a long paper, scope the
  brief to the sections that matter for the decision.
