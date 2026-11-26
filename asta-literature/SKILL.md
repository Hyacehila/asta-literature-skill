---
name: asta-literature
description: Use Ai2's Asta Scientific Corpus MCP to look up papers or authors, follow scholarly citations, find passages supporting a claim, or prepare a literature review with traceable sources.
license: Apache-2.0
---

# Asta literature

Answer in the user's language. Use the tools from the configured Asta Scientific Corpus server (the setup below names it `asta`). Read its current tool schemas when names, fields or limits are uncertain. If the tools are missing, tell the user that this skill requires the remote MCP server and link the setup in their language. [English setup](https://github.com/Hyacehila/asta-literature-skill/blob/main/README.md#connect-asta) · [中文配置](https://github.com/Hyacehila/asta-literature-skill/blob/main/README.zh-CN.md#配置-asta)

## Choose the first call

| User has or wants | Tool and decision |
|---|---|
| DOI, arXiv, CorpusId or Semantic Scholar paper ID | Use `get_paper`. Request the fields the question needs, for example `title,authors,year,abstract,url,externalIds`. An abstract is optional. |
| Several known IDs | Use `get_paper_batch` with the array `ids`. Match records using paper IDs or verified external IDs. Flag unmatched inputs, including duplicates or different IDs for the same work. |
| A known title | Use `search_paper_by_title`, then verify title, authors and year before following citations. Ask for a distinguishing detail when plausible matches would change the answer. |
| A topic | Use `search_papers_by_relevance` with a concise keyword query. Start with a small result set. Publication dates and venues can narrow this search. |
| Earlier work cited by a paper | Request `references` with `get_paper`. Resolve selected references by ID for more detail. |
| Later work citing a paper | Use `get_citations` and read the nested `citingPaper`. A citation establishes a link. Check the citing text before claiming an extension, replication or disagreement. |
| An author or their publications | Use `search_authors_by_name` to resolve namesakes by affiliation, identifiers or known papers. Pass the chosen `authorId` to `get_author_papers` using `paper_fields`. |
| Evidence for a method, condition or claim | Use `snippet_search`. Supply a focused query, a small `limit` and, when known, a comma-separated `paper_ids` string. Record the source paper and available section. |

For a simple lookup, return the requested metadata and a paper link. Label author abstracts, translated abstracts and your own summaries distinctly. For a complex question, give each substantive claim a source and describe how far the evidence supports it.

## Read only what the task needs

- Consult [MCP tool details](references/tools.md) for argument names, examples, response shapes, filters and failure handling.
- Use [Topic searches and follow-ups](references/discovery.md) for selection criteria, multi-round search, citation expansion, deduplication and evidence-based screening.
- Use [Reviews and citation checks](references/reviews.md) to synthesize several papers, update an existing report, produce BibTeX or verify theorem assumptions.

If the service reports a credential error, direct the user to check the local key mapping and restart the client. For HTTP 429, respect any retry delay, reduce concurrency and use the quota in the user's approval email. For an empty search, refine the query or relax a filter and state the remaining gap. Keep credentials in the MCP client's configuration environment. [Service terms and attribution](NOTICES.md)
