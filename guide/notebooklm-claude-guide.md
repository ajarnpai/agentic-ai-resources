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
- **uv**: A high-performance Python package manager (recommended for speed and reliability).
- **Google Account**: For access to [notebooklm.google.com](https://notebooklm.google.com).

---

## Phase 1: Environment Setup

### 1. Install dependencies
Ensure you have the latest tools installed. We use `uv` to handle everything.


**A. Install Python Engine**: 
Run this in your Terminal:
```bash
uv python install
```
**Important Prompt Answers:**
- Add command directory to your PATH now? **y**
- Install CPython note? **y**
- View online help? **n**

**B. Verify Python**: 
Run `python --version`. If it shows 3.11 or higher, you are good to go!



### 2. Install the `uv` Manager
`uv` is significantly faster than standard `pip` and handles virtual environments automatically.

| Platform | Command |
| :--- | :--- |
| **Windows** | `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 \| iex"` |
| **macOS** | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |

Run `uv --version`. You should see version details, and your system now has `uv`, `uvx`, and `uvw` ready!

**✨ Important:** Restart your Terminal or PowerShell session now for the changes to take effect.

### 3. Install NotebookLM MCP
**Ensure you have restarted your Terminal/PowerShell window**, then run:
```bash
uv tool install notebooklm-mcp-cli
```
**Verify Installation**:
You should see a message confirming: `Installed 2 executables: nlm, notebooklm-mcp`.
*This installs the CLI and the MCP server bridge globally.*

---

## Phase 2: Authentication

You must authenticate with Google to give the CLI access to your notebooks. This creates a secure local profile.

```bash
nlm login
```
1. A browser window will open.
2. Sign in with your Google account (and authenticate on your device if prompted).
3. Once successful, it will show: `Successfully authenticated! ... Account: name@gmail.com`.
4. Return to the terminal.

To verify your connection:
```bash
nlm login --check
```

---

### 3. Setup Claude Desktop (Required)

> ⚠️ **Windows Users:** Install Claude Desktop using the direct **.exe installer** from [claude.ai/download](https://claude.ai/download) — **NOT** from the Microsoft Store. The Store version uses sandboxing that prevents it from reading the config file.

#### Step A: Locate or Create your Config File
Navigate to the following directory in your file explorer:
- **Windows:** `%AppData%\Claude\claude_desktop_config.json`
- **Mac:** `~/Library/Application Support/Claude/claude_desktop_config.json`

#### Step B: Add the JSON Config
If the file is **new/empty**, paste this full block:
```json
{
  "mcpServers": {
    "notebooklm-mcp": {
      "command": "notebooklm-mcp"
    }
  }
}
```

> ⚠️ **Reinstall Warning:** After any reinstall of Claude Desktop on Windows, always re-check your config file. The installer can sometimes overwrite it with defaults, deleting your MCP entries.

#### Step C: Verify the Config File
In PowerShell, run:
```powershell
cat "$env:APPDATA\Claude\claude_desktop_config.json"
```
You should see `mcpServers` in the output. If not, the file was overwritten—re-add the JSON block.

#### Step D: Restart Claude Desktop
Fully quit the app (check your system tray/menu bar) and reopen it.

---

> 💡 **Tip for Existing Configs (Windows):** If you already have other entries, run this PowerShell command to safely merge the NotebookLM entry:
> ```powershell
> $config = Get-Content "$env:APPDATA\Claude\claude_desktop_config.json" | ConvertFrom-Json
> $config | Add-Member -Type NoteProperty -Name "mcpServers" -Value @{"notebooklm-mcp" = @{"command" = "notebooklm-mcp"}}
> $config | ConvertTo-Json -Depth 10 | Set-Content "$env:APPDATA\Claude\claude_desktop_config.json"
> ```

If you **already have an existing config**, simply add the following line inside your `mcpServers` object:

```json
"notebooklm": { "command": "notebooklm-mcp" }
```

> **💡 Tip for Existing Configs:** If you're unsure where to paste it, just ask Claude:  
> *"Can you fix this JSON for me? I want to add NotebookLM to my mcpServers block."*  
> **>> Copy the JSON block above into Claude along with your existing JSON file content.**

**3. Restart Claude Desktop:**
Fully quit the app and reopen it for the changes to load.

---

## Phase 4: Other IDEs & Connections (Optional)

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

---

## Phase 5: Troubleshooting

### Issue 1: MCP Not Working (Config looks correct)
Claude Desktop may have been installed from the Microsoft Store (sandboxed version).

**Check Command (PowerShell):**
```powershell
ls "$env:LOCALAPPDATA\Packages\" | Where-Object {$_.Name -like "*Claude*"}
```
If you see `Claude_pzs8sxrjxfjjc`, you have the Store version. Uninstall it via Settings → Apps, then reinstall using the direct .exe from [claude.ai/download](https://claude.ai/download).

### Issue 2: `notebooklm-mcp` Command Not Recognized
Run the following fix:
```powershell
uv tool install notebooklm-mcp-cli
```
Then close and reopen PowerShell and try again.

### Issue 3: Python Not Found / `uv` Not Working
Run verification commands:
```powershell
python --version
uv --version
```
If either fails, reinstall Python from python.org. **Critical:** check **"Add Python to PATH"** during installation.

### Issue 4: NotebookLM Returns Auth Errors
Your Google login session has expired. Re-authenticate:
```powershell
nlm login
```
Cookies expire every 2–4 weeks so this is expected periodically.

### Issue 5: Reinstaller Overlap Fix
If a Claude Desktop update overwrote your config, run this merge command:
```powershell
$config = Get-Content "$env:APPDATA\Claude\claude_desktop_config.json" | ConvertFrom-Json
$config | Add-Member -Type NoteProperty -Name "mcpServers" -Value @{"notebooklm-mcp" = @{"command" = "notebooklm-mcp"}}
$config | ConvertTo-Json -Depth 10 | Set-Content "$env:APPDATA\Claude\claude_desktop_config.json"
```
Then fully restart Claude Desktop.

*Kanis Saengchote · Chulalongkorn Business School*  
*Co-created with Gemini Antigravity*
