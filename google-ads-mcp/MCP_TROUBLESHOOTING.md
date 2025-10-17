# Troubleshooting Guide

Common issues and solutions for the Google Ads MCP plugin.

---

## Installation Issues

### Plugin Not Found

**Symptom:** Can't find or install `google-ads-mcp`

**Solutions:**
```bash
# Verify marketplace is added
/plugin marketplace list

# Add marketplace if missing
/plugin marketplace add https://github.com/mikerhodes/ads2ai-marketplace

# Refresh and try again
/plugin install google-ads-mcp
```

### Installation Fails

**Symptom:** Error during plugin installation

**Solutions:**
1. Check you have Python 3.12+ installed:
   ```bash
   python3 --version
   ```

2. Install `uv` if missing:
   ```bash
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

3. Restart Claude Code and try again

---

## MCP Server Issues

### MCP Server Not Found

**Symptom:** `/mcp` shows no Google Ads server, or tools not available

**Possible Causes & Solutions:**

1. **Plugin not installed**
   ```bash
   /plugin list  # Check if google-ads-mcp is listed
   ```

2. **Claude Code not restarted**
   - Quit Claude Code completely (Cmd+Q)
   - Reopen and try again

3. **Invalid .mcp.json**
   ```bash
   # Check the file exists and is valid JSON
   cat ~/.claude/plugins/google-ads-mcp*/. claude-plugin/.mcp.json
   ```

### Connection Refused

**Symptom:** `ERR_CONNECTION_REFUSED` or "MCP server not responding"

**Possible Causes & Solutions:**

1. **Credentials not set up**
   ```bash
   # Run the setup command
   /setup-google-ads-credentials
   ```

2. **Invalid credentials file**
   ```bash
   # Check the file exists
   ls ~/.claude/plugins/google-ads-mcp*/google_ads_mcp/google-ads.yaml

   # Verify it has all required fields
   cat ~/.claude/plugins/google-ads-mcp*/google_ads_mcp/google-ads.yaml
   ```

3. **Port already in use**
   ```bash
   # Kill any running MCP servers
   pkill -f "ads_mcp.server"
   ```

4. **Test server manually**
   ```bash
   cd ~/.claude/plugins/google-ads-mcp*/google_ads_mcp
   uv run -m ads_mcp.server
   ```

   If this works, the issue is with plugin configuration.
   If this fails, check credentials.

---

## Credential Issues

### Invalid Credentials

**Symptom:** "Google Ads credentials error" or "Authentication failed"

**Check These:**

1. **All required fields present in `google-ads.yaml`**
   ```yaml
   client_id: YOUR_VALUE
   client_secret: YOUR_VALUE
   refresh_token: YOUR_VALUE
   developer_token: YOUR_VALUE
   use_proto_plus: True  # ← Must be set!
   ```

2. **use_proto_plus is True**
   - This is required!
   - Must be exactly: `use_proto_plus: True`

3. **No trailing spaces or quotes**
   - Values should not have extra quotes
   - Correct: `client_id: 123-abc.apps.googleusercontent.com`
   - Wrong: `client_id: "123-abc.apps.googleusercontent.com"`

4. **Refresh token is current**
   - Tokens can expire or be revoked
   - Regenerate using `COMPLETE_SETUP_GUIDE.md` instructions

### Developer Token Invalid

**Symptom:** "Developer token is not approved" or "Access denied"

**Solutions:**

1. **Check token in Google Ads API Center**
   - Go to [Google Ads API Center](https://ads.google.com/aw/apicenter)
   - Verify your token is listed
   - Check status (Test/Production)

2. **Test account gets instant access**
   - Use a test account for development
   - Production accounts need approval from Google

3. **Wait for approval**
   - Production tokens require manual approval
   - Can take 24-48 hours
   - Use test account in the meantime

### OAuth Errors (400, 401, 403)

**Symptom:** OAuth error codes during setup

**400 Bad Request:**
- Check OAuth client type is **Desktop app** (not Web app!)
- Verify redirect URIs if using Web app
- Recreate as Desktop app in GCP

**401 Unauthorized:**
- Refresh token expired or revoked
- Regenerate refresh token
- Check client ID/secret match the OAuth client

**403 Forbidden:**
- Google Ads account doesn't have API access
- Developer token not approved
- Check account permissions

---

## Query Issues

### Account Not Found

**Symptom:** "Account ID 1234567890 not found" or "No access to account"

**Solutions:**

1. **Verify you have access**
   ```bash
   /test-google-ads-connection
   ```
   This shows all accounts you can access

2. **Check account ID is correct**
   - Must be 10 digits
   - No dashes
   - Example: `9882330267` not `988-233-0267`

3. **Using MCC account ID?**
   - Some queries require specific sub-account IDs
   - Use `/test-google-ads-connection` to see hierarchy

### Alias Not Found

**Symptom:** "Unknown account: SSC" or alias doesn't work

**Solutions:**

1. **Check `.claude/accounts.json` exists**
   ```bash
   cat ~/.claude/plugins/google-ads-mcp*/.claude/accounts.json
   ```

2. **Verify JSON syntax**
   - Use [JSONLint](https://jsonlint.com/) to validate
   - Common issues: trailing commas, missing quotes

3. **Run setup command**
   ```bash
   /setup-account-aliases
   ```

4. **Check alias spelling**
   - Aliases are case-sensitive
   - Check exact spelling in `accounts.json`

### Query Timeout

**Symptom:** "Query timeout" or "Request took too long"

**Solutions:**

1. **Narrow date range**
   - Use `LAST_7_DAYS` instead of `LAST_YEAR`
   - Paginate large result sets

2. **Reduce selected fields**
   - Only select fields you need
   - Fewer fields = faster queries

3. **Use specific resource filters**
   - Filter by campaign ID if possible
   - Add WHERE clauses to limit results

4. **Check network connection**
   - Slow connection can cause timeouts
   - Try again with stable network

---

## Common Error Messages

### "FileNotFoundError: google-ads.yaml"

**Cause:** Credentials file doesn't exist

**Solution:**
```bash
/setup-google-ads-credentials
```

### "ValueError: missing use_proto_plus key"

**Cause:** `google-ads.yaml` missing required field

**Solution:**
Add this line to your `google-ads.yaml`:
```yaml
use_proto_plus: True
```

### "Invalid customer ID format"

**Cause:** Account ID has dashes or wrong format

**Solution:**
- Use format: `1234567890` (10 digits, no dashes)
- Remove any formatting: `123-456-7890` → `1234567890`

### "PERMISSION_DENIED"

**Cause:** Don't have access to the account or resource

**Solutions:**
1. Verify account access with `/test-google-ads-connection`
2. Check MCC account permissions
3. Ensure account is linked to your OAuth user

### "GAQL syntax error"

**Cause:** Invalid GAQL query syntax

**Solutions:**
1. Check field names against documentation
2. Verify FROM resource is correct
3. Use proper WHERE clause syntax
4. Check date range format

**Get GAQL documentation:**
```
Get GAQL syntax documentation
```

---

## Performance Issues

### Slow Queries

**Optimize these:**

1. **Date ranges** - Use shorter periods
2. **Field selection** - Only select what you need
3. **Filters** - Add WHERE clauses
4. **Segments** - Avoid unnecessary segmentation

**Example:**
```sql
-- Slow
SELECT * FROM campaign WHERE segments.date DURING LAST_YEAR

-- Fast
SELECT campaign.name, metrics.cost_micros
FROM campaign
WHERE segments.date DURING LAST_7_DAYS
  AND campaign.id = 1234567890
```

### Plugin Slow to Load

**Solutions:**

1. **Update plugin**
   ```bash
   /plugin update google-ads-mcp
   ```

2. **Clear Claude Code cache**
   - Quit Claude Code
   - Restart and test

3. **Check system resources**
   - Close other applications
   - Ensure adequate memory

---

## Getting Help

### Check Documentation

1. **Setup Guide:** `COMPLETE_SETUP_GUIDE.md`
2. **Account Aliases:** `.claude/ACCOUNTS_JSON_GUIDE.md`
3. **GAQL Reference:** Use MCP tool to get docs
4. **Plugin README:** Main `README.md`

### Test Connection

Always start with:
```bash
/test-google-ads-connection
```

This shows:
- ✅ MCP server status
- ✅ Accessible accounts
- ✅ Connection health
- ❌ Specific errors if any

### Community Support

- **Ads2AI Discord** - Community help
- **GitHub Issues** - Bug reports
- **Documentation** - [8020agent.com](https://8020agent.com)

### Reporting Issues

Include this information:

1. **Error message** - Full error text
2. **What you tried** - Commands run
3. **Environment** - OS, Python version
4. **Test results** - Output of `/test-google-ads-connection`

**Example:**
```
Issue: Can't connect to MCP server
Tried: /setup-google-ads-credentials, /test-google-ads-connection
Error: "Connection refused"
OS: macOS 14.0
Python: 3.12.0
Test output: [paste output]
```

---

## Quick Diagnostic Checklist

Run through this checklist:

- [ ] Plugin installed: `/plugin list`
- [ ] Credentials set up: `ls ~/.claude/plugins/*/google_ads_mcp/google-ads.yaml`
- [ ] `use_proto_plus: True` in config
- [ ] Restarted Claude Code
- [ ] Test connection: `/test-google-ads-connection`
- [ ] Check Claude Code logs for errors

If all check out but still having issues, ask in Ads2AI Discord with details!

---

**Most issues are solved by:**
1. Running `/setup-google-ads-credentials`
2. Restarting Claude Code
3. Running `/test-google-ads-connection`

Try these first! 🚀
