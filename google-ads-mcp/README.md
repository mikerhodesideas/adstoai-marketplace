# Google Ads MCP Plugin

Complete Google Ads integration for Claude Code - query your Google Ads data with natural language.

## What's Included

### MCP Server (Bundled)
- Direct Google Ads API access
- GAQL query execution
- Account management
- Real-time performance data

### Commands (3)
- `/setup-google-ads-credentials` - Interactive credential setup
- `/test-google-ads-connection` - Verify API connection
- `/setup-account-aliases` - Configure friendly account names

### Skills (4)
- **Account Info** - Query account structure, MCC hierarchies
- **Campaign Performance** - Analyze metrics and trends
- **GAQL Query Builder** - Build queries with natural language
- **Account Manager** - Auto-update account aliases

## Quick Start

### 1. Install the Plugin

```bash
# Add marketplace
/plugin marketplace add https://github.com/mikerhodesideas/adstoai-marketplace

# Install plugin
/plugin install google-ads-mcp
```

### 2. Set Up Google Ads API Access

**You'll need:**
- Google Cloud Platform account
- Google Ads account with API access
- ~15 minutes for OAuth setup

**Follow the complete setup guide:**
- See `MCP_COMPLETE_SETUP_GUIDE.md` in the plugin directory
- Or visit: https://8020agent.com (documentation)

### 3. Add Your Credentials

```bash
/setup-google-ads-credentials
```

This interactive command will:
- Guide you through credential setup
- Store them securely in your plugin directory
- Test the connection

**Where credentials are stored:**
```
~/.claude/plugins/google-ads-mcp@adstoai/google_ads_mcp/google-ads.yaml
```

**Security:** Your credentials are stored locally and NEVER committed to git.

### 4. Test Connection

```bash
/test-google-ads-connection
```

### 5. Start Querying!

```
"List my Google Ads accounts"
"Show campaign performance for last 30 days"
"Which campaigns have the highest conversion rate?"
```

## Example Queries

**Campaign Performance:**
```
"Show me top performing campaigns this month"
"Compare spend between my accounts"
"What's my total ad spend for Q4?"
```

**Account Management:**
```
"List all active campaigns"
"Show me account structure"
"Which ads are running in campaign X?"
```

**Custom Analysis:**
```
"Find campaigns with CTR above 5%"
"Show conversion rates by campaign"
"What's my average CPC this week?"
```

## Documentation

- **Setup Guide**: `MCP_COMPLETE_SETUP_GUIDE.md`
- **Troubleshooting**: `MCP_TROUBLESHOOTING.md`
- **Account Aliases**: `.claude/ACCOUNTS_JSON_GUIDE.md`
- **Online Docs**: https://8020agent.com

## Support

- **Community**: [Circle Q&A](https://mikerhodes.circle.so/c/ai-questions/)
- **Join AdstoAI**: [8020agent.com/ai](https://8020agent.com/ai)

## Security & Privacy

**Your credentials are safe:**
- Stored locally in your plugin directory
- Protected by `.gitignore`
- Never transmitted or shared
- You have full control

**Each user must create their own credentials:**
- No shared access
- OAuth follows Google's security best practices
- Refresh tokens are user-specific

## Requirements

- Python 3.12+
- `uv` package manager (auto-installed by Claude Code)
- Google Cloud Platform account
- Google Ads account with API access

## License

Apache 2.0

---

**Created by Mike Rhodes** | [8020agent.com](https://8020agent.com)
