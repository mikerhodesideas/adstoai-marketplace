# Ads to AI Marketplace

Claude Code plugin marketplace for Google Ads professionals. Access powerful Agent Skills and tools for campaign analysis, reporting, and automation.

## Available Plugins

### google-ads-mcp

Agent Skills for working with Google Ads data through the Model Context Protocol (MCP).

**Features:**
- Account information and MCC hierarchy navigation
- Campaign performance analysis with automatic data formatting
- GAQL query building assistance
- Account alias support for easy reference

**[View Plugin Details →](./google-ads-mcp/README.md)**

## Installation

### Quick Start

1. **Add this marketplace to Claude Code:**
   ```bash
   /plugin marketplace add mikerhodesideas/adstoai-marketplace
   ```

2. **Install the Google Ads MCP plugin:**
   ```bash
   /plugin install google-ads-mcp@adstoai-marketplace
   ```

3. **Restart Claude Code** to activate the skills

### Prerequisites

To use the Google Ads MCP plugin, you need:

1. **Google Ads MCP Server** configured and running
   - Follow the [complete setup guide](https://github.com/google-marketing-solutions/google_ads_mcp)
   - Requires Google Cloud Platform OAuth credentials
   - Requires Google Ads API developer token

2. **MCP Server Configuration** in `~/.claude/mcp_settings.json`:
   ```json
   {
     "mcpServers": {
       "GoogleAds": {
         "command": "uv",
         "args": [
           "run",
           "--directory",
           "/path/to/google_ads_mcp",
           "-m",
           "ads_mcp.server"
         ],
         "cwd": "/path/to/google_ads_mcp",
         "timeout": 30000
       }
     }
   }
   ```

## Usage

Once installed, the Google Ads skills activate automatically when you work with Google Ads data:

```
"Show me campaign performance for the last 30 days"
"What accounts do I have access to?"
"Build a GAQL query for keyword metrics"
"Tell me about my MCC structure"
```

## For Community Members

### Recommended Setup

If you're part of the **Ads to AI Community**, here's the recommended configuration:

1. **Install the marketplace and plugin** (see Quick Start above)

2. **Add account aliases** to your projects:
   - Copy `google-ads-mcp/accounts.json.template` to `.claude/accounts.json` in your project
   - Update with your Google Ads account details
   - Now you can reference accounts by name instead of ID

3. **Configure team projects** with the plugin:
   - Add to `.claude/settings.json` in your repository:
   ```json
   {
     "marketplaces": ["mikerhodesideas/adstoai-marketplace"],
     "plugins": ["google-ads-mcp@adstoai-marketplace"]
   }
   ```
   - Team members automatically get the plugin when they trust the folder

### Community Resources

- [8020agent.com](https://8020agent.com) - Free Google Ads automation tool
- [Ads to AI Community](https://8020agent.com) - Join the community (€799/year)
- [Google Ads Scripts](https://8020agent.com/scripts) - Performance Max scripts and more

## Development & Contribution

### Local Testing

To test changes locally:

```bash
# Clone the repository
git clone https://github.com/mikerhodesideas/adstoai-marketplace.git
cd adstoai-marketplace

# Add as local marketplace
/plugin marketplace add ./adstoai-marketplace

# Install the plugin
/plugin install google-ads-mcp@adstoai-marketplace
```

### Adding New Plugins

1. Create a new directory in the marketplace root
2. Add `.claude-plugin/plugin.json` with metadata
3. Add your plugin components (skills, commands, agents, hooks)
4. Update `.claude-plugin/marketplace.json` to include the new plugin
5. Submit a pull request

## Support

- **Plugin Issues:** [Open an issue](https://github.com/mikerhodesideas/adstoai-marketplace/issues)
- **MCP Server Issues:** [google_ads_mcp repository](https://github.com/google-marketing-solutions/google_ads_mcp)
- **Community Support:** [Ads to AI Community](https://8020agent.com)

## License

Apache 2.0

## About

Created by [Mike Rhodes](https://mikerhodes.com.au) for the Google Ads community.

Part of the [Ads to AI](https://8020agent.com) ecosystem helping Google Ads professionals leverage AI and automation.
