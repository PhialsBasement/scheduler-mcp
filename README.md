[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/phialsbasement-scheduler-mcp-badge.png)](https://mseep.ai/app/phialsbasement-scheduler-mcp)

# MCP Scheduler

A robust task scheduler server built with Model Context Protocol (MCP) for scheduling and managing various types of automated tasks.

![License](https://img.shields.io/badge/license-MIT-blue.svg)

## Overview

MCP Scheduler is a versatile task automation system that allows you to schedule and run different types of tasks:

- **Shell Commands**: Execute system commands on a schedule
- **API Calls**: Make HTTP requests to external services
- **AI Tasks**: Generate content through OpenAI models
- **Reminders**: Display desktop notifications with sound

The scheduler uses cron expressions for flexible timing and provides a complete history of task executions. It's built on the Model Context Protocol (MCP), making it easy to integrate with AI assistants and other MCP-compatible clients.

## Features

- **Multiple Task Types**: Support for shell commands, API calls, AI content generation, and desktop notifications
- **Cron Scheduling**: Familiar cron syntax for precise scheduling control
- **Run Once or Recurring**: Option to run tasks just once or repeatedly on schedule
- **Execution History**: Track successful and failed task executions
- **Cross-Platform**: Works on Windows, macOS, and Linux
- **Interactive Notifications**: Desktop alerts with sound for reminder tasks
- **MCP Integration**: Seamless connection with AI assistants and tools
- **Robust Error Handling**: Comprehensive logging and error recovery

## Installation

### Prerequisites

- Python 3.10 or higher
- [uv](https://astral.sh/uv) (recommended package manager)

### Installing uv (recommended)

```bash
# For Mac/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# For Windows (PowerShell)
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

After installing uv, restart your terminal to ensure the command is available.

### Project Setup

```bash
# Clone the repository
git clone https://github.com/yourusername/mcp-scheduler.git
cd mcp-scheduler

# Create and activate a virtual environment with uv
uv venv
source .venv/bin/activate  # On Unix/MacOS
# or
.venv\Scripts\activate     # On Windows

# Install dependencies with uv
uv pip install -r requirements.txt
```

### Standard pip installation (alternative)

If you prefer using standard pip:

```bash
# Clone the repository
git clone https://github.com/yourusername/mcp-scheduler.git
cd mcp-scheduler

# Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate  # On Unix/MacOS
# or
.venv\Scripts\activate     # On Windows

# Install dependencies
pip install -r requirements.txt
```

## Usage

### Running the Server

```bash
# Run with default settings (stdio transport)
python main.py

# Run with server transport on specific port
python main.py --transport sse --port 8080

# Run with debug mode for detailed logging
python main.py --debug
```

### Integrating with Claude Desktop or other MCP Clients

To use your MCP Scheduler with Claude Desktop:

1. Make sure you have Claude Desktop installed
2. Open your Claude Desktop App configuration at:
   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%\Claude\claude_desktop_config.json`
3. Create the file if it doesn't exist, and add your server:

```json
{
  "mcpServers": [
    {
      "type": "stdio",
      "name": "MCP Scheduler",
      "command": "python",
      "args": ["/path/to/your/mcp-scheduler/main.py"]
    }
  ]
}
```

Alternatively, use the `fastmcp` utility if you're using the FastMCP library:

```bash
# Install your server in Claude Desktop
fastmcp install main.py --name "Task Scheduler"
```

### Command Line Options

```
--address        Server address (default: localhost)
--port           Server port (default: 8080)
--transport      Transport mode (sse or stdio) (default: stdio)
--log-level      Logging level (default: INFO)
--log-file       Log file path (default: mcp_scheduler.log)
--db-path        SQLite database path (default: scheduler.db)
--config         Path to JSON configuration file
--ai-model       AI model to use for AI tasks (default: gpt-4o)
--version        Show version and exit
--debug          Enable debug mode with full traceback
--fix-json       Enable JSON fixing for malformed messages
```

### Configuration File

You can use a JSON configuration file instead of command-line arguments:

```json
{
  "server": {
    "name": "mcp-scheduler",
    "version": "0.1.0",
    "address": "localhost",
    "port": 8080,
    "transport": "sse"
  },
  "database": {
    "path": "scheduler.db"
  },
  "logging": {
    "level": "INFO",
    "file": "mcp_scheduler.log"
  },
  "scheduler": {
    "check_interval": 5,
    "execution_timeout": 300
  },
  "ai": {
    "model": "gpt-4o",
    "openai_api_key": "your-api-key"
  }
}
```

## MCP Tool Functions

The MCP Scheduler provides the following tools:

### Task Management

- `list_tasks`: Get all scheduled tasks
- `get_task`: Get details of a specific task
- `add_command_task`: Add a new shell command task
- `add_api_task`: Add a new API call task
- `add_ai_task`: Add a new AI task
- `add_reminder_task`: Add a new reminder task with desktop notification
- `update_task`: Update an existing task
- `remove_task`: Delete a task
- `enable_task`: Enable a disabled task
- `disable_task`: Disable an active task
- `run_task_now`: Run a task immediately

### Execution and Monitoring

- `get_task_executions`: Get execution history for a task
- `get_server_info`: Get server information

## Cron Expression Guide

MCP Scheduler uses standard cron expressions for scheduling. Here are some examples:

- `0 0 * * *` - Daily at midnight
- `0 */2 * * *` - Every 2 hours
- `0 9-17 * * 1-5` - Every hour from 9 AM to 5 PM, Monday to Friday
- `0 0 1 * *` - At midnight on the first day of each month
- `0 0 * * 0` - At midnight every Sunday

## Environment Variables

The scheduler can be configured using environment variables:

- `MCP_SCHEDULER_NAME`: Server name (default: mcp-scheduler)
- `MCP_SCHEDULER_VERSION`: Server version (default: 0.1.0)
- `MCP_SCHEDULER_ADDRESS`: Server address (default: localhost)
- `MCP_SCHEDULER_PORT`: Server port (default: 8080)
- `MCP_SCHEDULER_TRANSPORT`: Transport mode (default: stdio)
- `MCP_SCHEDULER_LOG_LEVEL`: Logging level (default: INFO)
- `MCP_SCHEDULER_LOG_FILE`: Log file path
- `MCP_SCHEDULER_DB_PATH`: Database path (default: scheduler.db)
- `MCP_SCHEDULER_CHECK_INTERVAL`: How often to check for tasks (default: 5 seconds)
- `MCP_SCHEDULER_EXECUTION_TIMEOUT`: Task execution timeout (default: 300 seconds)
- `MCP_SCHEDULER_AI_MODEL`: OpenAI model for AI tasks (default: gpt-4o)
- `OPENAI_API_KEY`: API key for OpenAI tasks

## Examples

### Adding a Shell Command Task

```python
await scheduler.add_command_task(
    name="Backup Database",
    schedule="0 0 * * *",  # Midnight every day
    command="pg_dump -U postgres mydb > /backups/mydb_$(date +%Y%m%d).sql",
    description="Daily database backup",
    do_only_once=False  # Recurring task
)
```

### Adding an API Task

```python
await scheduler.add_api_task(
    name="Fetch Weather Data",
    schedule="0 */6 * * *",  # Every 6 hours
    api_url="https://api.weather.gov/stations/KJFK/observations/latest",
    api_method="GET",
    description="Get latest weather observations",
    do_only_once=False
)
```

### Adding an AI Task

```python
await scheduler.add_ai_task(
    name="Generate Weekly Report",
    schedule="0 9 * * 1",  # 9 AM every Monday
    prompt="Generate a summary of the previous week's sales data.",
    description="Weekly sales report generation",
    do_only_once=False
)
```

### Adding a Reminder Task

```python
await scheduler.add_reminder_task(
    name="Team Meeting",
    schedule="30 9 * * 2,4",  # 9:30 AM every Tuesday and Thursday
    message="Don't forget the team standup meeting!",
    title="Meeting Reminder",
    do_only_once=False
)
```

## Development

If you want to contribute or develop the MCP Scheduler further, here are some additional commands:

```bash
# Install the MCP SDK for development
uv pip install "mcp[cli]>=1.4.0"

# Or for FastMCP (alternative implementation)
uv pip install fastmcp

# Testing your MCP server
# With the MCP Inspector tool
mcp inspect --stdio -- python main.py

# Or with a simple MCP client
python -m mcp.client.stdio python main.py
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Built on the [Model Context Protocol](https://modelcontextprotocol.io/)
- Uses [croniter](https://github.com/kiorky/croniter) for cron parsing
- Uses [OpenAI API](https://openai.com/blog/openai-api) for AI tasks
- Uses [FastMCP](https://github.com/jlowin/fastmcp) for enhanced MCP functionality
