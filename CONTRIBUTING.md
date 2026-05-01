# Contributing to ThorryOS

Thanks for the interest. ThorryOS is a personal Claude Code OS published as a marketplace; the bar for adding a skill is **"this is a real, repeated pattern"** — not "this would be cool."

---

## Suggesting a skill or command

Open a [GitHub issue](https://github.com/mthorry/thorryos/issues) with:

1. **What pattern you keep doing manually.** One paragraph. Be concrete.
2. **Trigger.** When should the skill auto-fire / when would you type the command?
3. **Rough output.** What should it produce — a Slack message, a Jira ticket, a file? Sketch the shape, not full prose.
4. **Evidence.** 2–3 real examples (chat snippets, Jira tickets, Slack messages, git commits) that show the pattern. If you can't produce 2 examples, the pattern probably isn't repeated enough yet.

The goal of the evidence requirement is to keep the marketplace lean. A skill that fires once a quarter adds context-window cost every session for negligible value.

---

## Authoring a skill or command

If you want to ship a draft yourself, open a PR. Conventions:

### Skill (`SKILL.md`)

- Lives at `<plugin>/skills/<name>/SKILL.md`
- Frontmatter:
  ```yaml
  ---
  name: <kebab-case-name>
  description: |
    Trigger-phrase-rich description. FIRES when [specific conditions].
    SKIPS for [adjacent patterns the skill should not handle].
  ---
  ```
- Body written **for Claude, not for the user.** Imperative voice ("Read the file…"), not "You should read the file."
- One file. If it needs reference material, put it in `<plugin>/skills/<name>/references/*.md` and have the skill read those on demand.

### Command (`<name>.md`)

- Lives at `<plugin>/commands/<name>.md`
- Frontmatter:
  ```yaml
  ---
  description: <one-line summary, shown in /help>
  argument-hint: <what to pass after the command>
  ---
  ```
- Body is the prompt that runs when the command fires. `$ARGUMENTS` substitutes whatever the user typed.

### Plugin assignment

Pick the plugin whose theme fits:

- **`thorry-pr-flow`** — anything in the IC operating model: planning, scoping, debugging, PR review, ship signaling.
- **`thorry-debriefs`** — meeting transcripts → structured readouts. Anything that turns a recorded conversation into decisions, action items, and risks.
- **`thorry-jira`** — Jira drafting, filing, triage, backlog scanning, plus the spike workflow (research doc + announcement).
- **`thorry-carrot-eligibility`** — only Carrot.Eligibility-domain context.

If your skill doesn't fit any existing plugin, propose a new one in the issue.

### What goes in `description`

The description is what Claude reads to decide whether to fire the skill. It should:

- Name **specific** triggers (file extensions, phrases, ticket prefixes)
- Name **specific** non-triggers ("SKIPS for [adjacent]")
- Be under ~3 sentences

Vague descriptions cause skills to over-fire (annoying) or under-fire (invisible). See `meeting-notes/SKILL.md` or `jira-triage/SKILL.md` for examples that strike the balance.

---

## What's out of scope

- **Generic productivity skills.** This marketplace is shaped around Carrot's stack; if your skill makes equal sense at any company, ship it as its own marketplace instead — it'll get more reach.
- **Code that runs against shared infrastructure.** Skills are markdown prompts, not scripts. If you need a daemon or a cron job, it goes in scheduled-tasks (the macOS Routines UI), not here.
- **Skills that bundle credentials.** Anything requiring an API key teammates should rotate themselves — don't bake it in.

---

## Versioning

We follow semver via git tags:

- Patch (`v0.1.0` → `v0.1.1`) — bug fix or small wording tweak in a skill
- Minor (`v0.1.0` → `v0.2.0`) — new skill, new command, or breaking change to a skill's trigger
- Major (`v0.x.x` → `v1.0.0`) — once the shape is stable enough that it won't move

Each `plugin.json` has a `version` field that should match the tag at release time.

---

## Code of conduct

Be helpful, be specific, don't waste each other's context window. That's it.
