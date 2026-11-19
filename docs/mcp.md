# MCP endpoint

`https://mydramadb.com/mcp` is a remote MCP server using Streamable HTTP, with no authentication. Most people
should add it through their assistant's settings by following [mydramadb.com/connect](https://mydramadb.com/connect)
and let the assistant's own MCP client do the work. The examples below show what that client sends and gets
back. Answers are trimmed to a few fields; `...` marks what was left out.

Your assistant should call `tools/list` and read the current tool schemas rather than guess arguments. Tool
arguments are sent to MyDramaDB, so do not include private or sensitive information.

## Find a drama with `search_dramas`

Request (after the usual `initialize` handshake):

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/call",
  "params": { "name": "search_dramas", "arguments": { "q": "The Glory", "page": 1 } }
}
```

Answer (`structuredContent`, trimmed):

```json
{
  "sourcePageUrl": "https://mydramadb.com/browse?q=The+Glory",
  "pagination": { "page": 1, "pageSize": 24, "total": 5, "pages": 1 },
  "items": [
    {
      "kind": "show",
      "show": {
        "slug": "the-glory",
        "names": { "preferred": "The Glory", "...": "..." },
        "year": { "state": "known", "value": 2022, "...": "..." },
        "workType": "television_series",
        "...": "..."
      }
    },
    { "kind": "show", "show": { "slug": "the-glory-part-2", "...": "..." } }
  ],
  "...": "..."
}
```

Use the returned `slug` with the other tools.

## Look up one episode with `get_episode`

```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "tools/call",
  "params": { "name": "get_episode", "arguments": { "slug": "the-glory", "season": 1, "number": 1 } }
}
```

Answer (trimmed). With the default spoiler setting, the episode title and description are withheld:

```json
{
  "sourcePageUrl": "https://mydramadb.com/shows/the-glory#episode-s1-e1",
  "episode": {
    "season": 1,
    "number": 1,
    "title": { "state": "missing", "reason": "withheld" },
    "airDate": { "state": "known", "value": "2022-12-30", "...": "..." },
    "runtimeMinutes": { "state": "known", "value": 47, "...": "..." },
    "description": { "state": "missing", "reason": "withheld" },
    "...": "..."
  },
  "...": "..."
}
```

## When a limit is reached

Each client may make 30 tool calls a day and 150 a week. Connecting and listing tools are not counted. After
that, a tool call still gets an answer, but it is an error result:

```json
{
  "jsonrpc": "2.0",
  "id": 4,
  "result": {
    "isError": true,
    "content": [
      {
        "type": "text",
        "text": "{\"code\":\"allowance_used\",\"message\":\"This connection has used its free MyDramaDB allowance for today; the full catalogue, search and Ask AI are free on https://mydramadb.com.\"}"
      }
    ]
  }
}
```

The weekly limit says "this week" instead of "today". Asking `search_dramas` for a page after page 5, or
`get_drama_updates` for a page after page 10, returns the code `page_limit` in the same way.

Do not retry in a loop. Open [mydramadb.com](https://mydramadb.com) to keep searching, or use Ask AI in the
site header.
