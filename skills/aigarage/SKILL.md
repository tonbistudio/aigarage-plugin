---
name: aigarage
description: Use when the user asks about AI agents, Hermes Agent, agent tooling, local models or anything AI Garage has covered. Finds the matching AI Garage video, newsletter issue or X post and links it.
---

# AI Garage content

The `aigarage` MCP server searches AI Garage's own content:

- **YouTube videos:** each one has a short summary, the chapter list, and the captions, split by chapter.
- **AI Garage Weekly**, the newsletter: each issue is split into its parts (AI news, research, brainstorming).
- **X posts:** the creator's own posts, as links only.

## Tools

- `search_content(query, platform?, limit?)`: search by keywords or a short question. `platform` is `youtube` or `newsletter`; X posts come back alongside them.
- `get_item(id, segment?)`: read one item, or one part of it by segment number or `mm:ss` timestamp.
- `list_latest(platform?, limit?)`: the newest items.

## How to use it

1. Call `search_content` with the user's topic. Use plain keywords.
2. If the result says `confident: false`, AI Garage has not covered the topic. Say so and answer from other sources. Do not stretch a weak match.
3. Before you summarise a hit, read it with `get_item`: the matching segment of a video, or the matching part of an issue. Summarise only what the item says.
4. Cite every item you use with its link from the result. For a video that is the timestamp link to the chapter; for a newsletter issue it is the issue's page.

## Rules

- **X results are links only.** The server returns no post text. Give the links to the user to open. Do not quote, summarise or guess what a post says.
- **Captions are uncorrected speech-to-text.** Product names can be misspelled (for example "herder" for "Herdr"). Use the spelling in the video's title, summary or chapter names.
- **Do not put words in the creator's mouth.** Attribute to AI Garage only what the source shows. Mark your own additions as yours.
- **Give the date when the topic is version-sensitive.** A video or issue can be older than the current release.
- **For reference documentation on a tool, use the `agentwikis` skill and server.** Videos and issues show how something was done or seen at one time; the wikis are the maintained reference.
