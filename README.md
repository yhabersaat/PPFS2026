# PPFS Assistant

![PPFS Assistant icon](ppfs-assistant/icon.png)

## Overview

Assistant for Power Platform French Summit 2026 attendees: agenda, sessions, registration and practical information, based on the official website.

PPFS Assistant helps attendees of the event in Paris on December 8 and 9, 2026, in person and online. It uses the uploaded agenda for session questions and the official website for registration, venue and other practical information.

## Basic information

| Setting | Published agent configuration |
| --- | --- |
| Display name | PPFS Assistant |
| Agent type | GitHub Copilot Harness |
| Authoring model / template | `CliCopilot` / `cliagent-1.0.0` |
| Model | Claude Sonnet 4.6 (`Sonnet46`), unchanged from the default |
| Environment | yhabersaat Dev EA |
| Environment ID | `3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06` |
| Environment URL | https://yhabersaat-devea.crm.dynamics.com/ |
| Solution | `PPFSAssistant` (PPFS Assistant), version `1.0.0.0` |
| Publisher | `yhabersaat`, prefix `yht` |
| Agent schema name | `yht_PPFSAssistant` |
| Primary language | English (`1033`) |
| Additional language | French (`1036`) |
| Authentication | Microsoft Entra ID: `Integrated`, trigger `Always` |
| Access control policy | `GroupMembership` |
| Memory | Enabled (`configuration.agentSettings.memory.enableMemory: true`) |
| Channels | Microsoft Teams (`MsTeams`) and Microsoft 365 Copilot (`Microsoft365Copilot`) |
| Custom icon | `ppfs-assistant\icon.png`, copied unchanged from `specs\icon.png` |
| Publishing status | Published successfully; confirmed by `publishedOn: 2026-10-16T07:41:42.0000000Z` after pulling |

English is the primary configured language; the instructions specify French by default and English when the attendee asks in English.

### Welcome message

> Bienvenue au Power Platform French Summit 2026 (Paris, 8 et 9 décembre) ! Je suis PPFS Assistant : posez-moi vos questions sur l'agenda, les sessions, les intervenants ou la logistique. You can also ask in English.

The post-push pull retained French, the unchanged icon, the welcome message, enabled memory and both channel entries.

## Features

- Answer agenda questions with each session's day, time, room and speakers. All agenda times use the `Europe/Paris` time zone.
- Find sessions by time slot, speaker, topic or level, from 100 (beginner) to 400 (expert), including parallel sessions.
- Use the official website for registration, venue and logistics; use the uploaded agenda for session details.
- Respond briefly in the attendee's language, using formal "vous" in French: at most three sentences or five list items. If more sessions match, give the first five and offer the rest.
- Decline off-topic questions, never request personal data and never invent missing details. When neither source contains an answer, say so and refer the attendee to the website's Contact page.

The agent is informational. No tools, agent skill components, behaviors, connections or topics were added.

## Knowledge sources

| Source | Type and location | Description returned by PAC |
| --- | --- | --- |
| PPFS official website | `WebsiteKnowledgeSource`: https://en.powerplatformfrenchsummit.com/ | Official website of the Power Platform French Summit 2026: registration, venue, speakers and practical information. |
| PPFS 2026 agenda | Uploaded file: `ppfs-assistant\capabilities\knowledge\files\agenda-ppfs2026.md` | This knowledge source searches information contained in agenda-ppfs2026.md |

The agenda covers December 8 and 9, including session times, rooms, titles, speakers, topics and levels. Its source file is `specs\agenda-ppfs2026.md`, copied unchanged into the agent workspace. The website loads its agenda through a script that website knowledge cannot read, so a static file supplies this information.

The requested agenda description was "Full agenda of the Power Platform French Summit 2026, December 8 and 9: session times, rooms, titles, speakers, topics and levels." PAC returned the generic description shown above during synchronization; this README reflects the returned metadata.

Local validation compiled exactly three components: `[agent] PPFS Assistant`, `[file] agenda-ppfs2026.md` and `[knowledge] https://en.powerplatformfrenchsummit.com/`. The subsequent cloud push reported three changes.

## Repository structure

```text
.
|-- .gitignore                       Excludes build output
|-- README.md                        Published agent overview and maintenance
|-- specs\
|   |-- ppfs-assistant.md             End-to-end specification
|   |-- agenda-ppfs2026.md            Original agenda file
|   `-- icon.png                      Original custom icon
`-- ppfs-assistant\
    |-- settings.mcs.yml              Agent configuration and instructions
    |-- agent.sync.yaml               PAC-managed sync metadata
    |-- icon.png                      Unmodified custom icon
    `-- capabilities\
        `-- knowledge\
            |-- yht_PPFSAssistant.*.mcs.yml   Website knowledge configuration
            `-- files\
                |-- agenda-ppfs2026.md        Uploaded agenda payload
                `-- agendappfs2026md_*.mcs.yml Metadata sidecar
```

The local workspace also has PAC-managed `.mcs\` connection and synchronization state, which is not tracked in Git. Do not edit `.mcs\` or `agent.sync.yaml` manually. The temporary `build\` directory is ignored and was removed after validation.

## Updating the agent

Use a Power Platform CLI authentication profile for the specified environment. From the repository root, pull remote changes before editing:

```powershell
pac copilot pull --project-dir ppfs-assistant
```

Edit `ppfs-assistant\settings.mcs.yml` or the relevant knowledge YAML files. Preserve the PAC-managed metadata and the specification's constraints. Before uploading, pull again to merge remote changes; stop and resolve any conflict before pushing:

```powershell
pac copilot pull --project-dir ppfs-assistant
pac copilot push --project-dir ppfs-assistant
```

Push updates the draft only. Publish, then pull the resulting publication metadata:

```powershell
pac copilot publish --bot yht_PPFSAssistant --environment 3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06
pac copilot pull --project-dir ppfs-assistant
```

Confirm that `publishedOn` advances and review the retained settings before committing and pushing changes to GitHub.

### Copilot Studio checks

Open PPFS Assistant and check the icon, the welcome message in the Test pane and **Channels**. If **Teams and Microsoft 365 Copilot** is not shown as added, select it, turn on **Make agent available in Microsoft Copilot**, select **Add channel**, then publish again. The YAML channel entries were retained; these UI checks are still required.
