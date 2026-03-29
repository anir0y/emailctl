# emailctl

Disposable email addresses from your terminal. Generate, receive, and send — all from the command line.

`emailctl` creates temporary email addresses on `pkrnilayam.live` that you can use for signups, testing, or privacy. Emails arrive instantly and can be read right from the CLI. AI-friendly with structured JSON output and an MCP server for LLM agents.

## Features

- **Generate** up to 9 unique disposable addresses per account
- **Read inbox** for any generated address in real-time
- **Send emails** from your disposable addresses
- **Google login** — one-time OAuth, no passwords to manage
- **JSON output** — pipe-friendly `--json` flag on every command
- **MCP server** — let AI agents manage email directly
- **Cross-platform** — macOS (ARM + Intel), Linux x64, Windows x64

## Quick Start

### 1. Download

Grab the latest release for your platform from the [Releases](https://github.com/anir0y/emailctl/releases) page:

| Platform | File |
|----------|------|
| macOS Apple Silicon (M1/M2/M3) | `emailctl-macos-arm64.tar.gz` |
| macOS Intel | `emailctl-macos-x64.tar.gz` |
| Linux x64 | `emailctl-linux-x64.tar.gz` |
| Windows x64 | `emailctl-win-x64.zip` |

### 2. Install

**macOS / Linux:**
```bash
# Extract
tar -xzf emailctl-<platform>.tar.gz
cd emailctl-<platform>

# One-time setup (builds native module if needed)
chmod +x emailctl setup.sh
./setup.sh

# Optionally add to PATH
sudo ln -s "$(pwd)/emailctl" /usr/local/bin/emailctl
```

**Windows:**
```powershell
# Extract the zip
# Run setup (one-time)
setup.bat

# Use from the extracted directory
emailctl.cmd --help
```

### 3. Login

```bash
emailctl login
```

The login URL is always printed to the terminal, so it works everywhere:

- **Desktop** — browser opens automatically
- **SSH / VM / headless** — copy the URL from the terminal, open it in any browser on your local machine, and complete the Google sign-in. The callback hits `localhost:9876` which resolves back to the CLI.

You only need to do this once.

### 4. Use

```bash
# Generate a disposable address
emailctl generate

# Generate with a custom prefix
emailctl generate --prefix myalias

# List your addresses
emailctl list

# Check inbox
emailctl inbox <address>

# Check inbox (last 5 messages, full body)
emailctl inbox <address> --limit 5 --full

# Send an email
emailctl send --from <your-address> --to someone@example.com --subject "Hello" --body "Hi there"

# Delete an address
emailctl delete <address>

# Check account status
emailctl status

# Logout
emailctl logout
```

## Requirements

- **Node.js v20+** — [Download](https://nodejs.org)
- A Google account (for authentication)

## JSON Output

Every command supports `--json` for structured output:

```bash
emailctl generate --json
# {"ok":true,"data":{"address":"a7kx9m@pkrnilayam.live","created_at":"2025-01-15T..."}}

emailctl inbox user@pkrnilayam.live --json
# {"ok":true,"data":{"address":"user@pkrnilayam.live","messages":[...],"total":3}}

emailctl list --json
# {"ok":true,"data":{"addresses":[...],"total":5}}
```

## MCP Server (for AI Agents)

`emailctl` includes an MCP server so AI assistants (Claude, etc.) can manage disposable email directly.

### Claude Code Setup

Add to your Claude Code MCP config:

```json
{
  "mcpServers": {
    "emailctl": {
      "command": "node",
      "args": ["/path/to/emailctl-<platform>/emailctl"]
    }
  }
}
```

### Available MCP Tools

| Tool | Description |
|------|-------------|
| `generate_address` | Create a new disposable email address |
| `create_address` | Create address with a specific prefix |
| `list_addresses` | List all active addresses |
| `read_inbox` | Read emails for an address |
| `send_email` | Send an email from a generated address |
| `delete_address` | Delete an address |

## How It Works

1. **You authenticate** with Google OAuth (one-time)
2. **Addresses are generated** on `pkrnilayam.live` domain
3. **All emails** to `*@pkrnilayam.live` land in a catch-all mailbox
4. **emailctl filters** messages by the `X-Original-To` header to show only yours
5. **Sending** uses authenticated SMTP through the mail server
6. **Your data** stays local in a SQLite database at `~/.emailctl/`

## Troubleshooting

### "Native module not found" error
Run the setup script again:
```bash
./setup.sh  # macOS/Linux
setup.bat   # Windows
```

### Login times out
- Make sure port 9876 is free (used for OAuth callback)
- Check that your browser can open `http://localhost:9876/callback`

### "Not authenticated" error
```bash
emailctl login   # Re-authenticate
emailctl status   # Verify login
```

## Privacy

- **No tracking** — emailctl doesn't phone home (except for login and email operations)
- **Local storage** — your address registry lives in `~/.emailctl/emailctl.db`
- **Google OAuth** — only requests email and profile scopes (no mail access to your Gmail)
- **Disposable by design** — delete addresses when you're done

## License

Proprietary. All rights reserved.

---

Built by [@anir0y](https://github.com/anir0y)
