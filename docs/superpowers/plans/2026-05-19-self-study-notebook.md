# Self-Study Data Extraction Notebook — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create `notebooks/self_study_data_extraction.ipynb` — a self-contained, beginner-friendly notebook covering REST API, GraphQL, and web scraping, structured as a problem-first story with concept diagrams, worked examples, and guided exercises.

**Architecture:** Single Jupyter notebook built cell-by-cell using the `NotebookEdit` tool. Each chapter follows Story Hook → Concept (with inline SVG diagram) → Worked Example (every line explained) → Exercise (3 progressive hints + empty code cell). The existing `extraction_scraping_lesson.ipynb` is not modified.

**Tech Stack:** Python 3 (`bde` conda env), `requests`, `python-dotenv`, `pandas`, `sqlalchemy`, `duckdb-engine`, `beautifulsoup4`, Jupyter `.ipynb` format

---

## File Structure

| Action | Path | Responsibility |
|--------|------|----------------|
| Create | `notebooks/self_study_data_extraction.ipynb` | Entire self-study notebook |

No other files are created or modified.

---

## Task 1: Create notebook file + Section 0 (What You'll Build)

**Files:**
- Create: `notebooks/self_study_data_extraction.ipynb`

- [ ] **Step 1: Create the empty notebook file**

Write this minimal valid `.ipynb` to `notebooks/self_study_data_extraction.ipynb`:

```json
{
 "cells": [],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "name": "python",
   "version": "3.10.0"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
```

- [ ] **Step 2: Add the title + mission statement cell**

Add markdown cell at index 0:

```markdown
# Data Extraction & Web Scraping — Self-Study Guide

Welcome! This notebook teaches you how to collect data from the internet using three essential techniques:

1. **REST API** — request structured data from a server using standard HTTP calls
2. **GraphQL** — ask for exactly the fields you need, nothing more
3. **Web Scraping** — extract data directly from HTML when no API exists

**By the end of this notebook you will be able to:**
- Authenticate and paginate through a real REST API (GitHub)
- Write GraphQL queries to fetch precise data
- Parse HTML with BeautifulSoup and handle multi-page scrapes

> **Who this is for:** You know basic Python (variables, loops, functions). You've never called an API or scraped a website before. No worries — everything is explained step by step.
```

- [ ] **Step 3: Add the "What You'll Build" preview cell**

Add markdown cell at index 1:

```markdown
## What You'll Build

By the end of this notebook you'll have extracted four real datasets:

| # | Dataset | How | Output |
|---|---------|-----|--------|
| 1 | `pandas` library release history | REST API | `pandas_releases` DataFrame + DuckDB table |
| 2 | Top contributors by commit count | REST API | `contributors` DataFrame + DuckDB table |
| 3 | GraphQL query of `pandas` releases | GraphQL | `graphql_releases` DataFrame |
| 4 | Countries of the world (name, capital, population, area) | Web scraping | `countries` DataFrame |
| 5 | NHL hockey team stats (all 24 pages) | Web scraping + pagination | `hockey_stats` DataFrame |

Here's a sneak peek at what `pandas_releases` will look like:

```
   version          published_at                          summary
0  v2.2.1   2024-03-18 15:30:00+00:00  ## What's new in 2.2.1...
1  v2.2.0   2024-01-19 17:00:00+00:00  ## What's new in 2.2.0...
2  v2.1.4   2024-01-08 20:00:00+00:00  ## What's new in 2.1.4...
...
```
```

- [ ] **Step 4: Verify the notebook opens**

Open `notebooks/self_study_data_extraction.ipynb` in VS Code/JupyterLab and confirm both cells render correctly. No code to run yet.

- [ ] **Step 5: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: scaffold self-study notebook with intro and preview"
```

---

## Task 2: Setup Section

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add setup section header + libraries overview**

Add markdown cell:

```markdown
---
## Setup

Before we write any code, let's get our tools ready.

### Libraries we'll use

| Library | What it does |
|---------|-------------|
| `requests` | Makes HTTP calls to APIs and websites |
| `python-dotenv` | Loads secret keys from a `.env` file so they stay out of your code |
| `pandas` | Creates DataFrames — like spreadsheets in Python |
| `sqlalchemy` | Lets pandas talk to databases |
| `duckdb` | A fast, file-based database — we'll save our results here |
| `beautifulsoup4` | Parses HTML so we can extract data from web pages |

Install them if needed:
```bash
pip install requests python-dotenv pandas sqlalchemy duckdb-engine beautifulsoup4
```
```

- [ ] **Step 2: Add GitHub token walkthrough**

Add markdown cell:

```markdown
### Step 1 of 2: Get a GitHub Personal Access Token

We need a token so GitHub knows who we are and lets us call their API.

**Follow these steps:**

1. Go to [github.com](https://github.com) and sign in
2. Click your profile photo (top-right) → **Settings**
3. Scroll down the left sidebar → click **Developer settings** (at the very bottom)
4. Click **Personal access tokens** → **Fine-grained tokens** → **Generate new token**
5. Give it a name (e.g. `self-study-scraping`)
6. Set **Expiration** to 30 days
7. Under **Repository access**, select **Public repositories (read-only)**
8. Scroll to the bottom → click **Generate token**
9. **Copy the token immediately** — GitHub only shows it once!

> ⚠️ Keep your token private. Never paste it directly into a notebook or commit it to GitHub.
```

- [ ] **Step 3: Add .env creation code cell**

Add code cell:

```python
import os

# Run this cell ONCE to create your .env file.
# After running, open the .env file and paste your token in place of the placeholder.

env_path = '../.env'

if os.path.exists(env_path):
    print(f"ℹ️  .env file already exists at '{env_path}'.")
    print("   Open it and make sure GITHUB_TOKEN is set to your actual token.")
else:
    with open(env_path, 'w') as f:
        f.write("# GitHub personal access token\n")
        f.write("GITHUB_TOKEN='<PASTE-YOUR-TOKEN-HERE>'\n")
    print(f"✅ Created '{env_path}'")
    print("   Now open the file and replace <PASTE-YOUR-TOKEN-HERE> with your actual token.")
```

- [ ] **Step 4: Add .env instructions markdown cell**

Add markdown cell:

```markdown
### Step 2 of 2: Add your token to the .env file

1. Find the `.env` file in the **root folder** of this project (one level above `notebooks/`)
2. Open it in a text editor
3. Replace `<PASTE-YOUR-TOKEN-HERE>` with your actual token, like this:

```
GITHUB_TOKEN='ghp_xxxxxxxxxxxxxxxxxxxx'
```

4. Save and close the file

> 📁 The `.env` file is listed in `.gitignore` so it will **never** be pushed to GitHub.
```

- [ ] **Step 5: Add token loading + verification code cell**

Add code cell:

```python
import os
from dotenv import load_dotenv

load_dotenv()  # 👈 reads the .env file and loads GITHUB_TOKEN into the environment
access_token = os.getenv('GITHUB_TOKEN')

# Verify the token loaded correctly (prints first/last 4 chars only — never print the full token!)
if access_token and access_token != '<PASTE-YOUR-TOKEN-HERE>':
    print(f"✅ Token loaded: {access_token[:4]}...{access_token[-4:]}")
else:
    print("❌ Token not found or still a placeholder. Check your .env file.")
```

- [ ] **Step 6: Run the token cell and verify output**

Expected output (with your real token):
```
✅ Token loaded: ghp_...xxxx
```

If you see `❌`, check that: (1) `.env` is in the project root (not inside `notebooks/`), (2) you replaced the placeholder, (3) you saved the file.

- [ ] **Step 7: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add setup section with token walkthrough"
```

---

## Task 3: Chapter 1 — Story Hook + HTTP Concept

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add chapter header + story hook**

Add markdown cell:

```markdown
---
## Chapter 1 — REST API

### The Mission

You've joined a team that tracks the health of popular open-source libraries. Your first task: **collect the full release history of the `pandas` library** — every version, when it was published, and what changed.

Pandas hosts all of this on GitHub. GitHub exposes it via a **REST API** — a web service that returns structured data when you send it the right request. Let's learn how.
```

- [ ] **Step 2: Add HTTP concept cell with SVG diagram**

Add markdown cell:

```markdown
### Concept: How a REST API Works

When you call an API, your Python script sends an **HTTP request** to a server. The server processes it and sends back an **HTTP response** containing data (usually in JSON format).

<svg viewBox="0 0 620 110" xmlns="http://www.w3.org/2000/svg" style="font-family:sans-serif;font-size:12px;max-width:620px;display:block;margin:16px 0;">
  <rect x="10" y="35" width="120" height="42" rx="6" fill="#dbeafe" stroke="#3b82f6" stroke-width="1.5"/>
  <text x="70" y="57" text-anchor="middle" fill="#1e40af" font-weight="bold">Your Script</text>
  <text x="70" y="71" text-anchor="middle" fill="#1e40af" font-size="10">(requests.get)</text>
  <rect x="490" y="35" width="120" height="42" rx="6" fill="#dcfce7" stroke="#22c55e" stroke-width="1.5"/>
  <text x="550" y="57" text-anchor="middle" fill="#166534" font-weight="bold">GitHub API</text>
  <text x="550" y="71" text-anchor="middle" fill="#166534" font-size="10">api.github.com</text>
  <defs>
    <marker id="arr-blue" markerWidth="8" markerHeight="6" refX="7" refY="3" orient="auto"><polygon points="0 0,8 3,0 6" fill="#3b82f6"/></marker>
    <marker id="arr-green" markerWidth="8" markerHeight="6" refX="7" refY="3" orient="auto"><polygon points="0 0,8 3,0 6" fill="#22c55e"/></marker>
  </defs>
  <line x1="130" y1="50" x2="488" y2="50" stroke="#3b82f6" stroke-width="1.5" marker-end="url(#arr-blue)"/>
  <text x="310" y="44" text-anchor="middle" fill="#1d4ed8" font-size="11">① GET /repos/pandas-dev/pandas/releases   +   Authorization header</text>
  <line x1="490" y1="63" x2="132" y2="63" stroke="#22c55e" stroke-width="1.5" marker-end="url(#arr-green)"/>
  <text x="310" y="80" text-anchor="middle" fill="#15803d" font-size="11">② 200 OK   +   JSON array of release objects</text>
</svg>

**Key terms:**

| Term | What it means |
|------|---------------|
| **Endpoint** | The URL you call, e.g. `https://api.github.com/repos/pandas-dev/pandas/releases` |
| **HTTP verb** | The type of action — `GET` = read data, `POST` = send data |
| **Header** | Extra info sent with the request — e.g. your identity token |
| **Status code** | A 3-digit number in the response: `200` = success, `4xx` = your error, `5xx` = server error |
| **JSON** | The data format APIs return — looks like a Python dict/list |
| **Bearer token** | Your API key, passed in the `Authorization` header |
```

- [ ] **Step 3: Add JSON structure concept cell**

Add markdown cell:

```markdown
### Concept: JSON is just Python dicts and lists

JSON responses from APIs look like this:

```json
[
  {
    "tag_name": "v2.2.1",
    "published_at": "2024-03-18T15:30:00Z",
    "body": "## What's new in 2.2.1 ..."
  },
  {
    "tag_name": "v2.2.0",
    "published_at": "2024-01-19T17:00:00Z",
    "body": "## What's new in 2.2.0 ..."
  }
]
```

- The outer `[...]` means it's a **list**
- Each `{...}` is a **dict** with key-value pairs
- In Python: `response.json()` gives you the list. `response.json()[0]["tag_name"]` gives you `"v2.2.1"`
```

- [ ] **Step 4: Verify markdown renders correctly**

Open the notebook, confirm the SVG diagram renders and the table is formatted. No code to run.

- [ ] **Step 5: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 1 story hook and HTTP concept cells"
```

---

## Task 4: Chapter 1 — Worked Example: Fetch pandas Releases

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add "make the request" introduction markdown**

Add markdown cell:

```markdown
### Worked Example: Fetching pandas Releases

Let's call the GitHub releases endpoint step by step. We'll explain every line.
```

- [ ] **Step 2: Add imports + first request code cell**

Add code cell:

```python
import requests
import os
from dotenv import load_dotenv

load_dotenv()
access_token = os.getenv('GITHUB_TOKEN')

response = requests.get(
    "https://api.github.com/repos/pandas-dev/pandas/releases",  # 👈 the endpoint
    headers={
        "Accept": "application/vnd.github+json",   # 👈 tells GitHub we want JSON format
        "Authorization": f"Bearer {access_token}", # 👈 proves who we are
        "X-GitHub-Api-Version": "2022-11-28"       # 👈 pins to a stable API version
    }
)
```

- [ ] **Step 3: Add status code check cell**

Add code cell:

```python
# A status code of 200 means success.
# Print it so we can confirm the request worked before going further.
print(response.status_code)
```

- [ ] **Step 4: Add troubleshooting markdown cell**

Add markdown cell:

```markdown
> **Troubleshooting — if you don't see `200`:**
>
> | Status code | Meaning | Fix |
> |-------------|---------|-----|
> | `401` | Unauthorized — token missing or wrong | Check your `.env` file. Print `access_token` to see if it loaded. |
> | `403` | Forbidden — token doesn't have permission | Create a new token with "Public repositories (read-only)" access |
> | `404` | Not found — URL is wrong | Double-check the endpoint URL for typos |
> | `422` | Unprocessable — bad query parameter | Check `per_page` / `page` values |
>
> **Quick debug:** Run `print(access_token)` to see if the token loaded. If it shows `None`, your `.env` file isn't being found.
```

- [ ] **Step 5: Add JSON parsing cells**

Add code cell:

```python
# .json() converts the response body from raw text into a Python list/dict
releases = response.json()
print(type(releases))  # should be <class 'list'>
print(len(releases))   # default page size is 30
```

Add code cell:

```python
# Look at the first release — it's the most recent version
releases[0]
```

Add code cell:

```python
# We care about three fields: tag_name, published_at, body
print("Version:", releases[0]['tag_name'])
print("Published:", releases[0]['published_at'])
```

- [ ] **Step 6: Add pagination explanation markdown**

Add markdown cell:

```markdown
### Pagination — Getting More Than 30 Results

By default GitHub returns 30 results per page. `pandas` has 100+ releases, so we need to page through.

Two query parameters control this:
- `per_page=100` — return up to 100 results per page (the maximum)
- `page=2` — get the second page of results

We add them to the URL after a `?`, separated by `&`:
```
/releases?per_page=100&page=1
/releases?per_page=100&page=2
```
```

- [ ] **Step 7: Add pagination code cells**

Add code cell:

```python
# Get page 1 with 100 results per page
response_p1 = requests.get(
    "https://api.github.com/repos/pandas-dev/pandas/releases?per_page=100&page=1",
    headers={
        "Accept": "application/vnd.github+json",
        "Authorization": f"Bearer {access_token}",
        "X-GitHub-Api-Version": "2022-11-28"
    }
)
releases_p1 = response_p1.json()
print(f"Page 1: {len(releases_p1)} releases")
print("Oldest on page 1:", releases_p1[-1]['tag_name'])
```

Add code cell:

```python
# Get page 2 (releases older than what page 1 returned)
response_p2 = requests.get(
    "https://api.github.com/repos/pandas-dev/pandas/releases?per_page=100&page=2",
    headers={
        "Accept": "application/vnd.github+json",
        "Authorization": f"Bearer {access_token}",
        "X-GitHub-Api-Version": "2022-11-28"
    }
)
releases_p2 = response_p2.json()
print(f"Page 2: {len(releases_p2)} releases")
print("Oldest on page 2:", releases_p2[-1]['tag_name'])
```

- [ ] **Step 8: Add DataFrame creation cells**

Add markdown cell:

```markdown
### Building a DataFrame

We only need three fields from each release. Let's use a list comprehension to extract them, then build a DataFrame.
```

Add code cell:

```python
import pandas as pd

# Extract only the fields we need from page 1 (the most recent 100 releases)
records = [
    {
        "version": r["tag_name"],          # 👈 the version number e.g. "v2.2.1"
        "published_at": r["published_at"], # 👈 ISO 8601 date string
        "summary": r["body"]               # 👈 the release notes
    }
    for r in releases_p1
]

pandas_releases = pd.DataFrame(records)
pandas_releases
```

Add code cell:

```python
# Convert published_at from a string to a proper datetime object
# This lets us do date arithmetic (e.g. calculate time between releases)
pandas_releases['published_at'] = pd.to_datetime(pandas_releases['published_at'])
pandas_releases.dtypes
```

- [ ] **Step 9: Run all Chapter 1 worked example cells and verify**

Run all code cells added in this task in order. Expected outputs:
- Status code cell → `200`
- `type(releases)` → `<class 'list'>`
- `len(releases)` → `30`
- `releases[0]` → dict with `tag_name`, `published_at`, `body`, and many other keys
- Page 1 → `100 releases`
- Page 2 → some number of releases (typically 30–60)
- DataFrame → table with `version`, `published_at`, `summary` columns
- `dtypes` → `published_at` should show `datetime64[ns, UTC]`

- [ ] **Step 10: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 1 worked example - fetch pandas releases"
```

---

## Task 5: Chapter 1 — Save to DuckDB

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add DuckDB explanation markdown**

Add markdown cell:

```markdown
### Saving to DuckDB

Now let's persist the data to a database. We use **DuckDB** — a lightweight, file-based database that needs no server setup. `SQLAlchemy` gives pandas a way to write DataFrames directly into it using `.to_sql()`.
```

- [ ] **Step 2: Add DuckDB save code cells**

Add code cell:

```python
import sqlalchemy as sqla
import os

# Get the absolute path to the project root (one level above notebooks/)
parent_dir = os.path.abspath(os.path.pardir)

# Create a connection to the DuckDB file
engine = sqla.create_engine(f'duckdb:///{parent_dir}/output/unit-2-4.db')
```

Add code cell:

```python
# Write the DataFrame to a table called "pandas_releases"
# if_exists='replace' will overwrite the table if it already exists
try:
    pandas_releases.to_sql("pandas_releases", engine, if_exists='replace', index=False)
    print("✅ Saved pandas_releases to DuckDB")
except Exception as e:
    print(f"❌ Error: {e}")
```

Add code cell:

```python
# Always dispose the engine when done — this closes the connection
# You can now open output/unit-2-4.db in DBeaver or any DuckDB client
engine.dispose()
print("Connection closed.")
```

- [ ] **Step 3: Run and verify**

Run the three cells. Expected:
- `✅ Saved pandas_releases to DuckDB`
- `Connection closed.`

Verify the file exists: `output/unit-2-4.db` should appear in the project's `output/` folder.

- [ ] **Step 4: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 1 DuckDB save cells"
```

---

## Task 6: Chapter 1 — Exercise: Contributors

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add exercise markdown cell with 3 hints**

Add markdown cell:

```markdown
---
### ✏️ Exercise 1: Who Has Contributed the Most Commits to pandas?

The `stats/contributors` endpoint returns a list of contributors and their commit counts.

**Your task:**
1. Call `https://api.github.com/repos/pandas-dev/pandas/stats/contributors` with the same headers as before
2. Extract the `login` (contributor username) and `total` (commit count) from each item
3. Build a DataFrame with columns `login` and `total_commits`
4. Find the contributor with the highest number of commits
5. Save the DataFrame to DuckDB as a table named `pandas_contributors`

> ⚠️ **Note on this endpoint:** GitHub computes contributor stats on demand. If you get a `202` status code (instead of `200`), the data isn't ready yet — wait 10 seconds and try again.

---

**💡 Hint 1 — The data structure:**  
Each item in the response list looks like this:
```python
{
    "total": 1234,         # total commits by this contributor
    "author": {
        "login": "jreback" # their GitHub username
    },
    "weeks": [...]         # weekly breakdown — ignore this
}
```

**💡 Hint 2 — Accessing nested values:**  
To get the login, use `item["author"]["login"]`. To get total commits, use `item["total"]`.

**💡 Hint 3 — Finding the top contributor:**  
After building the DataFrame, use `.sort_values("total_commits", ascending=False).head(1)` to find the contributor with the most commits.
```

- [ ] **Step 2: Add empty code cell**

Add code cell:

```python
# Your code here
```

- [ ] **Step 3: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 1 contributors exercise"
```

---

## Task 7: Chapter 2 — Story Hook + GraphQL Concept

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add chapter header + story hook**

Add markdown cell:

```markdown
---
## Chapter 2 — GraphQL

### The Mission

You got the releases data from REST — but the response contained dozens of fields you didn't need (`assets`, `author`, `reactions`, etc.). Each wasted field costs bandwidth and processing time. 

Your team asks: **"Can we fetch only the exact fields we need?"**

Enter **GraphQL** — GitHub's newer API that lets you specify exactly what you want. Same data, but you're in control of the shape.
```

- [ ] **Step 2: Add REST vs GraphQL concept with diagram**

Add markdown cell:

```markdown
### Concept: REST vs GraphQL

**The over-fetching problem with REST:**

```
REST response for one release (~30 fields):
{
  "url": "...",           ← don't need
  "assets_url": "...",    ← don't need
  "upload_url": "...",    ← don't need
  "html_url": "...",      ← don't need
  "id": 12345,            ← don't need
  "author": {...},        ← don't need
  "tag_name": "v2.2.1",  ✅ need
  "published_at": "...", ✅ need
  "body": "...",         ✅ need
  ... 21 more fields ...  ← don't need
}
```

**With GraphQL, you ask for only what you need:**

```graphql
query {
  repository(owner: "pandas-dev", name: "pandas") {
    releases(first: 100) {
      edges {
        node {
          tagName       ← only this
          publishedAt   ← only this
          description   ← only this
        }
      }
    }
  }
}
```

<svg viewBox="0 0 620 90" xmlns="http://www.w3.org/2000/svg" style="font-family:sans-serif;font-size:11px;max-width:620px;display:block;margin:16px 0;">
  <rect x="10" y="20" width="120" height="50" rx="5" fill="#dbeafe" stroke="#3b82f6" stroke-width="1.5"/>
  <text x="70" y="43" text-anchor="middle" fill="#1e40af" font-weight="bold">Your Script</text>
  <text x="70" y="58" text-anchor="middle" fill="#1e40af" font-size="10">requests.post</text>
  <rect x="490" y="20" width="120" height="50" rx="5" fill="#f3e8ff" stroke="#9333ea" stroke-width="1.5"/>
  <text x="550" y="43" text-anchor="middle" fill="#6b21a8" font-weight="bold">GraphQL API</text>
  <text x="550" y="58" text-anchor="middle" fill="#6b21a8" font-size="10">api.github.com/graphql</text>
  <defs>
    <marker id="arr-p" markerWidth="8" markerHeight="6" refX="7" refY="3" orient="auto"><polygon points="0 0,8 3,0 6" fill="#9333ea"/></marker>
    <marker id="arr-pb" markerWidth="8" markerHeight="6" refX="7" refY="3" orient="auto"><polygon points="0 0,8 3,0 6" fill="#7c3aed"/></marker>
  </defs>
  <line x1="130" y1="37" x2="488" y2="37" stroke="#9333ea" stroke-width="1.5" marker-end="url(#arr-p)"/>
  <text x="310" y="31" text-anchor="middle" fill="#7e22ce" font-size="10">POST /graphql  +  JSON body: {"query": "..."}</text>
  <line x1="490" y1="50" x2="132" y2="50" stroke="#7c3aed" stroke-width="1.5" marker-end="url(#arr-pb)"/>
  <text x="310" y="67" text-anchor="middle" fill="#6b21a8" font-size="10">200 OK  +  {"data": { exactly what you asked for }}</text>
</svg>

**Key differences:**

| | REST | GraphQL |
|--|------|---------|
| Endpoint | One per resource (`/releases`, `/issues`, ...) | Single endpoint (`/graphql`) |
| HTTP verb | `GET` for reading | `POST` (always) |
| Response shape | Fixed — server decides | Flexible — you decide |
| Over-fetching | Common | Never |
```

- [ ] **Step 3: Add GraphQL query anatomy markdown**

Add markdown cell:

```markdown
### Concept: Anatomy of a GraphQL Query

```graphql
query {                                          # ← operation type: "query" means read
  repository(owner: "pandas-dev", name: "pandas") {  # ← the object to start from, with arguments
    releases(first: 100) {                       # ← sub-field with argument (limit to 100)
      totalCount                                 # ← a scalar field (just returns a number)
      edges {                                    # ← "edges" contains the items in the list
        node {                                   # ← "node" is the actual release object
          tagName                                # ← scalar fields we want on each release
          description
          publishedAt
        }
      }
    }
  }
}
```

- **`edges` and `node`** — GraphQL uses a "connection" pattern for lists. `edges` is the list, each `node` is one item.
- **`totalCount`** — a bonus: we get the total count for free without fetching all records.
- **Nested fields** — you can go as deep as the schema allows. Each `{...}` block specifies which sub-fields to return.
```

- [ ] **Step 4: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 2 story hook and GraphQL concept cells"
```

---

## Task 8: Chapter 2 — Worked Example: GraphQL Releases Query

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add worked example intro**

Add markdown cell:

```markdown
### Worked Example: Fetching Releases via GraphQL

Let's replicate what we did with REST — but using a GraphQL query. Notice that we use `POST` (not `GET`) and send the query in the request body.
```

- [ ] **Step 2: Add GraphQL request code cell**

Add code cell:

```python
# Define the query as a multi-line string
query = """
query {
    repository(owner: "pandas-dev", name: "pandas") {
        releases(first: 100) {
            totalCount
            edges {
                node {
                    tagName
                    description
                    publishedAt
                }
            }
        }
    }
}
"""

# GraphQL always uses POST, and the query goes in the JSON body
response = requests.post(
    "https://api.github.com/graphql",           # 👈 single endpoint for all GraphQL queries
    headers={"Authorization": f"Bearer {access_token}"},
    json={"query": query}                        # 👈 query is sent as a JSON field called "query"
)

print(response.status_code)  # should be 200
```

- [ ] **Step 3: Add response navigation cells**

Add code cell:

```python
data = response.json()

# GraphQL wraps all results under a "data" key
# Navigate: data → repository → releases → totalCount
print("Total releases:", data['data']['repository']['releases']['totalCount'])
```

Add code cell:

```python
# Get the list of releases — remember: edges → node
edges = data['data']['repository']['releases']['edges']
print("Releases returned:", len(edges))

# Look at the first release
edges[0]['node']
```

Add code cell:

```python
# Build a DataFrame — same approach as REST but navigating the nested structure
graphql_releases = pd.DataFrame([
    {
        "version": edge["node"]["tagName"],
        "published_at": edge["node"]["publishedAt"],
        "description": edge["node"]["description"]
    }
    for edge in edges
])

graphql_releases.head()
```

- [ ] **Step 4: Run and verify**

Run all cells. Expected:
- Status code → `200`
- Total releases → a number like `180`
- `edges[0]['node']` → dict with `tagName`, `description`, `publishedAt`
- DataFrame → table with 3 columns, up to 100 rows

- [ ] **Step 5: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 2 GraphQL releases worked example"
```

---

## Task 9: Chapter 2 — Exercise: Query Issues

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add exercise markdown with 3 hints**

Add markdown cell:

```markdown
---
### ✏️ Exercise 2: Query the First 50 Issues via GraphQL

Now write your own GraphQL query. Fetch the first 50 open issues from the `pandas` repository with these fields: `title`, `createdAt`, and the author's `login`.

**Your task:**
1. Write a GraphQL query for the `issues` resource (use `first: 50`)
2. On each issue node, request: `title`, `createdAt`, and `author { login }`
3. Send it via `requests.post` to `https://api.github.com/graphql`
4. Navigate the response and build a DataFrame with columns: `title`, `created_at`, `author`

---

**💡 Hint 1 — Query structure:**  
Start with `repository(owner: "pandas-dev", name: "pandas")`, then `issues(first: 50)`, then `edges { node { ... } }`.

**💡 Hint 2 — The `author` field is nested:**  
`author` is an object, not a scalar. Inside it, request `login`:
```graphql
author {
  login
}
```

**💡 Hint 3 — Navigating the response:**  
The path is: `data['data']['repository']['issues']['edges']`  
Each edge's node has: `edge['node']['title']`, `edge['node']['createdAt']`, `edge['node']['author']['login']`
```

- [ ] **Step 2: Add empty code cell**

Add code cell:

```python
# Your code here
```

- [ ] **Step 3: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 2 issues exercise"
```

---

## Task 10: Chapter 3 — Story Hook + Web Scraping Concept

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add chapter header + story hook**

Add markdown cell:

```markdown
---
## Chapter 3 — Web Scraping

### The Mission

Not all data has an API. You need **world country statistics** — population, capital, area — for a geography dashboard. There's no API for this dataset, but the data is sitting right there on a public website.

**Web scraping** lets you extract data directly from a website's HTML. Instead of calling an API, you download the page and parse the HTML to find the data you need.
```

- [ ] **Step 2: Add HTML tree concept cell with SVG diagram**

Add markdown cell:

```markdown
### Concept: HTML is a Tree of Tags

Every webpage is built from **HTML tags** nested inside each other, forming a tree structure. BeautifulSoup lets you navigate and search this tree.

<svg viewBox="0 0 580 220" xmlns="http://www.w3.org/2000/svg" style="font-family:monospace;font-size:12px;max-width:580px;display:block;margin:16px 0;">
  <!-- html root -->
  <rect x="240" y="5" width="100" height="28" rx="4" fill="#fef9c3" stroke="#ca8a04" stroke-width="1.5"/>
  <text x="290" y="24" text-anchor="middle" fill="#713f12">&lt;html&gt;</text>
  <!-- head -->
  <rect x="100" y="55" width="100" height="28" rx="4" fill="#e0f2fe" stroke="#0284c7" stroke-width="1.5"/>
  <text x="150" y="74" text-anchor="middle" fill="#075985">&lt;head&gt;</text>
  <!-- body -->
  <rect x="380" y="55" width="100" height="28" rx="4" fill="#e0f2fe" stroke="#0284c7" stroke-width="1.5"/>
  <text x="430" y="74" text-anchor="middle" fill="#075985">&lt;body&gt;</text>
  <!-- lines from html -->
  <line x1="290" y1="33" x2="150" y2="55" stroke="#9ca3af" stroke-width="1.5"/>
  <line x1="290" y1="33" x2="430" y2="55" stroke="#9ca3af" stroke-width="1.5"/>
  <!-- title under head -->
  <rect x="60" y="110" width="100" height="28" rx="4" fill="#dcfce7" stroke="#16a34a" stroke-width="1.5"/>
  <text x="110" y="129" text-anchor="middle" fill="#166534">&lt;title&gt;</text>
  <line x1="150" y1="83" x2="110" y2="110" stroke="#9ca3af" stroke-width="1.5"/>
  <!-- div, h3, p, span under body -->
  <rect x="290" y="110" width="100" height="28" rx="4" fill="#dcfce7" stroke="#16a34a" stroke-width="1.5"/>
  <text x="340" y="129" text-anchor="middle" fill="#166534">&lt;div&gt;</text>
  <rect x="430" y="110" width="100" height="28" rx="4" fill="#dcfce7" stroke="#16a34a" stroke-width="1.5"/>
  <text x="480" y="129" text-anchor="middle" fill="#166534">&lt;p&gt;</text>
  <line x1="430" y1="83" x2="340" y2="110" stroke="#9ca3af" stroke-width="1.5"/>
  <line x1="430" y1="83" x2="480" y2="110" stroke="#9ca3af" stroke-width="1.5"/>
  <!-- h3, span under div -->
  <rect x="240" y="165" width="80" height="28" rx="4" fill="#fce7f3" stroke="#db2777" stroke-width="1.5"/>
  <text x="280" y="184" text-anchor="middle" fill="#831843">&lt;h3&gt;</text>
  <rect x="360" y="165" width="80" height="28" rx="4" fill="#fce7f3" stroke="#db2777" stroke-width="1.5"/>
  <text x="400" y="184" text-anchor="middle" fill="#831843">&lt;span&gt;</text>
  <line x1="340" y1="138" x2="280" y2="165" stroke="#9ca3af" stroke-width="1.5"/>
  <line x1="340" y1="138" x2="400" y2="165" stroke="#9ca3af" stroke-width="1.5"/>
  <!-- annotations -->
  <text x="8" y="174" fill="#6b7280" font-size="10">soup.find('h3')</text>
  <text x="8" y="188" fill="#db2777" font-size="10" font-weight="bold">↗ finds this</text>
</svg>

**BeautifulSoup lets you search by:**
- **Tag name:** `soup.find('h3')` — finds the first `<h3>` tag
- **Tag + class:** `soup.find_all('h3', 'country-name')` — finds all `<h3>` tags with class `country-name`
- **CSS selector:** `soup.select('div.country > h3')` — uses CSS selector syntax
```

- [ ] **Step 3: Add CSS selectors concept cell**

Add markdown cell:

```markdown
### Concept: CSS Selectors

CSS selectors let you target HTML elements precisely. Here are the ones you'll use most:

| Selector | Example | What it matches |
|----------|---------|-----------------|
| Tag | `h3` | All `<h3>` elements |
| Class | `.country-name` | All elements with `class="country-name"` |
| Tag + class | `h3.country-name` | `<h3>` elements with `class="country-name"` |
| Child | `div > p` | `<p>` that is a direct child of `<div>` |

**In BeautifulSoup:**
```python
soup.find('h3', 'country-name')          # first <h3 class="country-name">
soup.find_all('span', 'country-capital') # all <span class="country-capital">
```
```

- [ ] **Step 4: Add ethics note markdown**

Add markdown cell:

```markdown
### ⚖️ Ethics & Legality of Web Scraping

Before scraping any site, check:

1. **`robots.txt`** — visit `https://example.com/robots.txt` to see what the site allows bots to access
2. **Terms of Service** — some sites explicitly prohibit scraping
3. **Rate limiting** — always add `time.sleep()` between requests. Rapid requests can overload a server
4. **Copyright** — scraped data may be copyrighted. Check before redistribution

> We're using [scrapethissite.com](https://www.scrapethissite.com) — a site designed specifically for scraping practice. It's safe and legal to scrape.
```

- [ ] **Step 5: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 3 story hook and web scraping concept cells"
```

---

## Task 11: Chapter 3 — Worked Example: Scrape Countries

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add worked example intro**

Add markdown cell:

```markdown
### Worked Example: Scraping Country Data

We'll scrape [scrapethissite.com/pages/simple/](https://www.scrapethissite.com/pages/simple/) — a page listing all countries with their capital, population, and area.

**Steps:**
1. Download the page with `requests.get()`
2. Parse the HTML with `BeautifulSoup`
3. Find the elements containing the data
4. Extract text and build a DataFrame
```

- [ ] **Step 2: Add BeautifulSoup import + fetch cell**

Add code cell:

```python
from bs4 import BeautifulSoup

# Download the page
r = requests.get("https://www.scrapethissite.com/pages/simple/")
print(r.status_code)   # 200 = success
print(type(r.text))    # r.text is the raw HTML as a string
```

- [ ] **Step 3: Add parsing intro cells**

Add code cell:

```python
# Parse the HTML — BeautifulSoup builds the tree structure we can navigate
soup = BeautifulSoup(r.text, "html.parser")
# 👈 "html.parser" is Python's built-in parser — no extra install needed
```

Add markdown cell:

```markdown
**Inspect the page first:** Right-click on a country name in your browser → "Inspect Element". You'll see something like:

```html
<div class="col-md-4 country">
  <h3 class="country-name">Andorra</h3>
  <span class="country-capital">Andorra la Vella</span>
  <span class="country-population">84000</span>
  <span class="country-area">468.0</span>
</div>
```

Each country is a `<div class="col-md-4 country">` containing child elements with specific classes. We'll use those classes to target exactly what we need.
```

- [ ] **Step 4: Add element finding cells**

Add code cell:

```python
# Find all country name elements — these are <h3> tags with class "country-name"
country_name_elements = soup.find_all('h3', 'country-name')
print(f"Found {len(country_name_elements)} country names")
country_name_elements[0]   # see the raw tag
```

Add code cell:

```python
# Extract just the text — .text gives the raw text, .strip() removes whitespace
print(country_name_elements[0].text.strip())
```

- [ ] **Step 5: Add full extraction code cell**

Add markdown cell:

```markdown
For efficiency, let's collect all data in one loop — for each country `<div>`, extract all four fields at once using `.find()` on the child elements.
```

Add code cell:

```python
countries = []

for country_div in soup.find_all('div', 'col-md-4 country'):
    country = {}
    country['name']       = country_div.find('h3').text.strip()
    country['capital']    = country_div.find('span', 'country-capital').text.strip()
    country['population'] = country_div.find('span', 'country-population').text.strip()
    country['area_km2']   = country_div.find('span', 'country-area').text.strip()
    countries.append(country)

countries_df = pd.DataFrame(countries)
countries_df.head()
```

- [ ] **Step 6: Run and verify**

Expected:
- Status code → `200`
- `len(country_name_elements)` → `250`
- First country name → `'Andorra'`
- DataFrame → 250 rows, columns: `name`, `capital`, `population`, `area_km2`

- [ ] **Step 7: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 3 countries scraping worked example"
```

---

## Task 12: Chapter 3 — Exercise: Hockey Stats with Pagination

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add multi-page scraping intro markdown**

Add markdown cell:

```markdown
### Multi-Page Scraping

Some sites split data across multiple pages. When you click "Page 2" the URL changes — for example:

```
https://www.scrapethissite.com/pages/forms/?page_num=2
```

We can automate this with a `for` loop. Here's a helper function that parses one page's table into a list of row dicts — you'll use it in the exercise below.
```

- [ ] **Step 2: Add helper function code cell (provided, not an exercise)**

Add code cell:

```python
import time

def parse_hockey_page(html_text: str) -> list:
    """
    Parse one page of the hockey stats table and return a list of row dicts.
    
    Args:
        html_text: Raw HTML string from requests.get().text
    
    Returns:
        List of dicts, one per team row.
    """
    soup = BeautifulSoup(html_text, "html.parser")
    header_row = soup.find('tr')
    headers = [th.text.strip() for th in header_row.find_all('th')]
    
    rows = []
    for team_row in soup.find_all('tr', 'team'):
        row = {}
        for header, cell in zip(headers, team_row.find_all('td')):
            row[header] = cell.text.strip()
        rows.append(row)
    return rows
```

- [ ] **Step 3: Add exercise markdown with 3 hints**

Add markdown cell:

```markdown
---
### ✏️ Exercise 3: Scrape All 24 Pages of NHL Hockey Stats

Use the `parse_hockey_page()` helper above to scrape all 24 pages from [scrapethissite.com/pages/forms/](https://www.scrapethissite.com/pages/forms/).

**Your task:**
1. Loop through pages 1–24 (the URL pattern is `?page_num=N`)
2. For each page, call `requests.get()` then `parse_hockey_page(r.text)`
3. Collect all row dicts into a single list called `all_rows`
4. Add `time.sleep(1)` between requests (be polite to the server!)
5. Convert `all_rows` to a DataFrame called `hockey_stats`
6. Find the team with the most total Wins across all years

---

**💡 Hint 1 — URL pattern:**  
`f"https://www.scrapethissite.com/pages/forms/?page_num={page}"` where `page` goes from 1 to 24.

**💡 Hint 2 — Collecting results:**  
Use `all_rows = []`, then `all_rows.extend(parse_hockey_page(r.text))` inside the loop. `.extend()` adds multiple items at once (unlike `.append()` which would add a list-of-dicts as a single item).

**💡 Hint 3 — Finding the team with most wins:**  
After converting to a DataFrame, cast the `Wins` column to numeric with `pd.to_numeric(hockey_stats['Wins'], errors='coerce')`, then use `.groupby('Team')['Wins'].sum().idxmax()`.
```

- [ ] **Step 4: Add empty code cell**

Add code cell:

```python
# Your code here
```

- [ ] **Step 5: Commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add Chapter 3 hockey stats exercise with pagination"
```

---

## Task 13: Wrap-Up Section

**Files:**
- Modify: `notebooks/self_study_data_extraction.ipynb` (add cells at end)

- [ ] **Step 1: Add wrap-up header markdown**

Add markdown cell:

```markdown
---
## Wrap-Up: What You've Learned

Congratulations — you've extracted real data using three different techniques! Here's how they compare:
```

- [ ] **Step 2: Add comparison table markdown**

Add markdown cell:

```markdown
### When to Use Which Tool

| | REST API | GraphQL | Web Scraping |
|--|----------|---------|--------------|
| **Best for** | Standard data access from any service | When you need precise, flexible queries | When no API exists |
| **Authentication** | Usually required (Bearer token, API key) | Usually required | Usually not required |
| **Data format** | JSON (fixed shape) | JSON (shape you define) | HTML (requires parsing) |
| **Reliability** | High — API contracts are stable | High — schema-driven | Low — HTML can change anytime |
| **Rate limits** | Common — check docs | Common — check docs | Unofficial — be conservative |
| **Pagination** | `page` + `per_page` params | `first`/`after` cursor | URL pattern (`?page=N`) |
| **Legal/ethical risk** | Low | Low | Check ToS + robots.txt |

**Rule of thumb:**
1. Check if there's a REST API first
2. If REST over-fetches and a GraphQL API exists, use that
3. Fall back to scraping only when no API is available
```

- [ ] **Step 3: Add "What's Next" markdown**

Add markdown cell:

```markdown
### What's Next

You've covered the fundamentals. Here are the natural next steps:

- **Rate limiting & retries** — production pipelines use `tenacity` or custom retry logic to handle `429 Too Many Requests` errors gracefully
- **Async requests** — `aiohttp` + `asyncio` lets you make hundreds of requests concurrently instead of one at a time
- **Dynamic pages** — some websites load data via JavaScript. `Selenium` or `Playwright` can control a real browser to scrape these
- **Scheduled pipelines** — combine what you've learned with `Apache Airflow` or `Prefect` to run extraction jobs on a schedule

Good luck! 🚀
```

- [ ] **Step 4: Final verification — run all cells in order**

Open the notebook, restart the kernel, and run all cells from top to bottom. Verify:
- Setup section: token loads with `✅`
- Chapter 1 worked example: all cells produce expected output
- Chapter 1 exercise: hint text renders, empty cell is present
- Chapter 2 worked example: GraphQL response parses correctly
- Chapter 2 exercise: hint text renders, empty cell is present
- Chapter 3 worked example: 250 countries scraped
- Chapter 3 exercise: helper function defined, empty cell present
- Wrap-up: tables render correctly

- [ ] **Step 5: Final commit**

```bash
git add notebooks/self_study_data_extraction.ipynb
git commit -m "feat: add wrap-up section — self-study notebook complete"
```

---

## Self-Review Checklist

**Spec coverage:**
- [x] Problem-first story structure — each chapter has a story hook
- [x] Section 0: What You'll Build with output preview
- [x] Setup: GitHub token step-by-step + .env creation
- [x] Chapter 1 REST: concept diagram, worked example (releases + pagination + DuckDB), exercise (contributors)
- [x] Chapter 2 GraphQL: concept diagram, REST vs GraphQL comparison, worked example, exercise (issues)
- [x] Chapter 3 Scraping: HTML tree diagram, CSS selectors, ethics note, worked (countries), exercise (hockey+pagination)
- [x] Wrap-up: summary comparison table + what's next
- [x] 3 progressive hints per exercise
- [x] Troubleshooting boxes after cells that can fail
- [x] All 4 DataFrames covered: releases, contributors (exercise), countries, hockey (exercise)
- [x] 2 DuckDB tables: pandas_releases (Task 5), pandas_contributors (Exercise 1 requirement)
- [x] No changes to `extraction_scraping_lesson.ipynb`

**No placeholders:** All code cells contain complete, runnable code. All markdown cells contain actual content.
