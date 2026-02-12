# What is OpenClaw?

OpenClaw is a **self-hosted, multi-channel AI gateway** that connects your favorite messaging apps to AI coding agents. Think of it as your personal AI assistant that you can message from anywhere—WhatsApp, Telegram, Discord, iMessage, and more—all routed through a single Gateway process running on your own hardware.

## The Problem It Solves

You want an AI assistant that:
- **Works where you already chat** (WhatsApp, Telegram, Discord, etc.)
- **Stays private** (runs on your hardware, not someone else's cloud)
- **Never stops** (always-on gateway, not browser-tab dependent)
- **Has memory** (persistent sessions across conversations)
- **Can act** (execute code, use tools, access files)

## How It Works

```
┌─────────────────────┐
│   Your Messages     │
│  (WhatsApp, etc.)   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│   OpenClaw Gateway  │
│  (your computer)    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│    AI Agent (Pi)    │
│  (Anthropic Claude) │
└─────────────────────┘
```

1. **You send a message** via WhatsApp, Telegram, Discord, or any supported channel
2. **Gateway receives it** and routes to the appropriate AI agent (Pi)
3. **Agent processes** using LLMs (Claude, GPT-4, etc.) with tools and memory
4. **Gateway delivers response** back to your original chat app

## Core Components

### Gateway
The central hub that:
- Connects to multiple messaging platforms simultaneously
- Routes messages to appropriate agents
- Manages sessions, memory, and conversation state
- Runs as a daemon/service on your machine

### Channels
Built-in integrations for:
- **WhatsApp** (via Baileys protocol)
- **Telegram** (via Bot API)
- **Discord** (via Bot API)
- **Slack** (via Bolt framework)
- **Signal** (via signal-cli)
- **iMessage** (macOS only)
- **WebChat** (browser control UI)
- Plus extensions: Microsoft Teams, Matrix, Zalo, BlueBubbles

### Agent (Pi)
The AI brain powered by:
- **Anthropic Claude** (recommended: Opus 4.6)
- **OpenAI GPT-4** (alternative)
- Other model providers (Bedrock, local models via Ollama)

Features:
- Multi-turn conversations with memory
- Tool use (bash, file operations, web search)
- Code execution and debugging
- Canvas rendering (live UI updates)
- Voice transcription and TTS

### CLI
Command-line interface for:
- Gateway management (`openclaw gateway`)
- Channel pairing (`openclaw channels login`)
- Sending messages (`openclaw message send`)
- Direct agent interaction (`openclaw agent`)
- Configuration (`openclaw config`)

## Key Features

### Multi-Channel Support
Connect **one Gateway** to **all your messaging apps**. No need to run separate bots or services.

### Session Management
Each conversation maintains:
- **Context** (previous messages, files shared)
- **Memory** (learned preferences, facts)
- **Tool state** (working directory, environment)

### Security & Privacy
- **Self-hosted**: your data stays on your hardware
- **Allowlists**: control who can message the bot
- **Token-based**: secure channel authentication
- **No telemetry**: fully offline operation (except LLM API calls)

### Extension System
- **Plugin SDK** for custom channels and capabilities
- **Hooks** for message processing, routing, and events
- **Skills** for specialized agent behaviors

### Media Support
Send and receive:
- Images (JPEG, PNG, WebP)
- Audio (voice messages, transcription)
- Documents (PDF, text files)
- Videos (mp4, processing via ffmpeg)

### Platform Support
Runs on:
- **Linux** (recommended for servers)
- **macOS** (with native menubar app)
- **Windows** (via WSL2)
- **Docker** (containers for testing)
- **iOS/Android** (mobile nodes with pairing)

## Quick Start

```bash
# Install globally
npm install -g openclaw@latest

# Run onboarding wizard (guides you through setup)
openclaw onboard --install-daemon

# Start the Gateway
openclaw gateway --port 18789

# Pair WhatsApp (or Telegram, Discord, etc.)
openclaw channels login
```

Then send a message to your bot from WhatsApp, and it will respond!

## Use Cases

### Development Assistant
Ask coding questions, debug errors, generate code snippets:
```
You: "Write a Python script to parse JSON logs"
Agent: [generates script with error handling and examples]
```

### Task Automation
Run commands, check server status, monitor logs:
```
You: "Check disk space on my VPS"
Agent: [executes df -h via bash tool, reports results]
```

### Research Helper
Search the web, summarize articles, compare information:
```
You: "What's new in TypeScript 5.5?"
Agent: [searches, reads docs, summarizes key features]
```

### Personal Assistant
Set reminders, answer questions, maintain context across days:
```
You: "Remind me about our discussion on Docker networking"
Agent: [retrieves previous conversation, provides context]
```

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    OpenClaw Gateway                      │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐ │
│  │   Channels   │  │   Routing    │  │   Sessions   │ │
│  │ WhatsApp,etc │  │ Multi-agent  │  │   Memory     │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘ │
│         │                  │                  │          │
│         └──────────────────┴──────────────────┘          │
│                            │                              │
│                            ▼                              │
│                  ┌─────────────────┐                     │
│                  │   Pi Agent      │                     │
│                  │  (via RPC/IPC)  │                     │
│                  └─────────────────┘                     │
└─────────────────────────────────────────────────────────┘
                            │
                            ▼
                  ┌─────────────────┐
                  │   LLM Providers │
                  │ Claude/OpenAI   │
                  └─────────────────┘
```

## Configuration

Configuration lives at `~/.openclaw/openclaw.json`:

```json5
{
  // Gateway settings
  gateway: {
    mode: "local",        // or "remote"
    port: 18789,
    bind: "loopback"      // or "0.0.0.0" for network access
  },
  
  // Channel-specific settings
  channels: {
    whatsapp: {
      allowFrom: ["+1234567890"],  // who can message you
      groups: {
        "*": { requireMention: true }  // require @mention in groups
      }
    },
    telegram: {
      enabled: true,
      allowFrom: ["username"]
    }
  },
  
  // Model configuration
  models: {
    default: "anthropic/claude-opus-4",
    providers: {
      anthropic: { enabled: true },
      openai: { enabled: false }
    }
  }
}
```

## Why Self-Host?

### Privacy
Your conversations, files, and context stay on **your hardware**. Only API calls to LLM providers leave your network.

### Control
You decide:
- Which channels to enable
- Who can message the bot
- What tools the agent can use
- Where data is stored

### Customization
Extend OpenClaw with:
- Custom plugins
- Additional channels
- Specialized agent behaviors
- Integration with your own services

### Cost
No monthly subscription. Pay only for:
- LLM API usage (per-token with Anthropic/OpenAI)
- Optional: VPS hosting if running remotely

## Common Patterns

### Desktop Gateway
Run Gateway on your laptop/desktop:
- Quick setup, no server needed
- Local-only or expose via Tailscale
- Perfect for development and testing

### VPS Gateway
Deploy to a cloud VPS:
- Always-on, accessible from anywhere
- Exposed via SSH tunnel or Tailscale
- Recommended for production use

### Docker Deployment
Run in containers:
- Isolated environment
- Easy updates via image tags
- Good for testing and CI

### Hybrid Setup
Gateway on VPS + mobile nodes:
- Gateway handles messaging
- Mobile nodes provide Canvas rendering
- Best of both worlds

## Technical Details

### Stack
- **Runtime**: Node.js 22+ (ESM)
- **Language**: TypeScript (strict mode)
- **Package Manager**: pnpm (with bun support)
- **CLI Framework**: Commander + clack/prompts
- **Build Tool**: tsdown
- **Test Framework**: Vitest with V8 coverage

### Dependencies
Key libraries:
- `@whiskeysockets/baileys` - WhatsApp protocol
- `grammy` - Telegram Bot API
- `@slack/bolt` - Slack integration
- `discord-api-types` - Discord interactions
- `@mariozechner/pi-*` - AI agent runtime
- `sharp` - image processing
- `playwright-core` - browser automation

### Project Structure
```
openclaw/
├── src/              # Core TypeScript source
│   ├── cli/          # CLI commands and options
│   ├── commands/     # Command implementations
│   ├── gateway/      # Gateway server
│   ├── channels/     # Channel integrations
│   ├── agents/       # Agent runtime
│   ├── routing/      # Message routing
│   └── infra/        # Shared utilities
├── docs/             # Documentation (Mintlify)
├── apps/             # Platform apps (macOS, iOS, Android)
├── extensions/       # Plugin channels
├── skills/           # Agent skill definitions
├── scripts/          # Build and dev scripts
└── test/             # Integration tests
```

## Resources

- **Docs**: https://docs.openclaw.ai
- **Website**: https://openclaw.ai
- **GitHub**: https://github.com/openclaw/openclaw
- **Discord**: https://discord.gg/clawd
- **Getting Started**: https://docs.openclaw.ai/start/getting-started
- **FAQ**: https://docs.openclaw.ai/start/faq

## Contributing

OpenClaw is MIT licensed and welcomes contributions:
- Bug reports and feature requests via GitHub Issues
- Pull requests for fixes and enhancements
- Plugin development via the Plugin SDK
- Documentation improvements

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Summary

OpenClaw transforms your messaging apps into interfaces for a powerful AI assistant. It's:
- ✅ **Self-hosted** (your hardware, your rules)
- ✅ **Multi-channel** (one gateway, all your apps)
- ✅ **Agent-native** (memory, tools, sessions)
- ✅ **Open source** (MIT license, community-driven)
- ✅ **Production-ready** (stable releases, active development)

Install it, pair your channels, and start chatting with your personal AI assistant!
