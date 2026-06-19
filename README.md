# Troubleshooter Agent + Skills

A disciplined, note-keeping troubleshooting workflow for the GitHub Copilot CLI.
It pairs one **agent** that runs the investigation loop with four **skills** it
orchestrates. All durable state lives in a per-project `.ai` folder so an
investigation can be paused and resumed across sessions (and across models).

## What's included

| Component | Type | Purpose |
| --- | --- | --- |
| `agents/troubleshooter.md` | Agent | Investigation lead. Runs a hypothesis → test → record loop, keeps `.ai` notes, gates work behind the user, arms failsafes against runaway loops, and orchestrates the skills below. |
| `skills/troubleshoot-init` | Skill | Scaffolds the `.ai` folder for a **new** investigation — the 7 standard notes (`00`–`06`) with templates. |
| `skills/troubleshoot-start-session` | Skill | Catches a **fresh conversation** up on an **existing** investigation by reading `.ai` notes, asking what changed, and reporting next steps. |
| `skills/troubleshoot-second-opinion` | Skill | Spins up a review agent (user picks the model) to critique the reasoning in the `.ai` folder, update the notes, and report back. |
| `skills/troubleshoot-code-review-agents` | Skill | Triages open next-steps into "answerable by static code review" vs. not, then dispatches code-review agents to chase the static items and write findings back to `.ai`. |

## How they fit together

```
troubleshooter (agent)
  ├── new project?      → troubleshoot-init
  ├── resuming?         → troubleshoot-start-session
  ├── stuck / high-stakes? → troubleshoot-second-opinion
  └── static-analysis work? → troubleshoot-code-review-agents
        (all read/write the project's .ai folder)
```

The agent is the conductor: it forms hypotheses (a.k.a. **pocs** = possible
causes), designs the cheapest decisive test, routes each test (static analysis,
telemetry/Kusto, or "ask the user"), records results in `.ai`, and repeats until
root cause is established **and verified** with both a positive and a negative
test.

## The `.ai` folder convention

Every investigation keeps its state in a `.ai/` folder inside the project:

- Numbered notes, e.g. `00-tracking.md`, `01-context.md`, `02-hypotheses.md`,
  `05-next-steps.md`, …
- `00-tracking.md` is the table of contents + open-loops roadmap; it's refreshed
  after every note and at session end.
- Entries are dated and signed with the model name; previous entries are appended
  to, not rewritten.

`troubleshoot-init` is the source of truth for the exact file layout and
templates.

## Installation

The easiest way to install is to let Copilot CLI do it for you. Open the
GitHub Copilot CLI and paste this prompt:

```text
Clone https://github.com/Cardigan/TroubleshooterAgent and install its
troubleshooting toolkit into my Copilot CLI so it's available to me.

The repo contains:
- `skills/` — four `troubleshoot-*` skills, each its own folder with a SKILL.md
- `agents/troubleshooter.md` — the troubleshooter agent
- `.github/instructions/instructions.md` — `.ai` folder conventions the agent
  and skills rely on

Please install these where my Copilot CLI will pick them up (skills as skills,
the agent as an agent, and the instructions where they'll apply), confirm what
you installed and where, and clean up any temp clone.
```

To scope the toolkit to a single repository instead of user-wide, tell Copilot
to copy `skills/` and `agents/` into that repo's `.copilot\skills\` and
`.copilot\agents\` directories.

### Manual install

If you'd rather do it by hand, clone the repo and copy the components into your
Copilot CLI config directory:

```powershell
git clone https://github.com/Cardigan/TroubleshooterAgent
cd TroubleshooterAgent

# User-level (applies everywhere)
Copy-Item -Recurse -Force .\skills\*  "$env:USERPROFILE\.copilot\skills\"
Copy-Item -Force          .\agents\*  "$env:USERPROFILE\.copilot\agents\"
```

## Usage

- **Start a new investigation:** ask Copilot to "start a troubleshooting
  investigation" (or invoke the `troubleshoot-init` skill / `troubleshooter`
  agent). It will scaffold `.ai/` and ask how much interaction you want
  (Guided / Supervised / Autonomous).
- **Resume later:** in a new session, ask it to "catch up on this investigation"
  (`troubleshoot-start-session`).
- **Sanity-check a conclusion:** ask for a "second opinion"
  (`troubleshoot-second-opinion`).
- **Chase static items:** ask which next-steps can be answered by code review
  (`troubleshoot-code-review-agents`).

## Notes / dependencies

- The agent and skills reference `.ai` folder conventions (numbering, dating,
  the "make a note" verbatim rule, the 3-agent review, etc.). Those conventions
  live in [`.github/instructions/instructions.md`](.github/instructions/instructions.md).
  `troubleshoot-init` embeds the essential structure; the instructions file
  carries the extended conventions.
- Interaction modes, failsafes (time watchdog, iteration cap, no-progress
  detector, bounded waits), and the kill-switch / "Running Processes" tracking
  are all defined in `agents/troubleshooter.md`.
