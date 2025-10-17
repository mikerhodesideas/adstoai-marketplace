# AdstoAI Marketplace

Official plugin marketplace for AdstoAI community members - Google Ads automation and AI tools for Claude Code.

## Quick Start

### Add the Marketplace

```bash
/plugin marketplace add https://github.com/mikerhodesideas/adstoai-marketplace
```

### Install Plugins

```bash
/plugin install google-ads-mcp
```

## Available Plugins

### Google Ads MCP - Complete Google Ads Integration

The most comprehensive Google Ads plugin for Claude Code - includes MCP server, commands, skills, and agents for complete Google Ads automation.

**What's Included:**

**🔌 MCP Server (Bundled)**
- Direct Google Ads API access via MCP tools
- Execute GAQL queries with natural language
- Access account hierarchy and structure
- Real-time performance data

**⚡ Commands (3)**
- `/setup-google-ads-credentials` - Interactive credential setup
- `/test-google-ads-connection` - Verify API connection
- `/setup-account-aliases` - Configure friendly account names (e.g., "SSC" instead of "9882330267")

**🎯 Agent Skills (4)**
- **Account Info** - Query account structure, MCC hierarchies, settings
- **Campaign Performance** - Analyze metrics, trends, and performance data
- **GAQL Query Builder** - Construct complex queries with natural language
- **Account Manager** - Auto-update aliases and manage account mappings

**💬 Natural Language Queries**
Ask questions like:
- "Show campaign performance for Scottish Shutters last 30 days"
- "Which campaigns have the highest conversion rate?"
- "Compare spend between my accounts this month"
- "What's my total ad spend for Q4?"

**Quick Setup:**
```bash
# Install plugin (includes MCP server, commands, and skills)
/plugin install google-ads-mcp

# Add your Google Ads credentials
/setup-google-ads-credentials

# Test the connection
/test-google-ads-connection

# Start querying
"List my Google Ads accounts"
"Show me top performing campaigns this month"
```

**Setup Time:** ~20 minutes (15 min for GCP OAuth, 5 min for plugin)

[Full Documentation →](./plugins/google-ads-mcp/README.md)

## Coming Soon

- **Scripts Helper** - Google Ads Scripts management tools
- **Reporting Tools** - Custom report builders
- **Campaign Optimizer** - AI-powered optimization suggestions

## Support

- **Documentation:** [8020agent.com](https://8020agent.com)
- **Issues:** [GitHub Issues](https://github.com/mikerhodesideas/adstoai-marketplace/issues)

## For Plugin Developers

Want to contribute a plugin? Check out the [Plugin Development Guide](./CONTRIBUTING.md).

---

**Maintained by:** Mike Rhodes | [8020agent.com](https://8020agent.com)
**License:** Apache 2.0
