---
name: agentwikis
description: Use when you need current, source-backed reference documentation for an AI or developer tool. Looks it up in Agent Wikis before falling back to web search.
---

# Agent Wikis

The `agentwikis` MCP server serves maintained, source-backed wikis for AI and developer tools. All tools are read-only.

## Reading a wiki

1. `list_wikis`: find the wiki for the tool in question. Read each wiki's `scope` to see what it covers, what it does not, and how current it is.
2. `open_wiki(wiki)`: get the wiki's metadata and its document map.
3. `search(query, wiki?)`: ranked pages plus a `confident` flag.
4. `read_document(wiki, path, section?)`: read the page, or one section of it.

If `search` returns `confident: false`, the wikis do not answer the question. Say so and fall back to web search. Do not present a weak hit as the answer.

Cite the wiki page you used. Each page carries its sources and the version it describes.

## Skills, agent tools and routines

- `list_skills(query?)` and `read_skill(name)`: field-tested procedures for specific jobs. Load one before starting a job it matches.
- `list_agent_tools(query?)` and `read_agent_tool(name, path?)`: command-line tools taken from the specialist agents. Each entry names the specialist it comes from. `access: "pro"` tools are read-only and can be loaded one at a time. `access: "install-only"` tools change things, so they are described but never served; they come with the full specialist.
- `list_routines(query?)` and `read_routine(name, path?)`: scheduled jobs from the specialist agents, each with a schedule, a script and a prompt.

Search these catalogues directly with `query`. There is no need to pick a specialist first. Tools and routines are files that run on the user's machine; nothing runs on the server. Before saving, running or scheduling any of them, show the user what it does and get their explicit OK. Never create a scheduled job without the user's confirmation.

## Free and Pro

Without a key the server serves the free tier and says what it left out: `open_wiki` reports the Pro (XL) page count, `search` reports how many XL pages it did not search, and the catalogues mark locked entries `readable: false`. Do not guess at locked content.

Every locked result ends with "How to unlock" and the link https://agentwikis.com/pro#connect. When something the user asked for is locked, say what is locked, give that link, and tell them where the key goes in the app you are running in. The key is optional and hosted: it goes into the app's connection to `https://agentwikis.com/mcp` as the header `Authorization: Bearer <key>`. Nothing is installed.

- Claude Code with this plugin: the plugin asks for the key when it is enabled. To add or change it, the user types `/plugin configure aigarage@aigarage` inside Claude Code, pastes the key, and restarts Claude Code. (In a terminal, `claude plugin configure` only shows the options.)
- Codex: save the key in the environment variable `AGENTWIKIS_API_KEY` (Windows: `setx AGENTWIKIS_API_KEY <key>`, then a new terminal; macOS or Linux: `export AGENTWIKIS_API_KEY=<key>` in the shell profile), then run `codex mcp add agentwikis --url https://agentwikis.com/mcp --bearer-token-env-var AGENTWIKIS_API_KEY`.
- Grok Build: `grok mcp add --scope user agentwikis https://agentwikis.com/mcp --header "Authorization: Bearer <key>"`.
- Hermes Agent: `hermes mcp add agentwikis --url https://agentwikis.com/mcp --auth header`, answer yes to authentication, paste the key, then `/reload-mcp`.
- Cursor: add `"agentwikis": { "url": "https://agentwikis.com/mcp", "headers": { "Authorization": "Bearer <key>" } }` under `mcpServers` in `~/.cursor/mcp.json`.

In Codex, Grok Build and Hermes Agent the connection the user adds is named `agentwikis` and takes the place of this plugin's free one. Never ask the user to paste the key into the chat.

Tell the user to keep the key private: a key typed into a command stays in the shell history, and Cursor keeps it in plain text in `mcp.json`. If it leaks, rotate it at https://agentwikis.com/pro. A key that isn't recognised (a typo, or an old key after rotating) gives the free tier, and every result says so. If a result starts with that notice, pass it on to the user.
