# Changelog

All notable changes to ThorryOS are recorded here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), versioning follows [SemVer](https://semver.org/).

## [v0.1.7] — 2026-05-01

### Changed

- **PRs opened during a `/plan` workflow now use the canonical ThorryOS attribution footer.** `/plan` overrides the global PR template's `Generated with [Claude Code]` line with `*This was created using ThorryOS with [Claude](https://claude.com/claude-code). [Learn more](https://github.com/mthorry/thorryos-public).*` — same wording as Jira sub-tasks, Slack messages, and Confluence pages produced by other ThorryOS skills. Brings PRs into the same attribution surface as the rest of the artifacts and keeps `Learn more` pointing at the public docs mirror.
- Affected: `thorry-pr-flow/commands/plan.md`.

[v0.1.7]: https://github.com/mthorry/thorryos/releases/tag/v0.1.7

## [v0.1.6] — 2026-05-01

### Changed

- **`/plan` now operates the full multi-PR workflow end-to-end, not just the prose breakdown.** When a plan contains 2+ PRs and the parent ticket is known, `/plan` files Jira sub-tasks with the canonical ThorryOS attribution footer, links them as a sequential `Blocks` chain so the dependency order is explicit on each ticket, and prescribes the stacked-PR branch convention (each PR branches off the previous one and targets it as the base, GitHub auto-retargets to `main` as the chain merges). Single-PR plans skip the subtask step. Parallel PRs (no `Blocks` chain) branch off `main` directly. Branch naming convention: `<TICKET-KEY>-<kebab-description>` using each subtask's own key, not the parent's.
- Affected: `thorry-pr-flow/commands/plan.md`.

[v0.1.6]: https://github.com/mthorry/thorryos/releases/tag/v0.1.6

## [v0.1.5] — 2026-05-01

### Changed

- **`/plan` and `/scope` now load domain context before producing output.** Both commands gained a "Step 0 — Load domain context" preamble that infers whether the task touches a domain with its own context skill and invokes it before breaking down PRs or shaping scope. Today the only registered domain is `eligibility-context` (Carrot.Eligibility) — when it fires, planning and scoping are framed against the documented current-state vs future-state model and `BEST_PRACTICES.md` anti-patterns are called out explicitly rather than silently designed around. New domain-context skills can be added to the bullet list as they're created.
- Affected: `thorry-pr-flow/commands/plan.md`, `thorry-pr-flow/commands/scope.md`.

[v0.1.5]: https://github.com/mthorry/thorryos/releases/tag/v0.1.5

## [v0.1.4] — 2026-05-01

### Changed

- **Attribution footer links now point to the public docs mirror** ([github.com/mthorry/thorryos-public](https://github.com/mthorry/thorryos-public)) instead of the private marketplace repo. Anyone clicking the "Learn more" link in a generated Slack message, Jira ticket, Confluence page, or meeting readout now lands on docs they can actually read — no more 404 for non-collaborators.
- Affected: `shipped-note`, `spike-wrap`, `meeting-notes` (1-liner + full readout), `jira-ticket`, `jira-file`, `/spike-doc`.

[v0.1.4]: https://github.com/mthorry/thorryos/releases/tag/v0.1.4

## [v0.1.3] — 2026-05-01

### Changed

- **Confluence pages (`/spike-doc`):** the two-line attribution stack collapses into a single combined italic line, matching the v0.1.2 Jira pattern. Same wording: `*This was created using ThorryOS with [Claude](https://claude.com/claude-code). [Learn more](https://github.com/mthorry/thorryos).*`

[v0.1.3]: https://github.com/mthorry/thorryos/releases/tag/v0.1.3

## [v0.1.2] — 2026-05-01

### Changed

- **Jira tickets:** the two-line attribution stack collapses into a single combined italic line, reducing footer clutter on tickets reviewed in refinement.
  - Before: separate Claude line + ThorryOS line below the divider.
  - After: `*This was created using ThorryOS with [Claude](https://claude.com/claude-code). [Learn more](https://github.com/mthorry/thorryos).*`
- Affected: `jira-ticket`, `jira-file` (which preserves jira-ticket's footer).

[v0.1.2]: https://github.com/mthorry/thorryos/releases/tag/v0.1.2

## [v0.1.1] — 2026-05-01

Polish pass before sharing with the team.

### Changed

- Generated artifacts (Slack messages, Jira tickets, Confluence pages, meeting readouts) now include a small italic source-attribution footer at the bottom: `*This was created using ThorryOS. [Learn more](https://github.com/mthorry/thorryos).*` (Slack mrkdwn equivalent on Slack-format outputs). Sits below the existing Claude attribution where one is already present.
- Affected: `shipped-note`, `spike-wrap`, `meeting-notes` (1-liner + full readout), `jira-ticket`, `jira-file`, `/spike-doc`.
- Plugin and marketplace versions bumped to `0.1.1`.

[v0.1.1]: https://github.com/mthorry/thorryos/releases/tag/v0.1.1

## [v0.1.0] — 2026-05-01

Initial public-ish release. ThorryOS goes from "scattered files in `~/.claude/`" to "a Claude Code plugin marketplace teammates can opt into."

### Added

#### Plugins
- **`thorry-pr-flow`** — IC operating model commands (`/plan`, `/scope`, `/triage`, `/pr-review`, `/pr-test`, `/import-review`, `/routine-draft`) plus the `simplify` and `shipped-note` skills.
- **`thorry-meetings`** — `/meeting` command, `meeting-notes` and `spike-wrap` skills.
- **`thorry-jira`** — `/spike-doc` and `/diagram-from-outline` commands; `jira-ticket`, `jira-file`, `jira-triage`, `quick-tickets` skills.
- **`thorry-carrot-eligibility`** — `eligibility-context` skill (Carrot-internal).

#### Tooling
- `CONTRIBUTING.md` — workflow for suggesting / authoring skills.
- `~/.claude/thorryos.config.json` config-file pattern — per-user default Slack channel, persisted across sessions and surviving `/plugin marketplace update`.

#### Newly authored skills/commands (vs. the source `~/.claude/`)
- `/spike-doc` — spike → Confluence doc + dependent Jira ticket cluster with blocks links, in one flow. Replaces the multi-step manual ritual that was the most time-consuming workflow in author's week.
- `/routine-draft` — ready-to-paste scheduled-task spec for the macOS Routines UI. Encodes the canonical four scope sources (Claude transcripts, git, GitHub PRs, Slack) and the dedupe-against-prior-state pattern.
- `/diagram-from-outline` — turns a phased work outline into a FigJam diagram via the Figma MCP.
- `simplify` skill — explicit reuse / dead-branch / loop-collapse review, scoped to "would Matt leave this as a PR comment" so it doesn't generate noise. Used by `/pr-review`.

### Design decisions

- **`~/.claude/` is the source of truth.** The marketplace is a synced mirror, not a parallel codebase. Edits flow one direction (`~/.claude/` → marketplace) via local maintainer tooling.
- **Per-user config outside the plugin cache.** `~/.claude/thorryos.config.json` survives marketplace updates so user preferences (default Slack channel) aren't reset on every pull.
- **Carrot specifics preserved deliberately.** The audience is Carrot engineers; the Jira instance, project keys, sprint IDs, NetCoreApp paths, and `*.local.get-carrot.com` host patterns are the value, not decoration.
- **Private repo, opt-in plugins.** Visibility is for teammates only (private GitHub). Once installed, plugins are off by default — engineers enable just the plugins they want via `/plugin`.

### Repo hygiene

- README: badges, table of contents, 60-second demo, "Built for Carrot engineers" framing up front instead of as a buried caveat, install path with what-success-looks-like guidance.
- LICENSE: MIT.
- `.gitignore` covering `.DS_Store`, IDE folders, `node_modules/`.

[v0.1.0]: https://github.com/mthorry/thorryos/releases/tag/v0.1.0
