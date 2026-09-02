# Connect Your AI Assistant to Your Data

## Query your database in plain English with MCP

This workshop shows why pasting your schema into a chat and hoping is not data access. Watch us connect your AI assistant to a real database with MCP, then query it in plain English: real tables, real joins, real results. And at the end, the catch: the connected AI answers a business question with full confidence, and gets it wrong.

## What is MCP?

Model Context Protocol (MCP) connects AI assistants to external tools and data. This server lets AI assistants execute SQL queries and inspect your PostgreSQL database schema.

## Setup

### 1. Install Dependencies

> **Note:** This project uses Poetry for dependency management. If you don't have Poetry installed, you can install it with:
> ```bash
> curl -sSL https://install.python-poetry.org | python3 -
> ```
> See the [official Poetry documentation](https://python-poetry.org/docs/#installation) for alternative installation methods.

```bash
poetry install
```

### 2. Configure Database

Set the PostgreSQL variables in `.env`:
```
DB_NAME=your_database
DB_USER=postgres
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=5432
```

## Testing

### Demo 1: Paste schema into Claude

Provide a database schema directly in the conversation and ask Claude questions about the tables and relationships.

### Demo 2: Upload Local CSV/file

Upload the data file and query the contents in natural language.

### Demo 3: API

Start the FastAPI testing server:

```bash
poetry run uvicorn api.simple_api:app --reload
```

Test the API at [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs).

### Demo 4: Connect Claude to Postgres with MCP

Run the MCP server through the MCP Inspector or connect it to VS Code using the configuration below.

### MCP Inspector (Recommended)

```bash
npx @modelcontextprotocol/inspector poetry run python postgres-mcp-server/main.py
```

This opens a web UI where you can:
- View available tools under the **Tools** tab
- Test `get_schema`
- See real-time results

### Quick Test

```bash
poetry run python postgres-mcp-server/main.py
```

Press `Ctrl+C` to stop. No errors = working correctly.

## MCP Servers

This workshop connects three MCP servers:

### PostgreSQL MCP server

**`execute_sql(query)`** - Execute a SQL query and return the results as rows.

**`get_schema(table)`** - Get column names and data types for a table.

### GitHub MCP server

Provides GitHub tools for working with repositories, issues, pull requests, and other GitHub data.

### Local filesystem MCP server

Provides tools for reading and working with files in the local workspace.

## Connect to VS Code

Add this to your VS Code MCP configuration, for example `.vscode/mcp.json`:

```json
{
  "servers": {
    "postgres": {
      "command": "poetry",
      "args": ["-C", "/absolute/path/to/postgres-mcp-server", "run", "python", "postgres-mcp-server/main.py"]
    }
  }
}
```

