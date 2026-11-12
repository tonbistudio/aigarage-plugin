# AI Garage plugin

Search AI Garage's YouTube videos, newsletter and X posts, plus the Agent Wikis reference library, from inside your agent.

## What you get

Two hosted MCP servers (nothing runs on your machine):

- `aigarage` at `https://agentwikis.com/content/aigarage/mcp`. No key. Tools: `search_content`, `get_item`, `list_latest`. Videos come back with chapter timestamp links, newsletter issues with their page link, X posts as links only.
- `agentwikis` at `https://agentwikis.com/mcp`. Free without a key. Tools: `list_wikis`, `open_wiki`, `search`, `read_document`, `list_skills`, `read_skill`, `list_agent_tools`, `read_agent_tool`, `list_routines`, `read_routine`.

Three skills:

- `aigarage`: when to check AI Garage content and how to cite it.
- `agentwikis`: how to look something up in Agent Wikis.
- `skill-from-content`: turn a video or newsletter issue into a local skill in which every step cites its source. It shows you the skill before saving it.

## Install

Claude Code:

    claude plugin marketplace add tonbistudio/aigarage-plugin
    claude plugin install aigarage@aigarage

Codex:

    codex plugin marketplace add tonbistudio/aigarage-plugin
    codex plugin add aigarage@aigarage

Grok Build:

    grok plugin install tonbistudio/aigarage-plugin

Hermes:

    hermes plugins install tonbistudio/aigarage-plugin --enable

Cursor reads the same folder (root `plugin.json`, `skills/`, `mcp.json`).

Any other MCP client can add the two server URLs above directly.

## Agent Wikis Pro

Optional. Everything works on the free tier without a key. A key from https://agentwikis.com/pro unlocks XL wiki pages, Pro skills, agent tools and routines. It goes into the app's connection to `https://agentwikis.com/mcp`; nothing is installed. The same steps are on https://agentwikis.com/pro#connect.

- Claude Code: the plugin asks for the key when you enable it. To add or change it later, type `/plugin configure aigarage@aigarage` inside Claude Code, paste the key, and restart Claude Code.
- Codex: save the key in the environment variable `AGENTWIKIS_API_KEY` (Windows: `setx AGENTWIKIS_API_KEY <key>`, then open a new terminal; macOS or Linux: add `export AGENTWIKIS_API_KEY=<key>` to your shell profile), then run
  `codex mcp add agentwikis --url https://agentwikis.com/mcp --bearer-token-env-var AGENTWIKIS_API_KEY`.
- Grok Build: `grok mcp add --scope user agentwikis https://agentwikis.com/mcp --header "Authorization: Bearer <key>"`.
- Hermes Agent: `hermes mcp add agentwikis --url https://agentwikis.com/mcp --auth header`, answer yes to authentication, and paste the key when asked.
- Cursor: add `"agentwikis": { "url": "https://agentwikis.com/mcp", "headers": { "Authorization": "Bearer <key>" } }` under `mcpServers` in `~/.cursor/mcp.json`.

In Codex, Grok Build and Hermes Agent the connection you add is named `agentwikis` and takes the place of the plugin's free one.

Keep the key private: a key typed into a command stays in the shell history, and Cursor keeps it in plain text in `mcp.json`. If it leaks, rotate it at https://agentwikis.com/pro. A key that isn't recognised (a typo, or an old key after rotating) gives the free tier, and every result says so.

Agent tools and routines run on your machine, never on the server. The agent shows you what one does and asks before saving, running or scheduling it. Each file it loads comes with a sha256.

## Data

The plugin installs no programs and runs nothing on your machine. It connects to two servers on agentwikis.com: your agent's searches go there, and the Pro key, if you add one, goes only to `https://agentwikis.com/mcp`. IP addresses are used in memory for rate limiting and are not stored; wiki searches are kept only as anonymous counts of the query text. Full policy: https://agentwikis.com/privacy

## Terms

See `TERMS.md`.
