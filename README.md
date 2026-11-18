# AFK Selfbot v2.0

Discord AFK self-bot — stays in a voice channel with auto-reconnect, custom Rich Presence (GTA VI), and full logging.

> ⚠️ Self-bots violate Discord ToS. Accounts that use them may be banned. Use at your own risk.

> Not affiliated with Discord or Rockstar Games. "Grand Theft Auto" and "GTA VI" are trademarks of Take-Two Interactive / Rockstar Games. The GTA VI game ID referenced here is used only to render a game Rich Presence.

## Features

- **Auto-join Voice Channel** — joins a VC on startup, with auto-reconnect if disconnected.
- **Custom Rich Presence** — shows "Playing Grand Theft Auto VI" on your Discord profile with a transparent logo (clicking the cover art opens the GTA VI game page inside Discord).
- **Click-through to the Discord game page** — the RPC uses the official GTA VI application ID from Discord's games DB (`1445160676522856458`), so clicking the cover art opens the in-Discord game profile, not an external website.
- **Logging** — structured logs to console and a daily file.
- **Graceful Shutdown** — cleanup on SIGINT/SIGTERM.

## Setup

### 1. Install dependencies

```bash
npm install
```

### 2. Configuration

Copy `.env.example` and fill in your data:

```bash
cp .env.example .env
```

Edit `.env`:

```env
DISCORD_TOKEN=your_user_token_here

# Optional
VOICE_CHANNEL_ID=123456789012345678
LOG_LEVEL=INFO
```

See `.env.example` for every available option (RPC app ID, logo, reconnect delay, self-mute/deaf, ...).

### 3. Run

```bash
npm start
```

Or with PM2:

```bash
pm2 start index.js --name afkbot
pm2 save && pm2 startup
```

## Rich Presence (GTA VI)

The bot displays a Rich Presence for "Playing Grand Theft Auto VI". By default the RPC uses **the official GTA VI application ID from Discord's games DB** (`1445160676522856458`), so when someone **clicks the activity cover art on your profile, it opens the Grand Theft Auto VI game page inside Discord** — not an external website.

**Default logo**: transparent GTA VI logo with padding (local files: `assets/gta6_padded88.png`), hosted on catbox because Discord only renders `mp:external/<hash>/...` images (Discord CDN attachment URLs can't be converted). It is converted automatically at startup via `RichPresence.getExternal`. If you'd rather use the cover art, set:

```env
ACTIVITY_LARGE_IMAGE=https://cdn.discordapp.com/app-icons/1445160676522856458/0788ab1237f5801d9f72af6d34665327.png
```

To use assets from your own Developer Portal application instead (e.g. named `gta6`):

1. Create/pick an application in the [Discord Developer Portal](https://discord.com/developers/applications), upload the logo to Art Assets, and name it `gta6`.
2. Set in `.env`:
   ```env
   RPC_APP_ID=<your app id>
   ACTIVITY_LARGE_IMAGE=gta6
   ```
   Note: with a custom `RPC_APP_ID`, clicking the cover art will **not** open the game page — click-through only works when `RPC_APP_ID` is the GTA VI game ID.

Any other public URL also works (`ACTIVITY_LARGE_IMAGE=https://...`); it gets converted to `mp:external/...` automatically. Discord attachment links generally do not work for this, and signed attachment URLs expire.

## Logging

Logs are written to the console and to `logs/bot-YYYY-MM-DD.log`.

**Log levels** (set via `LOG_LEVEL` in `.env`):
- `DEBUG` — everything, including low-level detail
- `INFO` — normal operation (join, reconnect, RPC resolution)
- `WARN` — things worth attention (disconnects)
- `ERROR` — errors that need handling

Example output:
```
2026-06-18 01:30:00 INFO  [BOOT]  AFK Selfbot starting...
2026-06-18 01:30:01 INFO  [READY] Login as: User#1234 (123456789)
2026-06-18 01:30:01 INFO  [RPC]   Large image resolved: "mp:external/.../files.catbox.moe/182dkk.png"
2026-06-18 01:30:01 INFO  [VOICE] Joined #general in "My Server" (987654321)
```

The bot also logs memory usage every 5 minutes (`[MEM] RSS ..., heap ..., guilds N`) to help spotting leaks on small VPS instances.

## Project Structure

```
├── index.js             # Entry point
├── assets/              # GTA VI logos (SVG master + transparent PNGs)
│   ├── gta6_gradient.svg   # Original SVG from Wikimedia Commons
│   ├── gta6_logo.png       # Transparent logo 1024×1024
│   ├── gta6_max.png        # Transparent logo, full scale
│   ├── gta6_padded.png     # Logo with 78% padding (RPC square slot)
│   └── gta6_padded88.png   # Logo with 88% padding (current RPC default)
├── src/
│   ├── config.js        # Configuration & validation
│   ├── logger.js        # Logging system
│   └── voice.js         # Voice channel management & auto-reconnect
├── logs/                # Log files (auto-generated, gitignored)
├── .env                 # Configuration (do not commit!)
├── .env.example         # Configuration template
├── LICENSE              # MIT
└── package.json
```

## License

MIT — see [LICENSE](./LICENSE).
