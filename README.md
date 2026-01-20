# opencode-claude-max-proxy

Use your Claude Max subscription ($200/month) with OpenCode.

A proxy server that translates Anthropic API requests to Claude Agent SDK calls, letting OpenCode use your Claude Max subscription instead of per-token API billing.

## Prerequisites

1. **Claude CLI** installed and authenticated:
   ```bash
   npm install -g @anthropic-ai/claude-code
   claude login
   ```

2. **OpenCode** installed

## Installation

```bash
git clone https://github.com/rynfar/opencode-claude-max-proxy
cd opencode-claude-max-proxy
bun install
```

## Usage

### Start the Proxy

```bash
bun run proxy
```

Starts on `http://127.0.0.1:3456`.

### Run OpenCode

```bash
ANTHROPIC_API_KEY=dummy ANTHROPIC_BASE_URL=http://127.0.0.1:3456 opencode
```

Select any `anthropic/claude-*` model.

### One-liner

```bash
bun run proxy &
ANTHROPIC_API_KEY=dummy ANTHROPIC_BASE_URL=http://127.0.0.1:3456 opencode
```

## Auto-start on macOS

Create a launchd service:

```bash
cat > ~/Library/LaunchAgents/com.claude-max-proxy.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.claude-max-proxy</string>
    <key>ProgramArguments</key>
    <array>
        <string>/path/to/bun</string>
        <string>run</string>
        <string>proxy</string>
    </array>
    <key>WorkingDirectory</key>
    <string>/path/to/opencode-claude-max-proxy</string>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
</dict>
</plist>
EOF

launchctl load ~/Library/LaunchAgents/com.claude-max-proxy.plist
```

Then add to `~/.zshrc`:

```bash
alias oc='ANTHROPIC_API_KEY=dummy ANTHROPIC_BASE_URL=http://127.0.0.1:3456 opencode'
```

## Model Mapping

| OpenCode Model | Claude SDK |
|----------------|------------|
| `anthropic/claude-opus-*` | opus |
| `anthropic/claude-sonnet-*` | sonnet |
| `anthropic/claude-haiku-*` | haiku |

## Configuration

```bash
CLAUDE_PROXY_PORT=8080 bun run proxy
CLAUDE_PROXY_HOST=0.0.0.0 bun run proxy
```

## How It Works

```
OpenCode ──► Proxy (localhost:3456) ──► Claude Agent SDK ──► Claude Max
         POST /messages              query()
```

The proxy receives Anthropic API requests, calls the Claude Agent SDK, and streams back Anthropic SSE format responses.

## License

MIT
