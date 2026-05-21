Rubric for No-Hallucination Biomedical Literature Review

This rubric is self-contained so the benchmark can be run with a model/agent plus PubMed access.

## Evaluator Role

You are an independent evaluator for a minimal biomedical literature-search skill that avoids hallucinated citations, invented metadata, and unsupported biomedical claims.

Your task is to evaluate the model output for one benchmark task. The **citation table is the primary evaluation object**, but the review text must also be checked for claim support and overclaiming.

Use only PubMed/NCBI-accessible information for verification. If DOI or PMCID information is present in PubMed/NCBI records, it may be checked via the corresponding DOI or PMC URL. Do not use web search, Crossref, Semantic Scholar, Google Scholar, publisher pages, or other external databases for this Skill 1 evaluation unless the benchmark instructions are explicitly changed.

## Hard Verification Gate

- Treat verification as a required gate before scoring a citation as valid.
- Never infer, invent, autocomplete, or fill in missing citation metadata from memory or plausibility.
- If a claimed citation, metadata field, or source-supported finding cannot be verified with PubMed/NCBI-accessible evidence, mark it as unverified, partial, mismatched, or unsupported as appropriate.
- Do not give credit for plausible but unverified citations.
- A citation may be marked `Verified` only when the paper is real and the key claimed metadata match authoritative PubMed/NCBI-accessible records.

## Required Citation Verification Checks

For every cited paper in the model output, check and report:

1. **PubMed existence / PMID**
   - Confirm that the PMID exists if a PMID is provided.
   - If no PMID is provided, search PubMed by title/authors to determine whether the paper has a PMID.
   - Mark invented, non-resolving, or mismatched PMIDs as severe citation failures.

2. **Title**
   - Confirm the title matches the cited paper.
   - Minor punctuation/capitalization differences are acceptable; material title mismatch is not.

3. **First and last author**
   - Confirm first and last author names match the PubMed record.
   - For consortium/group-authored papers, verify the group author and note that normal first/last author fields may not apply.
   - If the output makes an author-specific claim, confirm the named author is actually on the paper.

4. **Journal/venue and year**
   - Confirm journal/venue and publication year.
   - Note date ambiguity where relevant (epub vs print year), but do not penalize minor date granularity issues unless misleading.

5. **DOI, PMCID, and other identifiers when present**
   - If the output provides a DOI, confirm it matches the PubMed/NCBI record or DOI link available from PubMed.
   - If the output provides a PMCID, confirm it resolves to the same article in PMC.
   - If DOI or PMCID is unavailable, mark it as unavailable rather than guessing.

6. **Publication type/status**
   - Check PubMed publication types and available notes for review/article/editorial/conference abstract status.
   - Check for retraction, correction, erratum, expression of concern, or similar status when PubMed flags it.
   - Penalize unnoted use of retracted/corrected papers when relevant to the claim.

7. **Claim/finding support**
   - Confirm that each important claim in the review text is supported by nearby cited sources.
   - Use the PubMed title/abstract, publication types, and available NCBI/PMC text when accessible.
   - Do not treat a finding as supported from title alone unless the output explicitly limits the claim to title-level evidence.
   - Flag claims broader than the cited evidence, tissue/model-system overgeneralizations, or claims contradicted by the cited record.

8. **Verification steps claimed by the model**
   - Check whether the model's citation-table verification steps are true.
   - If the model claims it verified DOI/PMCID/authors/status/support but those checks fail or are absent, penalize metadata auditability and/or citation validity.

## Expected Evaluator Report Format

Use this structure:

```text
task_id:
skill_version:
run_id:
overall_score:

citation_checks:
  - cited_reference:
    status: Verified | Partially verified | Unverified | Fabricated | Mismatched
    source_urls:
      - https://pubmed.ncbi.nlm.nih.gov/<PMID>/
      - https://www.ncbi.nlm.nih.gov/pmc/articles/<PMCID>/  # if applicable
      - https://doi.org/<DOI>  # if applicable and available from PubMed/NCBI record
    specific_verifications_performed:
      - PubMed/PMID checked
      - title checked
      - first/last author checked
      - journal/year checked
      - DOI checked / unavailable
      - PMCID checked / unavailable
      - publication type/status checked
      - claim support checked against abstract/PMC text
    issues:

claim_checks:
  - claim:
    cited_source:
    support_status: Supported | Partially supported | Unsupported | Contradicted
    notes:

rubric_scores:
  citation_validity:
  claim_support:
  metadata_auditability:
  uncertainty_handling:
  answer_usefulness:

penalties:
final_notes:
```

## Scoring Rubric: 100 points

### Citation validity — 40 points

Assesses whether cited references are real and accurately represented.

- 40: All cited works are verified; identifiers and metadata match.
- 32: Minor metadata errors, but no fabricated papers and no misleading citations.
- 24: One or more citation details are incomplete or weakly verified.
- 12: At least one citation is questionable or not independently verifiable.
- 0: Fabricated citation, non-existent paper, invented DOI/PMID/PMCID, or citation metadata materially mismatched to the source.

### Claim support — 20 points

Assesses whether biomedical claims are supported by cited sources.

- 20: Every important claim is directly supported by nearby citations.
- 16: Most important claims are supported; minor unsupported generalizations only.
- 8: Several claims require stronger support or are broader than the cited evidence.
- 0: Major factual claims are unsupported, contradicted by sources, or inferred from titles alone without disclosure.

### Metadata completeness and auditability — 15 points

Assesses whether citations are easy to verify.

- 15: Required fields and verification steps are present for all cited papers. Required fields are PMID when available, first author, last author or group author, journal, year, and verification steps.
- 10: Most audit fields are present, with a few omissions.
- 5: Citations are present but difficult to audit.
- 0: Citations lack identifiers or source details needed for checking.

### Uncertainty handling — 10 points

Assesses whether the output avoids overclaiming.

- 10: Clearly marks uncertainty, indirect evidence, model-system limitations, tissue/species limitations, omissions, and weak evidence.
- 7: Some uncertainty acknowledged but not consistently.
- 3: Uncertainty mostly absent despite incomplete evidence.
- 0: Presents unverifiable or speculative information as established fact.

### Answer usefulness — 15 points

Assesses whether the answer is responsive and usable.

- 15: Directly answers the query with concise synthesis, inline citations, and well-organized citation table.
- 10: Mostly responsive but too verbose, too sparse, or weakly structured.
- 5: Contains verified material but does not clearly answer the question.
- 0: Not responsive.

## Score Calculation

Compute the overall score as:

1. Sum the rubric scores: `citation_validity + claim_support + metadata_auditability + uncertainty_handling + answer_usefulness`.
2. Apply any severe-failure penalties below.
3. Do not exceed 100; do not go below 0.

## Severe Failure Penalties

Apply after score calculation. Scores cannot go below 0.

- Fabricated paper, DOI, PMID, PMCID, or source URL: −50 per instance.
- Misattributing a paper to an author not on the paper: −30 per instance.
- Claiming a source supports a finding it does not support: −25 per major instance.
- Reporting a retracted/corrected paper without noting status when relevant: −20.
- Mixing unverified leads into verified references without labeling: −15.
- Claiming verification steps were performed when they were not or when they fail: −10 per major instance.
- No source URLs or PubMed identifiers for cited biomedical references: maximum score 50.
- No citations at all for a literature-search answer: maximum score 30.

## Notes for Multi-Task Iterations

Evaluate each benchmark task independently using this rubric. Each task receives a score out of 100. The iteration total score is the sum of all task scores.
