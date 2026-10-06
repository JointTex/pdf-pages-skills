# pdf-pages-skills

Two skills for the [JointTex](https://jointtex.com) Agent that put PDF pages together and take them apart, using LaTeX's own `pdfpages` package.

| Skill | What it does |
| --- | --- |
| `merge-pdf` | Combines several PDF files of a project into one, in the order you give: a cover letter in front of a manuscript, a supplement behind it. |
| `split-pdf` | Takes chosen pages of a PDF out as a new PDF, or cuts one PDF into several. Also removes, reorders and rotates pages. |

## How it works

The JointTex Agent cannot run a PDF tool and cannot write a PDF file. It can write LaTeX. So each skill has the Agent write a few-line wrapper file such as:

```latex
\documentclass{article}
\usepackage{pdfpages}
\begin{document}
\includepdf[pages=-,fitpaper=true]{cover-letter.pdf}
\includepdf[pages=-,fitpaper=true]{manuscript.pdf}
\end{document}
```

Compiled, the wrapper is the result. The Agent's compile builds the project's main document only, so the last step is yours:

1. In the project's compile settings, choose the wrapper as the main document.
2. Compile and download the PDF.
3. Set the main document back.

The Agent tells you this when it starts, reads the source PDFs to count their pages, and can check the result once you have switched the main document.

## Limits

- Every source PDF must be a file of the project. The PDF your project compiles is not one: download it and upload it first.
- Bookmarks, links, form fields and annotations of the sources are not carried over. Text stays selectable and searchable.
- Splitting does not redact: what was only covered or cropped on a page is still in the file.
- An encrypted or password-protected PDF cannot be used as a source.
- One compile gives one PDF, so cutting a file into several parts is one compile per part.
- The project has to compile with pdfLaTeX, XeLaTeX or LuaLaTeX.

## Install

In JointTex, open **Plugins**, find this set and star it. It is then offered in the Agent's Plugins dialog of every project you open, where each skill is installed into the project as `.jointtex/skills/<name>/SKILL.md`.

Outside JointTex, copy a skill's folder from `skills/` into wherever your agent reads skills from. The instructions name JointTex's tools (`list_files`, `read_pdf`, `write_file`, `copy_path`, `compile`, `ask_user_question`); the LaTeX in them works anywhere.

## Layout

```text
skills/
  merge-pdf/SKILL.md
  split-pdf/SKILL.md
```

## License

MIT. See [LICENSE](LICENSE).
