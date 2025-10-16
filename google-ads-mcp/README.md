# Google Ads MCP Plugin

Claude Code plugin for working with Google Ads data through the Model Context Protocol (MCP). Provides Agent Skills for campaign analysis, GAQL query building, and account management.

## Features

### Agents

**Black Friday Campaign Preparation** - Multi-step workflow agent that prepares your Google Ads campaigns for Black Friday, Cyber Monday, and holiday shopping season:
- Year-over-year performance analysis
- Budget scaling recommendations based on historical ROI
- Campaign readiness assessment
- Seasonal optimization checklist
- Event monitoring plan

**Usage:** "Prepare my account for Black Friday" or "Get ready for BFCM"

### Agent Skills

This plugin includes four powerful Agent Skills that activate automatically when working with Google Ads:

#### 1. Getting Google Ads Account Information
- Retrieves account details (name, ID, currency, timezone)
- Handles MCC hierarchies and client lists
- Auto-activates when you ask about accounts or account structure

#### 2. Analyzing Google Ads Campaign Performance
- Pulls campaign metrics (clicks, impressions, cost, conversions)
- Handles micros conversion automatically
- Triggers on performance analysis requests

#### 3. Building Google Ads GAQL Queries
- Constructs proper GAQL syntax
- Provides query templates for common scenarios
- Accesses field documentation when needed

#### 4. Calculating Budget Pacing
- Daily and monthly budget pacing analysis
- Over/under-spending alerts
- End-of-period spend forecasting
- Campaign-level and account-level pacing
- Actionable budget adjustment recommendations

**Usage:** "How is my budget pacing?" or "Will I hit my monthly budget?"

## Prerequisites

You must have the Google Ads MCP server configured. See the [complete setup guide](https://github.com/google-marketing-solutions/google_ads_mcp) for:

1. Google Cloud Platform OAuth setup
2. Google Ads API credentials
3. MCP server installation and configuration

## Installation

### Add the Marketplace

```bash
/plugin marketplace add mikerhodesideas/adstoai-marketplace
```

### Install the Plugin

```bash
/plugin install google-ads-mcp@adstoai-marketplace
```

Restart Claude Code to activate the skills.

## Configuration

### Account Aliases (Optional but Recommended)

Create `.claude/accounts.json` in your project to map account names to IDs:

1. Copy the template from this plugin:
   ```bash
   cp accounts.json.template .claude/accounts.json
   ```

2. Edit with your account details:
   ```json
   {
     "my_account": {
       "id": "9882330267",
       "aliases": ["Main", "Primary Account"],
       "name": "My Google Ads Account",
       "currency": "USD",
       "type": "client"
     }
   }
   ```

3. Now you can say "show me Main account performance" instead of remembering the customer ID

### MCP Server Configuration

Add to `~/.claude/mcp_settings.json`:

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

## Usage Examples

Once installed, the agent and skills work automatically:

### Black Friday Preparation (Agent)
```
"Prepare my account for Black Friday"
"Get ready for BFCM 2024"
"What should I change before the holiday shopping season?"
```

### Budget Pacing (Skill)
```
"How is my budget pacing this month?"
"Will I hit my monthly budget?"
"Which campaigns are overspending?"
"Am I on track with my spending?"
```

### Account Information (Skill)
```
"What accounts do I have access to?"
"Tell me about the MCC account structure"
"Show me client accounts under my MCC"
```

### Campaign Performance (Skill)
```
"Show me campaign performance for the last 30 days"
"What's the cost and conversion data for my campaigns?"
"Which campaigns have the best CTR?"
```

### GAQL Query Building (Skill)
```
"Build a query to get keyword performance"
"Show me how to query ad group metrics"
"What fields are available in the campaign view?"
```

## Data Format Notes

The skills automatically handle Google Ads data formatting:

- **Cost values**: Converted from micros (divides by 1,000,000)
- **Percentages**: Converted from decimals (multiplies by 100)
- **Customer IDs**: Always digits only, no dashes

## Troubleshooting

### Skills Not Activating

1. Verify plugin is installed: `/plugin`
2. Check skills are loaded: Ask "What skills are available?"
3. Restart Claude Code if you just installed

### MCP Connection Issues

1. Verify MCP server is configured in `~/.claude/mcp_settings.json`
2. Test MCP server: `uv run -m ads_mcp.server` in the google_ads_mcp directory
3. Check credentials in `google-ads.yaml`

### Account Alias Not Working

1. Ensure `.claude/accounts.json` exists in your project
2. Verify JSON syntax is valid
3. Check account ID matches your Google Ads account

## What's Included

```
google-ads-mcp/
├── .claude-plugin/
│   └── plugin.json                          # Plugin metadata
├── agents/
│   └── black-friday-prep.md                 # Black Friday preparation agent
├── skills/
│   ├── google-ads-account-info/             # Account structure skill
│   │   └── SKILL.md
│   ├── google-ads-campaign-performance/     # Campaign metrics skill
│   │   └── SKILL.md
│   ├── gaql-query-builder/                  # Query building skill
│   │   └── SKILL.md
│   └── budget-pacing/                       # Budget pacing skill
│       └── SKILL.md
├── accounts.json.template                    # Template for account aliases
└── README.md                                 # This file
```

## Resources

- [Google Ads MCP Server](https://github.com/google-marketing-solutions/google_ads_mcp)
- [Complete Setup Guide](https://github.com/google-marketing-solutions/google_ads_mcp/blob/main/COMPLETE_SETUP_GUIDE.md)
- [Google Ads Query Language (GAQL)](https://developers.google.com/google-ads/api/docs/query/overview)
- [Agent Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)

## Support

For issues or questions:
- Plugin issues: Open an issue in this repository
- MCP server issues: See [google_ads_mcp](https://github.com/google-marketing-solutions/google_ads_mcp)
- Community support: [Ads to AI Community](https://8020agent.com)

## License

Apache 2.0

## Author

Mike Rhodes - [8020agent.com](https://8020agent.com)
