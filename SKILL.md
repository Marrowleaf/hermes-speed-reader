---
name: speed-reader
description: Extract key points from long articles, PDFs, and URLs with configurable summary lengths and automatic Obsidian integration
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  tags:
    - productivity
    - reading
    - summarization
    - obsidian
    - pdf
    - web-scraping
  related_skills:
    - ocr-and-documents
    - browser
    - obsidian-vault
---

# Speed Reader

Quickly extract key points from long-form content — articles, PDFs, or URLs — and save structured summaries to your Obsidian vault with source links and reading time estimates.

## Overview

Speed Reader helps you process information faster by distilling long content into digestible summaries. It supports three input types (web URLs, PDFs, and pasted text) and three summary formats (1-paragraph, 5-bullets, full digest). Summaries are automatically saved to your Obsidian vault using the PARA method.

## Commands

### `/speed-read <input> [format]`
Main command. Summarize content from a URL, PDF path, or pasted text.

**Arguments:**
- `<input>` — URL, PDF file path, or quoted text
- `[format]` — Summary format: `paragraph` (default), `bullets`, or `digest`

**Examples:**
```
/speed-read https://example.com/long-article paragraph
/speed-read ~/Documents/report.pdf bullets
/speed-read "Long pasted text content here..." digest
```

### `/speed-read-batch <file>`
Process multiple inputs from a text file (one per line).

**Example:**
```
/speed-read-batch ~/urls-to-read.txt bullets
```

### `/speed-read-config`
View or update configuration options.

**Subcommands:**
- `show` — Display current configuration
- `set <key> <value>` — Update a setting
- `reset` — Restore defaults

### `/speed-read-history`
Show recent summaries saved to the vault.

## Configuration

| Key | Default | Description |
|-----|---------|-------------|
| `default_format` | `paragraph` | Default summary format |
| `vault_path` | `~/obsidian-vault` | Path to Obsidian vault |
| `save_to_vault` | `true` | Auto-save summaries to vault |
| `para_folder` | `3-Resources` | PARA folder for summaries |
| `include_source_link` | `true` | Include source URL/path in note |
| `include_reading_time` | `true` | Estimate and show reading time saved |
| `max_content_length` | `50000` | Max characters to process |

## Workflow

### 1. Input Detection
The skill automatically detects input type:
- **URL** — Starts with `http://` or `https://`, uses browser tools to scrape
- **PDF** — Ends with `.pdf`, uses `ocr-and-documents` skill for extraction
- **Text** — Quoted or pasted text, processed directly

### 2. Content Extraction

#### Web Articles (URLs)
```
1. Use browser tools to navigate to the URL
2. Extract the main content (strip navigation, ads, footers)
3. Detect article title, author, and publish date
4. Fall back to raw HTML parsing if browser tools unavailable
```

#### PDFs
```
1. Delegate to ocr-and-documents skill for text extraction
2. If PDF is scanned, OCR is applied automatically
3. Extract metadata (title, author, page count)
4. Return extracted text for summarization
```

#### Pasted Text
```
1. Accept text directly from the command
2. Clean formatting artifacts
3. Proceed to summarization
```

### 3. Summarization

Based on the selected format:

**Paragraph** — A single concise paragraph capturing the thesis and key supporting points.

**Bullets** (5-bullets) — Five bullet points covering:
1. Main thesis/argument
2. Key evidence or data point
3. Notable insight or finding
4. Counter-argument or limitation
5. Takeaway / call to action

**Digest** — Full structured summary:
```markdown
# [Title]

**Source:** [URL/path]
**Author:** [Author name]
**Reading Time:** [X] min (Original: [Y] min — saved [Z]%)

## Key Points
- Point 1
- Point 2
- Point 3

## Notable Quotes
> "Exact quote from the text…"

## Analysis
Brief analytical commentary.

## Action Items
- [ ] Follow up on…
```

### 4. Saving to Obsidian

Summaries are saved to the vault under `3-Resources/Summaries/`:

```markdown
---
title: "Summary: [Article Title]"
date: 2025-01-15
source: "https://example.com/article"
author: "[Author]"
original_reading_time: 25
summary_reading_time: 2
time_saved_pct: 92
tags: [summary, topic1, topic2]
---

[Summary content here]
```

## Reading Time Estimation

Reading time is calculated at **238 words per minute** (average adult reading speed).

**Time saved formula:**
```
original_words = word_count(full_content)
summary_words = word_count(summary)
original_time_min = ceil(original_words / 238)
summary_time_min = ceil(summary_words / 238)
time_saved_pct = round(100 * (1 - summary_words / original_words))
```

## Examples

### Summarize a web article
```
User: /speed-read https://arxiv.org/abs/2401.00001 bullets

→ 📄 Fetching article...
→ 📝 Extracting content (3,240 words)
→ ✅ Summary ready (5 bullets)

**Reading time saved: 12 min → 1 min (92% reduction)**

• The paper introduces a novel architecture for…
• Key result: 15% improvement over baseline on…
• The approach uses attention mechanisms combined with…
• Limitation: only tested on English-language datasets
• Takeaway: promising direction for future multimodal research

💾 Saved to: 3-Resources/Summaries/Summary - Novel Architecture.md
```

### Summarize a PDF
```
User: /speed-read ~/Documents/quarterly-report.pdf digest

→ 📄 Processing PDF (24 pages)...
→ 📝 Extracting text via ocr-and-documents...
→ ✅ Full digest ready

💾 Saved to: 3-Resources/Summaries/Summary - Q3 Report.md
```

### Batch processing
```
User: /speed-read-batch ~/reading-list.txt bullets

→ Processing 12 items...
→ [1/12] ✅ https://example.com/article-1
→ [2/12] ✅ https://example.com/article-2
→ [3/12] ⚠️ https://example.com/article-3 (paywall — partial content)
→ ...
→ ✅ 11/12 summaries saved to vault
```

## Pitfalls

1. **Paywalled content** — Browser tools may not bypass paywalls. You'll get partial content with a warning. Consider asking the user for a text paste instead.

2. **JavaScript-heavy pages** — Single-page apps (SPAs) may not render full content with basic scraping. The browser tools should wait for dynamic content to load, but sometimes content is lazy-loaded.

3. **Scanned PDFs** — Without OCR, scanned PDFs return no extractable text. Always rely on `ocr-and-documents` for PDFs, which handles this automatically.

4. **Very long documents** — For documents exceeding `max_content_length`, the skill truncates but warns the user. Consider using `digest` format for long content, which provides the most comprehensive extraction.

5. **Rate limiting** — Batch processing URLs too quickly can trigger rate limits. The skill adds a 2-second delay between requests by default.

6. **Encoding issues** — Non-UTF-8 content may produce garbled text. The extraction step attempts encoding detection, but unusual encodings may need manual intervention.

7. **Missing metadata** — Not all articles have clear author or date metadata. The skill uses fallbacks (OG tags, structured data) but may leave fields blank.

8. **Obsidian folder** — If the `3-Resources/Summaries/` folder doesn't exist in the vault, it must be created before saving. The skill handles this, but check vault path configuration.

9. **Duplicate summaries** — Re-summarizing the same URL overwrites the previous summary. The skill uses the source URL as a key for deduplication.

10. **Reading time inaccuracy** — Content with heavy images, code blocks, or data tables may have skewed reading estimates. The formula counts words only.

## Verification Steps

1. **Check skill is loaded:**
   ```
   /speed-read-config show
   ```
   Should display current configuration with vault path and default format.

2. **Test URL summarization:**
   ```
   /speed-read https://example.com paragraph
   ```
   Should return a concise paragraph summary and confirm save location.

3. **Test PDF summarization:**
   ```
   /speed-read ~/test-document.pdf bullets
   ```
   Verify that `ocr-and-documents` is invoked for PDF extraction.

4. **Test text summarization:**
   ```
   /speed-read "The quick brown fox jumps over the lazy dog. This classic pangram contains every letter of the alphabet." paragraph
   ```
   Should return a summary and save to vault.

5. **Verify Obsidian save:**
   Check `~/obsidian-vault/3-Resources/Summaries/` for the generated markdown files.

6. **Verify reading time estimates:**
   Confirm that `original_reading_time` > `summary_reading_time` and `time_saved_pct` is displayed.

7. **Test batch processing:**
   Create a text file with 2-3 URLs and run `/speed-read-batch` to verify sequential processing.

8. **Check error handling:**
   Test with an invalid URL, a non-existent PDF, and empty text to confirm graceful error messages.