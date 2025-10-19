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
  "twitchCategoryId": "-1",
  "twitchCategoryName": "Steam"
}
```

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `processName` | string | Yes | Process executable name without `.exe` |
| `normalizedInstallPaths` | array | No | Common installation paths (privacy-safe) |
| `twitchCategoryId` | string | Yes | Always `"-1"` for ignored programs |
| `twitchCategoryName` | string | Yes | Human-readable program name |

## How It Works

1. These JSON files are merged into `programs.json` (auto-generated)
2. TKit downloads both `mappings.json` (games) and `programs.json` during sync
3. When a process is detected, TKit checks the programs list first
4. If matched, the process is automatically ignored without showing any dialog

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
3. Set `twitchCategoryId` to `"-1"` (string)
4. Submit a Pull Request

### Example: Adding Spotify

**File**: `programs/spotify.json`
```json
{
  "processName": "spotify",
  "twitchCategoryId": "-1",
  "twitchCategoryName": "Spotify"
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

## Privacy

Same privacy rules apply as games:
- Only store normalized paths with recognizable platform anchors
- No usernames or personal directories
- Paths are optional for programs
