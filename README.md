# TKit Community Mappings

Community-sourced game-to-Twitch-category mappings for [TKit](https://github.com/evobug-com/TKit).

## What is this?

This repository stores crowdsourced mappings that automatically detect which game you're playing and update your Twitch category accordingly. When you play a game that TKit doesn't recognize, you can submit a mapping to help other streamers!

## How It Works

### For Users

1. **Automatic Sync**: TKit downloads the latest mappings from this repository every 6 hours
2. **Unknown Game Detection**: When you play an unmapped game, TKit shows a dialog
3. **Easy Contribution**: Search for the correct Twitch category and click "Save & Contribute"
4. **Automatic PR**: TKit creates a Pull Request here with your mapping
5. **Verification**: If the mapping exists, your submission adds a verification (increases confidence)
6. **Everyone Benefits**: All TKit users get updated mappings within 6 hours

### For Contributors

When you submit or verify a mapping through TKit:
- **New Mapping**: Creates a Pull Request adding the game to `mappings.json`
- **Existing Mapping**: Creates a PR incrementing `verificationCount` and merging installation paths
- **Auto-Merge**: PRs are reviewed and merged by maintainers
- **Community Benefit**: Updated mappings sync to all TKit users automatically

## Repository Structure

This repository uses a **distributed file architecture** to prevent merge conflicts:

```
tkit-community-mapping/
├── games/                    # Source of truth - each game has its own file
│   ├── bf6.json
│   ├── phasmophobia.json
│   └── lethal-company.json
└── mappings.json             # Auto-generated (merged from games/)
```

### How It Works

1. **Individual Game Files** (`games/*.json`)
   - Each game has its own JSON file named after its process name
   - Contributors/automated PRs modify only one file
   - **Zero merge conflicts** - each PR touches a different file

2. **Auto-Generated Mappings** (`mappings.json`)
   - Automatically merged from all `games/*.json` files via GitHub Action
   - Regenerated on every push to `main` branch
   - **Do not edit directly** - changes will be overwritten

3. **TKit Client Sync**
   - Downloads `mappings.json` every 6 hours
   - No changes needed in the app

### Editing Files

- **To add/update a game**: Edit or create `games/{processName}.json`
- **Never edit**: `mappings.json` (auto-generated)

### Benefits of This Architecture

✅ **Zero Merge Conflicts** - Each PR touches only one game file
✅ **Clean Diffs** - Reviewers see exactly which game changed
✅ **Scalable** - 1000+ games won't cause massive JSON diffs
✅ **Easy Manual Editing** - Contributors can easily add/edit individual games
✅ **Backward Compatible** - TKit clients still read `mappings.json`

## Game File Schema (`games/*.json`)

Individual game files are stored in [`games/`](./games/) directory. Each file contains a single game mapping:

**Example: `games/dota2.json`**
```json
{
  "processName": "dota2",
  "normalizedInstallPaths": [
    "steamapps/common/dota 2",
    ".steam/steam/steamapps/common/dota 2"
  ],
  "twitchCategoryId": "29595",
  "twitchCategoryName": "Dota 2",
  "verificationCount": 5,
  "lastVerified": "2025-10-12",
  "source": "community"
}
```

### Field Definitions

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `processName` | string | Yes | Game executable name without `.exe` extension (e.g., `dota2`) |
| `normalizedInstallPaths` | array | No | Privacy-safe installation paths (see Privacy section) |
| `twitchCategoryId` | string | Yes | Twitch category ID from Twitch API |
| `twitchCategoryName` | string | Yes | Human-readable game name |
| `verificationCount` | integer | Yes | Number of users who verified this mapping (min: 1) |
| `lastVerified` | string | Yes | ISO 8601 date (YYYY-MM-DD) of last verification |
| `source` | string | Yes | Always `"community"` for this repository |

## Privacy-Safe Path Tracking

To distinguish games with identical process names (e.g., multiple games called `game.exe`), we optionally store **normalized installation paths**.

### What Gets Stored ✅

Only paths with recognized platform anchors:
- `steamapps/common/dota 2`
- `epic games/fortnite`
- `.steam/steam/steamapps/common/counter-strike global offensive`
- `.wine/drive_c/program files/game`
- `games/lutris/wine/game`

### What NEVER Gets Stored ❌

Paths without recognized anchors (would expose usernames):
- ❌ `/home/johnsmith/games/custom` → **Rejected** (username visible)
- ❌ `C:\Users\Sarah\MyGames\game` → **Rejected** (username visible)
- ✅ Only process name stored instead

### Supported Platforms

**Windows:**
- Steam, Epic Games, GOG, EA/Origin
- Ubisoft, Battle.net, Rockstar Games
- Xbox Game Pass, Microsoft Store

**Linux:**
- Steam (native + Flatpak)
- Lutris, Wine/Proton
- System directories (`/usr/share/games`, `/opt/games`)

## Contributing

### Automatic Submission (Recommended)

1. Install TKit from [releases](https://github.com/evobug-com/TKit/releases)
2. When TKit detects an unmapped game, it will show a dialog
3. Search for the correct Twitch category
4. Click "Save & Contribute" to submit automatically
5. A Pull Request will be created here for review

### Manual Submission

If you prefer to submit manually:

1. Fork this repository
2. Create or edit a file in `games/` directory:
   - Filename: `games/{processName}.json` (e.g., `games/dota2.json`)
   - Content:
   ```json
   {
     "processName": "game",
     "normalizedInstallPaths": ["steamapps/common/game name"],
     "twitchCategoryId": "12345",
     "twitchCategoryName": "Game Name",
     "verificationCount": 1,
     "lastVerified": "2025-10-12",
     "source": "community"
   }
   ```
3. Submit a Pull Request with title: `Add mapping: game → Game Name`
4. The GitHub Action will automatically regenerate `mappings.json` when merged

**Finding Twitch Category ID:**
- Search on [Twitch](https://www.twitch.tv/directory)
- Open browser DevTools → Network tab
- Search for the game
- Look for API calls to `gql` endpoint
- Find `categoryID` in the response

### Verifying Existing Mappings

Help improve accuracy by verifying existing mappings:

1. In TKit, when it correctly detects your game, click "✓ Verify"
2. This increments `verificationCount` and updates `lastVerified`
3. Helps prioritize accurate mappings

## Quality Guidelines

### Acceptable Submissions ✅

- Correct Twitch category (exact match)
- Process name with or without `.exe` (both work)
- Privacy-safe paths only
- Multiple entries with same `processName` if they have different `normalizedInstallPaths`

### Rejections ❌

- Incorrect category (will be closed)
- Exact duplicate submissions (same processName AND same paths)
- Paths containing usernames/personal folders
- Test/spam submissions

## Path Merging

When multiple users submit/verify the same game from different platforms, the Cloudflare Worker automatically merges paths within the same game file:

**Example: `games/game.json` evolution**

```json
// User 1 submits Steam version
{
  "processName": "game",
  "normalizedInstallPaths": ["steamapps/common/game"],
  "verificationCount": 1
}

// User 2 verifies Epic version → Cloudflare Worker merges paths
{
  "processName": "game",
  "normalizedInstallPaths": ["steamapps/common/game", "epic games/game"],
  "verificationCount": 2
}
```

- Paths are merged within the **single game file**
- Duplicates are automatically removed
- GitHub Action then regenerates `mappings.json` with the updated game data

## Handling Duplicate Process Names

Some games use identical or generic executable names (e.g., `game.exe`, `launcher.exe`, `bf.exe`). TKit handles this through **path-based disambiguation**.

### Multiple Games, Same Process Name

When multiple games share the same process name, create **separate files using descriptive names**:

```
games/
├── battlefield-2042-game.json     # processName: "game"
└── fortnite-game.json             # processName: "game"
```

**Example: `games/battlefield-2042-game.json`**
```json
{
  "processName": "game",
  "normalizedInstallPaths": ["steamapps/common/battlefield 2042"],
  "twitchCategoryId": "512710",
  "twitchCategoryName": "Battlefield 2042",
  "verificationCount": 3,
  "lastVerified": "2025-10-12",
  "source": "community"
}
```

**Example: `games/fortnite-game.json`**
```json
{
  "processName": "game",
  "normalizedInstallPaths": ["epic games/fortnite"],
  "twitchCategoryId": "33214",
  "twitchCategoryName": "Fortnite",
  "verificationCount": 5,
  "lastVerified": "2025-10-12",
  "source": "community"
}
```

The GitHub Action merges both into `mappings.json` which will contain **multiple entries with the same `processName`** but different paths.

### How TKit Resolves Conflicts

When TKit detects a process (e.g., `game.exe`), it uses a **multi-step matching strategy**:

1. **Path + Process Match** (Most Accurate)
   - Extracts installation path: `C:\Epic Games\Fortnite\game.exe` → `epic games/fortnite`
   - Searches for: `processName="game"` **AND** path contains `epic games/fortnite`
   - Returns: **Fortnite** ✅

2. **Process Name Match** (Fallback)
   - If no path-based match found, matches by `processName` only
   - Returns: **First matching entry** (may be incorrect)

3. **Normalized Match** (Flexible Matching)
   - Normalizes process name: `Game-Launcher.exe` → `gamelauncher`
   - Compares normalized names for flexible matching

### Edge Case: Custom Installation Paths

If a game is installed in a custom location **without a recognizable platform anchor**:

```
C:\Users\John\MyGames\game.exe
→ Path normalizer returns null (privacy protection)
→ Only processName is stored
→ May match incorrectly if multiple games share the name
```

**Solution:**
- TKit will prompt the user if multiple matches exist
- User can manually select the correct category
- Store the correct mapping in local database to avoid future conflicts

### Best Practices

When submitting mappings for games with common process names:

1. **Always include `normalizedInstallPaths`** if the game is from a recognized platform
2. **Use specific path segments** (e.g., `steamapps/common/full game name`, not just `steamapps/common`)
3. **Verify the paths** match what users will actually have installed
4. **Document ambiguity** in PR description if you know multiple games use the same exe name

## Cross-Platform Notes

### Process Names

**All platforms use the same format** - `.exe` extension is automatically removed before submission:

```json
"processName": "dota2"
```

**Not:**
```json
"processName": "dota2.exe"  // ❌ Don't use this
```

This ensures Windows (`dota2.exe`) and Linux (`dota2`) submissions create a single mapping entry.

### Path Formats

Always use forward slashes (`/`) in paths:
```json
✅ "steamapps/common/dota 2"
❌ "steamapps\\common\\dota 2"
```

Use lowercase:
```json
✅ "steamapps/common/dota 2"
❌ "SteamApps/Common/Dota 2"
```

## Maintenance

### For Maintainers

**Reviewing Pull Requests:**

Automated PRs from TKit are pre-validated and include:
- ✅ Valid schema
- ✅ Duplicate detection
- ✅ Automatic path merging
- ✅ Alphabetical sorting by `processName`
- ✅ Proper JSON formatting (2-space indentation)

Simply review the PR description and merge if accurate.

**Manual PR Review:**

1. Verify Twitch category ID is correct
2. Check that the PR modifies a file in `games/` directory (not `mappings.json` directly)
3. For games with common process names, verify:
   - Filename is descriptive (e.g., `battlefield-2042-game.json` not just `game.json`)
   - `normalizedInstallPaths` are specific enough to distinguish games
4. Ensure paths are privacy-safe (no usernames)
5. Merge the PR → GitHub Action will automatically regenerate `mappings.json`

**Updating Mappings Manually:**

```bash
# Pull latest changes
git pull origin main

# Create or edit a game file in games/ directory
# Example: games/dota2.json
nano games/dota2.json

# Commit and push
git add games/
git commit -m "Add/Update: [game name]"
git push origin main

# GitHub Action will automatically regenerate mappings.json
```

**DO NOT manually edit `mappings.json`** - it is auto-generated and changes will be overwritten.

**Handling Invalid Submissions:**

- Close PR with explanation
- Block spam or malicious submissions
- Remove inappropriate mappings

## Technical Details

### Sync Frequency
- TKit checks for updates every **6 hours**
- Can be manually triggered via "Sync Now" in settings
- Downloads from: `https://raw.githubusercontent.com/evobug-com/tkit-community-mapping/refs/heads/main/mappings.json`

### Submission Flow

```
TKit Client
    ↓ (User submits mapping)
Cloudflare Worker API
    ↓ (Authenticates via GitHub App)
GitHub REST API
    ↓ (Creates branch + commits games/{game}.json + PR)
This Repository
    ↓ (Maintainer reviews + merges to main)
GitHub Action: Merge Mappings
    ↓ (Reads all games/*.json)
    ↓ (Regenerates mappings.json)
    ↓ (Commits to main)
mappings.json Updated
    ↓ (6-hour sync cycle)
All TKit Clients
```

### Privacy

- Only game executable names, Twitch categories, and privacy-safe paths are submitted
- No personal information, usernames, or system details are collected
- All submissions are public via GitHub Pull Requests
- Paths containing usernames are automatically rejected

### Rate Limits

- Cloudflare Worker: 100,000 requests/day (free tier)
- GitHub API: Rate limited by GitHub App installation
- TKit handles rate limit errors gracefully

## Statistics

<!-- Update these monthly -->
- **Total Mappings:** Check [`mappings.json`](./mappings.json)
- **Most Verified:** TBD
- **Platforms Covered:** Windows, Linux
- **Contributors:** [See contributors](https://github.com/evobug-com/tkit-community-mapping/graphs/contributors)

## API

This repository is consumed by:

1. **TKit Client** - Downloads `mappings.json` every 6 hours
2. **Cloudflare Worker** - Creates Pull Requests with new submissions
3. **Community Scripts** - Parse `mappings.json` for analytics

### Endpoints

- **Raw JSON**: `https://raw.githubusercontent.com/evobug-com/tkit-community-mapping/main/mappings.json`
- **API (with CORS)**: Use jsDelivr CDN for better caching

## Support

- **TKit Issues**: https://github.com/evobug-com/TKit/issues
- **Mapping Issues**: https://github.com/evobug-com/tkit-community-mapping/issues
- **Cloudflare Worker**: [Worker README](https://github.com/evobug-com/TKit/tree/main/cloudflare-worker)

## License

This repository is public domain (CC0). Contributions are licensed under the same terms.

---

**Made with ❤️ by the TKit community**
