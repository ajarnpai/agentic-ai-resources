# Connecting NotebookLM to Claude & AI Tools
## Technical Implementation & Configuration Guide
**Windows & macOS · Claude Desktop · Claude Code · Cursor · Cline**

---

## The Power of Grounded Intelligence

General AI models are powerful, but they often lack the context of your specific research, notes, or proprietary data. **NotebookLM** solves this by letting you create a personalized "Source of Truth." 

By connecting NotebookLM to your preferred AI tools via the **Model Context Protocol (MCP)**, you enable:
1. **Direct Research Access:** Your AI can browse and query your entire library of uploaded PDFs, text, and YouTube transcripts.
2. **Reduced Hallucinations:** Responses are grounded in your specific sources, with clickable citations.
3. **Multi-Model Harmony:** Use any model (Claude 3.5 Sonnet, GPT-4o) while keeping your data securely in the Google NotebookLM ecosystem.

---

## Prerequisites

- **Python 3.11+**: The core engine for the connector.
- **Node.js 18+**: Required for some MCP operations.
- **uv**: A high-performance Python package manager (recommended for speed and reliability).
- **Google Account**: For access to [notebooklm.google.com](https://notebooklm.google.com).

---

## Phase 1: Environment Setup

### 1. Install dependencies
Ensure you have the latest tools installed. If you are on Windows, ensure "Add Python to PATH" is checked during installation.

### 2. Install the `uv` Manager
`uv` is significantly faster than standard `pip` and handles virtual environments automatically.

| Platform | Command |
| :--- | :--- |
| **Windows** | `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 \| iex"` |
| **macOS** | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |

### 3. Install NotebookLM MCP
Open a new Terminal or PowerShell window and run:
```bash
uv tool install notebooklm-mcp-cli
```
*This installs the CLI and the MCP server bridge globally.*

---

## Phase 2: Authentication

You must authenticate with Google to give the CLI access to your notebooks. This creates a secure local profile.

```bash
nlm login
```
1. A browser window will open.
2. Sign in with your Google account.
3. Once successful, return to the terminal.

To verify your connection:
```bash
nlm login --check
```

---

## Phase 3: AI Tool Integration

### A. Claude Desktop (Core Setup)
Claude Desktop requires an explicit entry in its configuration file.

**1. Locate your Config File:**
- **Windows:** `%AppData%\Claude\claude_desktop_config.json`
- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`

**2. Add the MCP Server:**
Edit the file and ensure the `mcpServers` object includes the `notebooklm` entry:

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "notebooklm-mcp"
    }
  }
}
```

> **💡 Tip for Existing Configs:** If you already have other tools in your JSON, simply ask Claude:  
> *"Can you fix this JSON for me? I want to add NotebookLM to my mcpServers block."*  
> **>> Copy the JSON block above into Claude along with your existing JSON file content.**

### B. Cursor IDE
1. Open **Settings** > **Features** > **MCP**.
2. Click **+ Add New MCP Server**.
3. Name it `NotebookLM`.
4. Type: `command`.
5. Command: `notebooklm-mcp`.

### C. Claude Code (CLI)
Run the following command to add the server to Claude Code's config:
```bash
nlm setup add claude-code
```

### D. Cline / Roo Code / VS Code
Edit your `mcp_config.json` (usually found in your VS Code extension storage) and add:
```json
"notebooklm": { "command": "notebooklm-mcp" }
```
*Tip: You can use `nlm setup add cline` for automatic detection.*

---

## Phase 4: Verification & Usage

1. **Restart your AI tool** (Quit the app completely, then reopen).
2. **Test Query:** Type one of the following commands:
    - `"List all my notebooks."`
    - `"What is in my [Notebook Name]?"`
    - `"Summarize the research in my documents."`

---

## Troubleshooting

| Error | Cause | Resolution |
| :--- | :--- | :--- |
| `notebooklm-mcp not found` | Path issues or PATH not refreshed. | Restart terminal. Re-run `uv tool install`. |
| `Auth expired` | Google token has timed out. | Run `nlm login`. |
| `Missing sources` | NotebookLM API latency. | Wait 30s or check notebook at notebooklm.google.com. |
| `JSON Syntax Error` | Missing comma or bracket in config. | Use a JSON validator or check for trailing commas. |

---

## Advanced Options

- **Switch Profiles:** `nlm login switch <name>`
- **Deep Research:** `nlm research "your query"` (Starts a fast/deep search to find new sources).
- **Studio Tools:** `nlm studio --type audio` (Create audio overviews/podcasts).

*Kanis Saengchote · Chulalongkorn Business School*  
*Co-created with Gemini Antigravity*
