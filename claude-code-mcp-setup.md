# AI SDK Assistant — Claude Code MCP Server Configuration

## Quick Setup (All Platforms)

Instead of using the CLI `claude mcp add` command (which has known issues with spaces in server names), paste the JSON directly into the appropriate config file.

> **Important:** The server name must not contain spaces. Use `CRSDK` consistently across all configs.

---

## Project Scope — `.mcp.json`

Create a `.mcp.json` file in your **project root**. This is the recommended approach for team collaboration.

### macOS / Linux

```json
{
  "mcpServers": {
    "CRSDK": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://sdk-rag-agent-production.up.railway.app/mcp"
      ]
    }
  }
}
```

### Windows

On Windows, `npx` must be wrapped with `cmd /c` to resolve correctly:

```json
{
  "mcpServers": {
    "CRSDK": {
      "type": "stdio",
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "mcp-remote",
        "https://sdk-rag-agent-production.up.railway.app/mcp"
      ]
    }
  }
}
```

Commit `.mcp.json` to version control to share with your team.

---

## User Scope — `~/.claude.json` (Global)

Available across all projects on your machine.

### Finding `~/.claude.json`

The `~` refers to your home directory. The full path is:

| OS | Path |
|----|------|
| **macOS** | `/Users/<your-username>/.claude.json` |
| **Linux** | `/home/<your-username>/.claude.json` |
| **Windows** | `C:\Users\<your-username>\.claude.json` |

This is a **hidden file** (starts with `.`). To see it:
- **macOS Finder:** Press `Cmd + Shift + .` to toggle hidden files
- **Windows Explorer:** View → Show → Hidden items
- **Terminal:** Open directly with `code ~/.claude.json` (VS Code) or `notepad %USERPROFILE%\.claude.json` (Windows)

If the file doesn't exist, create it and paste the JSON below. Claude Code will pick it up on next launch.

### macOS / Linux

Merge into your existing `~/.claude.json` (create it if it doesn't exist):

```json
{
  "mcpServers": {
    "CRSDK": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://sdk-rag-agent-production.up.railway.app/mcp"
      ]
    }
  }
}
```

### Windows

```json
{
  "mcpServers": {
    "CRSDK": {
      "type": "stdio",
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "mcp-remote",
        "https://sdk-rag-agent-production.up.railway.app/mcp"
      ]
    }
  }
}
```

---

## Local Scope — `.claude.local.json` (Private)

Private to the current user and project only. Place this in your project root.

### macOS / Linux

```json
{
  "mcpServers": {
    "CRSDK": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://sdk-rag-agent-production.up.railway.app/mcp"
      ]
    }
  }
}
```

### Windows

```json
{
  "mcpServers": {
    "CRSDK": {
      "type": "stdio",
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "mcp-remote",
        "https://sdk-rag-agent-production.up.railway.app/mcp"
      ]
    }
  }
}
```

---

## Scope Priority

When the same server is defined in multiple scopes: **Local > Project > User**

---

## Tool Permissions

To auto-approve all AI SDK Assistant tools, add this to your settings config:

```json
{
  "approvedTools": ["mcp__CRSDK"]
}
```

Or approve specific tools only:

```json
{
  "approvedTools": [
    "mcp__CRSDK__search_sdk",
    "mcp__CRSDK__search_code_examples",
    "mcp__CRSDK__search_documentation",
    "mcp__CRSDK__search_api_functions",
    "mcp__CRSDK__search_compatibility",
    "mcp__CRSDK__search_hybrid",
    "mcp__CRSDK__search_exact_api",
    "mcp__CRSDK__search_error_codes",
    "mcp__CRSDK__search_warning_codes",
    "mcp__CRSDK__search_by_source_file",
    "mcp__CRSDK__get_sdk_context",
    "mcp__CRSDK__get_current_sdk_version",
    "mcp__CRSDK__set_sdk_version",
    "mcp__CRSDK__set_sdk_language"
  ]
}
```

**Permission config locations:**
- **User scope:** `~/.claude.json`
- **Project scope:** `.claude/settings.json`
- **Local scope:** `.claude/settings.local.json`

---

## Verification

After saving the config, restart Claude Code or start a new session, then run:

```
claude mcp list
```

You should see:

```
CRSDK: npx mcp-remote https://sdk-rag-agent-production.up.railway.app/mcp - ✓ Connected
```

---

## CLI Alternative

If you prefer using the CLI instead of editing JSON files directly:

### macOS / Linux

```bash
claude mcp add CRSDK --scope user -- npx mcp-remote https://sdk-rag-agent-production.up.railway.app/mcp
```

### Windows (PowerShell or CMD)

```bash
claude mcp add CRSDK --scope user -- cmd /c npx mcp-remote https://sdk-rag-agent-production.up.railway.app/mcp
```

---

## Troubleshooting

| Issue | Solution |
|-------|---------|
| `npx` not found | Ensure Node.js (v18+) is installed and `npx` is on your PATH. Verify with `npx --version`. |
| Windows: `npx` not recognized | Use the `cmd /c` wrapper as shown in the Windows configs above. If still failing, try the full path: `C:\\Program Files\\nodejs\\npx.cmd` |
| Server not connecting | Ensure you're not behind a proxy/VPN blocking `railway.app`. |
| `approvedTools` not working | Verify the server name in your MCP config matches the prefix in `approvedTools` (both should use `CRSDK`). |
| `.mcp.json` not detected | File must be in the **project root** directory. Restart Claude Code after creating it. |
| macOS: permission prompt on first use | For project-scoped `.mcp.json`, Claude Code will prompt for approval on first use. Accept to continue. |
