```
 ████████╗██╗  ██╗ ██████╗ ██████╗ ██████╗ ██╗   ██╗
 ╚══██╔══╝██║  ██║██╔═══██╗██╔══██╗██╔══██╗╚██╗ ██╔╝
    ██║   ███████║██║   ██║██████╔╝██████╔╝ ╚████╔╝ ___  ___
    ██║   ██╔══██║██║   ██║██╔══██╗██╔══██╗  ╚██╔╝ / _ \/ __|
    ██║   ██║  ██║╚██████╔╝██║  ██║██║  ██║   ██║ | (_) \__ \
    ╚═╝   ╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═╝   ╚═╝  \___/|___/
```

# ThorryOS

> 📖 **Public docs.** The plugin marketplace itself lives in a private repo at [`mthorry/thorryos`](https://github.com/mthorry/thorryos) — ping [@mthorry](https://github.com/mthorry) on Slack to be added as a collaborator before installing. This repo just holds the README, CHANGELOG, and CONTRIBUTING so the docs are readable without access.

[![Version](https://img.shields.io/badge/version-0.1.3-blue.svg)](CHANGELOG.md)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Plugins](https://img.shields.io/badge/plugins-4-orange.svg)](#whats-in-here)
[![Skills + Commands](https://img.shields.io/badge/skills%20%2B%20commands-19-purple.svg)](#whats-in-here)
[![Built for](https://img.shields.io/badge/built%20for-Carrot%20engineers-ff69b4.svg)](#built-for-carrot-engineers)
[![Claude Code](https://img.shields.io/badge/claude%20code-plugin%20marketplace-7c3aed.svg)](https://docs.claude.com/en/docs/claude-code/plugins)

> **A Claude Code plugin marketplace** with the IC operating model, meeting flow, Jira workflow, and eligibility-domain helpers Matt Thorry runs day-to-day. Opt-in plugins so teammates pick what's useful and skip the rest.

---

## Table of contents

- [Why this exists](#why-this-exists)
- [Quick install](#quick-install) — 60 seconds, copy-paste
- [Built for Carrot engineers](#built-for-carrot-engineers)
- [What's in here](#whats-in-here)
  - [`thorry-pr-flow`](#thorry-pr-flow)
  - [`thorry-meetings`](#thorry-meetings)
  - [`thorry-jira`](#thorry-jira)
  - [`thorry-carrot-eligibility`](#thorry-carrot-eligibility)
- [60-second demo](#60-second-demo)
- [Configuration](#configuration--claudethorryosconfigjson) — `~/.claude/thorryos.config.json`
- [Updates](#updates)
- [How this is built](#how-this-is-built) — for the curious
- [What's NOT shipped](#whats-not-shipped)
- [Layout](#layout)
- [Contributing / suggesting skills](#contributing--suggesting-skills)
- [Versioning](#versioning)
- [License](#license)

---

## Why this exists

Engineering velocity at Carrot depends on shipping small PRs, fast cycle time, and reducing toil — see the IC operating model. ThorryOS bundles the slash commands and skills that make those rules **invocable**, not just aspirational. `/plan` decomposes work into PR-sized units. `/triage` enforces ranked-hypothesis debugging. `jira-ticket` produces refinement-ready tickets in seconds. The point is to remove the "remember to do this the right way" tax.

This started as `~/.claude/` on Matt's laptop. ThorryOS publishes it as a marketplace so teammates can adopt the patterns piece by piece without copying files around.

---

## Quick install

In Claude Code, run these two commands:

```
/plugin marketplace add mthorry/thorryos
/plugin
```

The first registers this repo as a marketplace; the second opens the picker so you can enable the plugins you want.

**Access required:** this repo is private under `mthorry`. Ping Matt to be added as a collaborator first, and make sure your `gh auth status` is logged in — `/plugin marketplace add` uses your local GitHub credentials to clone.

You'll see four plugins:

```
  thorry-pr-flow             [ ]   IC operating model commands + shipped-note
  thorry-meetings            [ ]   Meeting transcripts + spike wrap-ups
  thorry-jira                [ ]   Jira ticket / file / triage / quick-pickup
  thorry-carrot-eligibility  [ ]   Eligibility domain auto-context (Carrot-internal)
```

Toggle any of them with the cursor + spacebar, then close the picker. Plugins are **opt-in** — installing the marketplace alone does nothing until you enable specific plugins.

---

## Built for Carrot engineers

ThorryOS is intentionally not generic. It assumes:

- The Carrot Jira instance (`carrotfertility.atlassian.net`) and project keys (`PN`, `OPSDSK`, `ENOC`)
- The carrot-app monorepo at `~/workspace/carrot-app`
- The admin-shell turbo workspace at `~/workspace/admin-shell`
- Carrot sprint conventions (no story points on filing, sprint id 217 = Prioritized Backlog)
- `*.local.get-carrot.com` as the only acceptable local-dev origin
- Custom field IDs: `customfield_10006` (story points), `customfield_10020` (sprint)
- The Hangfire NuGet auth flow for carrot-app builds

If you're not at Carrot, almost everything except `/plan`, `/scope`, `/triage`, and `meeting-notes` will need rewriting before it's useful. We've left the Carrot specifics in deliberately — they're the value, not decoration.

---

## What's in here

Four plugins, each independently installable. Skills auto-fire on description match; commands are user-typed.

### `thorry-pr-flow`

The IC operating model commands plus simplification + ship visibility.

| Type | Name | What it does |
|---|---|---|
| command | `/plan <task>` | Decompose work into PR-sized units with risks, edges, sequencing |
| command | `/scope <task>` | Find the smallest viable v1 and what to defer |
| command | `/triage <symptom>` | Ranked-hypothesis debugging — validate before code changes |
| command | `/pr-review <PR or Jira>` | Pre-submit AI review: bugs, edges, tests, ticket alignment, simplify delegation |
| command | `/pr-test <PR or Jira>` | Execute a PR's manual test plan against local/dev (hard guardrail: never stage or prod) |
| command | `/import-review` | Import / pipeline / data ingestion review against the Backend section |
| command | `/routine-draft <goal>` | Draft a scheduled-task spec ready to paste into the macOS Routines UI |
| skill | `simplify` | Find reuse / dead branches / loop-collapses in a diff. Used by `/pr-review`, fires on its own when asked |
| skill | `shipped-note` | Auto-drafts a 1-line team-channel note when non-trivial work ships |

### `thorry-meetings`

| Type | Name | What it does |
|---|---|---|
| command | `/meeting <transcript>` | Turn a Zoom (or other) transcript into structured readout |
| skill | `meeting-notes` | Same readout, auto-fires when a transcript is detected (`.vtt` file, timestamp lines, etc.) |
| skill | `spike-wrap` | Draft a Slack-ready team note announcing a completed spike |

### `thorry-jira`

| Type | Name | What it does |
|---|---|---|
| command | `/spike-doc <PN-NNNN>` | Spike → Confluence doc + dependent ticket cluster with blocks links, in one flow |
| command | `/diagram-from-outline <outline>` | Turn a phased work outline into a FigJam diagram |
| skill | `jira-ticket` | Draft refinement-ready Jira tickets (title, description, DoD, blocks/blocked-by) |
| skill | `jira-file` | File a batch of drafted tickets with parent epic + blocking links wired up |
| skill | `jira-triage` | Auto-triage on-call tickets (`OPSDSK-*`, `ENOC-*`, on-call-flagged `PN-*`) with ranked hypotheses |
| skill | `quick-tickets` | Surface 1-2-pointer / under-20-LoC tickets you can pick up |

### `thorry-carrot-eligibility`

| Type | Name | What it does |
|---|---|---|
| skill | `eligibility-context` | Auto-load `NetCoreApp/Carrot.Eligibility/AI_Context_Docs/{overview,best-practices}.md` whenever you're working in that domain |

Useful only if you have the carrot-app repo cloned at the path it expects. If you don't work in `Carrot.Eligibility`, leave this plugin disabled.

---

## 60-second demo

Try this with `thorry-pr-flow` enabled:

```
/plan add a soft-delete column to the EmployeeImportLog table
```

You'll get back something like:

```
1. Minimum viable outcome
   Add a `DeletedAt` column to EmployeeImportLog and route reads to filter it out.

2. Ambiguities
   - Is the column nullable + indexed, or are you backfilling? — judgment call: nullable, no backfill,
     index added in a follow-up PR
   - Should the EF migration be reversible? — YES, always.

3. PR breakdown
   - PR #1 (~80 LOC): EF migration adding nullable `DeletedAt`, no read changes
   - PR #2 (~120 LOC): repository methods filter `WHERE DeletedAt IS NULL`, add unit tests
   - PR #3 (~40 LOC): one-off cleanup — delete an obvious caller path that re-fetches

4. Risks and edges
   - Existing readers don't filter → they'll see soft-deleted rows. Test with a seeded deleted row.
   - Migration on a 50M-row table is fine for nullable add; index in a separate PR with CONCURRENTLY.

…
```

That's it. No back-and-forth, no "now decompose it more." The skill enforces the operating model on the first response.

---

## Configuration — `~/.claude/thorryos.config.json`

Some skills (currently `spike-wrap`, `shipped-note`) ask once for a default Slack channel and then remember it across sessions. The answer is stored at `~/.claude/thorryos.config.json`:

```json
{
  "default_slack_channel": {
    "name": "#your-team-channel",
    "id": "123XYZ"
  }
}
```

- **First run:** the skill prompts for your default channel, resolves its ID via `slack_search_channels`, and writes both to the file.
- **Opt out:** answer "skip" — the skill writes `"default_slack_channel": null` so you don't get re-prompted; it'll just ask which channel each time.
- **Change it later:** edit the file by hand, delete the file to start over, or set the value to `null`.

This config lives outside the plugin cache so it survives `/plugin marketplace update`. The same file is shared across all ThorryOS skills, so setting the default once covers spike-wrap, shipped-note, and any future Slack-posting skills.

---

## Updates

To pull the latest changes after a new release:

```
/plugin marketplace update thorryos
```

Your enabled plugins re-load against the new versions automatically. Your `thorryos.config.json` survives because it's outside the plugin cache.

See [CHANGELOG.md](CHANGELOG.md) for what's new in each release.

---

## How this is built

For curious teammates:

- **Plugin cache survives updates:** per-user config (`~/.claude/thorryos.config.json`) lives outside any plugin's directory, so `/plugin marketplace update` doesn't blow it away.
- **Versioning:** semver via git tags (`v0.1.0`, etc). Each `plugin.json` has a matching `version` field.
- **Skills vs commands:** commands are user-typed (`/plan`, `/triage`); skills auto-fire when their description matches. Both live inside the per-plugin directories.

See [CONTRIBUTING.md](CONTRIBUTING.md) for the workflow if you want to suggest a skill or contribute one.

---

## What's NOT shipped

- **`CLAUDE.md`** — Matt's personal global instructions live in `~/.claude/CLAUDE.md` and aren't a plugin-shaped artifact. Several skills reference "CLAUDE.md Output > Activity" / "CLAUDE.md Tool Usage" — they still work without that exact file, but the references are flavor, not a hard dependency.
- **Auto-memory** — per-cwd memories under `~/.claude/projects/<encoded-cwd>/memory/` are personal and stay out of the marketplace.
- **Slack ID cache** — `~/.claude/references/slack-ids.md` (channel + user IDs) is personal and not shipped. Slack-posting skills check for it as an optional cache and fall back to live `slack_search_*` calls if absent.
- **`settings.json` / hooks** — those are per-user harness configuration, not plugin artifacts. Use the `update-config` skill to manage your own.

---

## Layout

```
thorryos/
├── .claude-plugin/
│   └── marketplace.json          # marketplace manifest — what /plugin reads
├── thorry-pr-flow/
│   ├── .claude-plugin/plugin.json
│   ├── commands/                 # slash commands (user-typed)
│   └── skills/                   # auto-fire on description match
├── thorry-meetings/
│   ├── .claude-plugin/plugin.json
│   ├── commands/
│   └── skills/
├── thorry-jira/
│   ├── .claude-plugin/plugin.json
│   ├── commands/
│   └── skills/
├── thorry-carrot-eligibility/
│   ├── .claude-plugin/plugin.json
│   └── skills/
├── CHANGELOG.md                  # release notes
├── CONTRIBUTING.md               # how to suggest / contribute skills
├── LICENSE                       # MIT
└── README.md                     # this file
```

---

## Contributing / suggesting skills

Have a skill or command you'd want? See [CONTRIBUTING.md](CONTRIBUTING.md) — short version: open a GitHub issue with the trigger, the rough output, and one or two real evidence examples (chat / Jira / Slack snippets) showing the work pattern. Keeps the bar at "this is a real, repeated pattern" not "this would be cool."

If you'd like to maintain your own version of any of these skills, fork the repo and edit the plugin files directly — they're plain markdown.

---

## Versioning

Semantic versioning via git tags:

- `v0.x.x` — pre-1.0, breaking changes possible between minor versions
- `v1.0.0` — first stable release; minor bumps are additive only

Current: `v0.1.0`. See [releases](https://github.com/mthorry/thorryos/releases) for what's shipped.

---

## License

MIT. See [LICENSE](LICENSE).

---

Authored and maintained by **Matt Thorry** ([@mthorry](https://github.com/mthorry)).
