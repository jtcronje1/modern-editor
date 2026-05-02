# Modern Editor

A tiny, single-file HTML editor for tweaking AI-generated pages without touching the design.

Open any `.html` file (a slideshow, a mockup, a one-pager an LLM produced for you), edit the text and colours in place, then save back to the same file. No build step, no dependencies, no install — just one HTML file.

## Why this exists

LLMs are very good at generating polished HTML. They get you to about 90%. The last 10% is usually small text changes, a colour swap, a tweaked headline — things that are awkward to do by going back and re-prompting, and overkill to open in a code editor or PowerPoint.

Modern Editor is the missing 10%: drop the file in, click on what you want to change, save it back.

## Features

- **In-place text editing** — toggle Edit mode and click any text to change it. The original layout, fonts, gradients, scripts and CDN dependencies all stay untouched.
- **Selection-based colour editing** — highlight any text to get a floating popover with text colour and highlight colour swatches plus a custom picker.
- **Element-based colour editing** — Alt+click any element (a card, a heading, a button) to set its background, text, or border colour.
- **Reset** — one click removes all inline colour styles you applied to an element, returning it to the original design.
- **Round-trip save** — Save (`Cmd+S` / `Ctrl+S`) writes back to the same file on disk via the File System Access API. Save As… for a new file. Falls back to download in browsers without FSA.
- **Drag-and-drop** to open. Beforeunload warning if you have unsaved changes.
- **Zero dependencies, single file** — fonts come from Google Fonts at runtime, everything else is inline.

## Usage

1. Download or clone this repo.
2. Open `index.html` in Chrome or Edge (other Chromium-based browsers also work).
3. Drag an HTML file onto the window, or click **Open**.
4. Toggle **Edit mode** on.
5. Tweak:
   - Click on any text and type to change it.
   - Select a range of text to get a colour popover.
   - Hold **Alt** and click an element to colour its background, text, or border.
6. Hit `Cmd+S` (or `Ctrl+S` on Windows) to save back to the original file.

The saved file is clean — none of the editor's UI artifacts (contenteditable attributes, selection outlines) are written to disk.

## Browser support

| Feature | Chrome / Edge | Safari | Firefox |
|---|---|---|---|
| Open & edit | Yes | Yes | Yes |
| Save back to same file | Yes (File System Access API) | No (downloads instead) | No (downloads instead) |
| Drag-and-drop with file handle | Yes | No | No |

For true round-trip editing, use Chrome or Edge. In other browsers the app still works, but Save behaves like Save As… and downloads a new copy.

## Design choices

- **Inline styles only.** Colour edits are applied as inline `style="..."` so they don't fight with the agent's CSS classes (Tailwind, custom CSS, framework styles). Reset just deletes the inline style and the original design returns.
- **No layout editing.** Layout changes cascade into reflow problems and break the agent's design intent. This tool is deliberately scoped to text and colour.
- **Plain-text paste.** Pastes are stripped of formatting so a copy from Google Docs or Word doesn't drag in foreign HTML and break the design.
- **Iframe with `srcdoc`.** The loaded file renders inside a sandboxed iframe so its scripts and styles can't leak into the editor chrome, and the original HTML round-trips byte-for-byte.

## Known limitations

- Single file only — multi-file projects with sibling CSS / JS / images aren't bundled. Relative image paths inside the HTML may not resolve when loaded via `srcdoc`.
- Some interactive slideshow frameworks (reveal.js, etc.) bind keyboard handlers that may compete with editing keystrokes.
- Undo works within an editing session but not across reloads.
- No formatting toolbar — basic shortcuts like `Cmd+B` for bold work, but there's no UI for it. Intentional, to keep the surface small.

## Tech

- One self-contained `index.html`
- Vanilla JS, no frameworks, no build step
- Native browser APIs only: `contenteditable`, `Selection`, File System Access, `<input type="color">`
- Inter from Google Fonts

## Licence

MIT.
