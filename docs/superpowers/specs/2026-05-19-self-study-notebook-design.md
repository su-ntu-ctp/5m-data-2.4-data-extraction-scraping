# Self-Study Notebook Design
**Date:** 2026-05-19  
**Status:** Approved

## Overview

Create a new beginner-friendly, self-study notebook (`notebooks/self_study_data_extraction.ipynb`) alongside the existing `extraction_scraping_lesson.ipynb`. The new notebook covers the same three topics — REST API, GraphQL, web scraping — but is designed for solo learners with basic Python knowledge and no data engineering background.

## Target Audience

- Basic Python (variables, loops, functions) — comfortable reading/writing simple scripts
- No prior API, HTTP, or web scraping experience
- No instructor present — must be fully self-contained

## Design Decisions

### Structure: Problem-First Story

Each chapter is framed around a real mission ("you've been asked to analyse pandas release history") so learners always understand *why* they're learning each concept. The three chapters map naturally:

- **Chapter 1 (REST API):** Fetch pandas release history + contributor commit counts → save to DuckDB
- **Chapter 2 (GraphQL):** Re-fetch the same releases more precisely, then query issues → contrast with REST
- **Chapter 3 (Web Scraping):** Scrape countries data (simple), then hockey stats with pagination (complex)

### Cell Rhythm (every chapter)

Every section follows the same four-cell pattern:

1. **Story hook** (markdown) — one short paragraph explaining the real-world motivation
2. **Concept cell** (markdown + inline SVG diagram) — explains the underlying concept with a visual before any code
3. **Worked example** (code cells with inline `👈` comments + markdown explanation) — every line explained
4. **Exercise** (markdown with 3 progressive hints + empty code cell) — hints go from general to specific so learners can try before peeking

### Diagrams (inline SVG in markdown cells)

- **Chapter 1:** HTTP request/response cycle diagram; JSON structure tree; status codes table; auth header anatomy
- **Chapter 2:** REST vs GraphQL side-by-side comparison (over-fetching illustration); GraphQL query anatomy labelled
- **Chapter 3:** HTML document tree; CSS selector targeting illustration

### Exercises

Each exercise has exactly 3 hints, numbered and progressively specific:
- Hint 1: what endpoint/tag/query to use
- Hint 2: what the data structure looks like
- Hint 3: specific field names or method to call

### Error Handling / Troubleshooting

Inline troubleshooting boxes appear immediately after any cell that can fail:
- After token loading: what to check if `access_token` is None
- After `requests.get()`: status code reference for 401 (bad token), 403 (no permission), 404 (bad URL), 202 (data not ready — GitHub stats endpoint)
- After BeautifulSoup parsing: what to do if `find_all()` returns empty

### Setup Section

Step-by-step GitHub token walkthrough with explicit instructions at each step. `.env` creation code cell runs once. Clear warning that `.env` must not be committed.

### Wrap-Up Section

Summary comparison table: REST vs GraphQL vs Web Scraping — when to use each, pros/cons, reliability. One-paragraph "what's next" pointing to rate limiting, error retries, and async requests as natural next steps.

## Notebook File

**Path:** `notebooks/self_study_data_extraction.ipynb`  
**Kernel:** Python 3 (`bde` conda environment — same as existing notebook)  
**Dependencies:** `requests`, `python-dotenv`, `pandas`, `sqlalchemy`, `duckdb`, `beautifulsoup4`

## Out of Scope

- No changes to `extraction_scraping_lesson.ipynb`
- No async/aiohttp, no Selenium, no JavaScript rendering
- No rate-limit retry logic (mentioned in wrap-up as a next step, not implemented)
- No solution cells — exercises are left open for self-study

## Section Outline

```
0. What You'll Build
   - Preview of final outputs: 4 DataFrames (releases, contributors, countries, hockey stats) and 2 DuckDB tables
   - Mission statement: "By the end, you'll be able to pull data from any REST API,
     write precise GraphQL queries, and scrape structured data from any website."

1. Setup
   - Libraries overview (what each one does, one line each)
   - GitHub token: step-by-step text instructions (Settings → Developer settings → Personal access tokens → Generate)
   - .env file creation
   - Load token + verify

2. Chapter 1 — REST API
   2a. Story hook
   2b. Concept: HTTP, JSON, status codes, auth (with diagram)
   2c. Worked: pandas releases — fetch, paginate, build DataFrame, save to DuckDB
   2d. Exercise: contributors + commit counts — hints, empty cell

3. Chapter 2 — GraphQL
   3a. Story hook ("REST gave us everything — GraphQL lets us ask for exactly what we need")
   3b. Concept: REST vs GraphQL over-fetching, query anatomy (with diagram)
   3c. Worked: replicate releases query in GraphQL, navigate edges/nodes
   3d. Exercise: query first 50 issues (title, createdAt, author.login) — hints, empty cell

4. Chapter 3 — Web Scraping
   4a. Story hook ("no API exists — we'll read the HTML directly")
   4b. Concept: HTML tree, CSS selectors, ethics + legality note (with diagram)
   4c. Worked: scrape countries page — find_all, tag+class, extract to DataFrame
   4d. Exercise: hockey stats with pagination — rate limiting, multi-page loop — hints, empty cell

5. Wrap-Up
   - Summary table: REST vs GraphQL vs Scraping
   - What's next: rate limiting, retries, async
```
