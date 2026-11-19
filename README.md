# MyDramaDB MCP

[MyDramaDB](https://mydramadb.com) helps you find Korean dramas to watch and follow the shows you love.
You can find Korean dramas by title, cast, genre and year, and each show has a page with its story, cast, crew
and episode details. Browsing and searching the site needs no account.

This repository documents the small public MCP endpoint that lets an AI assistant look up a drama, its cast or
one episode and link to the page on MyDramaDB. It is deliberately small and rate limited. For browsing,
searching and asking questions, use the website.

## The MCP endpoint

| | |
| --- | --- |
| Address | `https://mydramadb.com/mcp` |
| Transport | Streamable HTTP |
| Authentication | None. No account, API key or token |
| Tools | Nine, all read-only |
| Setup guide | [mydramadb.com/connect](https://mydramadb.com/connect) |

Add it to your assistant as a remote MCP server with no authentication. The
[connect page](https://mydramadb.com/connect) has the exact steps for Codex, Claude Code, Cursor and
VS Code / GitHub Copilot.

| Tool | What it does |
| --- | --- |
| `search_dramas` | Find drama titles, aliases, remakes, people or shows matching genre/year filters. |
| `get_drama` | Get published drama facts and sources. Plot prose is withheld by default. |
| `get_cast` | Find cast, character and creator credits, optionally filtered. |
| `get_person` | Get published actor or creator information and a paginated filmography. |
| `list_episodes` | List episode metadata and approved evidence availability. |
| `get_episode` | Get exact season and episode metadata, schedule and approved description within the spoiler ceiling. |
| `search_episode_evidence` | Retrieve reviewed paraphrased evidence for a question about one episode. |
| `get_drama_updates` | Get published catalogue changes by category and page. |
| `search_drama_mentions` | Search published drama titles and person names for the ChatGPT Desktop composer picker. |

Every answer carries a `sourcePageUrl`, the page on mydramadb.com that the answer comes from. Examples are in
[docs/mcp.md](docs/mcp.md).

## Limits

| | Per day | Per week |
| --- | --- | --- |
| MCP tool calls (`tools/call` and `resources/read`) | 30 | 150 |

- Limits are per client. Days start at 00:00 UTC and weeks start on Monday at 00:00 UTC.
- Connecting and listing tools (`initialize`, `tools/list` and similar) are not counted.
- Search results stop after the first 5 pages (24 results a page). The updates list stops after the first
  10 pages.
- When the allowance is used up, a tool call returns an error result with the code `allowance_used`. Asking
  for a page past the last one available returns the code `page_limit`.

The exact answers are shown in [docs/mcp.md](docs/mcp.md).

## For anything more, use the site

The website has everything the endpoint leaves out, free and without an account:

- [Search and browse](https://mydramadb.com/browse) every drama and person
- [The schedule](https://mydramadb.com/schedule) of what airs this week
- Ask AI in the header of [mydramadb.com](https://mydramadb.com), the site's own assistant, for questions about
  a story or what to watch next

## Report a problem

Use the [feedback page](https://mydramadb.com/feedback) on the site. Please include the tool you called and the
answer you got.

## Terms and licence

Use of the MCP endpoint is under the [MyDramaDB Terms of Service](https://mydramadb.com/terms).
The Terms do not allow bulk copying, scraping, mirroring or republishing the catalogue, or using it as AI
training data.

<!-- TODO before publishing: add the sentence permitting use of the public MCP endpoint within its limits,
     once the Terms of Service carry that permission. Do not publish this repository until then. -->

The text of this repository is Copyright 2026 MyDramaDB. All rights reserved. The catalogue data and the
website are not licensed by this repository.

MCP Registry name: `io.github.MyDramaDB/mydramadb`
