# Specification: PPFS Assistant

> Run by GitHub Copilot CLI with the Copilot Studio plugin `mcs-assistant` ([microsoft/copilot-studio-plugin](https://github.com/microsoft/copilot-studio-plugin)) installed. From the working folder that contains `specs/`, start GitHub Copilot CLI in "allow all" mode (`copilot --yolo`) and ask it: "Run the specification @specs/ppfs-assistant.md exactly as written, step by step."

## 1. Rules

- You orchestrate. Every Copilot Studio operation is delegated to the `mcs-assistant` skills named in section 4: they run the Power Platform CLI themselves. Never run Copilot Studio commands yourself. You only handle the prerequisite checks, the icon file, Git, the README and the pull request.
- This specification is the explicit request for every skill it invokes, gives all their inputs and confirms every pull they run before a push: they have nothing to ask. The only question of the run is the final confirmation asked by `publish-agent`: wait for the presenter's answer.
- Never use an agent or skill whose name starts with `copilot-studio:` (the former Skills for Copilot Studio plugin, which may be stale).
- The agent stays minimal: no behavior, tool, agent skill, connection or topic.
- If a step fails, stop and show the full error, except for the known publishing error in step 6.
- Resuming: if the agent workspace already exists and is connected to Copilot Studio, the agent exists. Never create it again: run step 1, switch to the agent branch, pull the agent with `pull-agent`, then continue from step 4, skipping what is already done.
- At the end, show the summary in section 6.

## 2. Parameters

| Parameter | Value |
| --- | --- |
| Environment | `3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06` (yhabersaat Dev EA), Power Platform CLI auth profile number 2 |
| Publisher prefix | `yht` (no schema name: let the plugin derive it) |
| Agent name | `PPFS Assistant` |
| Agent type | GitHub Copilot Harness, default model |
| Agent workspace | `ppfs-assistant/` in the working folder |
| Solution and publisher | Those created by the plugin with the agent, kept as is |
| GitHub repository | `https://github.com/yhabersaat/PPFS2026.git`, base branch `main` |
| Agent branch | `ghcp-ppfs-assistant-v1`: every commit of this run goes there, never directly to `main` |
| Presenter's GitHub account | `yhabersaat` |

## 3. The agent

### Description

Assistant for Power Platform French Summit 2026 attendees: agenda, sessions, registration and practical information, based on the official website. A GitHub Copilot Harness agent has no description field: use it in the README only.

### Instructions

Use them word for word, with their line breaks. Never rephrase, summarize or complete them.

> You are PPFS Assistant, the assistant for attendees of the Power Platform French Summit 2026 (Paris, December 8 and 9, 2026, in person and online).
> You answer questions about the agenda, sessions, speakers, registration and logistics.
> Knowledge sources:
> - For the agenda (sessions, times, rooms, speakers, topics, levels, session language), use the file agenda-ppfs2026.md. All times are in the Europe/Paris time zone.
> - For registration, venue, practical information and anything else, use the official Summit website.
> Answering agenda questions:
> - Always give the day, time and room of a session, and its speakers.
> - Several sessions run in parallel in different rooms: when asked what is on at a given time, list every session in that time slot.
> - To find a speaker's sessions, search the agenda by speaker name.
> - When asked for sessions on a topic or for a level (100 - Débutant to 400 - Expert), use the Topics and Level of each session.
> Style and rules:
> - Always reply in the language of the attendee's latest message, even if earlier messages or the sources are in another language: English question, English answer; French question, French answer, with the formal vous form.
> - Keep answers short: at most 3 sentences, or a list of at most 5 items, with no emojis and no headings. Only if 6 or more sessions match, give the first 5, then say how many remain and offer to list them; when 5 or fewer match, list them all and do not mention other sessions.
> - Never invent a time, room, speaker, price or address. Give a price or ticket type only if it is written word for word in a source; otherwise say the website does not state it and point to the registration page.
> - Each agenda entry starts with its day and date: copy them exactly, never deduce the day from the time.
> - For an off-topic question, politely decline in one sentence and remind the attendee what you can help with.
> - Never ask for personal data.

### Knowledge sources

| Source | Description |
| --- | --- |
| Public website `https://en.powerplatformfrenchsummit.com/` | Official website of the Power Platform French Summit 2026: registration, venue, speakers and practical information. |
| Uploaded file `specs/agenda-ppfs2026.md` (the website loads its agenda with a script it cannot read; never change this file) | Full agenda of the Power Platform French Summit 2026, December 8 and 9: session times, rooms, titles, speakers, topics and levels. |

### Settings

Set in the agent settings file. The property names come from the Copilot Studio schema, which the plugin does not document. Keep everything else as the plugin created it.

| Setting | Value | Property |
| --- | --- | --- |
| Additional language | French (1036); English (1033) stays the primary language | `supportedLanguages`, at the root, right after `language` |
| Channels | Microsoft Teams and Microsoft 365 Copilot | `channels` under `configuration`, before `authoringModel`, with the channel IDs `MsTeams` and `Microsoft365Copilot` |
| Welcome message | Bienvenue au Power Platform French Summit 2026 (Paris, 8 et 9 décembre) ! Je suis PPFS Assistant : posez-moi vos questions sur l'agenda, les sessions, les intervenants ou la logistique. You can also ask in English. | `greetingText` under `configuration.agentSettings`, next to `model`, as one double-quoted string |
| Memory | Enabled | `memory.enableMemory` under `configuration.agentSettings`, next to `model` |
| Model | Default, unchanged | `model` |
| Icon | `specs/icon.png`, copied unchanged next to the settings file as `icon.png` | File, not a property |

## 4. Steps

1. **Prerequisites**: check that the Power Platform CLI is version 2.13.1 or later, that Git is installed, that the `mcs-assistant` plugin is installed (if the former `copilot-studio` plugin is also installed, just mention it in the summary), and that the GitHub CLI is signed in to the presenter's account and set up for Git. Check that `specs/icon.png` is a PNG of 30 KB or less, otherwise stop. Select the Power Platform CLI auth profile from section 2.

2. **Git**, in this exact order:
   - Initialize the working folder as a repository linked to the GitHub repository from section 2, without adding any file yet.
   - If `main` exists on GitHub, bring it in. Otherwise create `main` with one empty commit "Initialize repository" that contains no file at all (stage nothing before it), and push `main`. Never commit `specs/` or any other file to `main`.
   - Create the agent branch from section 2 from `main` and switch to it. If it already exists on GitHub (a previous run stopped), switch to it instead; if its pull request is already merged, stop and ask the presenter to delete the branch.
   - On the agent branch, commit "Add specification" with `specs/`, then push the branch. If there is nothing to commit because `specs/` is already identical on the branch, do not fail: skip the commit and continue.

3. **Create and configure the agent** with `mcs-assistant:create-agent`, on the guided design path, without publishing. Give it the parameters from section 2 and all of section 3 except the icon: the instructions word for word (they contain apostrophes: make sure they are escaped for the shell when passed to the Power Platform CLI), the settings, and the two knowledge sources, to be added with `mcs-assistant:add-knowledge`. Ask it to add nothing else. The skill creates the agent, writes its files, validates them, pushes and pulls. Note the agent ID, schema name and solution it reports.

4. **Add the icon and push**: copy the icon (section 3), then push with `mcs-assistant:push-agent`. Pull with `mcs-assistant:pull-agent` and check that the instructions (identical to section 3), the settings, both knowledge sources and the icon are all there. If something is missing, do not stop: list it in the summary. Commit "Create PPFS Assistant" with the agent workspace, then push.

5. **Publish** with `mcs-assistant:publish-agent`, giving it the agent, the environment and, as readiness evidence, the workspace and the successful push of step 4. Before publishing, note the agent's current publication date (it may be absent). The skill asks for confirmation: wait for the presenter's answer.

6. **Check the publication**:
   - Known Power Platform CLI 2.13.1 issue: the publish can end with the error "Invalid response format" (parameter rawResponse) after the request was sent. With exactly this error, do not stop and do not publish again.
   - In every case, pull with `mcs-assistant:pull-agent` and check that the publication date is later than the one noted in step 5. If not, pull again every 30 seconds, up to 5 times, then record "publication not confirmed".
   - If files changed, commit "Publish PPFS Assistant", then push.

7. **README**: write `README.md` in English from the published agent files: overview (name, description, role), basic information (type, model, environment, solution and publisher, schema name, languages, memory, channels, icon shown as an image, welcome message, publication status), features and out-of-scope, knowledge sources, repository structure (`specs/` and the agent workspace), and how to update the agent with the plugin (pull, edit, push, publish). Invent nothing. Commit "Add README", then push.

8. **Pull request**: with the GitHub CLI, open a pull request from the agent branch to `main`, titled "Add PPFS Assistant agent". Write its description in English from what this run actually did, inventing nothing:
   - **Summary**: what the agent is for, in two sentences;
   - **Agent**: name, schema name, ID, type, model, environment, solution and publisher;
   - **Configuration**: instructions (summarized), knowledge sources, languages, welcome message, memory, channels, icon;
   - **How it was built**: the plugin skills invoked, in order, with their result;
   - **Validation**: settings retained after the pull in step 4, and the publication result;
   - **Review checklist**: instructions match the specification, both knowledge sources present, no other component, agent tested in Copilot Studio.
   Do not merge it: merging is the presenter's review step.

## 5. Success criteria

- PPFS Assistant is a published GitHub Copilot Harness agent with its instructions, both knowledge sources, French as additional language, memory, the icon, the welcome message and the Teams and Microsoft 365 Copilot channels, and no other component.
- Every Copilot Studio operation went through an `mcs-assistant` skill.
- The agent branch on GitHub contains `README.md`, `specs/` and the agent workspace, and an open pull request to `main` describes the agent.

## 6. Summary

Show: agent name, schema name, ID, type, solution and publisher; the plugin skills invoked, in order, with their result; the settings retained after the pull in step 4; the publication result; the repository URL, the agent branch, its last commit and the pull request URL. End with this reminder: in Copilot Studio, check the icon, the welcome message (Test pane) and Channels; if Teams and Microsoft 365 Copilot is not added, add it with "Make agent available in Microsoft Copilot" turned on, then publish again.
