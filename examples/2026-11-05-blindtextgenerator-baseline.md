# Accessibility baseline: Blind Text Generator (lorem ipsum page)

Worked example of the `accessibility-qa` skill on a public page. This is a baseline check, not a WCAG audit, and it makes no conformance claim.

## Scope

| Item | Value |
|---|---|
| Page | https://www.blindtextgenerator.com/lorem-ipsum |
| State tested | Default state on load (Lorem ipsum, 200 words, 1 paragraph) |
| Task | Choose a text and length, then copy the generated text |
| Platform | Chromium via Playwright, desktop 1280x720, plus a 320px width reflow check |
| Date | 2026-11-05 |
| Standard | WCAG 2.2 AA ([W3C overview](https://www.w3.org/WAI/standards-guidelines/wcag/)) |
| Out of scope / not run | Screen reader, generating different texts, advanced options, the other language pages, mobile devices |

Automated tool: axe-core 4.10.2, tags wcag2a, wcag2aa, wcag21a, wcag21aa, wcag22aa. Checks marked "inferred" were read from the DOM, not performed with the keyboard.

## Scenarios

| Test Scenarios | Test Steps | Test Result |
|---|---|---|
| Automated scan (axe-core) | Run axe on the default state. | Issue (F1) - 2 violations: `label` (confirmed, F1) and `target-size` (not confirmed, F7). 18 rules passed. 21 contrast and 7 target-size nodes left incomplete. |
| Accessible name for the words-count field | Inspect `#idInputWords` for a label. | Issue (F1) - no explicit or implicit label. |
| Language, image alt text, link names | Check `lang`, images without `alt`, links without a name in the DOM. | Pass - `lang="en"`, 0 images without `alt`, 0 unnamed links. |
| Landmarks and headings | Check landmarks and `h1` use in the DOM. | Not verified (F5) - no landmarks, 5 visible `h1`. Effect on navigation not tested with a screen reader. |
| Skip link | Tab from the top of the page. | Not verified (F5) - none found. First stops are the logo, then 6 language links. |
| Keyboard reachability of the copy control | Compare clickable elements with the tab order (read from the DOM). | Not verified (F4) - `img#idCopyToClipboard` has no role or `tabindex`, so it is not in the tab order. Not tried with the keyboard. |
| Visible focus | Tab twice, screenshot, compare focused and unfocused styles. | Issue (F3) - the "Russian" language link is off-screen (x -847, y -106) with no visible indicator. |
| "Text copied" message | Inspect the message in the DOM. | Not verified (F6) - hidden `h1` in a plain `div`, no `role` or `aria-live`. Not tested with a screen reader. |
| Reflow at 320px | Resize to 320px wide and check for horizontal page scroll. | Issue (F2) - scrollWidth 960 at a 320px viewport, no viewport meta tag. |
| Screen-reader pass | Walk the task with a screen reader. | Not run |
| Contrast and colour-only cues | Measure the 21 text elements axe could not decide. Check colour-only cues. | Not run |

## Findings

### Confirmed issues

**F1. Words-count field has no label.**
- Affected: `#idInputWords` (the "Number" field), default state.
- Steps: run axe, or inspect the field.
- Expected: a programmatic label such as "Number of words or characters".
- Observed: no explicit or implicit label. axe reports `label` as critical.
- User impact: a screen-reader user hears an unnamed text field.
- Verified: axe and DOM inspection. Not verified with a screen reader. Related criteria to confirm in the Quick Reference: 1.3.1, 4.1.2.

**F2. The page does not reflow at 320px.**
- Steps: set the viewport to 320px wide and check the page width.
- Expected: no horizontal page scroll (confirm the criterion in the Quick Reference, for example 1.4.10).
- Observed: scrollWidth 960 at a 320px viewport, and no viewport meta tag.
- User impact: users who zoom to 400% or use a small screen must scroll sideways.
- Verified: measured in the browser.

**F3. Focus goes to an off-screen link with no visible indicator.**
- Steps: press Tab twice from the top of the page.
- Observed: the second stop is the "Russian" language link, positioned at x -847, y -106 (off-screen). Its outline stays `none`, and the viewport screenshot shows no focus indicator.
- User impact: a keyboard user cannot see where focus is at this stop. The other language links were not measured, so whether they behave the same is untested.
- Verified: position and styles measured in the browser, plus a screenshot. The relationship between this link and the flag icons shown at the top right was not checked.

### Issues inferred from the DOM (retest manually)

- **F4. The copy control cannot be reached by keyboard.** It is an image with a click handler and no `tabindex` or role.
- **F5. No landmarks, no skip link and five `h1` headings.** This makes navigating by landmark or heading less useful.
- **F6. The "copied" message is probably not announced.** Retest with a screen reader.
- **F7. Small touch targets.** 20 links fail axe's `target-size` check, for example 103x12px and 84x12px, with little space between them. Confirm the criterion (2.5.8 in WCAG 2.2) in the Quick Reference.

## Not verified

- Contrast (21 text elements axe could not decide) and colour-only cues.
- Any screen-reader announcement.
- Generating other texts, advanced options, other language pages and real mobile devices.
- Real Tab order through all 48 stops. Only the first two were tabbed.

## Recommended next steps

1. Add a visible label to the number field, and make the copy control a real `button` with a name.
2. Fix the layout so it reflows at 320px, and add a viewport meta tag.
3. Run a VoiceOver pass for the "copied" message and the form, and measure the 21 contrast items.

## Evidence

- [Focused link, viewport screenshot](evidence/blindtext-focus-link.png)
