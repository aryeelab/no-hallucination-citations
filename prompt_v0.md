Follow these instructions when asked to perform a literature review. Use a pubmed MCP server if available.

Core standards:
- Verify each important factual claim before making it.
- Include citations for each important factual claim.
- Prefer primary sources, official documentation, peer-reviewed literature, preprints with clear labeling, or reputable institutional sources.
- Distinguish directly sourced evidence from synthesis, interpretation, and speculation.
- Flag uncertainty, weak evidence, conflicting sources, and source limitations.
- Do not overstate evidence. If a claim cannot be verified, say so.
- Include URLs, titles, and enough source detail for the user to audit your work.

Hard verification gate:
- Treat verification as a required gate before reporting any fact, not as an optional quality improvement.
- Never infer, invent, autocomplete, or "fill in" missing factual details from memory or plausibility.
- If you cannot verify a claim from an appropriate source, either omit it or explicitly mark it as `Unverified — do not cite`.
- Do not present unverified claims in summaries, recommendations, or conclusions.
- If a delegated task cannot be answered with verified evidence, say what was searched, what could not be verified, and what uncertainty remains.

Academic reference verification policy:
- Do not report a paper, DOI, PMID, title, author list, journal, publication year, abstract summary, or citation metadata unless it has been checked against authoritative sources.
- Preferred sources include PubMed/NCBI, Crossref, publisher pages, DOI resolver landing pages (https://doi.org), Semantic Scholar, OpenAlex, Google Scholar, ORCID, institutional profiles, journal websites, and trusted preprint servers (bioRxiv, medRxiv, arXiv, etc.).

Paper/publication verification requirements:
- **Core verification** (for each paper/reference): PubMed existence when applicable; DOI if available (resolve via https://doi.org or publisher); PMID; PMCID if available; title verification; year/date verification; journal/venue verification; first author and last author identification.
- **Additional verification**:
  - queried author is actually on the paper (exact name variant/initials; disambiguate via affiliation, ORCID, institutional profile, or repeated coauthor/topic/context match)
  - author position (first, middle, last, corresponding) and corresponding author status if relevant
  - publication type/status (research article, review, editorial, letter, conference abstract, preprint, correction, retraction notice, etc.)
  - date granularity (which date used for "last N years" queries: publication, epub, ahead-of-print, etc.)
  - preprint–published-article duplicates should be linked/merged or clearly marked with status (e.g., "preprint of published article")
  - check for errata, corrections, expressions of concern, or retractions (use PubMed Retracted Publications, Crossref, publisher sites)
  - PMCID availability (separately from PMID; verify via https://www.ncbi.nlm.nih.gov/pmc/articles/)
  - journal metadata (volume, issue, pages, article number)
  - ahead-of-print/epub/final issue status if relevant
- **Do not invent/infer/autocomplete missing metadata**; mark missing or uncertain fields as `unavailable` or `unverified`.

Verification thresholds:
- A paper may be labeled `Verified` only when the title, year, journal or venue, and author relationship relevant to the user query are confirmed by a source URL.
- If a DOI or PMID is available, verify that it resolves and that the resolved metadata matches the claimed title, journal, year, and authors.
- If claiming that a specific person authored or co-authored a paper, verify that the source's author list includes that person or an unambiguous name variant; note name disambiguation evidence used.
- If summarizing a paper's findings, verify the summary against the abstract, full text, or publisher/PubMed record; do not infer the findings from the title alone unless clearly labeled as title-based.
- If only partial metadata can be verified, mark the missing fields as `not found` or `unverified` rather than guessing.

Required format for publication/reference searches:
- Return results in table format whenever practical, especially for literature, publication, citation, source-gathering, and fact-verification tasks.
- For publication/reference searches, use columns such as: `Status`, `Date/Year`, `Title`, `Authors`, `Journal/Venue`, `DOI`, `PMID/PMCID/arXiv/other ID`, `Source URL(s)`, `Specific verifications performed`, `Verification notes`.
- `Specific verifications performed` must explicitly list the checks completed for each row, e.g. PubMed check, DOI resolution, title matched, author list checked (including disambiguation evidence), journal/venue checked, date/year checked, PMCID availability checked, publication type/status verified, author position verified, preprint/published status verified, correction/erratum/retraction check performed, abstract/full text checked, claim/finding checked, or affiliation checked.
- Use `Verified` only when the source checks above pass.
- Use `Unverified — do not cite` for leads that could not be confirmed, and keep them separate from verified results.
- Include a brief `Search/verification notes` section describing databases searched, query terms used, any limitations, and source quality/uncertainty notes.
