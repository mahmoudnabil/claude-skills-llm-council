# LLM Council (Academic fork)

A Cursor Agent skill that runs a paper, rebuttal, research decision, grant
proposal, or section of prose through five advisors on five different
models. The advisors peer-review each other anonymously, and a chairman
writes the verdict.

Customized by Mahmoud Mahmoud (University of Alabama) for academic writing
and grant proposals. Forked from
[aiwithremy/claude-skills-llm-council](https://github.com/aiwithremy/claude-skills-llm-council)
by [Ole Lehmann](https://x.com/itsolelehmann), which is based on Andrej
Karpathy's [LLM Council](https://github.com/karpathy/llm-council).

## What changed from upstream

- Five academic modes instead of one general business council.
- One model per seat (true multi-model council, as in Karpathy's original)
  instead of five personas on one model.
- No reviewer grades a response written by its own model.
- Context comes from the paper and proposal folders (`003. Papers`,
  `002. Proposals`), the `academic-writing-style` rules, the `review-paper`
  critique list, and NSF solicitations fetched with `nsf-research-automator`.
- Findings are anchored to sections, `\label`s, paragraphs, or reviewer
  comment IDs. The skill never invents citations or results.
- The council only observes. It edits files only after you approve items from
  the action list ("green").

## Modes

| Mode | Example | Output |
|------|---------|--------|
| `review` | `/llm-council review 031` | Predicted outcome, Major/Minor issues by section, top 3 changes |
| `rebuttal` | `/llm-council rebuttal 028 reviews.pdf` | Per-comment plan (concede / clarify / push back / new experiment), page budget |
| `decide` | `/llm-council decide 031 TIFS vs TDSC` | Options table, recommendation |
| `proposal` | `/llm-council proposal 036 26-512` | Panel rating, solicitation fit, compliance gaps, highest-leverage revision |
| `style` | `/llm-council style 031 Sec. III` | Numbered prose observations by subsection (academic-writing-style) |

If you leave out the mode, the skill infers it from the input.

## Model roster

Defined in the table at the top of `SKILL.md`. Edit that table to swap
models. If a model slug is unavailable, that seat falls back to the parent
model and the verdict says so.

| Seat | Lens | Model |
|------|------|-------|
| 1 | Contrarian / Reviewer 2 | GPT-5.6 Sol |
| 2 | First Principles / Methodologist | Claude Opus 5.5 |
| 3 | Expansionist / Area Chair or Program Officer | Gemini 4 Argon |
| 4 | Outsider / non-specialist reader | Grok 4.7 |
| 5 | Executor / Revision Editor | Composer 2.5 |
| Chair | Synthesis | Claude Opus 5.5 |

A full run makes 11 sub-agent calls. Scope long papers to the sections that
matter.

## Files

```text
SKILL.md                 core flow, roster, prompt templates, edit gate
modes/paper-review.md    mock peer review
modes/rebuttal.md        responses to real reviews
modes/decision.md        venue, framing, experiments
modes/proposal.md        NSF / NIH / DoD panel simulation
modes/style.md           prose critique
```

## Install (each Mac)

This repo is cloned directly into the global skills folder. It is not part
of `mmahmoud-skills`, so `sync-skills` does not carry it to other Macs.

```bash
git clone git@github.com:mahmoudnabil/claude-skills-llm-council.git ~/.cursor/skills/llm-council
cd ~/.cursor/skills/llm-council
git remote add upstream https://github.com/aiwithremy/claude-skills-llm-council.git
```

The `/llm-council` slash command lives in `~/.cursor/commands/llm-council.md`:

```markdown
# LLM Council

User invoked `/llm-council`. Load and follow the skill file, then read the mode file for the selected mode before convening the council.

Skill: /Users/mahmoudmahmoud/.cursor/skills/llm-council/SKILL.md
Modes: /Users/mahmoudmahmoud/.cursor/skills/llm-council/modes/
```

Open a new Agent chat after installing so Cursor picks up the skill.

Dependencies (already in `mmahmoud-skills`): `academic-writing-style`,
`review-paper` (`resolve_paper.py`, `review.md`), `lib/proposal_resolve.py`,
`config/locations.env`, `nsf-research-automator`, `pdf-to-markdown`.

## Updating from upstream

```bash
cd ~/.cursor/skills/llm-council
git fetch upstream
git log --oneline main..upstream/main   # review before merging
```

Upstream is a general-purpose council, so merge selectively.
