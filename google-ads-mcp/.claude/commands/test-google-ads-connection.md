# Test Google Ads MCP Connection

You are testing if the Google Ads MCP server is properly connected.

## What This Does

Verifies that Claude Code can communicate with the Google Ads MCP server and access the user's accounts.

## Test Steps

### 1. Check MCP Server is Configured

Look for the google-ads MCP in the available tools:

- Can you see `mcp__google-ads__list_accessible_accounts`?
- Can you see `mcp__google-ads__execute_gaql`?

If NO:
```
❌ MCP server not detected.

Possible issues:
1. Did you restart Claude Code after running /setup-google-ads-mcp?
2. Check ~/.claude/mcp_settings.json has the google-ads entry
3. Run /setup-google-ads-mcp again if needed

Need help? Share your mcp_settings.json file.
```

If YES, continue to step 2.

### 2. List Accessible Accounts

Try to list the user's Google Ads accounts:

```
Testing connection to Google Ads API...
```

Use the MCP tool: `mcp__google-ads__list_accessible_accounts`

### 3. Report Results

If successful:
```
✅ SUCCESS! Google Ads MCP is working!

Your accessible accounts:
1. Account ID: XXXXXXXXXX
2. Account ID: XXXXXXXXXX
[etc.]

Total: X accounts

Next steps:
- Run /setup-account-aliases to add friendly names for these accounts
- Or start querying: "Show me campaigns for account XXXXXXXXXX"

Try: "Show me all campaigns for [account ID]"
```

If failed:
```
❌ Connection failed.

Error: [error message]

Troubleshooting:
1. Check your google-ads.yaml file has all required fields
2. Verify use_proto_plus: True is set
3. Test the server manually:
   cd ~/path/to/google_ads_mcp
   uv run -m ads_mcp.server

Need help? Share the error message in Ads2AI Discord.
```

### 4. Suggest Next Steps

If connection works:
- Offer to set up account aliases: `/setup-account-aliases`
- Show example queries
- Link to documentation

## Error Messages

### "MCP tools not found"
→ MCP server not configured. Run `/setup-google-ads-mcp`

### "Credentials invalid"
→ Issue with google-ads.yaml. Check COMPLETE_SETUP_GUIDE.md

### "Developer token invalid"
→ Check Google Ads API Center for valid token

### "Account access denied"
→ OAuth scope issue. May need to regenerate refresh token

## Success Criteria

✅ MCP tools are available
✅ Can list accessible accounts
✅ Returns at least one account ID

## Output Format

Use clear visual indicators:
- ✅ for success
- ❌ for errors
- ⚠️ for warnings
- 📝 for next steps

Make it obvious whether the test passed or failed.
