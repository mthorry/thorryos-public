# Changelog

All notable changes to ThorryOS are recorded here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), versioning follows [SemVer](https://semver.org/).

## [v0.2.24] — 2026-09-02

### Added

- **`/plan` now handles spike tickets and gates on local testing.** A `[SPIKE]` or analysis-only ticket produces zero PRs and no Jira subtasks — the deliverable is a written artifact, and the output location (a Confluence page vs. a comment on the spike ticket) is decided by how many follow-on tickets and decisions it carries. Separately, `/plan` stops after a clean `/pr-review` and hands the branch off for real-app UI testing with the numbered steps written out, rather than prompting to commit.
- Affected: `thorry-pr-flow/commands/plan.md`.

### Changed

- **Instruction prose across commands and skills rewritten to match the house writing style.** These files are now checked by a style linter, and this release clears every phrasing it flagged: `load-bearing`, `lands`/`landed`, and `sanity-check` are replaced with wording that says what actually happens — "affects multiple teams", "went to the right person", "gut-check". Command behavior is unchanged; what changed is the wording these files teach Claude to imitate.
- Affected: `thorry-pr-flow/commands/{plan,pr-review,pr-test}.md`, `thorry-jira/commands/spike-doc.md`, `thorry-jira/skills/quick-tickets/SKILL.md`, `thorry-debriefs/skills/meeting-notes/SKILL.md`, `thorry-carrot-eligibility/skills/eligibility-context/SKILL.md`.

[v0.2.24]: https://github.com/mthorry/thorryos/releases/tag/v0.2.24

## [v0.2.23] — 2026-08-17

### Changed

- **`/jira-ticket` now omits the "Blocks / Blocked by" section entirely when a ticket has no real dependencies.** Previously the skill always emitted six sections with `Blocked by: none` / `Blocks: none` filler; an all-`none` block is noise, so a dependency-free ticket now has five sections and the block only appears when it conveys a real blocker relationship. Matches how refinement-ready tickets should read.
- Affected: `thorry-jira/skills/jira-ticket/SKILL.md`.

[v0.2.23]: https://github.com/mthorry/thorryos/releases/tag/v0.2.23

## [v0.2.22] — 2026-08-13

### Changed

- **`/pr-review` now treats any `(required)` repo-guideline violation as blocking, regardless of runtime risk.** When a diff breaks a guideline tagged `(required)` (from a `guidelines/README.md`-linked file — e.g. the DI-lifetime rule "any DB-dependent service must be `Scoped`"), the review classifies it as must-fix-before-merge even when the blast radius looks low, matching the org's automated `carrot-pr-agent`. The `convention-reviewer` agent now appends the guideline's severity tag (`[guideline: required|recommended|suggestion]`) to each guideline-derived finding so the classification is mechanical, not judgment. Also tightened `/plan` steps-to-test to resolve concrete seed values (display names, seeded emails, DB-assigned IDs) instead of emitting placeholder tokens.
- Affected: `thorry-pr-flow/commands/pr-review.md`, `thorry-pr-flow/agents/convention-reviewer.md`, `thorry-pr-flow/commands/plan.md`.

[v0.2.22]: https://github.com/mthorry/thorryos/releases/tag/v0.2.22

## [v0.2.21] — 2026-08-12

### Added

- **New `staff-engineer-review` agent — a blended staff-level reviewer distilled from real review history.** Auto-detects backend (C#/.NET) vs frontend (React/TS) from the changed files and applies the matching reviewer profile: a Backend reviewer (blended from carrot-app's senior reviewers) and a Frontend reviewer (blended from carrot-frontend / admin-shell's). Strong-suggestion posture — it never frames anything as blocking or "changes requested," and never names individual engineers. Runs standalone, or as the 6th `/pr-review` fan-out agent scoped to higher-altitude design judgment (layering, API shape, duplication → shared abstraction, boundary defensiveness) so it doesn't restate what the specialist agents already own; its findings feed the review's mechanical blocking/risk classification and populate a new section 7. Also fixed a stale "section 7" reference in the `/pr-review` context-loading step.
- Affected: `thorry-pr-flow/agents/staff-engineer-review.md` (new), `thorry-pr-flow/commands/pr-review.md`.

[v0.2.21]: https://github.com/mthorry/thorryos/releases/tag/v0.2.21

## [v0.2.20] — 2026-08-12

### Added

- **`/pr-review` now walks the repo's `guidelines/README.md` index, matching the org's `carrot-pr-agent`.** When a reviewed repo carries a root `guidelines/` directory (carrot-app does), `/pr-review` loads its scenario-gated index and applies each guideline whose scenario the diff triggers — so the local pre-submit pass stops diverging from the automated GitHub reviewer. The `convention-reviewer` agent gained a checklist question (Q8) that applies the triggered guidelines literally, respecting their `required/recommended/suggestion` severities; the DI-lifetime rule includes the out-of-diff registration lookup (grep `Dependencies.cs`, read the actual lifetime at head) that flags a DB-dependent service left as `Transient`/`Singleton`. Repos without the index skip the check silently.
- Affected: `thorry-pr-flow/commands/pr-review.md`, `thorry-pr-flow/agents/convention-reviewer.md`.

[v0.2.20]: https://github.com/mthorry/thorryos/releases/tag/v0.2.20

## [v0.2.19] — 2026-06-22

### Changed

- **README "60-second demo" now reflects the one-PR default.** The demo's `/plan` output still showed a three-PR breakdown of a soft-delete column add — the multi-PR-happy behavior the v0.2.17 `/plan` rewrite was meant to retire. It now returns a single PR (migration + read filter + tests together) plus one genuinely separate PR carrying an explicit `separate because:` trigger (the `CREATE INDEX CONCURRENTLY` step, isolated for deployment risk), so the demo teaches the current default and the per-PR trigger format rather than contradicting them.
- Affected: `README.md` (docs only — no plugin behavior changed).

[v0.2.19]: https://github.com/mthorry/thorryos/releases/tag/v0.2.19

## [v0.2.18] — 2026-06-22

### Changed

- **`/summarize-meeting` renamed to `/summarize-transcript`, and the workflow now shapes output to the recording type.** The command name was meeting-centric, but the underlying `meeting-notes` skill handles any transcript — including one-way recordings (Loom, screencast, async video). The renamed command instructs the skill to drop the "decisions / dissents / attendees" framing for a solo investigation or demo and keep only the sections that fit (TL;DR, what was established, action items, open questions). The skill's trigger description now covers `.srt` files and SRT-style comma timestamps, and explicitly names one-way recordings. The stale `/meeting` reference (dead since the v0.2.12 rename) was removed from the skill description and the `meet-prep` cross-reference now points at `/summarize-transcript`.
- Affected: `thorry-debriefs/commands/summarize-transcript.md` (was `summarize-meeting.md`), `thorry-debriefs/commands/meet-prep.md`, `thorry-debriefs/skills/meeting-notes`.

[v0.2.18]: https://github.com/mthorry/thorryos/releases/tag/v0.2.18

## [v0.2.17] — 2026-06-09

### Changed

- **`/plan` and `/spike-doc` now default to one PR / one ticket, splitting only on a concrete trigger.** The PR-breakdown step in `/plan` was rewritten to start from a single PR and multiply only when a named trigger fires — reviewer benefit (diff too large / parallel review), sequencing benefit (a later slice depends on an earlier one being deployed first), or deployment-risk isolation. "Smaller diff," "groups more neatly," "refactor-then-use," and "one PR per logical unit" are explicitly called out as non-reasons; if no trigger can be named, the work folds into the previous PR. `/spike-doc` ticket drafting now mirrors this: a ticket with 6 DoD bullets is fine, two artificially small tickets that only make sense together are not. Aligns both with the CLAUDE.md "default to one PR" principle.
- **`eligibility-db-setup` documents the PN-2193 reset-parity behavior.** The skill now describes what `reset-ee-db` / `reset-carrot-ee-db` actually populate (row-for-row `eligibility_entry` parity with dev `Employee`, sequence advanced past `100_000_000`, solo-EE fixtures across invitation states, `invitation` rows for EEs with registration data) and how to tune solo-EE volume/state mix via `PopulateEligibilityDb` in `appsettings.db.json`. The old "manually seed an entry" content is retained as the one-off path.
- Affected: `thorry-pr-flow/commands/plan.md`, `thorry-jira/commands/spike-doc.md`, `thorry-carrot-eligibility/skills/eligibility-db-setup`.

[v0.2.17]: https://github.com/mthorry/thorryos/releases/tag/v0.2.17

## [v0.2.16] — 2026-06-03

### Changed

- **`/plan` PR description shape now writes for a human reviewer first.** Replaces the `[x]`/`[ ]` checkbox "Test plan" with a **Steps to test locally** section: numbered, concrete, `do X → expect Y` steps a teammate can actually run — covering setup-once, happy path, an edge or failure case, and a regression check when load-bearing. Reframes the body sections (Summary, What changed, Choices, Steps to test, optional Agent notes) so a teammate skimming their queue can pick the PR up without reading the diff first. Adds an explicit **Agent notes** section at the bottom for LLM/agent-relevant context (file paths, invariants, non-auto-linking ticket refs) so humans can skip it. Body budget bumped from ~10/15 to ~15/25 lines — the steps-to-test section earns the room. Worked example included in the spec so the format is reproducible. `/pr-test` was already executing this section verbatim; now it has real steps to execute.
- **Removed the ThorryOS attribution footer from all producer skills and commands.** `jira-ticket`, `jira-file`, `spike-doc`, `spike-wrap`, `meeting-notes`, `shipped-note`, and `pr-review` no longer append `_This was created using ThorryOS..._` to their outputs. The footer added noise to every ticket, Confluence page, Slack post, and PR comment without conveying useful information — Co-Authored-By trailers already mark AI involvement where it matters. `jira-ticket` retains an optional `_Source: ..._` italic line for spike-doc / meeting linkage when applicable.
- Affected: `thorry-pr-flow/commands/plan.md`, `thorry-pr-flow/commands/pr-review.md`, `thorry-pr-flow/commands/routine-draft.md`, `thorry-pr-flow/skills/shipped-note`, `thorry-debriefs/skills/meeting-notes`, `thorry-jira/commands/spike-doc.md`, `thorry-jira/skills/jira-ticket`, `thorry-jira/skills/jira-file`, `thorry-jira/skills/spike-wrap`.

[v0.2.16]: https://github.com/mthorry/thorryos/releases/tag/v0.2.16

## [v0.2.15] — 2026-05-26

### Added

- **New `/context-refresh` command in `thorry-pr-flow`.** Takes a meeting transcript, Confluence URL, Google Doc URL, Jira URL, or Slack thread as input, reads your current `~/.claude/CLAUDE.md` and memory files (including `prodeng-context/org.yaml`), and compares them against the new information to surface stale sections — team names, contacts, mandates, priorities, shared infrastructure. Produces numbered diff-style proposals the user accepts or rejects per item; nothing is written until confirmed. Useful after all-hands meetings, reorgs, departures, and any org event that invalidates standing context. Emerged from a recurring pattern of manually auditing `~/.claude/` after every major org change.
- **`meeting-notes` adds a briefing mode (`--briefing` / `-b`) for informational meetings.** Auto-detect logic now uses three tiers: _briefing_ (one speaker ≳65% of lines, or no first-person commitment language / Q&A back-and-forth — catches all-hands, tech talks, exec announcements, vendor demos), _concise_ (short two-way discussions), and _verbose_ (substantive multi-party meetings). Briefing mode outputs a read-only summary — captured claims, announcements, numbers, stated positions — without an action-item table, since informational meetings don't produce commitments. Addresses the pattern of `/summarize-meeting` producing an empty "No action items" section for broadcast-style sessions.
- Affected: `thorry-pr-flow/commands/context-refresh.md`, `thorry-debriefs/skills/meeting-notes`.

[v0.2.15]: https://github.com/mthorry/thorryos/releases/tag/v0.2.15

## [v0.2.14] — 2026-05-20

### Changed

- **`/plan` now runs `/pr-review` as a pre-flight before opening a PR.** Before calling `gh pr create`, the workflow invokes `/pr-review` in local-diff mode against the merge-base with `main` and surfaces findings to the user — blocking items get fixed before the PR opens; non-blocking items get summarized for the user to address now or defer. Skips only if `/pr-review` already ran this session against the current HEAD with no new commits since, or if the user explicitly says to skip. Catches the class of issues (incomplete grep audits, missing failure-path tests, misleading exception types, docs that contradict the change) that would otherwise show up as the first round of human review.
- **`/plan` no longer auto-transitions the Jira ticket to "Ready for Review" on `gh pr create`.** New "Jira transition timing" section spells out the gate: CI green, CodeRabbit complete, PR no longer in draft. Transitioning before that puts a broken PR in reviewers' queues and skips the automated pre-human-review feedback loop. The transition is now a separate later step, prompted by the user once those gates have closed.
- **`shipped-note` no longer fires on `gh pr create`.** Opening a PR is not shipping; the skill now fires only on `gh pr merge` success, explicit "shipped/merged/deployed/got it in" from the user, or an already-merged PR URL. Description also reinforces "never post without explicit user confirmation." Caught when the skill drafted a shipped-note immediately after a PR was opened — that's not what shipping means.
- Affected: `thorry-pr-flow/commands/plan.md`, `thorry-pr-flow/skills/shipped-note`.

[v0.2.14]: https://github.com/mthorry/thorryos/releases/tag/v0.2.14

## [v0.2.13] — 2026-05-19

### Added

- **`/pr-review` now scans the diff for new suppressions before reviewer fan-out.** A fast local pass flags newly-added suppression tokens — `#pragma warning disable`, `[SuppressMessage]`, `// eslint-disable`, `// @ts-ignore`, ` as any`, empty `catch` blocks, `.only(` / `.skip(` test markers, and permissive config-threshold drift — and classifies each as `justified` (specific inline reason), `vague` ("TODO", "fix later"), or `unjustified` (no comment). Section 1 of the review now leads with these so the reviewer sees the author's intentional escapes before reading anything else. If clean, the section collapses to `No new suppressions.`
- **Opt-in personal-wiki integration in producer skills.** `meeting-notes` (thorry-debriefs), `shipped-note` (thorry-pr-flow), and `spike-doc` + `spike-wrap` (thorry-jira) now check for a wiki spec file at `~/.claude/references/wiki.md` and, if present, fold their artifacts (meeting pages, ship records, spike docs) into a personal markdown knowledge graph with cross-linked entity pages and a chronological log. No-op by default — skills behave exactly as before for anyone who hasn't created the spec file. Inspired by Karpathy's LLM-maintained-wiki pattern.
- Affected: `thorry-pr-flow/commands/pr-review.md`, `thorry-debriefs/skills/meeting-notes`, `thorry-pr-flow/skills/shipped-note`, `thorry-jira/skills/spike-wrap`, `thorry-jira/commands/spike-doc.md`.

[v0.2.13]: https://github.com/mthorry/thorryos/releases/tag/v0.2.13

## [v0.2.12] — 2026-05-19

### Changed

- **`/meeting` renamed to `/summarize-meeting` in `thorry-debriefs`.** Clarifies that this command produces a read-only structured readout of a transcript (no external actions). Pair it with `/close-meeting` to distribute outcomes. The `close-meeting.md` cross-reference updated to match.
- Affected: `thorry-debriefs/commands/summarize-meeting.md` (was `meeting.md`).

[v0.2.12]: https://github.com/mthorry/thorryos/releases/tag/v0.2.12

## [v0.2.11] — 2026-05-13

### Changed

- **`/pr-review` now loads the CodeRabbit review as pre-context before spawning agents.** If a CodeRabbit comment exists on the PR (`coderabbit-ai` bot), its walkthrough and flagged issues are fetched via `gh api` and passed to each reviewer agent — so agents focus on gaps CR missed rather than duplicating its file-by-file pass. If no CR comment exists, the step is silently skipped.
- **`/plan` adds a "Definition of done" section before the PR breakdown.** Section 3 is now 3–5 observable outcomes a reviewer can verify from outside the code. Subsequent sections renumbered (PR breakdown → 4, Risks → 5, Dependencies → 6, Defer → 7). Aligns with Working Principle 5: DoD agreed before work starts.
- **`shipped-note` hyperlinks Jira ticket keys in Slack mrkdwn format.** Bare keys (e.g. `PN-2244`) are no longer written — they trigger the Jira Slack integration and post noisy ticket cards. The skill now always emits `<https://carrotfertility.atlassian.net/browse/KEY|KEY>`, which suppresses that behavior.
- **Reviewer agents support local-diff (no-PR) mode.** All four sub-agents (`correctness-reviewer`, `test-coverage-auditor`, `ticket-fit-reviewer`, `convention-reviewer`) now accept either a PR number or an inline diff, enabling `/pr-review` to work without a remote PR.
- Affected: `thorry-pr-flow/commands/pr-review.md`, `thorry-pr-flow/commands/plan.md`, `thorry-pr-flow/skills/shipped-note`, `thorry-pr-flow/agents/`.

[v0.2.11]: https://github.com/mthorry/thorryos/releases/tag/v0.2.11

## [v0.2.10] — 2026-05-11

### Changed

- **`/pr-review` now requires explicit approval before posting to GitHub.** After agents complete, the review is output locally and the user is asked "Post this review to the PR? (yes / no)" — nothing is sent to GitHub until confirmed. Also added heredoc + raw-backtick guidance for `gh pr comment` so inline code renders correctly in GitHub markdown.
- **`/plan` tightens PR description length targets.** Context section drops from "one short paragraph" to "1-3 sentences"; bullet count drops from 3-6 to 2-3; line limit drops from ~20 to ~15. Added heredoc shell formatting guidance for `gh pr create` bodies.
- Affected: `thorry-pr-flow/commands/pr-review.md`, `thorry-pr-flow/commands/plan.md`.

[v0.2.10]: https://github.com/mthorry/thorryos/releases/tag/v0.2.10

## [v0.2.9] — 2026-05-08

### Changed

- **Removed the strict ≤300-line PR size threshold from `/pr-review` and global guidance.** The hard line-count check has been replaced with judgment-based guidance: flag size only when the diff is large enough to meaningfully slow a reviewer down and a logical split point exists. The previous rule was creating friction on well-scoped but larger changes and conflicted with the "well-scoped single PR beats two artificially small ones" principle already in `CLAUDE.md`.
- Affected: `thorry-pr-flow/commands/pr-review.md`.

[v0.2.9]: https://github.com/mthorry/thorryos/releases/tag/v0.2.9

## [v0.2.8] — 2026-05-08

### Changed

- **`eligibility-db-setup` skill now points at `setup-ee-db.sh` rather than inlining setup steps.** The actual setup logic lives in a new shell script (`NetCoreApp/EligibilityDb/setup-ee-db.sh`) that any teammate can run directly — no Claude required. First-time: `./EligibilityDb/setup-ee-db.sh`. Reset: `./EligibilityDb/setup-ee-db.sh --reset`. The `--exit-code-from` flag on `docker compose up` is used internally to resolve the hang-at-exit-code-0 issue without requiring users to know about it. The skill now acts as a lightweight error guide and seed SQL reference, deferring to the script for the happy path.
- Affected: `thorry-carrot-eligibility/skills/eligibility-db-setup`.

[v0.2.8]: https://github.com/mthorry/thorryos/releases/tag/v0.2.8

## [v0.2.7] — 2026-05-08

### Added

- **New `eligibility-db-setup` skill in `thorry-carrot-eligibility` — local EligibilityDB setup runbook.** Fires when the user mentions `docker compose --profile migrate-eligibilitydb`, `reset-carrot-ee-db`, `migrate-eligibilitydb-after-reset`, `eligibility_entry` seed inserts, or "set up local EEDB." Walks the canonical setup ladder (Docker Desktop → `docker compose migrate` → DataGrip → Rider reset → seed SQL) and includes an error table for the five most common failures: Docker not running, wrong working directory, `sa` user mismatch, command hanging at exit code 0, and VPN required for stage/prod. References the Confluence setup page as the authoritative source.
- Affected: `thorry-carrot-eligibility/skills/eligibility-db-setup`.

[v0.2.7]: https://github.com/mthorry/thorryos/releases/tag/v0.2.7

## [v0.2.6] — 2026-05-06

### Added

- **New `/meet-prep` command in `thorry-debriefs` — pre-meeting brief from a Slack thread.** Pass a Slack thread URL or a topic string; the command reads the thread (or searches for the most relevant recent one), then produces a structured brief: background, expected agenda/decisions, questions to be ready to answer, talking points, and risks/blockers to surface proactively. Complements `/meeting`, which processes transcripts after the fact.
- Affected: `thorry-debriefs/commands/meet-prep.md`.

### Changed

- **`/pr-review` now includes a sub-agent maintenance rule.** A new note at the top of the command makes explicit that any edit to `pr-review.md` must also update the five parallel reviewer agents (`correctness-reviewer`, `test-coverage-auditor`, `ticket-fit-reviewer`, `convention-reviewer`, `migration-reviewer`) in the same session. Prevents the agents drifting out of sync when the command evolves. Output format also tightened: `simplify` delegation removed, section references simplified, `migration-reviewer` folded cleanly into the correctness section.
- **`/plan` attribution footer updated.** Link text changed to `[ThorryOS](https://github.com/mthorry/thorryos-public)` so the footer is self-contained without a separate Claude link.
- Affected: `thorry-pr-flow/commands/pr-review.md`, `thorry-pr-flow/commands/plan.md`, all five `thorry-pr-flow/agents/*.md`.

[v0.2.6]: https://github.com/mthorry/thorryos/releases/tag/v0.2.6

## [v0.2.5] — 2026-05-04

### Changed

- **`/meeting` now supports `--concise` and `--verbose` flags, with auto-detection as the default.** Quick syncs and short meetings (under ~30 min or ~300 transcript lines) auto-select concise mode: a 2–3 sentence summary, one-liner decisions, a compact action items table, and a Slack 1-liner — no rationale breakdowns, attribution map, or dissents section. Larger meetings get the full verbose readout. Pass `--concise` / `-c` or `--verbose` / `-v` to override the auto-detection.
- Affected: `thorry-debriefs/commands/meeting.md`, `thorry-debriefs/skills/meeting-notes/SKILL.md`.

[v0.2.5]: https://github.com/mthorry/thorryos/releases/tag/v0.2.5

## [v0.2.4] — 2026-05-04

### Changed

- **`/plan` and `CLAUDE.md` no longer bias toward splitting work into multiple PRs by default.** The PR breakdown instruction now reads: split only when it improves review speed, enables parallel review, or isolates deployment risk — not to produce smaller diffs for their own sake. The objective function entry ("more, smaller PRs") and the Pull Requests section of `CLAUDE.md` are updated to match. A well-scoped single PR is explicitly preferred over two artificially small ones.
- **`/import-review` now loads eligibility domain context before producing findings.** When the resolved diff touches `Carrot.Eligibility` paths, `EligibilityEntry`, `EligibleDependent`, `EligibilityDB`, `EligibilityService`, or related patterns, the skill invokes `eligibility-context` (loading `overview.md` + `BEST_PRACTICES.md`) and frames findings against the documented current-state vs future-state model, with explicit callout of anti-patterns.
- **`/triage` and `jira-triage` now apply pre-validation rules before ranking hypotheses.** No error text in the ticket → make "retrieve the actual error from logs" the entire validation plan rather than generating code-path hypotheses blind. Member action failures → check entity existence first (EligibilityEntry present?) before reasoning about why the action was rejected. Checks are ordered cheapest-first: entity existence → actual error text → business logic → config/data shape.
- Affected: `thorry-pr-flow/commands/plan.md`, `thorry-pr-flow/commands/import-review.md`, `thorry-pr-flow/commands/triage.md`, `thorry-jira/skills/jira-triage/SKILL.md`.

[v0.2.4]: https://github.com/mthorry/thorryos/releases/tag/v0.2.4

## [v0.2.3] — 2026-05-04

### Changed

- **`/plan` PR descriptions are now bounded and stop polluting Jira cross-references.** New "PR description shape" rule: aim for ~15 lines, max ~20 — one short context paragraph, 3–6 scope bullets, optional one-line "Choices" only for things a reviewer might push back on, and a test plan. Long risk justifications, alternatives considered, and "future work" lists move to PR comments. Separately, **don't cross-reference sibling PRs or tickets in the body for stack-relationship signaling** (e.g. "stacked on #8367 (PN-2247)"); Jira's GitHub integration auto-links the PR to every ticket key it finds, polluting the ticket-to-PR cross-references on every sibling. The stack relationship is already visible via the GitHub base branch.
- **`/triage` now accepts Jira ticket keys or URLs and runs a Jira-aware flow.** When invoked with `/triage ENOC-123` or a Jira URL, the command fetches the ticket + comments, extracts symptom / customer / run context / troubleshooting history, and produces a structured readout (ranked hypotheses → validation plan → what to skip → customer context → recurrence detection). Falls back to the generic triage flow for free-form bug descriptions. Bypasses the project filter on the `jira-triage` skill — explicit `/triage` works on any Jira ticket.
- Affected: `thorry-pr-flow/commands/plan.md`, `thorry-pr-flow/commands/triage.md`.

[v0.2.3]: https://github.com/mthorry/thorryos/releases/tag/v0.2.3

## [v0.2.2] — 2026-05-04

### Changed

- **`/pr-review` now appends a ThorryOS attribution footer to every comment it posts to GitHub.** Any inline or top-level PR comment posted via `gh` will end with `_This was sent using [ThorryOS](…) with [Claude](…)._`, making AI involvement visible on the PR thread.
- **`/pr-test` now reuses the primary checkout when it's already on the PR branch** instead of always creating a `/tmp` worktree. Concurrent phase execution (worktree I/O overlapped with diff analysis) is also made explicit.
- Affected: `thorry-pr-flow/commands/pr-review.md`, `thorry-pr-flow/commands/pr-test.md`.

[v0.2.2]: https://github.com/mthorry/thorryos/releases/tag/v0.2.2

## [v0.2.1] — 2026-05-04

### Changed

- **`/plan` now creates draft PRs for blocked work and promotes the first subtask to Ready for Review.** When a sequential (stacked) PR plan is executed, PR 2 and beyond are opened with `gh pr create --draft` so they don't land in reviewers' queues before their blocker merges. PR 1 is opened as a regular non-draft PR, and its Jira subtask is immediately transitioned to "Ready for Review." When each blocker merges and GitHub auto-retargets the next branch, the author un-drafts it. Genuinely parallel PRs (no Blocks chain) are all opened non-draft with all subtasks promoted.
- Affected: `thorry-pr-flow/commands/plan.md`.

[v0.2.1]: https://github.com/mthorry/thorryos/releases/tag/v0.2.1

## [v0.2.0] — 2026-05-01

### Changed (breaking)

- **Renamed `thorry-meetings` → `thorry-debriefs`.** The plugin's job has always been "turn raw input into a structured readout for an audience" — calling it `meetings` understated the scope and miscued the trigger. `debriefs` matches what the skills actually do: extract decisions, action items, and risks from a completed event. Anyone with `thorry-meetings` enabled will need to enable `thorry-debriefs` after `/plugin marketplace update`.
- **Moved `spike-wrap` from `thorry-meetings` (now `thorry-debriefs`) to `thorry-jira`.** The skill announces a completed spike, which is the natural pair to `/spike-doc` (research → Confluence + ticket cluster) already living in `thorry-jira`. Grouping them puts the spike workflow under one plugin instead of straddling two. If you previously had only `thorry-meetings` enabled and used `spike-wrap`, you'll need to enable `thorry-jira` to keep using it.

### Affected

- `thorry-meetings/` → `thorry-debriefs/` (directory renamed)
- `thorry-meetings/skills/spike-wrap/` → `thorry-jira/skills/spike-wrap/` (skill relocated)
- `thorry-debriefs/.claude-plugin/plugin.json`, `thorry-jira/.claude-plugin/plugin.json` (descriptions updated)
- `.claude-plugin/marketplace.json`, `README.md`, `CONTRIBUTING.md`, `scripts/sync-from-claude.sh`

[v0.2.0]: https://github.com/mthorry/thorryos/releases/tag/v0.2.0

## [v0.1.8] — 2026-05-01

### Added

- **`/pr-review` now fans out to parallel specialist agents instead of running ten sections sequentially in the main context.** Sections 1–2 (correctness + edges), 4 (tests), 9 (ticket fit), and 10 (conventions) now spawn one focused agent each — `correctness-reviewer`, `test-coverage-auditor`, `ticket-fit-reviewer`, `convention-reviewer` — in a single round trip. A fifth, `migration-reviewer`, conditionally fires when the diff includes EF / SQL migrations and folds back into section 1. Each agent reads the diff in its own context window with read-only tools and returns a paragraph of grounded findings, so wall-clock time roughly equals the slowest reviewer (not the sum) and the diff stays out of the orchestrator's main context. Each agent enforces the "would Matt actually leave this as a PR comment?" filter independently and returns a clean line (e.g. "No correctness issues.") when nothing's wrong, so the user always sees that the lens ran. `agents/` is a new top-level layout type for ThorryOS plugins, alongside `commands/` and `skills/`.
- Affected: `thorry-pr-flow/agents/correctness-reviewer.md`, `thorry-pr-flow/agents/test-coverage-auditor.md`, `thorry-pr-flow/agents/ticket-fit-reviewer.md`, `thorry-pr-flow/agents/convention-reviewer.md`, `thorry-pr-flow/agents/migration-reviewer.md`, `thorry-pr-flow/commands/pr-review.md`.

[v0.1.8]: https://github.com/mthorry/thorryos/releases/tag/v0.1.8

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
