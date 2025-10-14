# Ignored Programs & Software

This directory contains processes that should be automatically ignored by TKit and never trigger the unknown game dialog.

## Purpose

Common software like system processes, game launchers, browsers, and communication tools are not games and shouldn't prompt users to map them. These JSON files tell TKit to automatically ignore these processes.

## Structure

Each JSON file represents a program that should be ignored:

```json
{
  "processName": "steam",
  "normalizedInstallPaths": [
    "program files (x86)/steam",
    ".steam/steam"
  ],
  "twitchCategoryId": "IGNORE",
  "twitchCategoryName": "Steam",
  "category": "launcher",
  "verificationCount": 1,
  "lastVerified": "2025-10-13"
}
```

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `processName` | string | Yes | Process executable name without `.exe` |
| `normalizedInstallPaths` | array | No | Common installation paths (privacy-safe) |
| `twitchCategoryId` | string | Yes | Always `"IGNORE"` for ignored programs |
| `twitchCategoryName` | string | Yes | Human-readable program name |
| `category` | string | Yes | Type of program (see categories below) |
| `verificationCount` | integer | Yes | Number of users who verified (min: 1) |
| `lastVerified` | string | Yes | ISO 8601 date (YYYY-MM-DD) |

## Categories

- `system`: Operating system processes (explorer.exe, dwm.exe, etc.)
- `launcher`: Game launchers and platforms (Steam, Epic, Origin, etc.)
- `browser`: Web browsers (Chrome, Firefox, Edge, etc.)
- `communication`: Chat and voice apps (Discord, TeamSpeak, etc.)
- `streaming`: Streaming/recording software (OBS, Streamlabs, etc.)
- `utility`: General utilities (Notepad++, VS Code, etc.)
- `anticheat`: Anti-cheat services (BattlEye, EasyAntiCheat, etc.)

## How It Works

1. These JSON files are merged into `programs.json` (auto-generated)
2. TKit downloads both `mappings.json` (games) and `programs.json` during sync
3. When a process is detected, TKit checks the programs list first
4. If matched, the process is automatically ignored without showing any dialog
5. In the UI, ignored programs are displayed in a separate "Software" category

## Contributing

### Common Programs to Add

- **System Processes**: cmd.exe, powershell.exe, taskmgr.exe
- **Launchers**: battle.net.exe, uplay.exe, origin.exe, gog.exe
- **Browsers**: firefox.exe, msedge.exe, opera.exe, brave.exe
- **Communication**: discordptb.exe, ts3client_win64.exe
- **Utilities**: notepad++.exe, code.exe (VS Code), spotify.exe
- **Anti-cheat**: battleye.exe, easyanticheat.exe, vanguard.exe

### Adding a New Program

1. Create a new JSON file: `programs/{processName}.json`
2. Use the schema above
3. Set `twitchCategoryId` to `"IGNORE"`
4. Choose appropriate `category` from the list
5. Submit a Pull Request

### Example: Adding Spotify

**File**: `programs/spotify.json`
```json
{
  "processName": "spotify",
  "twitchCategoryId": "IGNORE",
  "twitchCategoryName": "Spotify",
  "category": "utility",
  "verificationCount": 1,
  "lastVerified": "2025-10-13"
}
```

## Guidelines

### ✅ Should Be Ignored

- System processes
- Game launchers/platforms
- Web browsers
- Communication apps
- Streaming/recording software
- Common utilities
- Anti-cheat services

### ❌ Should NOT Be Ignored

- Actual games (go in `games/` directory)
- Game servers
- Development tools used for making games
- Custom game launchers that are game-specific

## Merging

The GitHub Action automatically:
1. Reads all `programs/*.json` files
2. Generates `programs.json` with all ignored programs
3. TKit clients download this during sync
4. Programs are displayed in a separate category in the UI

## Privacy

Same privacy rules apply as games:
- Only store normalized paths with recognizable platform anchors
- No usernames or personal directories
- Paths are optional for programs
