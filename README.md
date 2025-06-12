# MCP Man Pages Server

A Model Context Protocol (MCP) server that provides access to Linux man pages using FastMCP. This server allows AI assistants to search, retrieve, and explore system documentation directly from your local machine.

## Features

- **Search man pages**: Find documentation by keyword or command name using `apropos`
- **Retrieve specific pages**: Get complete man page content by name and optional section
- **List sections**: Browse available man page sections (1-9) with descriptions
- **Clean formatting**: Man page content is cleaned and formatted for AI consumption
- **Async operations**: All operations are asynchronous with timeout protection
- **MCP Resources**: Expose man pages as resources with `man://` URIs

## Installation

### Using uv (recommended)

```bash
# Clone the repository
git clone https://github.com/guyru/man-mcp-server.git
cd man-mcp-server

# Install dependencies
uv sync

# Install with development tools
uv sync --extra dev
```

### Using pip

```bash
# Clone the repository
git clone https://github.com/guyru/man-mcp-server.git
cd man-mcp-server

# Install the package and dependencies from pyproject.toml
pip install .

# Or install in development mode (editable install)
pip install -e .

# For development with optional dependencies
pip install -e .[dev]
```

## Usage

### Running the Server

```bash
# Using uv
uv run python3 man_server.py

# Using python directly
python3 man_server.py

# With MCP development tools
uv run mcp dev man_server.py
```

### VS Code Integration

To integrate this MCP server with VS Code, you need to create a configuration file:

1. **Create the VS Code MCP configuration directory** (if it doesn't exist):
   ```bash
   mkdir -p .vscode
   ```

2. **Create `.vscode/mcp.json`** with the following content:
   ```json
   {
     "servers": {
       "man-mcp-server": {
         "type": "stdio",
         "command": "uv",
         "args": ["run", "--directory", "/path/to/man-mcp-server", "python3", "man_server.py"]
       }
     }
   }
   ```

3. **Update the path** in the configuration above to match your actual project directory.

4. **Alternative configuration** if you're not using uv:
   ```json
   {
     "servers": {
       "man-mcp-server": {
         "type": "stdio",
         "command": "python3",
         "args": ["/path/to/man-mcp-server/man_server.py"]
       }
     }
   }
   ```

This configuration allows MCP-compatible VS Code extensions to communicate with your man pages server.

## Available Tools

### 1. search_man_pages
Search for man pages by keyword or topic:
```python
search_man_pages("permission")  # Find pages about permissions
search_man_pages("network")     # Find networking-related pages
```

### 2. get_man_page
Retrieve the full content of a specific man page:
```python
get_man_page("ls")           # Get ls man page (any section)
get_man_page("chmod", "1")   # Get chmod from section 1 specifically
get_man_page("printf", "3")  # Get printf from section 3 (C library)
```

### 3. list_man_sections
List all available man page sections with descriptions:
```python
list_man_sections()  # Shows sections 1-9 with descriptions
```

## Available Resources

The server exposes man pages as resources using `man://` URIs:

- `man://sections` - List of all available sections
- `man://search/{keyword}` - Search results for a keyword
- `man://{section}/{page}` - Specific man page content

Examples:
- `man://search/network` - Search results for "network"
- `man://1/ls` - The ls command man page from section 1
- `man://3/printf` - The printf function man page from section 3

## Requirements

- **Operating System**: Linux with standard man page system
- **Python**: 3.10 or higher
- **Commands**: `man`, `apropos` (usually pre-installed)
- **Dependencies**: `mcp` library

## Development

### Testing the Server

```bash
# Test search functionality
uv run python3 -c "
from man_server import man_service
import asyncio
print(asyncio.run(man_service.search_man_pages('ls')))
"

# Test page retrieval
uv run python3 -c "
from man_server import man_service
import asyncio
content = asyncio.run(man_service.get_man_page('ls', '1'))
print(content[:200] + '...')
"
```

### Using with MCP Inspector

```bash
# Run with MCP development tools for debugging
uv run mcp dev man_server.py
```

## Error Handling

The server includes comprehensive error handling:

- **Missing pages**: Graceful handling with informative error messages
- **Timeout protection**: Subprocess calls are protected with configurable timeouts
- **Fallback methods**: If `apropos` fails, falls back to `man -k`
- **Content validation**: Ensures retrieved content is not empty
- **Clean formatting**: Removes ANSI codes and formatting for AI consumption

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
