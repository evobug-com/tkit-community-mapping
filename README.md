# TKit Community Mappings

A community-driven repository of game-to-Twitch-category mappings for the TKit application.

## What is This?

This repository contains a crowdsourced database of mappings between game executables and their corresponding Twitch categories. These mappings are used by [TKit](https://github.com/evobug-com/TKitFlutter) to automatically update streamers' Twitch categories when they switch games.

## How It Works

### For Users

1. **Automatic Sync**: TKit automatically downloads the latest mappings from this repository every 6 hours
2. **Unknown Game Detection**: When you play a game that's not mapped, TKit shows a dialog
3. **Easy Contribution**: Search for the correct Twitch category and click "Save" with "Contribute to community" enabled
4. **Verification**: If the mapping already exists, your submission verifies it (increases confidence)

### For Contributors

When you submit or verify a mapping:
- **New Mapping**: Creates a GitHub issue with the mapping details
- **Existing Mapping**: Creates a verification issue to increment the verification count
- **Review Process**: Maintainers review submissions and update `mappings.json`
- **Everyone Benefits**: All TKit users get the updated mappings within 6 hours

## Mappings Structure

The `mappings.json` file contains an array of game mappings:

```json
{
  "version": "1.0.0",
  "lastUpdated": "2025-01-15T00:00:00Z",
  "mappings": [
    {
      "processName": "game.exe",
      "twitchCategoryId": "12345",
      "twitchCategoryName": "Game Name",
      "verificationCount": 5,
      "lastVerified": "2025-01-15",
      "source": "community"
    }
  ]
}
```

### Field Descriptions

| Field | Type | Description |
|-------|------|-------------|
| `processName` | string | Executable filename (e.g., "LeagueofLegends.exe") |
| `twitchCategoryId` | string | Twitch category ID from Twitch API |
| `twitchCategoryName` | string | Human-readable category name |
| `verificationCount` | integer | Number of users who verified this mapping |
| `lastVerified` | string | Date of last verification (YYYY-MM-DD) |
| `source` | string | Always "community" for this repository |

## Contributing

### Automated Contributions (Recommended)

The easiest way to contribute is through TKit itself:

1. Play a game that TKit doesn't recognize
2. When the "Game Not Mapped" dialog appears:
   - Search for the correct Twitch category
   - Select it from the results
   - Enable "Contribute to community mappings" (enabled by default)
   - Click "Save"
3. TKit automatically creates a GitHub issue with the mapping
4. Maintainers will review and merge your contribution

### Manual Contributions

You can also contribute by:

1. **Searching existing issues** to see if your mapping is already submitted
2. **Commenting on existing issues** to verify a mapping (+1 for verification)
3. **Creating an issue** manually with the mapping details (use the template)
4. **Submitting a Pull Request** directly to `mappings.json`

### Contribution Guidelines

**Good Mappings:**
- ✅ Common game executables (e.g., "LeagueofLegends.exe")
- ✅ Correct Twitch category match
- ✅ Currently active/popular games
- ✅ Verified by multiple users

**Avoid:**
- ❌ Non-game executables (system apps, browsers, etc.)
- ❌ Incorrect or misleading category matches
- ❌ Adult content or inappropriate categories
- ❌ Duplicate submissions (TKit will detect these)

## Verification System

### Why Verification Matters

- **Accuracy**: Multiple verifications increase confidence in a mapping
- **Currency**: Regular verifications show the mapping is still valid
- **Community Trust**: High verification counts indicate reliable mappings

### How Verification Works

1. **First Submission**: User submits a new mapping → Creates issue labeled `mapping-submission`
2. **Subsequent Submissions**: Same mapping submitted → Creates issue labeled `verification` referencing original
3. **Threshold**: Mappings with 3+ verifications are considered highly reliable
4. **Auto-Update**: After each verification, `verificationCount` is incremented and `lastVerified` is updated

## Issue Labels

| Label | Description |
|-------|-------------|
| `mapping-submission` | New game mapping proposal |
| `verification` | Verification of existing mapping |
| `auto-generated` | Created automatically by TKit |
| `needs-review` | Requires maintainer attention |
| `invalid` | Incorrect or inappropriate mapping |
| `duplicate` | Duplicate of existing mapping/issue |

## Repository Maintenance

### For Maintainers

**Reviewing Submissions:**

1. Check the issue for mapping details
2. Verify the Twitch category ID is correct
3. Search for duplicates in `mappings.json`
4. If new: Add to `mappings.json`
5. If verification: Increment `verificationCount`, update `lastVerified`
6. Close the issue with appropriate label

**Updating mappings.json:**

```bash
# Pull latest changes
git pull origin main

# Edit mappings.json
# - Add new mappings alphabetically by processName
# - Increment verificationCount for verified mappings
# - Update lastVerified date
# - Update file's lastUpdated timestamp

# Commit and push
git add mappings.json
git commit -m "Add/Update: [game names]"
git push origin main
```

**Handling Invalid Submissions:**

- Label as `invalid` and close with explanation
- Block spam or malicious submissions
- Remove inappropriate mappings

## Statistics

- **Total Mappings**: See `mappings.json`
- **Last Updated**: Check `lastUpdated` field in `mappings.json`
- **Top Contributors**: Coming soon
- **Most Verified Games**: Coming soon

## Technical Details

### Sync Frequency
- TKit checks for updates every **6 hours**
- Can be manually triggered via "Sync Now" in settings
- Downloads from: `https://raw.githubusercontent.com/evobug-com/tkit-community-mappings/main/mappings.json`

### Rate Limits
- GitHub API: 60 requests/hour for anonymous submissions
- TKit automatically handles rate limit errors
- Users are notified if rate limit is exceeded

### Privacy
- Only game executable names and Twitch categories are submitted
- No personal information, usernames, or system details are collected
- All submissions are public via GitHub issues

## Support

- **TKit Issues**: [TKit GitHub Issues](https://github.com/evobug-com/TKitFlutter/issues)
- **Mapping Issues**: [Create an issue here](https://github.com/evobug-com/tkit-community-mappings/issues)
- **Discord**: Coming soon

## License

This repository is licensed under the MIT License. See LICENSE file for details.

Community contributions are welcomed and appreciated!

---

**Made with ❤️ by the TKit community**
