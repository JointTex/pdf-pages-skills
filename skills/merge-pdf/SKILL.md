---
name: merge-pdf
description: Combine several PDF files of the project into one PDF, in a given order, with the pdfpages package. Use when asked to merge, join, concatenate or append PDFs, or to put a cover letter or supplement in the same file as a manuscript.
---

# Merge PDF files into one

You cannot run a PDF tool here, and you cannot write a PDF yourself. What you can do is write a small LaTeX file that pulls the pages of the source PDFs in with `pdfpages`; compiled, that file is the merged PDF. Your `compile` builds the project's main document only, and you cannot change which file that is, so the person does the last step. Say so at the start, so the plan is clear to them.

## 1. Settle what goes in, and in what order

- Find the PDFs with `list_files`. Every source must be a file of the project: one on the person's computer has to be uploaded first.
- A PDF the project compiles (the manuscript itself) is not a project file. Ask the person to download it and upload it to the project, then use that copy.
- Open each source with `read_pdf` and its `path` to confirm it opens and to learn its page count. Add the counts up: you will check the result against the total.
- If the order is not stated, or two files could be meant, ask with `ask_user_question`. Do not guess the order.

## 2. Write the wrapper

Write a new file at the root of the project, named for the result (`merged.tex`; `submission-with-supplement.tex`). Never overwrite a file that exists, and never edit the project's main document for this.

```latex
% Merges PDFs with pdfpages. Compile this file as the main document; its PDF is the merged file.
\documentclass{article}
\usepackage{pdfpages}
\begin{document}
\includepdf[pages=-,fitpaper=true]{cover-letter.pdf}
\includepdf[pages=-,fitpaper=true]{manuscript.pdf}
\includepdf[pages=-,fitpaper=true]{supplement.pdf}
\end{document}
```

- One `\includepdf` per source, in the order of the result. `pages=-` is every page.
- `fitpaper=true` gives each page the size and orientation it has in its source, so nothing is scaled or cropped. Keep it unless the person asks for one page size throughout; then drop it, set the size on the class (`\documentclass[a4paper]{article}`), and tell them pages of another size are scaled to fit.
- Paths are relative to the wrapper. A source in a folder is `{figures/appendix.pdf}`.
- A file name with spaces or with dots before `.pdf` can fail to be found. Do not rename the person's file: make a copy with a plain name using `copy_path` and include the copy, and say that you did.
- Only part of a source: `pages={1-3,7}`. A blank page between two parts (so the next starts on a right-hand page when printed): add `{}` to the list, as in `pages={1-3,{}}`.

## 3. Hand over the compile

Tell the person, in these steps:

1. In the project's compile settings, choose the wrapper as the main document.
2. Compile, and download the PDF. That is the merged file.
3. Set the main document back to what it was.

`pdfpages` needs pdfLaTeX, XeLaTeX or LuaLaTeX. If the project compiles with LaTeX to DVI, say that the engine has to be one of those three for this compile.

If they tell you the wrapper is now the main document, run `compile` yourself and then `read_pdf` with no arguments: the page count must equal the total from step 1, and the first page of each part should be the one you expect. Report a mismatch; do not explain it away.

## What to tell the person about the result

- Bookmarks, links inside the sources, form fields and annotations are not carried over: only what is drawn on each page comes through. Text stays selectable and searchable.
- No page numbers are added and none are changed. Numbering the merged file through is a different task: ask before doing it.
- An encrypted or password-protected source cannot be included. It has to be unlocked first, outside the project.
- The wrapper stays in the project so the merge can be repeated after a source changes. Offer to delete it, and any copy you made, when they are done.
