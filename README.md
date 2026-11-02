# Universal Railway MCP Server

A production-ready Python MCP (Model Context Protocol) server designed for deployment on Railway.

This server provides tools, resources, and prompts while **enforcing** that connected AI clients use the system prompt supplied by the server.

## Features

- Streamable HTTP transport (ideal for Railway)
- Configurable system prompt via `SYSTEM_PROMPT` environment variable
- Enforced system prompt exposed as both a prompt and a resource
- Example tools that demonstrate compliance checking
- Health endpoint at `/health`
- Ready for Railway one-click deploy

## System Prompt Enforcement

The server makes the system prompt available via:

1. **Prompt**: `enforced_system` — clients should use this as the active system prompt
2. **Resource**: `system://prompt` — readable by the client
3. **Tool descriptions**: every tool includes language instructing the model to follow the server-provided system prompt
4. **Compliance tool**: `verify_prompt_compliance` returns whether the model has acknowledged the prompt

Clients (Claude Desktop, Cursor, etc.) should set the system prompt to the content returned by the `enforced_system` prompt or the `system://prompt` resource.

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | HTTP port (set automatically by Railway) | `8000` |
| `SYSTEM_PROMPT` | The system prompt the AI must follow | Built-in default |
| `SERVER_NAME` | Name advertised by the MCP server | `Universal MCP Server` |

## Local Development

```bash
pip install -r requirements.txt
export SYSTEM_PROMPT="You are a helpful assistant that always cites sources."
python server.py
```

Server listens on `http://0.0.0.0:8000/mcp`

## Deploy to Railway

1. Fork or use this repository
2. Create a new project on Railway → Deploy from GitHub
3. Select this repository
4. (Optional) Set `SYSTEM_PROMPT` in the service Variables
5. Generate a public domain under Settings → Networking

Your MCP endpoint will be:

```
https://<your-service>.up.railway.app/mcp
```

### Client Configuration Example

```json
{
  "mcpServers": {
    "universal-railway": {
      "type": "streamable-http",
      "url": "https://<your-service>.up.railway.app/mcp"
    }
  }
}
```

## Tools

- `echo` — echo a message
- `get_current_time` — returns current UTC time
- `verify_prompt_compliance` — checks acknowledgment of the system prompt
- `list_available_prompts` — lists prompts the server provides

## License

MIT
