# Mode: style (prose critique)

Prose-level critique of a section or paragraph range against
`~/.cursor/skills/academic-writing-style/SKILL.md`. No content or method
critique unless the prose misstates the content. Output feeds the
academic-writing-style workflow: observations first, edits only after
"green".

## Extra brief fields

- Exact scope: file and section or `\label`, or paragraph range. Keep it to
  one section per council run so findings stay paragraph-level.
- The full academic-writing-style rules, quoted in the brief (banned
  patterns, required patterns, structure rules, common fixes checklist).
- Venue style constraints if any (IEEE vs Springer conventions, word limit).

## The five lenses

All five seats read the same prose, each hunting a different class of
problem.

**Seat 1, Contrarian (language violations).** Every banned pattern:
vague intensifiers, informal words, weasel hedges without data, plus
passive voice where active works, "percent" used where "percentage points"
is meant, and em dashes.

**Seat 2, First Principles (claims and precision).** Every claim missing
what, for whom, or where; modifiers that should carry a number; claims that
outrun the evidence the text gives; ambiguous compound modifiers.

**Seat 3, Expansionist (structure and story).** Overview paragraph present
and tells the complete story; contributions consolidated into 3 to 4 items
with numbers; conclusions that reinforce quantitative results and cover
every contribution; places where the prose undersells a result.

**Seat 4, Outsider (reader experience).** Terms used before definition,
forward references, paragraph flow and transitions, orphaned or redundant
paragraphs, list-like or AI-sounding prose ("First/Second/Third",
colon-driven lists without synthesis), hard-to-parse sentences.

**Seat 5, Executor (LaTeX and mechanics).** Citations (one per claim when
space is tight, named baselines), missing `\label{}`, broken `\ref{}`,
dead commented-out text, numbered display equations that could go inline,
and line savings available if the section is over budget.

## Extra peer-review question

4. Which flagged item is not actually a violation of the
   academic-writing-style rules (false positive)?

## Chairman output format

Numbered observations organized by subsection, matching the
academic-writing-style review workflow. Each observation: location, the
rule it violates, the quoted phrase, and a proposed replacement.

```
### Observations

**III-A System Model**
1. Para 1, "significantly reduces" -> vague intensifier. Proposed:
   "reduces median latency by [value from Table II]". Use only numbers
   that appear in the paper.
2. ...

**III-B Threat Model**
...

### Where the Council Agrees
[Patterns that repeat across the section.]

[shared sections from SKILL.md: Clashes, Blind Spots, Action List, One Thing]
```

After the verdict, wait for "green" before editing, then apply clean
replacements and build-check, per academic-writing-style.
