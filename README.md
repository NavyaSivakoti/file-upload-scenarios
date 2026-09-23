# Upload Lab

A single-page test site with 68 file upload scenarios for checking whether a browser extension, recorder or test tool picks the correct selector for a file input.

Most real upload controls hide the actual `<input type="file">` behind a styled button, a dropzone or a wrapper. Each scenario here hides it in a different way, including edge cases designed to make selector tools fail.

**Live page:** https://YOUR-USERNAME.github.io/upload-lab/upload-lab-final.html

## How to use it

1. Open the page and pick a scenario from the index on the left.
2. Capture the upload control with your tool: right click it to generate a locator, or record yourself uploading a file.
3. Replay the upload and check the scenario's status line.

A scenario only turns green when a file actually lands in the input, so a replay that clicks the right button but never uploads a file will not pass. During automated runs, assert on the status text (for example `Received 1 file: photo.jpg`) to prove the upload worked.

## Reading the results

| Status | Meaning |
|---|---|
| Grey | Waiting for a file |
| Green | File received by the input and accepted by the page |
| Amber | File received, but something is off, for example the type is outside the `accept` list or it went into a hidden duplicate |
| Red | The step may have passed, but the page rejected or ignored the file |

The counter at the top shows how many scenarios have received a file. **Show event log** lists every upload with a timestamp, and **Show HTML** under each scenario displays its original markup.

## URL options

| Option | Example | What it does |
|---|---|---|
| `?only=` | `?only=R-03` or `?only=CFI-01,CFI-02,CFI-03` | Renders only the listed scenarios. The rest are removed from the page, so locator uniqueness behaves like a real single form. |
| `#` | `#F-13` | Jumps straight to a scenario. |

Short names like "Selfie" or "browse" also appear in other scenarios, so use `?only=` when you need a clean result for one case.

## Scenarios

| Group | IDs | What it covers |
|---|---|---|
| Identity form | CFI-01 to CFI-03 | ID front, ID back and selfie dropzones with identical markup but different parent structure |
| Designed to break | F-01 to F-27 | Real-world patterns that commonly defeat selector tools |
| How the input is hidden | H-01 to H-08 | `display:none`, `hidden`, off-screen, zero size, transparent overlay and more |
| Where the input sits | R-01 to R-07 | Sibling, deeply nested, cousin, end of `<body>`, shared input, created on click, replaced after upload |
| Shadow DOM and iframes | E-01 to E-03 | Open and closed shadow roots, same-origin iframe |
| Accessible names | N-01 to N-07 | Icon-only buttons, glyphs in names, substring and duplicate names, changing labels, non-English text |
| Input attributes | A-01 to A-07 | `multiple`, `accept`, `capture`, `webkitdirectory`, `disabled`, `required` |
| Timing and visibility | D-01 to D-05 | Delayed render, modal, hidden tab, scroll container, wizard step |
| Negative checks | X-01 to X-04 | Controls that are not uploads and must not get a file input selector |

<details>
<summary>Designed-to-break scenarios (F-01 to F-27)</summary>

| ID | Scenario | What it tests |
|---|---|---|
| F-01 | Button inside the dropzone | The nearest clickable element has no input inside it |
| F-02 | Right click lands on an SVG path | The event target is an icon, not the button |
| F-03 | Transparent click catcher | An invisible overlay receives the right click |
| F-04 | Right click disabled | The page suppresses the browser context menu |
| F-05 | Name changes on hover | The captured name differs from the name at replay |
| F-06 | Live counter in the name | The name changes after each upload |
| F-07 | Random id and class per load | Generated attributes break after a reload |
| F-08 | Input exists only while hovered | The input is missing when replay does not hover |
| F-09 | Input created on first click | No input exists on a fresh page |
| F-10 | Dropzone replaced after upload | A second upload has no target |
| F-11 | Inline spans glue words | Raw text differs from the computed accessible name |
| F-12 | Invisible characters | Non-breaking, soft hyphen and zero-width characters in labels |
| F-13 | Long names sharing first words | Truncated names match more than one control |
| F-14 | Name is only an icon glyph | The accessible name is one unprintable character |
| F-15 | aria-hidden wrapper | Role-based locators ignore the control |
| F-16 | Two inputs in one dropzone | Strict mode violation, or the wrong input gets the file |
| F-17 | Hidden mobile duplicate | A first-match selector hits the hidden copy |
| F-18 | Rows shuffled per load | Position-based selectors hit the wrong row |
| F-19 | Repeating sections | Identical labels in sections added at runtime |
| F-20 | Sandboxed iframe | Opaque-origin frame, similar to third-party verification widgets |
| F-21 | Iframe inside a shadow root | Needs both a shadow and a frame hop |
| F-22 | Source chooser menu | Inputs exist only after opening a menu |
| F-23 | Disabled until a type is chosen | Upload blocked until another field is set |
| F-24 | Drag and drop only | The input gets the file but the page ignores it |
| F-25 | Page validates the file | Wrong type or small images are rejected after upload |
| F-26 | File System Access API | No file input exists at all |
| F-27 | Camera-only selfie | Webcam capture with no file option |

</details>

## Running it locally

The page is one self-contained HTML file with no build step. Open it directly in a browser, or serve it locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000/upload-lab-final.html
```

F-26 needs a secure context (`https://` or `localhost`) and does not work when the file is opened from disk. F-27 needs a camera, so it fails on headless or cloud runners by design.

## Notes

- The only external resource is Google Fonts. The page falls back to system fonts if they cannot load.
- Some scenarios describe expected limitations rather than bugs, for example closed shadow roots (E-02) and camera-only capture (F-27). Each card's side panel explains what counts as a pass.
- All markup is original and written for testing. Scenario IDs are stable, so they can be referenced in test plans and bug reports.
