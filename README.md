# TKit Community Mappings

Community-sourced game-to-Twitch-category mappings for [TKit](https://github.com/evobug-com/TKitFlutter).

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

## mappings.json Schema

All mappings are stored in [`mappings.json`](./mappings.json):

```json
{
  "mappings": [
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
  ]
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

1. Install TKit from [releases](https://github.com/evobug-com/TKitFlutter/releases)
2. When TKit detects an unmapped game, it will show a dialog
3. Search for the correct Twitch category
4. Click "Save & Contribute" to submit automatically
5. A Pull Request will be created here for review

### Manual Submission

If you prefer to submit manually:

1. Fork this repository
2. Edit `mappings.json`:
   ```json
   {
     "processName": "game.exe",
     "normalizedInstallPaths": ["steamapps/common/game name"],
     "twitchCategoryId": "12345",
     "twitchCategoryName": "Game Name",
     "verificationCount": 1,
     "lastVerified": "2025-10-12",
     "source": "community"
   }
   ```
3. Submit a Pull Request with title: `Add mapping: game.exe → Game Name`

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
- One mapping per game executable

### Rejections ❌

- Incorrect category (will be closed)
- Duplicate submissions (check existing mappings first)
- Paths containing usernames/personal folders
- Test/spam submissions

## Path Merging

When multiple users submit the same game from different platforms, paths are automatically merged:

```json
// User 1 submits Steam version
{
  "processName": "game.exe",
  "normalizedInstallPaths": ["steamapps/common/game"]
}

// User 2 verifies Epic version
{
  "processName": "game.exe",
  "normalizedInstallPaths": ["steamapps/common/game", "epic games/game"]
}
```

Duplicates are automatically removed during merge.

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
2. Check for duplicates in `mappings.json`
3. Ensure paths are privacy-safe (no usernames)
4. Merge or request changes

**Updating Mappings Manually:**

```bash
# Pull latest changes
git pull origin main

# Edit mappings.json
# - Add new mappings alphabetically by processName
# - Increment verificationCount for verified mappings
# - Merge paths from different platforms

# Commit and push
git add mappings.json
git commit -m "Add/Update: [game names]"
git push origin main
```

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
    ↓ (Creates branch + commits + PR)
This Repository
    ↓ (Maintainer reviews + merges)
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

- **TKit Issues**: https://github.com/evobug-com/TKitFlutter/issues
- **Mapping Issues**: https://github.com/evobug-com/tkit-community-mapping/issues
- **Cloudflare Worker**: [Worker README](https://github.com/evobug-com/TKitFlutter/tree/main/cloudflare-worker)

## License

This repository is public domain (CC0). Contributions are licensed under the same terms.

---

**Made with ❤️ by the TKit community**
