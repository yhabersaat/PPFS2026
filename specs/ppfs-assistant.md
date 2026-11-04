# Specification: PPFS Assistant

> Run by GitHub Copilot CLI with the Copilot Studio plugin ([microsoft/copilot-studio-plugin](https://github.com/microsoft/copilot-studio-plugin)) and the Copilot Studio MCP server ([Copilot Studio MCP](https://learn.microsoft.com/en-us/microsoft-copilot-studio/agents-experience/copilot-studio-mcp)) installed. From the working folder that contains `specs/`, start GitHub Copilot CLI in "allow all" mode (`copilot --yolo`) and ask it: "Run the specification @specs/ppfs-assistant.md exactly as written, step by step."

## Goal

This run creates and publishes PPFS Assistant, a Copilot Studio agent that answers attendees' questions about the Power Platform French Summit 2026, with its knowledge sources, its Microsoft Learn tool and two evaluation sets. It delivers the agent's files on a GitHub branch with an open pull request for review. Section 3 says what to build, section 5 how we know it works, and section 1 what must not change.

## 1. Rules

- Use the Copilot Studio plugin for everything about the agent: you choose its skills. Use the Copilot Studio MCP server only for two things: adding the Microsoft Learn Docs tool and creating the evaluation sets.
- Use only the plugin named above. Never use the former Skills for Copilot Studio plugin.
- This specification answers every question in advance. The run waits for the presenter only twice: the Copilot Studio MCP server sign-in in step 1, and the publishing confirmation.
- Build exactly what section 3 describes, word for word, and nothing else. Its values are verified in this environment: use them as written, even when a plugin guide lists other values.
- Evaluation sets are created and saved, never run.
- If a step fails, stop and show the error, except for the known publishing error in step 6.
- Resuming: if the agent folder already exists and is connected to Copilot Studio, the agent exists. Never create it again: check the prerequisites, switch to the agent branch, get the agent's latest files from Copilot Studio, then continue from the first step not done. A tool or evaluation set that already exists with the same name is never added twice.
- Never retry the same failed operation more than twice: stop and report it.
- At the end, show the summary in section 6.

### Must not change

- `main`: no file is ever committed or pushed to it, and the pull request is never merged.
- The files in `specs/`: used as they are, never edited.
- Other agents, connections and settings of the environment: never created, changed or deleted, except the PPFS Assistant agent itself.
- Publishing: never ask for a publication without the presenter's confirmation, and never twice in the same run. Creating the agent may publish an initial version on its own: this is expected and allowed, and it is not the publication of step 6.

## 2. Parameters

| Parameter | Value |
| --- | --- |
| Environment | `3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06` (yhabersaat Dev EA), Power Platform CLI auth profile 2 |
| Publisher prefix | `yht` |
| Agent name | `PPFS Assistant` |
| Agent type | GitHub Copilot Harness |
| Agent folder | `ppfs-assistant/` in the working folder |
| Solution and publisher | Those created with the agent, kept as is |
| GitHub repository | `https://github.com/yhabersaat/PPFS2026.git`, base branch `main` |
| Agent branch | `ghcp-ppfs-assistant-v1` |
| Presenter's GitHub account | `yhabersaat` |

## 3. The agent

### Description

Assistant for Power Platform French Summit 2026 attendees: agenda, sessions, speakers, sponsors, registration and practical information, based on the official website, and technical answers from Microsoft Learn. Used in the README only.

### Instructions

Use them word for word, with their line breaks. Never rephrase, summarize or complete them. They contain apostrophes and no double quotes: when they are passed to the Power Platform CLI, make sure the apostrophes are escaped for the shell. If creating the agent puts them on one line, restore the line breaks in the agent's files before pushing.

> You are PPFS Assistant, the assistant for attendees of the Power Platform French Summit 2026 (Paris, December 8 and 9, 2026, in person and online).
> You answer questions about the agenda, sessions, speakers, sponsors, registration and logistics, and technical questions about the content of the sessions.
> Knowledge sources and tools:
> - For the agenda (sessions, times, rooms, speakers, topics, levels, session language), use the file agenda-ppfs2026.md. All times are in the Europe/Paris time zone.
> - For sponsors, partners and exhibitors, use the file sponsors-ppfs2026.md.
> - For registration, venue, practical information and anything else about the Summit, use the official Summit website.
> - For every technical question about the content of a session, always use the Microsoft Learn Docs tool, never your own knowledge, and give the link of the page you used. Never use it for the agenda, speakers, sponsors or logistics.
> Answering agenda questions:
> - Always give the day, time and room of a session, and its speakers.
> - Several sessions run in parallel in different rooms: when asked what is on at a given time, list every session in that time slot.
> - To find a speaker's sessions, search the agenda by speaker name.
> - When asked for sessions on a topic or for a level (100 - Débutant to 400 - Expert), use the Topic and Level of each session.
> Style and rules:
> - Always reply in the language of the attendee's latest message, even if earlier messages or the sources are in another language: English question, English answer; French question, French answer, with the formal vous form.
> - Keep answers short: at most 3 sentences, or a list of at most 5 items, with no emojis and no headings. Only if 6 or more sessions match, give the first 5, then say how many remain and offer to list them; when 5 or fewer match, list them all and do not mention other sessions.
> - Never invent a time, room, speaker, price or address. Give a price or ticket type only if it is written word for word in a source; otherwise say the website does not state it and point to the registration page.
> - Each agenda entry starts with its day and date: keep that day and date exactly, translated into the attendee's language when needed, and never deduce or recalculate the weekday.
> - For an off-topic question, politely decline in one sentence and remind the attendee what you can help with.
> - Never ask for personal data.

### Knowledge sources

Add each source with its name and description, word for word. The website takes its name as its component name. An uploaded file keeps its file name as its component name (plugin rule), so its name below is used only in the README and the pull request. Never change the files.

| Source | Name | Description |
| --- | --- | --- |
| Website `https://www.powerplatformfrenchsummit.com/` | PPFS 2026 Official Website | Official website of the Power Platform French Summit 2026 (December 8 and 9, EFREI Paris). Use it for registration and tickets, venue and access, speakers and practical information. For the session schedule, prefer the agenda file. For sponsors, prefer the sponsors file. |
| File `specs/ks/agenda-ppfs2026.md` | PPFS 2026 Agenda | Full PPFS 2026 agenda (December 8 and 9, 2026). Session days, times, rooms, speakers, topics, levels and languages. Use it for any schedule or speaker question. For tickets, venue and practical information, use the official website. |
| File `specs/ks/sponsors-ppfs2026.md` | PPFS 2026 Sponsors | List of the PPFS 2026 sponsors (December 8 and 9, 2026) by sponsorship level: Platinum, Gold and Silver. Use it for any question about sponsors, partners or exhibitors. |

### Tool

| Tool | Name | Description |
| --- | --- | --- |
| Microsoft Learn Docs MCP Server (Microsoft connector, default configuration, existing connection in the environment) | Microsoft Learn Docs | Searches official Microsoft documentation on Microsoft Learn. Use it for every technical question about the content of a Summit session (Copilot Studio, Power Apps, Power Automate, Dataverse, Power BI, Fabric, Azure, Microsoft 365 Copilot and related technologies). Do not use it for the Summit agenda, speakers, sponsors or logistics. |

The tool is added with the Copilot Studio MCP server, on the agent created by this run:

- A connection to this connector already exists in the environment (connector `shared_microsoftlearndocsmcpserver`, connection `d357457f54d4467a8bf197612dc0f00b`): use it with the connector's default configuration. Never create, change or authorize a connection.
- Discover the connector's tool bindings in the environment (connector ID, operation ID and connection reference): the display name alone is not enough.
- Read the agent's current definition, then update it with its complete tools list: the existing tools, if any, plus the Microsoft Learn Docs tool with these bindings and the name and description above. Change nothing else. The update saves a draft: do not publish.
- If the server cannot add it, ask the presenter to add it in Copilot Studio (Tools, Add a tool, Model Context Protocol, Microsoft Learn Docs MCP Server) with the name and description above, then continue.

### Settings

The "Where" column gives the place in the agent's settings file, verified in earlier runs in this environment. Keep everything else as the plugin created it.

| Setting | Value | Where |
| --- | --- | --- |
| Model | `claude-sonnet-5-5` (Claude Sonnet 5.5; this exact value is already used by an agent in this environment, and replaces the default Sonnet46, which is being retired) | `series` under `configuration.agentSettings.model` |
| Languages | English (primary) and French | `supportedLanguages: [1036]` at the root, right after `language` |
| Channels | Microsoft Teams and Microsoft 365 Copilot | `channels` under `configuration`, before `authoringModel`, with `MsTeams` and `Microsoft365Copilot` |
| Welcome message | Bienvenue au Power Platform French Summit 2026 (Paris, 8 et 9 décembre) ! Je suis PPFS Assistant : posez-moi vos questions sur l'agenda, les sessions, les intervenants ou la logistique. You can also ask in English. | `greetingText` under `configuration.agentSettings`, next to `model`, as one double-quoted string |
| Memory | Enabled | `memory.enableMemory: true` under `configuration.agentSettings`, next to `model` |
| Icon | `specs/icon.png` | PNG of 30 KB or less, copied unchanged as `icon.png` next to the settings file, then pushed |

### Evaluation sets

Created with the Copilot Studio MCP server from these files, used as they are and never changed. Their `agentId` and `suiteId` fields are empty on purpose: the agent does not exist before this run.

| File | Name | Type |
| --- | --- | --- |
| `specs/evals/ppfs-assistant-baseline.json` | PPFS Assistant Baseline | Single response, 12 test cases |
| `specs/evals/ppfs-assistant-conversations.json` | PPFS Assistant Conversations | Conversations, 5 test cases |

The test methods are set afterwards in Copilot Studio.

## 4. Steps

1. **Prerequisites**: check that the Power Platform CLI (version 2.13.1 or later, auth profile from section 2 selected), Git, Node.js, the GitHub CLI, the Copilot Studio plugin and the Copilot Studio MCP server are ready for the environment and account in section 2, and that the files named in section 3 exist (the icon is a PNG of 30 KB or less). The other files in `specs/evals/` (CSV files and templates) are not used. The Copilot Studio MCP server asks for a new sign-in each time it starts: if it is not signed in or has no environment selected, start its sign-in, wait for the presenter to complete it, then select the environment from section 2. If anything else is missing, stop.

2. **Git**, in this order:
   - Initialize the working folder as a repository linked to the GitHub repository, without adding any file yet.
   - If `main` exists on GitHub, bring it in. Otherwise create `main` with one empty commit "Initialize repository" that contains no file at all (stage nothing before it), and push it.
   - Create the agent branch from `main` and switch to it. If it already exists on GitHub, switch to it instead; if its pull request is already merged, stop and ask the presenter to delete the branch.
   - Commit "Add specification" with `specs/` on the agent branch and push. If there is nothing to commit, skip the commit and continue.
   Every commit of the run goes to the agent branch, never to `main`.

3. **Create the agent** in the environment, with the parameters from section 2 and everything in section 3 except the evaluation sets: instructions, knowledge sources, settings and icon, then push them, then add the tool (section 3). Note the agent ID, schema name and solution. Do not ask for a publication yet. If creating the agent publishes an initial version on its own, accept it and continue. Note the agent's publication date after this step (it may be empty).

4. **Check and commit**: get the agent's latest files from Copilot Studio (this brings the tool into the agent folder) and check that everything in section 3 is there. List anything missing in the summary. Commit "Create PPFS Assistant" and push.

5. **Create the evaluation sets** with the Copilot Studio MCP server, one per file in section 3:
   - Send only three values: the agent ID noted in step 3 (if it was not reported, find the agent by its name in the environment), the name from section 3, and the file's test cases exactly as written. Never send the file's other fields (`environmentId`, `suiteId`, `suiteType`): the service rejects extra fields and assigns the evaluation set ID itself.
   - Save them, never run them. Note their IDs and number of test cases. Never write an ID into the files.

6. **Publish** the agent, after the presenter's confirmation, even if it already shows an initial publication from step 3. Then check it:
   - Known Power Platform CLI issue: publishing can end with the error "Invalid response format" (parameter rawResponse) after the request was sent. With exactly this error, do not stop and do not publish again.
   - In every case, get the agent's latest files and check that the publication date is later than the one noted in step 3. If not, check again every 30 seconds, up to 5 times, then record "publication not confirmed".
   - Commit any changed files as "Publish PPFS Assistant" and push.

7. **README**: write `README.md` in English: what the agent does, its configuration, knowledge sources, tool, evaluation sets (created, not run), repository structure, and how to update the agent with the plugin. Commit "Add README" and push.

8. **Pull request**: open a pull request from the agent branch to `main`, titled "Add PPFS Assistant agent", with a description in English of what this run did: summary, agent, configuration, how it was built, validation, and a review checklist. Do not merge it.

## 5. How we know it works

Check each point at the end of the run and report it as passed or failed in the summary. Never report a point as passed without having checked it.

- The agent's files, fetched from Copilot Studio after publishing, contain the instructions word for word, the three knowledge sources with their names, the Microsoft Learn Docs tool, every setting from section 3, and no other component.
- The agent's publication date is later than the one noted in step 3.
- Copilot Studio lists the two evaluation sets with 12 and 5 test cases, and neither has been run.
- The agent branch on GitHub contains `README.md`, `specs/` and the agent folder, and its pull request to `main` is open.
- `main` and the files in `specs/` are unchanged.

## 6. Summary

Show: the agent (name, ID, type, model, solution), the operations run in order with their result, each check from section 5 as passed or failed, the evaluation sets with their IDs, the publication result, and the repository, branch and pull request links. End with a reminder to set the test methods of the evaluation sets in Copilot Studio.
