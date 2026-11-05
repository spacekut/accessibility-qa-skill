# accessibility-qa-skill

A Claude Code skill for a practical, beginner-friendly baseline accessibility check on a web page: what to automate, what to verify manually, and how to record evidence. A starter QA workflow, not a full WCAG audit.

Install by copying `.claude/skills/accessibility-qa/` into a project's `.claude/skills/` (or `~/.claude/skills/` for all projects).

## Using it

Start a new Claude Code session after installing, then ask in plain language, for example:

- "Run an accessibility baseline on https://example.com/checkout"
- "Do a keyboard test on this page"
- "What should I automate for accessibility on this page?"

The live browser checks need a Playwright MCP server available to Claude Code. Without one, the skill gives you the commands and steps and marks them as not run.

A worked report is in [`examples/2026-11-05-blindtextgenerator-baseline.md`](examples/2026-11-05-blindtextgenerator-baseline.md). For a plain-language summary of the skill, its example run and how it was tested, see [`NOTES.txt`](NOTES.txt).

## Using this without Claude

`SKILL.md` is an instruction file for Claude Code, so it does nothing on its own. The method is still a plain checklist you can follow by hand. Open [`.claude/skills/accessibility-qa/SKILL.md`](.claude/skills/accessibility-qa/SKILL.md) and use it like this:

| Part | By hand |
|---|---|
| Automated scan | Use the axe DevTools browser extension, or the axe command-line tool (`@axe-core/cli`). Neither has been tried as part of this repo. |
| Keyboard pass | Tab, Shift+Tab, Enter, Space, Escape and arrow keys through one important task. The "Manual baseline" table in `SKILL.md` is the checklist. |
| Screen-reader pass | A person with a screen reader, for example VoiceOver on a Mac (Cmd+F5). Nothing replaces this. |
| Contrast | The browser DevTools colour picker, or any contrast checker. Check the thresholds against the W3C pages linked in `SKILL.md`. |
| Report | Reuse the table and the confirmed / passed / not verified labels from the example report. |

You lose what Claude adds: scoping the task, driving the browser, running the scans and writing up the findings. The guardrails still apply to a human tester. Don't mark a check as passed unless you ran it, and don't treat a clean axe result as proof that a page is accessible.
