# Specification: PPFS Assistant

> This specification is executed end to end by GitHub Copilot CLI, in a terminal, with the Copilot Studio plugin `mcs-assistant` ([microsoft/copilot-studio-plugin](https://github.com/microsoft/copilot-studio-plugin)) installed.
> To launch it, from the working folder that contains `specs/`: open GitHub Copilot CLI with `copilot --yolo` (all tools, paths and URLs allowed without confirmation), then enter the prompt:
>
> ```text
> Run the specification @specs/ppfs-assistant.md exactly as written, step by step.
> ```

## 1. What you must do

You are the orchestrator. You run every command needed, yourself, in the terminal, to create, configure, publish and version the Copilot Studio agent "PPFS Assistant", a **GitHub Copilot Harness** agent (authoring mode `cli-copilot`). Follow the steps in section 5, in order, without asking for confirmation.

- Solution creation and packaging: Power Platform CLI (`pac`).
- Cloning, knowledge and push to Copilot Studio: skills from the `mcs-assistant` plugin (`clone-agent`, `add-knowledge`, `push-agent`).
- Instructions, language, welcome message, memory, channels and icon: you edit `settings.mcs.yml` and copy `icon.png` yourself with your file tools.
- Publishing: Power Platform CLI (`pac copilot publish`).
- Versioning: `git`, to the GitHub repository in section 3.
- The agent stays minimal: a name, a custom icon, instructions, a welcome message, memory, the Teams and Microsoft 365 channels and two knowledge sources (the public website and the agenda file `specs/agenda-ppfs2026.md`).
- Every permission is granted in advance, including cloning, pushing and publishing: the environment is a development environment. This specification is the explicit request and confirmation for every skill it invokes.
- If a command fails, stop and show the full error. Do not attempt a workaround. The only exception is the known `pac copilot publish` error described in step 11.
- Run every command in this specification exactly as written. Never add, remove or change an argument (in particular, never add `--environment`, `--schema-name` or `--instructions` to `pac copilot init`: with them, `pac` creates an extra solution and publisher in the cloud).
- Resuming: if `ppfs-assistant/` already exists with `settings.mcs.yml`, `agent.sync.yaml` and `.mcs/`, and `pac copilot list` already shows `PPFS Assistant`, steps 1 to 7 are done. Run step 1 again, then continue from step 8 and skip any part of steps 8 to 12 that is already done (instructions already set, `supportedLanguages` already present, welcome message, memory, channels and `icon.png` already as in section 4, a knowledge file for the website already in `capabilities/knowledge/`, `agenda-ppfs2026.md` already in `capabilities/knowledge/files/`, README already pushed).
- At the end, show the summary described in section 7.

## 2. Constraints

- Do **not** add any behavior (`behaviors/`), tool (`capabilities/tools/`), agent skill component, connection or topic.
- Apart from the edits in steps 3 and 8, never change `displayName`, `schemaName`, `template`, `language`, `authoringModel`, `recognizer`, `model`, `memory`, `greetingText`, `channels` or the authentication settings in `settings.mcs.yml`.
- Never edit, resize or convert `specs/icon.png`: copy it as is.
- Never change `.mcs/` or `agent.sync.yaml`: these files are managed by `pac`.
- Do not delegate to any sub-agent: use only the three `mcs-assistant` skills named in section 5. Never use an agent or skill whose name starts with `copilot-studio:` (the former Skills for Copilot Studio plugin, such as `copilot-studio:copilot-studio-author`).
- Never use the `mcs-assistant:create-agent` or `mcs-assistant:publish-agent` skills, even though this specification creates and publishes an agent: creation goes through the solution in steps 3 to 6, and publishing goes through `pac` in step 11.
- Never push the `build/` folder to GitHub.
- Do not ask any question during the run: every answer is in this specification. The agent to configure is always `ppfs-assistant/`.

## 3. Parameters

| Parameter | Value |
| --- | --- |
| Power Platform environment | `3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06` (yhabersaat Dev EA, `https://yhabersaat-devea.crm.dynamics.com/`) |
| `pac auth` profile | Index 2 (UNIVERSAL profile on yhabersaat Dev EA) |
| Publisher | Unique name `yhabersaat`, prefix `yht` |
| Solution | Unique name `PPFSAssistant`, display name `PPFS Assistant`, version `1.0.0.0` |
| Agent | Display name `PPFS Assistant`, schema name `yht_PPFSAssistant` |
| Agent type | GitHub Copilot Harness (`--authoring-mode cli-copilot`, `template: cliagent-1.0.0`) |
| Model | Plugin and `pac` default (`series: Sonnet46`, Claude Sonnet 4.6), unchanged |
| Memory | Enabled: `memory.enableMemory: true` under `configuration.agentSettings` |
| Icon | Custom icon `specs/icon.png` (PNG, 30 KB maximum), copied as `icon.png` at the root of the agent workspace |
| Welcome message | `greetingText` under `configuration.agentSettings` (section 4) |
| Channels | Microsoft Teams and Microsoft 365 Copilot: `channels` under `configuration` (section 4) |
| Primary language | English (1033), set by `pac copilot init` |
| Additional language | French (1036) |
| Agent authentication | Microsoft Entra ID (defaults: `authenticationMode: Integrated`, `authenticationTrigger: Always`) |
| GitHub repository | `https://github.com/yhabersaat/PPFS2026.git`, branch `main` |
| Build folder | `build/` (not versioned) |
| Agent folder | `ppfs-assistant/` |
| Publish at the end | Yes, without confirmation |

## 4. The agent

### Description

Assistant for Power Platform French Summit 2026 attendees: agenda, sessions, registration and practical information, based on the official website.

A GitHub Copilot Harness agent has no description field in its YAML files: this description is used in the README (step 12).

### Instructions

```text
You are PPFS Assistant, the assistant for attendees of the Power Platform French Summit 2026 (Paris, December 8 and 9, 2026, in person and online).
You answer questions about the agenda, sessions, speakers, registration and logistics.
Knowledge sources:
- For the agenda (sessions, times, rooms, speakers, topics, levels, session language), use the file agenda-ppfs2026.md. All times are in the Europe/Paris time zone.
- For registration, venue, practical information and anything else, use the official Summit website.
Answering agenda questions:
- Always give the day, time and room of a session, and its speakers.
- Several sessions run in parallel in different rooms: when asked "what is on at a given time", list every session in that time slot.
- To find a speaker's sessions, search the agenda by speaker name.
- When asked for sessions on a topic or for a level (100 - Débutant to 400 - Expert), use the Topics and Level of each session.
Style and rules:
- Answer in the attendee's language: French by default, English if the question is in English. In French, use the formal "vous".
- Keep answers short: at most 3 sentences, or a list of at most 5 items. If more sessions match, give the first 5 and offer to list the rest.
- Never invent a time, room, speaker, price or address. If the information is in neither source, say so clearly and point to the website's Contact page.
- For an off-topic question, politely decline in one sentence and remind the attendee what you can help with.
- Never ask for personal data.
```

### Knowledge sources

| Name | Type | URL |
| --- | --- | --- |
| PPFS official website | Public website (`WebsiteKnowledgeSource`) | `https://en.powerplatformfrenchsummit.com/` |

| PPFS 2026 agenda | Uploaded file | `specs/agenda-ppfs2026.md` (copied by the skill into `capabilities/knowledge/files/`) |

Website source description: `Official website of the Power Platform French Summit 2026: registration, venue, speakers and practical information.`

Agenda file description: `Full agenda of the Power Platform French Summit 2026, December 8 and 9: session times, rooms, titles, speakers, topics and levels.`

The official website loads its agenda with a script that the website knowledge source cannot read, so the agenda is provided as a static uploaded file.

### Welcome message

Shown when a conversation starts. It is set as `greetingText` under `configuration.agentSettings`, at the same indentation as `model:`, as one double-quoted YAML string:

```yaml
    greetingText: "Bienvenue au Power Platform French Summit 2026 (Paris, 8 et 9 décembre) ! Je suis PPFS Assistant : posez-moi vos questions sur l'agenda, les sessions, les intervenants ou la logistique. You can also ask in English."
```

### Memory

```yaml
    memory:
      enableMemory: true
```

Under `configuration.agentSettings`, at the same indentation as `model:`.

### Channels

Microsoft Teams and Microsoft 365 Copilot, as a `channels` list directly under `configuration:`, before `authoringModel:`:

```yaml
  channels:
    - channelId: MsTeams
    - channelId: Microsoft365Copilot
```

### Icon

`specs/icon.png`, copied unchanged as `icon.png` at the root of the agent workspace (next to `settings.mcs.yml`). `pac` packs and syncs this file as the agent icon.

### Languages

- Primary language: English, `language: 1033` in `settings.mcs.yml`, unchanged.
- Additional language: French, as a root-level entry in `settings.mcs.yml`:

  ```yaml
  supportedLanguages:
    - 1036
  ```

## 5. Steps

1. **Prerequisites**: `pac help` (the version appears in the header and must be 2.13.1 or later; `pac` has no `--version` option), `pac copilot init --help` (must list the `--authoring-mode` option; the command may end with an error about missing arguments, which is expected), `node --version`, `git --version`, `copilot plugin list` (the `mcs-assistant` plugin must be installed; if the former `copilot-studio@skills-for-copilot-studio` plugin is also listed, do not stop and do not uninstall it: mention it in the section 7 summary and never use its agents or skills, as section 2 requires), `gh auth status` (signed in to the GitHub account `yhabersaat`), then `gh auth setup-git` so that `git push` uses this account without asking for credentials. Check that `specs/icon.png` exists, is a PNG file and is 30 KB or less (Copilot Studio rejects larger icons): if not, stop before creating anything. Select the Power Platform profile with `pac auth select --index 2`, then check with `pac auth list` that it is active on the environment in section 3.

2. **Prepare the Git repository** in the working folder:

   ```powershell
   git init -b main
   git remote add origin https://github.com/yhabersaat/PPFS2026.git
   git fetch origin
   ```

   If the remote `main` branch exists (repository created with a README or a license), get it: `git pull origin main --allow-unrelated-histories`. Create a `.gitignore` file containing the line `build/`. Commit "Add specification" (`specs/` and `.gitignore`), then `git push -u origin main`.

3. **Generate the GitHub Copilot Harness agent locally**:

   ```powershell
   pac copilot init --name "PPFS Assistant" --publisher-prefix yht --project-dir build/scaffold --authoring-mode cli-copilot
   ```

   This command only writes local files. Run it exactly as shown, without `--environment`. `build/scaffold/` must then contain only `settings.mcs.yml` (with `authoringModel: CliCopilot` and `template: cliagent-1.0.0`) and `agent.sync.yaml`.

   Then, with your file tools:
   - copy `specs/icon.png` to `build/scaffold/icon.png`, unchanged;
   - edit `build/scaffold/settings.mcs.yml`: add the `channels` list from section 4 directly under `configuration:`, before `authoringModel:`; under `configuration.agentSettings`, keep `model` (`series: Sonnet46`) as it is and add `greetingText` and `memory` from section 4, at the same indentation as `model:`, after `model` and its `series` line.

   Change nothing else. The result must look like this (the instructions are added in step 8):

   ```yaml
   configuration:
     channels:
       - channelId: MsTeams
       - channelId: Microsoft365Copilot
     authoringModel: CliCopilot
     recognizer:
       kind: CLICopilotRecognizer
     agentSettings:
       model:
         series: Sonnet46
       greetingText: "<welcome message from section 4>"
       memory:
         enableMemory: true
       instructions:
         ...
   ```

4. **Package the solution**:

   ```powershell
   pac copilot pack --publisher-prefix yht --project-dir build/scaffold --solution-name PPFSAssistant --output-path build
   ```

   The command must list `[agent] PPFS Assistant`. The icon, welcome message, memory and channels are part of this agent component.

5. **Set the version and attach the solution to the `yhabersaat` publisher** (otherwise `pac` uses version `1.0` and creates a publisher named `yht`):

   ```powershell
   pac solution unpack --zipfile build/PPFSAssistant.zip --folder build/solution --packagetype Unmanaged
   ```

   In `build/solution/Other/Solution.xml`:
   - replace `<Version>1.0</Version>` with `<Version>1.0.0.0</Version>`;
   - in `<Publisher>`, replace `<UniqueName>yht</UniqueName>` with `<UniqueName>yhabersaat</UniqueName>`, and `description="yht"` with `description="yhabersaat"` (localized name and description);
   - in `<SolutionManifest>`, replace the localized name `description="PPFSAssistant"` with `description="PPFS Assistant"`.

   Change nothing else, then:

   ```powershell
   pac solution pack --zipfile build/PPFSAssistant_yhabersaat.zip --folder build/solution --packagetype Unmanaged
   ```

6. **Import the solution**:

   ```powershell
   pac solution import --path build/PPFSAssistant_yhabersaat.zip --environment 3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06 --publish-changes
   ```

   Then check that `pac solution list --environment 3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06` shows `PPFSAssistant` at version `1.0.0.0`, and that `pac copilot list --environment 3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06` shows `PPFS Assistant`.

7. **Clone the agent** with the `mcs-assistant:clone-agent` skill (agent `yht_PPFSAssistant`, environment from section 3, output root `.`, local folder name `ppfs-assistant`). The skill runs `pac copilot clone` itself, once: do not run that command yourself.

   If `pac` created the folder under another name, rename it to `ppfs-assistant/`. The folder must contain `settings.mcs.yml`, `agent.sync.yaml` and `.mcs/`, and normally `icon.png`. Then delete the `build/` folder: only one agent (`ppfs-assistant/`) must remain in the working folder. Commit "Create PPFS Assistant" (`ppfs-assistant/`), then `git push`.

8. **Configure the agent** in `ppfs-assistant/`, yourself, without delegating to a sub-agent:
   - **Instructions**: replace the value of the `StaticSegment` in `configuration.agentSettings.instructions.segments` in `settings.mcs.yml` with the instructions from section 4, as a YAML literal block (`value: |`). Change nothing else in this file.
   - **Additional language**: add the `supportedLanguages` entry from section 4 at the root of `settings.mcs.yml`, right after `language: 1033`. Do not change `language`.
   - **Icon, welcome message, memory and channels**: they normally come from step 3. Check them in `ppfs-assistant/` and fix only what differs from section 4: if `icon.png` is missing, copy `specs/icon.png` to `ppfs-assistant/icon.png`, unchanged; if `greetingText`, `memory` or `channels` is missing or different in `settings.mcs.yml`, set it exactly as in section 4, at the position described in step 3. This also applies when resuming on an agent created before these settings existed.
   - **Website knowledge source**: invoke the `mcs-assistant:add-knowledge` skill with the website URL and its description from section 4. It writes `capabilities/knowledge/yht_PPFSAssistant.<slug>_<id>.mcs.yml` of kind `WebsiteKnowledgeSource`.
   - **Agenda knowledge source**: invoke the `mcs-assistant:add-knowledge` skill a second time with the local file `specs/agenda-ppfs2026.md` and the agenda file description from section 4. It copies the file into `capabilities/knowledge/files/` and writes its metadata sidecar next to it. Do not change `specs/agenda-ppfs2026.md`.

9. **Validate**: compile the agent without sending it, with `pac copilot pack --publisher-prefix yht --project-dir ppfs-assistant --solution-name PPFSAssistant --output-path build/check`. The command must list 3 components: `[agent] PPFS Assistant`, `[file] agenda-ppfs2026.md` and `[knowledge] https://en.powerplatformfrenchsummit.com/`. If it fails, stop and show the error (section 1). Then delete `build/`.

10. **Push to Copilot Studio** with the `mcs-assistant:push-agent` skill on `ppfs-assistant/`. The skill runs `pac copilot pull` then `pac copilot push` itself: do not run these two commands yourself. When the skill has finished, run `pac copilot pull --project-dir ppfs-assistant` once yourself (the skill does not pull after pushing), then check that `settings.mcs.yml` still contains `supportedLanguages` with `1036`, `greetingText`, `memory.enableMemory: true` and both `channels` entries, and that `ppfs-assistant/icon.png` exists. If one of them is missing, do not stop and do not push again: list it in the section 7 summary as a setting to check in Copilot Studio (section 8). Commit "Configure PPFS Assistant", then `git push`.

11. **Publish**, without asking for confirmation:
    - Note the current value of `publishedOn` in `ppfs-assistant/settings.mcs.yml` (it may be absent).
    - Run `pac copilot publish --bot yht_PPFSAssistant --environment 3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06`.
    - Known `pac` 2.13.1 issue: with a GitHub Copilot Harness agent, `pac` sends the publish request, then can crash while reading the publish status, with `System.ArgumentException: Invalid response format` (parameter `rawResponse`). If, and only if, the command fails with exactly this error, do not stop: it is not a publishing failure. Do not run `pac copilot status` (it reads the same status and fails the same way).
    - Verify publishing: run `pac copilot pull --project-dir ppfs-assistant` and check that `publishedOn` in `settings.mcs.yml` is present and later than the value noted above. If it is not, wait 30 seconds and pull again, up to 5 times. If it is still not later, do not stop: record "publication not confirmed" for the section 7 summary.
    - If local files changed, commit "Publish PPFS Assistant", then `git push`.

12. **Generate the README**: create `README.md` at the root of the repository, in English, from the agent as published (reread `ppfs-assistant/settings.mcs.yml` and the files in `ppfs-assistant/capabilities/knowledge/`). It contains:
    - **Overview**: name, description from section 4 and the agent's role;
    - **Basic information**: agent type (GitHub Copilot Harness), model, environment, solution (version 1.0.0.0) and publisher, schema name, primary and additional languages, authentication, memory, channels (Microsoft Teams and Microsoft 365 Copilot), custom icon (`ppfs-assistant/icon.png`, shown as an image), welcome message, publishing status;
    - **Features**: what the agent can do and what is out of scope, based on its instructions;
    - **Knowledge sources**: website URL and agenda file, with their descriptions;
    - **Repository structure**: purpose of `specs/` (specification, agenda file and icon) and `ppfs-assistant/` (`settings.mcs.yml`, `icon.png`, `capabilities/knowledge/`);
    - **Updating the agent**: `pac copilot pull`, editing the YAML files, `pac copilot push`, then publishing.

    Do not invent any information that is not in this specification or in the agent's files. Commit "Add README", then `git push`.

## 6. Success criteria

- The `PPFSAssistant` solution (publisher `yhabersaat`, version `1.0.0.0`) exists in the environment and contains the PPFS Assistant agent.
- In Copilot Studio, the agent is a GitHub Copilot Harness agent; it shows its instructions, the website source and the agenda file, with no tool, skill or behavior; English is the primary language and French an additional language.
- The validation in step 9 lists the 3 expected components.
- The agent is published, uses the default model (`Sonnet46`), has memory enabled, shows the custom icon and the welcome message, and lists Microsoft Teams and Microsoft 365 Copilot as channels.
- The GitHub repository contains `README.md`, `specs/`, `ppfs-assistant/` and `.gitignore`, without `build/`, with at least the specification, creation, configuration and README commits on `main`.

## 7. Expected summary

Show: the solution name, version and publisher, the agent's schema name and type, the list of changed files, the validation result, the result of the push to Copilot Studio, whether French is listed as an additional language, whether the icon, welcome message, memory and channels were retained after the pull in step 10, the publishing result, and the GitHub repository URL with the last pushed commit.

## 8. After the run (in Copilot Studio)

Check only, nothing to run: in Copilot Studio, open PPFS Assistant and check the icon, the welcome message (Test pane) and **Channels**. If **Teams and Microsoft 365 Copilot** is not shown as added, select it, turn on **Make agent available in Microsoft Copilot**, select **Add channel**, then publish again. Mention this check at the end of the section 7 summary.
