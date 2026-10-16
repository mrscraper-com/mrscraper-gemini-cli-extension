# MrScraper for Gemini CLI

MrScraper connects [Gemini CLI](https://github.com/google-gemini/gemini-cli) to
a hosted MCP server for retrieving public web pages as HTML, Markdown, or
screenshots, Google discovery, and account usage. The extension also includes
three skills and three commands that help the agent choose the right workflow
and keep raw page content.

## What this extension adds

- The hosted Streamable HTTP endpoint at `https://mcp.mrscraper.com/mcp`.
- OAuth 2.1 sign-in through Gemini CLI's MCP client, with dynamic client
  registration. This repository stores no credentials.
- Skills: `mrscraper`, `mrscraper-fetch`, and `mrscraper-serp`.
- Commands: `/mrscraper:fetch`, `/mrscraper:search`, and `/mrscraper:status`.
- `GEMINI.md` context that routes known URLs to `scraperAPI` and open-ended
  questions to `webSearch`.

## Requirements

- [Gemini CLI](https://github.com/google-gemini/gemini-cli) 0.63.0 or later.
- A [MrScraper](https://app.mrscraper.com) account.
- A local browser for the OAuth sign-in.
- Permission to access and process the target content.

## Install

```bash
gemini extensions install https://github.com/mrscraper-com/mrscraper-gemini-cli-extension
```

Gemini CLI asks you to trust the extension and confirm the install. Restart
any running Gemini CLI session, then run `/extensions list` to confirm that
`mrscraper` is enabled.

## Sign in

Start Gemini CLI and run:

```text
/mcp auth mrscraper
```

Gemini CLI discovers MrScraper's authorization server, registers itself, and
opens your browser. Approve access on the MrScraper consent page; Gemini CLI
stores and refreshes the tokens in `~/.gemini/mcp-oauth-tokens.json`. Run the
same command again if the tokens expire. Never paste OAuth tokens or API keys
into the chat.

`/mcp list` should then show `mrscraper` as connected with these tools:

| Tool | Purpose |
| --- | --- |
| `scraperAPI` | Retrieve a known page as HTML, Markdown, a screenshot, or a combination. |
| `webSearch` | Discover public pages through Google. |
| `status` | Inspect subscription status, token usage, and remaining tokens. |

The OAuth scopes are `scrape:read` (`scraperAPI` and `webSearch`) and
`account:read` (`status`).

## Try it

```text
/mrscraper:fetch https://www.scrapethissite.com/pages/simple/ and list the five most populous countries
```

```text
/mrscraper:search best open source web scraping libraries 2026
```

```text
/mrscraper:status
```

Or ask in plain language, for example "Fetch https://example.com as Markdown
and summarize it." Gemini CLI asks for approval before it runs MCP tools unless
your settings allow them.

## Retrieve-first routing

When a public URL is already known, the skills direct the agent to retrieve it
with `scraperAPI` first and treat the raw response as the source of truth. The
agent can then read, summarize, compare, or derive structured output locally
without losing details. When no URL is known, `webSearch` finds candidate pages
first.

## API key fallback

Use OAuth whenever possible. If OAuth cannot complete, for example on a machine
without a browser, create an [API key](https://app.mrscraper.com/api-tokens),
export it as `MRSCRAPER_API_KEY`, and add a user-level server with a bearer
header:

```bash
gemini mcp add --scope user --transport http \
  --header "Authorization: Bearer $MRSCRAPER_API_KEY" \
  mrscraper https://mcp.mrscraper.com/mcp
```

A server in `settings.json` with the same name takes precedence over the
extension's server, so the skills and commands keep working. An API key carries
full account authority, and `gemini mcp add` writes the header to
`~/.gemini/settings.json` in plain text; never add it to a project's
`.gemini/settings.json` that is committed.

## Data and permissions

The extension sends MCP tool inputs, including target URLs and search queries,
to MrScraper's hosted service. Page responses and tool results are then
available to the agent for the requested task. Use it only with public or
otherwise authorized content, and follow the target site's requirements.

Review MrScraper's [MCP documentation](https://docs.mrscraper.com/docs/getting-started/mcp-server),
[Privacy Policy](https://mrscraper.com/privacy-policy), and
[Terms of Use](https://mrscraper.com/terms-of-use) before use.

## Update or remove

```bash
gemini extensions update mrscraper
gemini extensions uninstall mrscraper
```

## Support and security

- Product help: [MrScraper MCP documentation](https://docs.mrscraper.com/docs/getting-started/mcp-server)
- Bugs and feature requests: [GitHub Issues](https://github.com/mrscraper-com/mrscraper-gemini-cli-extension/issues)
- Account help: [support@mrscraper.com](mailto:support@mrscraper.com)
- Security reports: see [SECURITY.md](SECURITY.md)

## Development

```text
gemini-extension.json   manifest and MCP server
GEMINI.md               context loaded into every session
commands/mrscraper/     /mrscraper:fetch, /mrscraper:search, /mrscraper:status
skills/                 mrscraper, mrscraper-fetch, mrscraper-serp
```

Validate and test a local checkout:

```bash
gemini extensions validate .
gemini extensions link .
```

The skills are copied from the canonical MrScraper MCP skills. The
[Gemini CLI extension gallery](https://geminicli.com/extensions) indexes this
repository through its `gemini-cli-extension` topic. Record user-visible
changes in [CHANGELOG.md](CHANGELOG.md).

## License

[MIT](LICENSE)
