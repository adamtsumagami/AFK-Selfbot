# AFK Selfbot v2.0

Discord AFK selfbot — tetap di voice channel dengan auto-reconnect, custom Rich Presence (GTA VI), dan logging lengkap.

> ⚠️ Self-bot melanggar ToS Discord. Akun yang memakainya bisa di-ban. Gunakan dengan risiko sendiri.

> Not affiliated with Discord or Rockstar Games. "Grand Theft Auto" and "GTA VI" are trademarks of Take-Two Interactive / Rockstar Games. The GTA VI game ID referenced here is used only to render a game Rich Presence.

## Fitur

- **Auto-join Voice Channel** — otomatis masuk VC saat startup dengan auto-reconnect jika disconnect.
- **Custom Rich Presence** — menampilkan "Playing Grand Theft Auto VI" dengan logo di profil Discord (mendukung asset Developer Portal, Discord CDN URL, atau Snowflake ID).
- **Logging** — logging terstruktur ke console dan file harian.
- **Graceful Shutdown** — cleanup saat SIGINT/SIGTERM.

## Setup

### 1. Install dependencies

```bash
npm install
```

### 2. Konfigurasi

Copy `.env.example` dan isi dengan data kamu:

```bash
cp .env.example .env
```

Edit `.env`:

```env
DISCORD_TOKEN=your_user_token_here

# Opsional
VOICE_CHANNEL_ID=123456789012345678
LOG_LEVEL=INFO
```

### 3. Jalankan

```bash
npm start
```

Atau dengan PM2:

```bash
pm2 start index.js --name afkbot
pm2 save && pm2 startup
```

## Rich Presence (GTA VI)

Bot menampilkan Rich Presence "Playing Grand Theft Auto VI". Secara default RPC memakai **application ID resmi GTA VI di games DB Discord** (`1445160676522856458`), jadi saat seseorang **klik cover art RPC di profil, yang terbuka adalah halaman game Grand Theft Auto VI di dalam Discord** — bukan website eksternal.

**Logo default**: logo GTA VI dengan latar transparan dan padding (file lokal: `assets/gta6_padded88.png`), di-host di catbox karena Discord hanya merender `mp:external/<hash>/...` (CDN attachment Discord tidak bisa dikonversi). Dikonversi otomatis lewat `RichPresence.getExternal`. Kalau mau cover art sebagai gantinya, pakai `https://cdn.discordapp.com/app-icons/1445160676522856458/0788ab1237f5801d9f72af6d34665327.png`.

Kalau mau pakai asset sendiri dari Developer Portal (misal nama `gta6`):

1. Buat/pilih aplikasi di [Discord Developer Portal](https://discord.com/developers/applications), upload logo ke Art Assets, beri nama `gta6`.
2. Set di `.env`:
   ```env
   RPC_APP_ID=<app id aplikasi milikmu>
   ACTIVITY_LARGE_IMAGE=gta6
   ```
   Catatan: dengan RPC_APP_ID custom, klik cover art tidak membuka halaman game — klik yang menuju game page hanya aktif jika RPC_APP_ID adalah game ID GTA VI.

Logo dari CDN Discord lain atau URL publik apa pun juga bisa dipakai (`ACTIVITY_LARGE_IMAGE=https://...`), otomatis dikonversi ke `mp:external/...`.

## Logging

Log ditulis ke console dan file di `logs/bot-YYYY-MM-DD.log`.

**Log levels** (set via `LOG_LEVEL` di `.env`):
- `DEBUG` — semua detail
- `INFO` — operasi normal (join, reconnect, RPC)
- `WARN` — hal yang perlu perhatian (disconnect)
- `ERROR` — error yang perlu ditangani

Contoh output:
```
2026-06-18 01:30:00 INFO  [BOOT]  AFK Selfbot starting...
2026-06-18 01:30:01 INFO  [READY] Login sebagai: User#1234 (123456789)
2026-06-18 01:30:01 INFO  [RPC]   Large image: "gta6" → "1546576904646303845"
2026-06-18 01:30:01 INFO  [VOICE] Joined #general di server "My Server" (987654321)
```

## Struktur Proyek

```
├── index.js             # Entry point
├── assets/              # Logo GTA VI (SVG master + PNG transparan)
│   ├── gta6_gradient.svg   # SVG asli dari Wikimedia Commons
│   ├── gta6_logo.png       # Logo transparan 1024×1024
│   ├── gta6_max.png        # Logo transparan, skala penuh
│   ├── gta6_padded.png     # Logo + padding 78% (slot persegi RPC)
│   └── gta6_padded88.png   # Logo + padding 88% (default RPC)
├── src/
│   ├── config.js        # Konfigurasi & validasi
│   ├── logger.js        # Logging system
│   └── voice.js         # Voice channel management
├── logs/                # Log files (auto-generated)
├── .env                 # Konfigurasi (jangan commit!)
├── .env.example         # Template konfigurasi
├── LICENSE              # MIT
└── package.json
```

## License

MIT — see [LICENSE](./LICENSE).

