---
name: agentwikis
description: Use when you need current, source-backed reference documentation for an AI or developer tool. Looks it up in Agent Wikis before falling back to web search.
---

# Agent Wikis

The `agentwikis` MCP server serves maintained, source-backed wikis for AI and developer tools. All of its MCP tools only read; nothing runs on the server.

## Reading a wiki

1. `list_wikis`: find the wiki for the tool in question. Read each wiki's `scope` to see what it covers, what it does not, and how current it is.
2. `open_wiki(wiki)`: get the wiki's metadata and its document map.
3. `search(query, wiki?)`: ranked pages plus a `confident` flag.
4. `read_document(wiki, path, section?)`: read the page, or one section of it.

If `search` returns `confident: false`, the wikis do not answer the question. Say so and fall back to web search. Do not present a weak hit as the answer.

Cite the wiki page you used. Each page carries its sources and the version it describes.

## Skills, agent tools and routines

- `list_skills(query?)` and `read_skill(name)`: field-tested procedures for specific jobs. Load one before starting a job it matches.
- `list_agent_tools(query?)` and `read_agent_tool(name, path?)`: command-line tools taken from the specialist agents. Each entry names the specialist it comes from. `access: "pro"` tools are small and read-only: `read_agent_tool` serves their files. `access: "download"` tools are bigger or change things: with Pro, `read_agent_tool` gives their README and a download link (valid 15 minutes) with a sha256; download it with curl, check the sha256, unpack it and follow its README. If a link has expired, call `read_agent_tool` again.
- `list_routines(query?)` and `read_routine(name, path?)`: scheduled jobs from the specialist agents, each with a schedule, a script and a prompt.

Search these catalogues directly with `query`. There is no need to pick a specialist first. Tools and routines are files that run on the user's machine; nothing runs on the server. Before saving, running or scheduling any of them, show the user what it does and get their explicit OK. Never create a scheduled job without the user's confirmation.

## Free and Pro

Without a key the server serves the free tier and says what it left out: `open_wiki` reports the Pro (XL) page count, `search` reports how many XL pages it did not search, and the catalogues mark locked entries `readable: false`. Do not guess at locked content.

Every locked result ends with "How to unlock" and the link https://agentwikis.com/pro#connect. When something the user asked for is locked, say what is locked, give that link, and tell them where the key goes in the app you are running in. The key is optional and hosted: it goes into the app's connection to `https://agentwikis.com/mcp` as the header `Authorization: Bearer aw_your_key`. Nothing is installed. Give the user the steps for their app as a numbered list with each command exactly as written, and tell them to put their key in place of `aw_your_key`, keeping the quotes.

- Claude Code with this plugin: the plugin asks for the key when it is enabled. To add or change it, the user types `/plugin configure aigarage@aigarage` inside Claude Code, pastes the key, and restarts Claude Code. (In a terminal, `claude plugin configure` only shows the options.)
- Codex:
  1. Save the key on the computer. On Windows, open PowerShell and run this, with the key in place of `aw_your_key` (keep the quotes). It prints `SUCCESS: Specified value was saved.`

     ```
     setx AGENTWIKIS_API_KEY "aw_your_key"
     ```

     On a Mac, run this instead (on Linux, use `~/.bashrc` in place of `~/.zshrc`):

     ```
     echo 'export AGENTWIKIS_API_KEY="aw_your_key"' >> ~/.zshrc
     ```

  2. Close the terminal and open a new one, so it can see the key.
  3. Run `codex mcp add agentwikis --url https://agentwikis.com/mcp --bearer-token-env-var AGENTWIKIS_API_KEY`.
  4. Start a new Codex session.
- Grok Build: run `grok mcp add --scope user agentwikis https://agentwikis.com/mcp --header "Authorization: Bearer aw_your_key"` with the key in place of `aw_your_key` (keep the quotes), then start a new Grok session.
- Hermes Agent: `hermes mcp add agentwikis --url https://agentwikis.com/mcp --auth header`, answer yes to authentication, paste the key, then `/reload-mcp`.
- Cursor: add `"agentwikis": { "url": "https://agentwikis.com/mcp", "headers": { "Authorization": "Bearer aw_your_key" } }` under `mcpServers` in `~/.cursor/mcp.json`.

In Codex, Grok Build and Hermes Agent the connection the user adds is named `agentwikis` and takes the place of this plugin's free one. Never ask the user to paste the key into the chat.

Tell the user to keep the key private: a key typed into a command stays in the shell history, and Cursor keeps it in plain text in `mcp.json`. If it leaks, rotate it at https://agentwikis.com/pro. A key that isn't recognised (a typo, or an old key after rotating) gives the free tier, and every result says so. If a result starts with that notice, pass it on to the user.
