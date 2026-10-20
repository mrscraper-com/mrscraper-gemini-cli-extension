# MrScraper

The `mrscraper` MCP server retrieves web pages and Google results through
MrScraper's hosted service at `https://mcp.mrscraper.com/mcp`.

## Tools

| Tool | Use it to |
| --- | --- |
| `scraperAPI` | Retrieve a known URL as HTML (default), Markdown, a screenshot, or a combination. |
| `webSearch` | Discover pages through Google from query text or a Google search URL. |
| `status` | Check subscription status, token usage, and remaining tokens. |

## Routing

- A known public URL: call `scraperAPI` first and treat the raw response as the
  source of truth. Ask for `markdown: true` when the user wants a summary or
  quotes. Load the `mrscraper-fetch` skill for options such as
  `browser_rendering`, `wait_for_selector`, `geo_code`, and `screenshot`.
- No URL yet: call `webSearch` with `raw: false` and `format: "json"`; organic
  results are in `data.data.organic`. Load the `mrscraper-serp` skill.
- Quota questions or before a large batch: call `status`.

Every response has `status_code`, `data`, and `headers`. For `scraperAPI`,
`headers["x-status-code"]` is the target page's status; check it before using
`data`.

## Authentication

The server uses OAuth 2.1. If a tool reports that authentication is required,
tell the user to run `/mcp auth mrscraper`. Never ask the user to paste API
keys or tokens into the chat.

Use MrScraper only with public or otherwise authorized content.
