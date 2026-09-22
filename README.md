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

### Demo 1: Paste schema into the AI Assistant

Provide a database schema directly in the conversation and ask the AI Assistant questions about the tables and relationships.

Example Questions:

- What columns do we have in the sessions table?
- What is the longest session recorded?
- What tables do we have available in the database? 
- How many users pay with Apple Pay? <-- we expect an incorrect answer 

### Demo 2: Upload Local CSV/file

Upload the data file and query the contents in natural language.

Example Questions:

- How many users are on an iOS device?
- What is the breakdown of users by country?


### Demo 3: API

Start the FastAPI testing server:

```bash
poetry run uvicorn api.simple_api:app --reload
```

Test the API at [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs).

Example Query:

```SQL
SELECT COUNT(DISTINCT s.user_id) AS apple_pay_users FROM payments p JOIN subscriptions s USING (subscription_id) WHERE LOWER(p.method) = 'apple_pay';
```


### Demo 4: Connect the AI Assistant to Postgres with MCP

Run the MCP server through the MCP Inspector or connect it to VS Code using the configuration below.

After implementing all three MCP servers, try these out:

- Give me a breakdown of revenue for 2025 by payment method. Then create an HTML report with the results, save it in a reports folder, and commit the report to GitHub.
- How many engaged users have we had over the last 7 days? <-- we expect an incorrect answer 
- How many committed customers did we lose in August 2026? <-- we expect an incorrect answer 

Note, we have the following business definitions:

- “Engaged user”: User had ≥3 sessions in the last 7 days. 
- “Committed customer”: A customer subscribed to the annual plan.


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
    },
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/x/all"
    },
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${workspaceFolder}"
      ]
     }
  }
}
```

