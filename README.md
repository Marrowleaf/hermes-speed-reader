# Speed Reader

> Extract key points from long articles, PDFs, and URLs with configurable summary lengths and automatic Obsidian integration.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/Marrowleaf/hermes-speed-reader)

## Features

- Three input types: web URLs, PDFs, and pasted text
- Three summary formats: paragraph, 5-bullets, full digest
- Automatic content detection and extraction from web pages
- PDF text extraction via ocr-and-documents skill
- Reading time estimation (238 words/min) with time-saved calculation
- Batch processing from a URL file
- Auto-save summaries to Obsidian vault (PARA method)
- Source links, author metadata, and publish dates
- Configurable max content length

## Installation

```bash
hermes skills install productivity/speed-reader
```

Or manually clone into `~/.hermes/skills/productivity/speed-reader/`.

## Usage

```
/speed-read https://example.com/long-article paragraph
/speed-read ~/Documents/report.pdf bullets
/speed-read "Long pasted text content here..." digest
/speed-read-batch ~/urls-to-read.txt bullets
/speed-read-config show
/speed-read-history
```

## Configuration

- `default_format`: paragraph, bullets, or digest (default: paragraph)
- `vault_path`: `~/obsidian-vault`
- `save_to_vault`: true/false (default: true)
- `para_folder`: PARA folder for summaries (default: 3-Resources)
- `include_source_link`: true/false (default: true)
- `include_reading_time`: true/false (default: true)
- `max_content_length`: Max characters to process (default: 50000)

## Requirements

- Hermes Agent v0.12+
- Browser tools (for web URL extraction)
- ocr-and-documents skill (for PDF processing)
- Obsidian vault (for auto-saving summaries)

## License

MIT

## Related Hermes Skills
- [hermes-airtable](https://github.com/Marrowleaf/hermes-airtable) — Airtable integration and automation
- [hermes-budget-tracker](https://github.com/Marrowleaf/hermes-budget-tracker) — Personal budget tracking and analysis
- [hermes-consensus-engine](https://github.com/Marrowleaf/hermes-consensus-engine) — Multi-model consensus and decision engine
- [hermes-nano-pdf](https://github.com/Marrowleaf/hermes-nano-pdf) — Lightweight PDF processing and manipulation
- [hermes-notion](https://github.com/Marrowleaf/hermes-notion) — Notion integration and management
