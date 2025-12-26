# Smartphone Advisor Data — CLI

Minimal Node.js CLI to manage Euro.com.pl smartphone listings and a local SQLite DB.

## Overview
Provides three commands:
- `euro-smartphone-data-split` — split `today_data.xlsx` into 260-line chunks and save each chunk as `data.xlsx` inside date-named folders (dd_mm_yyyy) in `data/euro/smartphone/listings`.
- `euro-smartphone-init-db` — create `data/euro/smartphone/listings/data.sqlite` with required schema (fails if DB already exists).
- `euro-smartphone-activity-management <folder>` — sync activity using the `data.xlsx` in the given folder (e.g. `26_12_2025`), updating `is_active`, `last_active`, and filtering records based on `last_processed` (6 months rule).

## Repo layout (relevant)
- cli/ — CLI project (entry: `cli/index.js`)
- data/euro/smartphone/listings/ — input/output Excel files and DB
- data/euro/smartphone/reviews/.gitkeep

## Prerequisites
- Node.js (>=16)
- npm
- sqlite3 CLI (optional for direct DB inspection)

## Install
```bash
# run from project root
cd cli
npm install
```

## Usage
Run from project root (or use npm link / add bin if desired):

Split Excel into dated folders:
```bash
node cli/index.js euro-smartphone-data-split
```

Create DB (runs once; errors if DB exists):
```bash
node cli/index.js euro-smartphone-init-db
```

Manage activity for folder (example):
```bash
node cli/index.js euro-smartphone-activity-management 26_12_2025
```

## Database
Path: `data/euro/smartphone/listings/data.sqlite`  
Schema columns: `product_name TEXT`, `product_code TEXT`, `url TEXT`, `price TEXT`, `is_active BOOLEAN`, `last_active DATE`, `last_processed DATE`, `analytics JSON`.

Inspect DB via sqlite3 CLI:
```bash
sqlite3 data/euro/smartphone/listings/data.sqlite
# inside sqlite3:
.tables
.schema your_table_name
.headers on
.mode column
SELECT * FROM your_table_name LIMIT 10;
.quit
```

Or install DB Browser for SQLite (macOS):
```bash
brew install --cask db-browser-for-sqlite
open data/euro/smartphone/listings/data.sqlite
```

## Git / .gitignore
If xlsx or sqlite files were already committed, stop tracking them:
```bash
git rm -r --cached data/euro/smartphone/listings/**/*.xlsx data/euro/smartphone/listings/data.sqlite
git commit -m "Stop tracking xlsx and sqlite files"
```

## Notes
- Backup DB before running destructive operations.
- Date format for folders: `dd_mm_yyyy`.
- Splitting uses 260 lines per chunk.
- Activity command replaces the folder's `data.xlsx` atomically (writes a new file, then replaces the original).

If you want, I can create or update the README with additional examples or add README badges. 