---
name: accessibility-qa
description: Use to run a practical baseline accessibility check on a web page and decide what to automate versus verify manually. Guides scoping, automated scans (axe-core), a keyboard-only pass, a screen-reader spot-check, contrast/visual checks, and evidence-based findings. A starter QA workflow, not a full WCAG audit or conformance certification. Triggers on "accessibility test", "a11y baseline", "keyboard test this page", "screen reader check", "what should I automate for accessibility".
---

# Accessibility QA (baseline)

A small, repeatable baseline for one page and one important user task. It is **not** a full WCAG audit and **cannot** establish WCAG conformance or legal compliance.

**Reference:** [WCAG overview (W3C WAI)](https://www.w3.org/WAI/standards-guidelines/wcag/). It recommends using the latest version, currently **WCAG 2.2** ([standard](https://www.w3.org/TR/WCAG22/), [Quick Reference](https://www.w3.org/WAI/WCAG22/quickref/)). Link to criteria rather than restating generic WCAG text.

## Workflow

1. **Scope.** Confirm the page, the one user task, the states to test (default, error, modal open, expanded, etc.), platform/browser, desktop vs mobile, and what is out of scope.
2. **Standards.** Use WCAG 2.2 AA unless told otherwise. Before citing a criterion number or threshold, look it up in the Quick Reference. If you can't reach W3C, say so and don't present criterion numbers or thresholds as verified.
3. **Automated checks.** Run only if the tools and environment exist; otherwise give the commands/steps and say they were not run.
4. **Keyboard-only pass** on the task (below).
5. **Screen-reader spot-check** on the same journey, if one is available for the tester's platform (e.g. VoiceOver on macOS).
6. **Contrast and visual cues** for the actual affected states.
7. **Record findings** with evidence and a status (see Evidence rules), and write the report in the format under Report format.
8. **Recommend a few high-impact next steps**, not an exhaustive list, unless a full audit was requested.

## Example target page

Sample public page for practising this workflow: <https://www.blindtextgenerator.com/lorem-ipsum> (a lorem ipsum generator). Example scope to confirm before testing: choose a text and length, then copy the generated text, in the default state on desktop and at a 320px width. A worked run is in `examples/2026-11-05-blindtextgenerator-baseline.md`. This is only an example target, and a past run is not evidence about the page today, so record real results per the Evidence rules.

## Automate vs. check manually

| Automate (repeatable, measurable) | Manual (needs a human or real AT) |
|---|---|
| axe-core (or equivalent) on each representative page state | Keyboard-only run of the task |
| Controls expose an accessible name, role, and state (e.g. Playwright role/name locators, accessibility snapshot) | Whether names are clear, accurate, and match the visible label |
| Measurable contrast pairs in the tested state | Screen-reader announcements, headings/landmarks, dialog behaviour |
| Focused regression asserts: modal opens/closes, focus lands on a given target | Focus return, skip links, zoom/reflow, colour-not-only cues |

**Limits of automation.** A clean axe run is not proof the page is accessible. Tools detect a missing accessible name; they can't judge whether it is good. Contrast tools only assess colours and states they can inspect. Don't demand `aria-label` everywhere: prefer native HTML and visible labels, and add ARIA only when semantics are missing.

**Limits of code review.** Source review and scans cannot prove what a screen reader announces. Manual assistive-technology testing is required for the behaviours it covers.

## Manual baseline

Use this table for scenarios (the same table is the report, see Report format). Keep them simple, specific, and high-impact.

| Test Scenarios | Test Steps | Test Result |
|---|---|---|

Starter rows (adapt to the page):

| Test Scenarios | Test Steps | Test Result |
|---|---|---|
| Keyboard path through the task | Tab / Shift+Tab through the task. Check logical order, visible focus at every stop, Enter/Space activate controls, arrow keys behave as expected for the widget. | Not run |
| No keyboard trap | Move through and out of every widget (menus, date pickers, embedded content) using only the keyboard. | Not run |
| Modal (if present) | Open with keyboard. Focus moves into the dialog. Escape/close button closes it. Focus returns to a sensible place (normally the trigger). | Not run |
| Skip link / focus targets (if present) | First Tab reveals the skip link. Activating it moves focus to the target. | Not run |
| Screen-reader pass | Walk the same journey. Check names, roles, states (selected/expanded), headings/landmarks, dialog announcement, error and status messages, and that meaningful content is actually read. | Not run |
| Colour is not the only cue | Check errors, selected items, and required fields still convey meaning without colour. | Not run |
| Focus and contrast in actual states | Measure text and UI-component contrast in default, hover, focus, error and disabled-vs-active states. | Not run |
| Zoom / responsive | Zoom to 200% and check a narrow viewport. Content and controls stay usable. Note desktop vs mobile differences. | Not run |

Leave **Test Result** as `Not run` until the step is actually executed. When the result is unknown, write the expected behaviour separately from observed evidence.

## Contrast: verified from W3C

- [1.4.3 Contrast (Minimum), AA](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html): text needs **4.5:1**, or **3:1** for large text (at least 18pt, or 14pt bold). Ratios are not rounded (4.499:1 fails). Exceptions: incidental text (e.g. inactive/disabled components, pure decoration), logotypes.
- [1.4.11 Non-text Contrast, AA](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html): UI component boundaries and states, focus indicators, and graphics needed to understand content need **3:1** against adjacent colours. Exceptions include inactive components and browser-default appearance.

For any other threshold, verify the applicable criterion, text size, component type, and exception in the Quick Reference first.

Criteria commonly relevant to the checks above (confirm in the Quick Reference before citing): 1.4.1, 1.4.10, 2.1.1, 2.1.2, 2.4.1, 2.4.3, 2.4.7, 2.4.11, 4.1.2, 4.1.3.

## Evidence rules

Classify every item as exactly one of:

- **Confirmed issue**: reproduced by a manual test, with evidence.
- **Passed check**: the test was actually run and met expectations.
- **Not verified / needs manual retest**: inferred from code or automation only, not run, or tool unavailable.

For each finding, record: affected page/state; steps to reproduce; expected behaviour; observed behaviour; user impact; evidence (screenshot, tool output, recording, SR transcript); and whether it was **verified manually** or **only inferred from code/automation**.

## Report format

The report is the scenario table, filled in, followed by numbered findings. Use exactly these three columns and no others, with one row per check run or planned, automated checks included. Don't replace the table with a different table or a bullet list.

| Test Scenarios | Test Steps | Test Result |
|---|---|---|

Start every **Test Result** with exactly one of these values, then a dash and a short piece of evidence:

- `Pass`: the check was run and met expectations. (Evidence status: passed check.)
- `Issue (F#)`: the problem was reproduced or measured in the page, not just reported by a tool. `F#` is the number of its finding below. (Evidence status: confirmed issue.) An axe violation counts only after you confirm it in the page, for example in the DOM or accessibility tree.
- `Not verified (F#)`: suspected from code, a scan or an incomplete tool result, and not reproduced. Add `F#` when it has a finding. (Evidence status: not verified / needs manual retest.)
- `Not run`: not executed, for example no screen reader was available.

Keep cells short. Put the detail in the findings, not in the table.

Order of the report: scope (page, task, state, platform, standard, what was not run), the table, findings `F1`, `F2`, ... with the fields from Evidence rules, a "Not verified" list, then next steps (a few, not exhaustive).

## Guardrails

- Prefer semantic HTML and native controls before ARIA.
- Never claim you accessed a page, ran a test, used a screen reader, or verified a fix unless you did.
- Don't mark a result as passed unless it was run.
- Don't claim full WCAG conformance from a limited page check.
- Don't call a mismatch between expected behaviour and implementation a confirmed defect until the intended behaviour is reconciled with current design decisions and a human has tested it.
- Distinguish desktop and mobile when behaviour differs by viewport.
- Don't modify application code, tests, CI, or product config as part of this workflow; report findings and recommendations only.

## Artifacts and cleanup

Browser runs leave files behind: page snapshots and console logs (Playwright's artifact folder, often under `/tmp`), screenshots saved to the current directory, and oversized tool output saved in the Claude Code session folder.

- Save screenshots to a scratch folder outside any git repo, never the current directory. Use a full path.
- For internal, customer or non-public pages, delete the artifacts when the run ends and tell the user what was removed and what could not be (the session transcript and session-folder copies stay unless the user deletes them).
- Before committing a report or evidence to a repo, check it for internal URLs, names and screenshots the user does not mean to publish.
