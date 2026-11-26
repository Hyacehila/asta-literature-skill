# MCP tool details

The authenticated server advertised eight tools on 2026-11-26. All eight were called with public paper or author queries. The names below are server tool names. Codex can display them with an MCP namespace. Prefer the connected server's schema if it changes. [Ai2 tool reference](https://allenai.org/asta/resources/mcp)

Every argument listed after the required one is optional. An empty filter string means the filter is unset. Tool results may contain several MCP content blocks, each with JSON serialized in its text. Read all returned blocks, including the batch results.

| Tool | Required argument | Optional arguments and observed defaults | Result to read |
|---|---|---|---|
| `get_paper` | `paper_id` string | `fields="title"` | Paper object with `paperId` and requested metadata. Use `externalIds` to resolve a DOI or arXiv ID. |
| `get_paper_batch` | `ids` array of strings | `fields="title"` | Paper records. Two valid IDs produced two content blocks. Reconcile by identifiers. |
| `get_citations` | `paper_id` string | `fields="title"`, `limit=100`, `publication_date_range=""` | Citation records wrap the result in `citingPaper`. The date filter applies to citing papers. |
| `search_authors_by_name` | `name` string | `fields="name"`, `limit=100` | Author records contain `authorId`. Request `affiliations,externalIds,papers` for disambiguation as needed. |
| `get_author_papers` | `author_id` string | `paper_fields="title"`, `limit=1000`, `publication_date_range=""` | Paper records for the chosen author. Set a small explicit limit for exploration. |
| `search_papers_by_relevance` | `keyword` string | `fields="title"`, `limit=50`, `publication_date_range=""`, `venues=""` | Ranked paper records. Relevance to the search expression still needs screening against the user's question. |
| `search_paper_by_title` | `title` string | `fields="title"`, `publication_date_range=""`, `venues=""` | Paper match with `matchScore` when available. Confirm identity against bibliographic details. |
| `snippet_search` | `query` string | `limit=20`, `venues=""`, `paper_ids=""`, `inserted_before=""` | Read hits in `data`. Each may include `paper`, `snippet.text`, `snippet.section`, `snippet.snippetKind` and `score`. Keep the link between passage and paper. |

## Example arguments

These examples use the authenticated interface. Field projections can be narrowed for everyday use.

| Tool | Arguments |
|---|---|
| `get_paper` | `{"paper_id":"ARXIV:2510.21652","fields":"title,year,authors,abstract,url,externalIds,citationCount,references"}` |
| `get_paper_batch` | `{"ids":["ARXIV:2510.21652","ARXIV:1706.03762"],"fields":"title,year,url"}` |
| `get_citations` | `{"paper_id":"ARXIV:1706.03762","fields":"title,year,url","limit":1,"publication_date_range":"2021:2022"}` |
| `search_authors_by_name` | `{"name":"Ashish Vaswani","fields":"name,url","limit":1}` |
| `get_author_papers` | `{"author_id":"<authorId returned by search>","paper_fields":"title,year,url","limit":1}` |
| `search_papers_by_relevance` | `{"keyword":"conformal prediction","fields":"title,year,url","limit":2,"publication_date_range":"2022:2024"}` |
| `search_paper_by_title` | `{"title":"Attention Is All You Need","fields":"title,year,url"}` |
| `snippet_search` | `{"query":"attention mechanisms","paper_ids":"ARXIV:1706.03762","limit":1}` |

## Filters and interpretation

- Use an inclusive publication range such as `2022:2024` or `2022:`. To find passages in papers published during a period, first select papers by publication date and then pass their IDs to snippet search. The service accepts up to 100 comma-separated IDs in `paper_ids`.
- `inserted_before` is an exclusive cutoff for insertion into the snippet index. A title match score describes bibliographic matching. A snippet score describes passage retrieval. Neither measures the quality of the science.
- Check `snippetKind` before calling a passage body evidence. Record an available section title. Snippet character offsets have no page-number meaning. Coverage varies by paper.
- Unknown IDs, missing abstracts and empty lists are possible. Report what resolved. Lower the page size or retry only the failed subset on transient errors. Honor server retry instructions on 429. A timeout or a failed call cannot establish an empty result set.
