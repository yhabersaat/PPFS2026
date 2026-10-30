# PPFS Assistant

PPFS Assistant helps attendees of the Power Platform French Summit 2026 with the agenda, sessions, sponsors, registration, and practical information. Its role and response rules are defined in the agent instructions; the official Summit website, the agenda file, and the sponsors file are its knowledge sources.

## Agent information

| Property | Value |
| --- | --- |
| Name | PPFS Assistant |
| Schema name | `yht_PPFSAssistant` |
| Agent ID | `c45834c6-ac2a-41be-9260-5cb9ebb8df05` |
| Type | GitHub Copilot Harness |
| Model | `claude-sonnet-5-5` |
| Environment | yhabersaat Dev EA (`3bd8a3f5-8e4c-eb9f-b86b-a7996cc5eb06`) |
| Solution | `yht_PPFSAssistant` (reported by the creation workflow) |
| Publisher | Prefix `yht`; the publisher's friendly name was not reported |
| Languages | English primary (`1033`); French (`1036`) |
| Memory | Enabled |
| Channels | Microsoft Teams (`MsTeams`); Microsoft 365 Copilot (`Microsoft365Copilot`) |
| Publication | Published on October 30, 2026 |

**Icon**

![PPFS Assistant icon](ppfs-assistant/icon.png)

**Welcome message**

> Bienvenue au Power Platform French Summit 2026 (Paris, 8 et 9 décembre) ! Je suis PPFS Assistant : posez-moi vos questions sur l'agenda, les sessions, les intervenants ou la logistique. You can also ask in English.

## Features and scope

- Answers agenda questions about sessions, times, rooms, speakers, topics, and levels, using Europe/Paris time and the agenda's day and date.
- Lists concurrent sessions for a requested time and finds sessions by speaker, topic, or level.
- Answers sponsor, partner, and exhibitor questions from the sponsors file.
- Answers registration, venue, and practical-information questions from the official website.
- Replies in the attendee's latest language, uses formal French, keeps answers short, and does not invent details or request personal data.

The agent has no behaviors, tools, agent skills, connections, or topics.

## Knowledge sources

| Name | Source | Use |
| --- | --- | --- |
| PPFS 2026 Official Website | [powerplatformfrenchsummit.com](https://www.powerplatformfrenchsummit.com/) | Registration and tickets, venue and access, speakers, and practical information. |
| PPFS 2026 Agenda | [`specs/ks/agenda-ppfs2026.md`](specs/ks/agenda-ppfs2026.md) | The full December 8–9, 2026 agenda: session days, times, rooms, speakers, topics, levels, and languages. |
| PPFS 2026 Sponsors | [`specs/ks/sponsors-ppfs2026.md`](specs/ks/sponsors-ppfs2026.md) | The Summit sponsors by sponsorship level: Platinum, Gold and Silver. |

The synchronized copies of both files are in the agent workspace.

## Repository structure

- `specs/` — specification and icon.
  - `specs/ks/` — knowledge files: agenda and sponsors.
  - `specs/evals/` — evaluation files for Copilot Studio.
- `ppfs-assistant/` — synchronized agent settings, icon, and knowledge-source files.
- `README.md` — this overview.

## Updating the agent

Use the `mcs-assistant` plugin skills for the connected workspace: run `pull-agent`, edit the agent files, run `push-agent`, then run `publish-agent` to make the updated cloud draft live. Publishing requires confirmation.
