# CLAUDE.md

This file provides guidance to Claude Code when working with the Google Ads MCP plugin.

## Project Overview

This is a **Claude Code plugin** that bundles a Google Ads MCP (Model Context Protocol) server, enabling natural language queries to the Google Ads API.

**Purpose:**
- Distributed via Ads2AI marketplace for community members
- Provides direct Google Ads API access through MCP
- Includes commands, skills, and comprehensive documentation

**Installation:**
```bash
/plugin marketplace add https://github.com/mikerhodes/ads2ai-marketplace
/plugin install google-ads-mcp
```

## Plugin Structure

### Core Components

1. **MCP Server** (`google_ads_mcp/`)
   - Based on Google's official `google_ads_mcp` implementation
   - Bundled with plugin for automatic deployment
   - Configured via `.claude-plugin/.mcp.json`
   - Uses `{{PLUGIN_DIR}}` for portable paths

2. **Commands** (`.claude/commands/`)
   - `setup-google-ads-credentials.md` - Interactive credential setup
   - `test-google-ads-connection.md` - Connection verification
   - `setup-account-aliases.md` - Account alias configuration

3. **Skills** (`.claude/skills/`)
   - `google-ads-account-info/` - Account structure queries
   - `google-ads-campaign-performance/` - Performance analysis
   - `gaql-query-builder/` - Query construction
   - `google-ads-account-manager/` - Alias management

4. **Documentation** (root directory)
   - `README.md` - Plugin overview
   - `COMPLETE_SETUP_GUIDE.md` - GCP OAuth setup
   - `TROUBLESHOOTING.md` - Common issues
   - `.claude/ACCOUNTS_JSON_GUIDE.md` - Account alias guide

## Account Aliases

**IMPORTANT:** Always check `.claude/accounts.json` for account name mappings.

**Example accounts:**
- **SSC** / Scottish Shutters → `9882330267`
- **MPM** / Mr Pool Man → `7435598264`
- **MRI** / Mike Rhodes Ideas (MCC) → `5407170017`

When user mentions an account name, map it to the customer ID before querying.

## MCP Tools Available

1. **`mcp__google-ads__list_accessible_accounts`**
   - Lists all accessible Google Ads customer IDs
   - Returns: Array of account ID strings

2. **`mcp__google-ads__execute_gaql`**
   - Executes Google Ads Query Language (GAQL) queries
   - Parameters: `query`, `customer_id`, `login_customer_id` (optional)
   - Returns: Array of result objects

3. **`mcp__google-ads__get_gaql_doc`**
   - Returns GAQL syntax documentation
   - Use for query building reference

4. **`mcp__google-ads__get_reporting_view_doc`**
   - Returns API field documentation for a view
   - Parameter: `view` name (e.g., "campaign", "ad_group")

## Key Data Formats

### Cost Values (Micros)
Google Ads returns costs in **micros** (1/1,000,000 of currency unit):
- `cost_micros: 5000000` = £5.00 / $5.00 / €5.00
- Always divide by 1,000,000 for display
- Example: `metrics.cost_micros / 1000000` = actual cost

### Percentages (Decimals)
Returned as decimals, not percentages:
- `ctr: 0.0633` = 6.33%
- Multiply by 100 for percentage display
- Example: `metrics.ctr * 100` = CTR %

### Customer IDs
- Format: 10 digits, no dashes
- Example: `9882330267` (NOT `988-233-0267`)
- Always use as strings in GAQL queries

### Date Ranges
Predefined constants:
- `LAST_7_DAYS`, `LAST_30_DAYS`, `LAST_90_DAYS`
- `THIS_MONTH`, `LAST_MONTH`
- `THIS_YEAR`, `LAST_YEAR`
- Custom: `BETWEEN 'YYYY-MM-DD' AND 'YYYY-MM-DD'`

## Common Query Patterns

### List Campaigns
```sql
SELECT campaign.id, campaign.name, campaign.status
FROM campaign
```

### Campaign Performance (Last 30 Days)
```sql
SELECT
  campaign.name,
  metrics.clicks,
  metrics.impressions,
  metrics.cost_micros,
  metrics.conversions
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
```

### Account Hierarchy
```sql
SELECT
  customer.id,
  customer.descriptive_name,
  customer.currency_code,
  customer.time_zone
FROM customer
WHERE customer.id = 1234567890
```

## Plugin Configuration

### MCP Server Config (`.claude-plugin/.mcp.json`)
```json
{
  "mcpServers": {
    "google-ads": {
      "command": "uv",
      "args": ["run", "--directory", "{{PLUGIN_DIR}}/google_ads_mcp", ...],
      "env": {
        "GOOGLE_ADS_CREDENTIALS": "{{PLUGIN_DIR}}/google_ads_mcp/google-ads.yaml"
      }
    }
  }
}
```

**Note:** `{{PLUGIN_DIR}}` is replaced with plugin installation path by Claude Code.

### Credentials Location
After user runs `/setup-google-ads-credentials`:
- Created at: `~/.claude/plugins/google-ads-mcp@*/google_ads_mcp/google-ads.yaml`
- Contains: OAuth credentials and developer token
- **Never committed to git** (protected by `.gitignore`)

## User Workflow

### First-Time Setup
1. User installs plugin from marketplace
2. Runs `/setup-google-ads-credentials` (prompts for creds)
3. Runs `/test-google-ads-connection` (verifies)
4. (Optional) Runs `/setup-account-aliases` (friendly names)
5. Starts querying with natural language

### Typical Queries
```
List my Google Ads accounts
Show campaigns for SSC
Get last 30 days performance for Scottish Shutters
What's my best performing campaign this month?
Compare spend between SSC and MPM
```

## Development Commands

### MCP Server (Direct)
```bash
cd google_ads_mcp
uv pip sync              # Install dependencies
uv run -m ads_mcp.server # Start server
uv run pytest            # Run tests
```

### Plugin Testing (Local)
```bash
/plugin install /Users/mike/Projects/ads-mcp  # Install from local path
/plugin uninstall google-ads-mcp              # Uninstall
```

## Important Constraints

### GAQL Queries
- Automatically append `omit_unselected_resource_names=true`
- Cost metrics always in micros
- Customer IDs are strings, not integers
- Use `PARAMETERS` clause for query options

### MCP Server
- Runs on `http://127.0.0.1:8000/mcp` (FastMCP)
- Starts automatically when tools are used
- Uses `streamable-http` transport
- Timeout: 30 seconds per query

### Security
- **Never** commit credentials
- **Never** log sensitive data
- **Always** use `.gitignore` for secrets
- User credentials stored per-installation

## Troubleshooting

### "MCP server not found"
→ Check plugin is installed: `/plugin list`
→ Verify `.mcp.json` is valid JSON

### "Connection refused"
→ Check credentials exist: `ls ~/.claude/plugins/*/google_ads_mcp/google-ads.yaml`
→ Run `/test-google-ads-connection` for diagnostics

### "Invalid credentials"
→ Verify `google-ads.yaml` has all required fields
→ Check `use_proto_plus: True` is set
→ Regenerate refresh token if expired

### "Account not found" (with alias)
→ Check `.claude/accounts.json` syntax
→ Verify account ID is correct (10 digits)
→ Run `/setup-account-aliases` to fix

## File Locations (When Installed as Plugin)

```
~/.claude/plugins/google-ads-mcp@ads2ai/
├── .claude-plugin/
│   ├── plugin.json              # Plugin metadata
│   └── .mcp.json                # MCP server config
├── .claude/
│   ├── CLAUDE.md                # This file
│   ├── commands/                # Slash commands
│   ├── skills/                  # Agent Skills
│   └── accounts.json            # User's account aliases
├── google_ads_mcp/
│   ├── google-ads.yaml          # User's credentials (created by command)
│   └── ads_mcp/                 # MCP server code
└── [documentation files]
```

## Support Resources

- **Setup Guide:** `COMPLETE_SETUP_GUIDE.md`
- **Aliases Guide:** `.claude/ACCOUNTS_JSON_GUIDE.md`
- **Troubleshooting:** `TROUBLESHOOTING.md`
- **Community:** Ads2AI Discord
- **Issues:** GitHub repository

---

**This plugin is designed for easy distribution via marketplace while maintaining user-specific credentials and configuration.**
