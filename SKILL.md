---
name: pdf-splitter
description: >
  This skill should be used whenever the user wants to split, divide, or extract pages from a PDF file.
  Triggers include: splitting a PDF into multiple files, extracting specific page ranges, dividing a PDF
  by chapters or bookmarks, separating each page into individual files, cutting a PDF, 拆分PDF, 分割PDF,
  按页拆分, 提取页面, 按章节分割, 按书签分割, 把PDF分成多个文件, PDF页面提取, 将PDF每页单独保存.
---

# PDF Splitter Skill

Split PDF files using multiple strategies via the bundled `scripts/split_pdf.py` script.

## Supported Splitting Modes

| Mode | Description | Key Argument |
|------|-------------|--------------|
| `by_pages` | Split every N pages into a separate file | `--chunk N` |
| `by_range` | Extract specific page ranges | `--ranges "1-5,10-15"` |
| `single_pages` | Save each page as its own PDF | — |
| `by_bookmarks` | Split at top-level bookmark/chapter boundaries | — |

## Workflow

### Step 1 — Clarify intent

Before running, determine from the user's request:
1. **Input file**: path to the source PDF.
2. **Mode**: which splitting strategy to apply (see table above).
3. **Output directory**: where to save the results (default: same folder as input).
4. **Mode-specific options** (chunk size or page ranges if applicable).

If any critical detail is missing, ask once before proceeding.

### Step 2 — Check Python & dependencies

The script self-installs `pypdf` if it is absent. Python 3.8+ is required.

Verify Python is available:
```bash
python --version
```

### Step 3 — Run the script

Script location: `scripts/split_pdf.py` (relative to this skill directory).

**Split every 10 pages:**
```bash
python scripts/split_pdf.py "path/to/input.pdf" --mode by_pages --chunk 10
```

**Extract page ranges 1–5 and 10–15:**
```bash
python scripts/split_pdf.py "path/to/input.pdf" --mode by_range --ranges "1-5,10-15"
```

**Save each page individually:**
```bash
python scripts/split_pdf.py "path/to/input.pdf" --mode single_pages
```

**Split by bookmarks/chapters:**
```bash
python scripts/split_pdf.py "path/to/input.pdf" --mode by_bookmarks
```

**Custom output directory:**
```bash
python scripts/split_pdf.py "path/to/input.pdf" --mode by_pages --chunk 5 --output "path/to/output_dir"
```

### Step 4 — Report results

After the script completes, report to the user:
- Total number of files created.
- Output directory path.
- List of generated filenames (if ≤ 10 files; otherwise summarise).

## Error Handling

| Situation | Action |
|-----------|--------|
| Input file not found | Ask the user to verify the path |
| No bookmarks in `by_bookmarks` mode | Script auto-falls back to `single_pages`; inform the user |
| `pypdf` install fails | Ask user to run `pip install pypdf` manually |
| Password-protected PDF | Inform user that encrypted PDFs are not supported by this script |

## Notes

- Page numbers are **1-indexed** in `--ranges` arguments.
- Output filenames are auto-generated from the input filename + page range or bookmark title.
- The script never modifies the original PDF.
