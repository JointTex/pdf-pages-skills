---
name: split-pdf
description: Take pages out of a PDF file of the project as a new PDF, or cut one PDF into several, with the pdfpages package. Use when asked to split a PDF, extract or keep certain pages, remove pages, or reorder or rotate pages.
---

# Take pages out of a PDF

You cannot run a PDF tool here, and you cannot write a PDF yourself. What you can do is write a small LaTeX file that pulls chosen pages of the source in with `pdfpages`; compiled, that file is the new PDF. Your `compile` builds the project's main document only, and you cannot change which file that is, so the person does the last step. One compile gives one PDF: cutting a file into three parts is three wrappers and three compiles by the person. Say so at the start, so they can decide whether it is worth it.

## 1. Settle which pages

- The source must be a file of the project. A PDF the project compiles is not: ask the person to download it and upload it to the project, then use that copy.
- Open it with `read_pdf` and its `path` to learn the page count, and, when the person named the part by content ("the appendix", "only the abstract page"), to find where that part starts and ends. Say which pages you found and why before going on.
- Page numbers here are positions in the file, counted from 1. They are often not the numbers printed on the pages (a front matter in roman numerals, an article that starts at page 211). When the person gives numbers, ask which they mean with `ask_user_question` unless the two agree.
- Check every page asked for exists. A range past the last page is an error in the compile, not an empty page.

## 2. Write the wrapper

Write a new file at the root of the project, named for what it holds (`appendix-only.tex`; `chapter-2.tex`). Never overwrite a file that exists, and never edit the project's main document for this.

```latex
% Extracts pages with pdfpages. Compile this file as the main document; its PDF is the extract.
\documentclass{article}
\usepackage{pdfpages}
\begin{document}
\includepdf[pages={12-18},fitpaper=true]{thesis.pdf}
\end{document}
```

The `pages` list does all of it:

| Wanted | `pages=` |
| --- | --- |
| pages 12 to 18 | `{12-18}` |
| page 5 to the end | `{5-}` |
| pages 1, 4 and 9 to 11 | `{1,4,9-11}` |
| everything except page 3 | `{1-2,4-}` |
| the last page | `{last}` |
| pages 3 and 4 swapped, in a five-page file | `{1,2,4,3,5}` |
| the whole file in reverse | `{last-1}` |

- `fitpaper=true` gives each page the size and orientation it has in the source. Keep it.
- To turn pages, add `angle=90` (counter-clockwise; `angle=-90` clockwise) and keep `fitpaper=true`. Pages that need different turns go in separate `\includepdf` lines.
- Paths are relative to the wrapper. A file name with spaces or with dots before `.pdf` can fail to be found: make a copy with a plain name using `copy_path` and include the copy, and say that you did.
- Several parts: one wrapper per part, each named for its part. Check the parts neither overlap nor leave a page out, unless that is what was asked.

## 3. Hand over the compile

Tell the person, in these steps, once per wrapper:

1. In the project's compile settings, choose the wrapper as the main document.
2. Compile, and download the PDF. That is the extract.
3. After the last one, set the main document back to what it was.

`pdfpages` needs pdfLaTeX, XeLaTeX or LuaLaTeX. If the project compiles with LaTeX to DVI, say that the engine has to be one of those three for this compile.

If they tell you a wrapper is now the main document, run `compile` yourself and then `read_pdf` with no arguments: the page count must be the number of pages asked for, and the first and last page should be the ones you expect. Report a mismatch; do not explain it away.

## What to tell the person about the result

- Bookmarks, links, form fields and annotations are not carried over: only what is drawn on each page comes through. Text stays selectable and searchable.
- Nothing is removed from the pages themselves: text that was only covered or cropped in the source is still in the file. This does not redact anything.
- The source is not changed.
- An encrypted or password-protected source cannot be included. It has to be unlocked first, outside the project.
- Offer to delete the wrappers, and any copy you made, when they are done.
