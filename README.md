# Markdown Rendering Pipeline

> Renders one Markdown source into a Quarto PDF whitepaper and two Marp slide decks (HTML + PDF), using a Pandoc Lua filter to reconcile the two tools' conflicting `---` syntax.

`Course project` · Netdb Lab, NCKU · AIASE 2026 · Individual
**Stack:** Quarto CLI · Marp CLI · Pandoc Lua Filter · Node.js

## Overview

`content.md` is a single Markdown file (a sample guide on periodized training and nutrition for professional baseball players) that I render through two independent pipelines without duplicating the source. Quarto converts it to a PDF whitepaper via `xelatex`, with Traditional Chinese font support and Mermaid/LaTeX rendering. Marp converts the same file to an HTML slide deck and a 16:9 PDF deck, styled with a custom theme in `style.css`. Both pipelines read the same `content.md`; only the target format changes.

## Key Design Decisions

| Decision | Rationale |
| --- | --- |
| Quarto for the PDF path | Needed Mermaid diagrams, LaTeX math, and CJK font control (`CJKmainfont="Microsoft JhengHei"`), which Quarto's Pandoc pipeline supports directly via `-V` variables |
| Marp for the slide path | Needed a lightweight converter that outputs both an interactive HTML deck and a static PDF from the same source, without a browser dependency for the PDF |
| Lua filter (`remove-hr.lua`) to strip `---` | `---` is a horizontal rule in Quarto's PDF output but a slide-break marker in Marp. The filter deletes `HorizontalRule` nodes only in the Quarto pass, so one file drives both renderers without edits |
| Custom Marp theme (`style.css`) | The default Marp theme clipped long paragraphs off the slide edges; narrowing the side margins fit the whitepaper-length text into a 16:9 slide |

## Limitations

- No wrapper script or config file: the input filename (`content.md`) and every render flag are typed directly into each command, so rendering a different source means editing the commands by hand, not passing an argument.
- No automated test for the render pipeline; correctness is checked by opening the three output files manually.
- `style.css` mitigates slide overflow with a fixed 22px font size and 90%-scaled tables/code, not dynamic scaling — content longer than what fits at that fixed size will still clip off the slide edges.
- The Quarto command hardcodes `CJKmainfont="Microsoft JhengHei"`, a Windows-bundled font; rendering on macOS/Linux without that font installed will fail or silently substitute a different font.
- `output/` is committed to the repo instead of generated on demand, so it can go stale if `content.md` changes without a re-render.

## Running It

Tested on Windows 10/11 with Node.js v20.11.x (LTS) and Quarto CLI v1.4.550.

```powershell
# Install toolchain
quarto install tool tinytex
npm install -g @marp-team/marp-cli

# Render PDF whitepaper (Quarto)
quarto render content.md --to pdf --pdf-engine=xelatex `
  -V CJKmainfont="Microsoft JhengHei" -V colorlinks=true `
  -V fvextraopts="breaklines=true" -V fontsize=13pt `
  --output output.pdf --output-dir output --lua-filter=remove-hr.lua

# Render slide deck (Marp)
marp content.md --theme-set style.css --theme my-theme -o output/slides.html
marp content.md --theme-set style.css --theme my-theme --pdf -o output/slides.pdf
```

## Structure

    content.md      Markdown source shared by both render pipelines
    remove-hr.lua   Pandoc filter that strips `---` from the Quarto PDF pass
    style.css       Marp theme override (narrower margins, custom fonts)
    output/         Generated output.pdf, slides.html, slides.pdf

---
Original course-assignment README (in Chinese, with the sample document's own instructions): [docs/course-requirements.md](docs/course-requirements.md)
