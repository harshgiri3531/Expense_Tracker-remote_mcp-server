# Expense Tracker MCP Server

A remote [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server built with [FastMCP](https://gofastmcp.com) that lets an AI assistant such as Claude record and analyse personal expenses. Data is stored in SQLite and accessed asynchronously through `aiosqlite`.

**Live server:** `https://possible-jade-tiglon.fastmcp.app/mcp`

## Features

- Add expenses with a date, amount, category, optional subcategory and note
- List expenses within any date range, newest first
- Summarise spending by category, with totals and counts, optionally filtered to one category
- Expose a predefined category and subcategory taxonomy as an MCP resource
- Runs over HTTP, so it can be deployed remotely (for example on FastMCP Cloud)

## Tools

| Tool | Parameters | Description |
|---|---|---|
| `add_expense` | `date`, `amount`, `category`, `subcategory` (optional), `note` (optional) | Adds a new expense and returns its ID |
| `list_expenses` | `start_date`, `end_date` | Lists all expenses in the inclusive date range, ordered by date (newest first) |
| `summarize` | `start_date`, `end_date`, `category` (optional) | Returns total amount and number of entries per category, ordered by total (highest first) |

Dates use the `YYYY-MM-DD` format.

## Resources

| URI | Description |
|---|---|
| `expense:///categories` | JSON list of expense categories and their subcategories (from `categories.json`) |

The bundled categories include food, transport, housing, utilities, health, education, family_kids, entertainment, shopping, subscriptions, personal_care, gifts_donations, finance_fees, business, travel, home, pet, taxes, investments and misc. Each one has its own subcategories.

## Project Structure

```
test-remote-server/
├── pyproject.toml
├── uv.lock
├── .python-version
├── README.md
└── src/
    └── test_remote_server/
        ├── __init__.py
        ├── main.py           # FastMCP server, tools and resource
        └── categories.json   # Category and subcategory taxonomy
```

## Tech Stack

- Python 3.14
- FastMCP
- aiosqlite (SQLite)
- uv (package and environment management)

## Getting Started

### Prerequisites

- Python 3.14 or later
- [uv](https://docs.astral.sh/uv/)

### Installation

```bash
git clone <https://github.com/harshgiri3531/Expense_Tracker-remote_mcp-server>
cd test-remote-server
uv sync
```

### Run locally

```bash
uv run python src/test_remote_server/main.py
```

The server starts over HTTP on `0.0.0.0:8000`. The MCP endpoint is available at `http://localhost:8000/mcp`.

## Connecting to Claude

1. Open Claude and go to **Settings → Connectors**.
2. Choose **Add custom connector**.
3. Enter the server URL, for example `https://<your-app-name>.fastmcp.app/mcp`.
4. Start a chat and ask things like:
   - "Add an expense of 250 for food today"
   - "Show all my expenses for January 2026"
   - "Summarise my spending by category this year"

## Database

The SQLite database (`expenses.db`) is created automatically on startup in the system temp directory. The `expenses` table has these columns:

| Column | Type | Notes |
|---|---|---|
| `id` | INTEGER | Primary key, auto-increment |
| `date` | TEXT | Required |
| `amount` | REAL | Required |
| `category` | TEXT | Required |
| `subcategory` | TEXT | Defaults to empty |
| `note` | TEXT | Defaults to empty |

> **Note:** because the database lives in the temp directory, data may not persist across restarts or redeployments on some hosting platforms. For permanent storage, point `DB_PATH` at a persistent volume or an external database.

## Deployment

This server is deployed on FastMCP Cloud. To deploy your own copy:

1. Push the project to GitHub.
2. Connect the repository on FastMCP Cloud.
3. Set the entrypoint to `src/test_remote_server/main.py:mcp`.
4. Deploy, then use the generated URL as the connector URL.

## Author

**Harsh Giri**
