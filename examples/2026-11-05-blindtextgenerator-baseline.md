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
| Automated scan (axe-core) | Run axe on the default state. | 2 violations: `label` (critical, 1 node) and `target-size` (serious, 20 nodes). 2 checks left incomplete: `color-contrast` (21 nodes) and `target-size` (7). 18 rules passed. |
| Accessible names for controls | Check links, form controls and images for names/alt. | **Issue:** the words-count text field (`#idInputWords`) has no label. No unnamed links, and no images without `alt`. |
| Page structure | Check landmarks, `h1` use, `lang`, title. | **Issue:** no landmarks (no `main`, `nav`, `header`, `footer`). 5 visible `h1` elements, plus a hidden one used for a status message. `lang="en"` is set. |
| Skip link | Tab from the top of the page. | **Issue (inferred from DOM and first stops):** no skip link. The first stops are the logo, then 6 language links, before the page content. |
| Keyboard reachability of the main task | Compare clickable elements with the tab order (inferred from the DOM). | **Issue:** the "Copy to clipboard" control (`img#idCopyToClipboard`, `alt="Select all"`) is clickable but has no role or `tabindex`, so it is not in the tab order (48 tabbable elements, none of them it). |
| Visible focus | Tab twice, then screenshot and compare the focused and unfocused styles of the focused link. | **Issue (confirmed):** the second tab stop, the "Russian" language link, is positioned off-screen (x -847, y -106) and shows no visible indicator, so focus is lost visually at that stop. |
| Status message | Inspect the "Text copied to clipboard" message. | **Issue (inferred):** the message is a hidden `h1` inside a plain `div`, with no `role` or `aria-live`, so it is unlikely to be announced. Not tested with a screen reader. |
| Reflow | Resize to 320px wide and check for horizontal page scroll. | **Issue (confirmed):** the page scrolls horizontally (scrollWidth 960 at a 320px viewport). There is no viewport meta tag. |
| Screen-reader pass | Not run. | Not run |
| Contrast and colour-only cues | axe could not decide 21 text elements. Colour-only cues not checked. | Not verified |

## Findings

### Confirmed issues

**1. Words-count field has no label.**
- Affected: `#idInputWords` (the "Number" field), default state.
- Steps: run axe, or inspect the field.
- Expected: a programmatic label such as "Number of words or characters".
- Observed: no explicit or implicit label. axe reports `label` as critical.
- User impact: a screen-reader user hears an unnamed text field.
- Verified: axe and DOM inspection. Not verified with a screen reader. Related criteria to confirm in the Quick Reference: 1.3.1, 4.1.2.

**2. The page does not reflow at 320px.**
- Steps: set the viewport to 320px wide and check the page width.
- Expected: no horizontal page scroll (confirm the criterion in the Quick Reference, for example 1.4.10).
- Observed: scrollWidth 960 at a 320px viewport, and no viewport meta tag.
- User impact: users who zoom to 400% or use a small screen must scroll sideways.
- Verified: measured in the browser.

**3. Focus goes to an off-screen link with no visible indicator.**
- Steps: press Tab twice from the top of the page.
- Observed: the second stop is the "Russian" language link, positioned at x -847, y -106 (off-screen). Its outline stays `none`, and the viewport screenshot shows no focus indicator.
- User impact: a keyboard user cannot see where focus is at this stop. The other language links were not measured, so whether they behave the same is untested.
- Verified: position and styles measured in the browser, plus a screenshot. The relationship between this link and the flag icons shown at the top right was not checked.

### Issues inferred from the DOM (retest manually)

4. **The copy control cannot be reached by keyboard.** It is an image with a click handler and no `tabindex` or role.
5. **No landmarks, no skip link and five `h1` headings.** This makes navigating by landmark or heading less useful.
6. **The "copied" message is probably not announced.** Retest with a screen reader.
7. **Small touch targets.** 20 links fail axe's `target-size` check, for example 103x12px and 84x12px, with little space between them. Confirm the criterion (2.5.8 in WCAG 2.2) in the Quick Reference.

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
