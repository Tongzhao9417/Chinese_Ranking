# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static webpage for querying 2026 Chinese Academy of Sciences (中科院) journal rankings. Pure frontend, no build tools or dependencies.

## Architecture

- `index.html` - Single-page app with embedded CSS/JS. Loads `data.json` at runtime via fetch.
- `data.json` - Flat JSON array exported from `2026_ranking.xlsx`. Each entry: `[id, name, issn, eissn, category, zone, top]`.
- `2026_ranking.xlsx` - Source data (1911 journals). Columns: 序号, 刊名, ISSN, EISSN, 类型, 新锐分区, TOP.

## Development

Serve locally (required for fetch to work):

```bash
python3 -m http.server 8000
```

## Data Pipeline

Regenerate `data.json` from Excel:

```bash
python3 -c "
import openpyxl, json
wb = openpyxl.load_workbook('2026_ranking.xlsx')
ws = wb['Sheet1']
data = []
for row in ws.iter_rows(min_row=2, values_only=True):
    data.append([row[0],row[1],row[2] or '',row[3] or '',row[4] or '',row[5] or '',row[6] or ''])
with open('data.json','w',encoding='utf-8') as f:
    json.dump(data, f, ensure_ascii=False)
"
```

## Data Quirks

- Zone values have inconsistent spacing in source ("1 区" vs "1区") — normalized client-side by stripping whitespace.
- TOP values vary ("TOP", "Top", "—", None) — normalized client-side to boolean.
- Table renders max 500 rows for performance; user narrows via search/filters.
