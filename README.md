# No-Hallucination Citations

## Goals

Construct a minimal prompt that avoids hallucinated citations, invented metadata, and unsupported biomedical claims in biomedical literature-search answers. Correctness is more important than completeness: it is acceptable to omit marginal or uncertain papers, but not to fabricate, misidentify, or overclaim citations.

## Current Status

v2 complete (v0–v2 run).

| v | BRCA | TF | Total | Key failure modes |
|---|------|----|------|-------------------------------|
| v0 | 74 | 92 | 166 | citation dump (8/35 uncited), unflagged model systems |
| v1 | 46 | 83 | 129 | PMID 40242782 hallucinated, YY1 claim unsupported by abstract |
| v2 | 49 | 84 | 133 | author misattribution (Qin vs Quinn), Matrin3 paper misframed as YY1 study |

## Pending / Next Actions
- [ ] Write v3 prompt to address persistent author misattribution and abstract overstretching, then run v3–v9

## Notes and Procedure

Autoresearch-style project for optimizing biomedical literature-search skill prompts. To reset the project for a fresh autoresearch loop, delete the entire `runs/` directory.

## Agent Configuration

**Use `vanilla` agents for all benchmark runs and evaluations.** This tests the candidate prompt in isolation without the researcher's built-in verification instructions.

The lead agent orchestrates the loop, delegates runs and evals to `vanilla` agents, synthesizes results, and writes modified prompts.

### Autoresearch Loop

Each iteration `vN` follows this order: **write prompt → run benchmark → evaluate → summarize**.

1. **Write the prompt.** For `v0`, copy the seed from `./skill_v0.md` to `runs/v0/skill.md`. For `vN>0`, write a new prompt addressing the previous iteration's failure modes. Save as `runs/vN/skill.md`. Experiment freely with the new skill text, but do not explicitly put a hard cap on the number of citations to be returned. 
2. **Identify the next version.** If `runs/` doesn't exist, start at `v0`. Otherwise, find the highest version with a `skill.md` but no complete `summary.md`, or the next increment if all existing versions are complete.
3. **Run the benchmark.** Execute every task in `evals/tasks/`. Use each file's basename (without extension) as `<task_id>`. Create `runs/vN/<task_id>/` and save runner output in `output.md`.
4. **Evaluate using `vanilla` agents.** Pass each evaluator: the full rubric, the runner `output.md`, the original task prompt, PubMed MCP access, and instruction to produce the evaluator report format from the rubric. Save reports in `runs/vN/<task_id>/report.md`. Do not proceed until all evaluations are complete.
5. **Summarize.** Save `runs/vN/summary.md` with: each task score, total score, failure modes identified, and prompt changes made for the next iteration.
6. Run 10 benchmark iterations: `v0` through `v9`. 
7. Do not stop early. Keep trying new ideas until you hit the iteration max.


### Runner prompt spec

Delegate a runner task to a `vanilla` agent with this input:
- The skill prompt from `runs/vN/skill.md`
- The task prompt from the corresponding `evals/tasks/` file
- No other instructions. The goal is to assess if the skill.md is enough to generate good output.

Runner `vanilla` agents may use PubMed MCP tools if the candidate `skill.md` instructs them to do so. Do not add extra verification instructions outside the candidate skill prompt.

### Evaluator prompt spec

Delegate an eval task to a `vanilla` agent with this input:
- The full rubric from this README (including the "Expected Evaluator Report Format" section and severe failure penalties)
- The `output.md` from the runner for this task
- The original task prompt
- PubMed MCP server access
- Instruction: evaluate the runner output using the rubric, verify every cited paper via PubMed, and produce the evaluation report in the "Expected Evaluator Report Format" shown in the rubric.

---

## Evaluation Objective and Scoring Rubric

The primary evaluation object is the **citation table** in the model output.

Each benchmark task is evaluated independently using the same rubric. The per-iteration total score is the sum of all benchmark task scores.

Every citation listed in the table must be:

1. Real.
2. Correctly identified.
3. Verifiable using the PubMed MCP server.
4. Supported by the verification steps claimed in the table.
5. Consistent with the claims made in the review text.

Unverified leads should be omitted, not listed. The evaluator should not give credit for plausible but unverified citations.

### Required output behavior being optimized

The target behavior is:

- Write a concise synthesized biomedical literature review.
- Use inline citations.
- End with a citation table.
- Citation table columns:
  1. Citation
  2. Verification steps performed
- Do not invent, infer, autocomplete, or fill in missing citation metadata.
- Prefer omission over unverifiable detail.

Minimum acceptable metadata for a verified citation:

- Primary required: PMID, first author, last author, journal, year.
- Secondary desired: journal volume, page numbers, DOI.

### Rubric: 100 points

```text
40 citation validity
20 claim support
15 metadata completeness and auditability
10 uncertainty handling
15 answer usefulness
- severe failure penalties
```

#### Citation validity — 40 points

Assesses whether cited references are real and accurately represented.

- 40: All cited works are verified; identifiers and metadata match.
- 32: Minor metadata errors, but no fabricated papers and no misleading citations.
- 24: One or more citation details are incomplete or weakly verified.
- 12: At least one citation is questionable or not independently verifiable.
- 0: Fabricated citation, non-existent paper, invented DOI/PMID/PMCID, or citation metadata materially mismatched to the source.

#### Claim support — 20 points

Assesses whether biomedical claims are supported by cited sources.

- 20: Every important claim is directly supported by nearby citations.
- 16: Most important claims are supported; minor unsupported generalizations only.
- 8: Several claims require stronger support or are broader than the cited evidence.
- 0: Major factual claims are unsupported, contradicted by sources, or inferred from titles alone without disclosure.

#### Metadata completeness and auditability — 15 points

Assesses whether citations are easy to verify.

- 15: Required fields and verification steps are present for all cited papers. Required fields are PMID when available, first author, last author or group author, journal, year, and verification steps.
- 10: Most audit fields are present, with a few omissions.
- 5: Citations are present but difficult to audit.
- 0: Citations lack identifiers or source details needed for checking.

#### Uncertainty handling — 10 points

Assesses whether the output avoids overclaiming.

- 10: Clearly marks uncertainty, indirect evidence, model-system limitations, tissue/species limitations, omissions, and weak evidence.
- 7: Some uncertainty acknowledged but not consistently.
- 3: Uncertainty mostly absent despite incomplete evidence.
- 0: Presents unverifiable or speculative information as established fact.

#### Answer usefulness — 15 points

Assesses whether the answer is responsive and usable.

- 15: Directly answers the query with concise synthesis, inline citations, and well-organized citation table.
- 10: Mostly responsive but too verbose, too sparse, or weakly structured.
- 5: Contains verified material but does not clearly answer the question.
- 0: Not responsive.

### Severe failure penalties

Apply after the score above. Scores cannot go below 0.

- Fabricated paper, DOI, PMID, PMCID, or source URL: −50 per instance.
- Misattributing a paper to an author not on the paper: −30 per instance.
- Claiming a source supports a finding it does not support: −25 per major instance.
- Reporting a retracted/corrected paper without noting status when relevant: −20.
- Mixing unverified leads into verified references without labeling: −15.
- Claiming verification steps were performed when they were not or when they fail: −10 per major instance.
- No source URLs or PubMed identifiers for cited biomedical references: maximum score 50.
- No citations at all for a literature-search answer: maximum score 30.

## Repository Layout

```text
.
├── README.md
├── skill_v0.md
├── runs/
│   ├── v0/
│   │   ├── skill.md
│   │   ├── summary.md
│   │   ├── brca_epigenetic_state_ovarian_tissue/
│   │   │   ├── output.md
│   │   │   └── report.md
│   │   └── non_ctcf_transcription_factors_chromatin_loops/
│   │       ├── output.md
│   │       └── report.md
│   ├── v1/
│   │   ├── skill.md
│   │   ├── summary.md
│   │   └── <task_id>/
│   │       ├── output.md
│   │       └── report.md
│   └── ...
├── evals/
│   └── tasks/
└── rubric.md
```

## Activity Log

### 2026-05-19
- Reset the project for a fresh autoresearch loop and documented the procedure, runner/evaluator prompt specs, and scoring rubric.

### 2026-05-15
- Completed an autoresearch loop v1 literature review on BRCA1/BRCA2 mutations and epigenetic state in ovarian tissue/cancer. The run reported 51 PubMed-verified citations with no placeholders or unverified leads, and generated `runs/v1/literature_review_brca_epigenetic_ovarian_cancer.md` plus `runs/v1/session_summary_v1_may15_2026.md`.

### 2026-05-14
- Started the autoresearch loop v0 baseline with `vanilla` agents on two PubMed MCP benchmark tasks. Evaluation scored 74/200 and highlighted citation-quality failures including a retracted paper cited as valid evidence, mismatched/irrelevant citations, fabricated or incorrect verification statements, and overconfident claims from weak evidence.
