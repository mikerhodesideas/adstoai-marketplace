# Setup Account Aliases Command

You are helping the user set up friendly aliases for their Google Ads accounts.

## What This Does

Creates or updates `.claude/accounts.json` in the current project with the user's Google Ads account IDs and friendly names.

## Prerequisites

The Google Ads MCP must be connected. If not, direct user to run:
```
/test-google-ads-connection
```

## Steps

### Step 1: Get Accessible Accounts

List all accounts the user has access to:

Use MCP tool: `mcp__google-ads__list_accessible_accounts`

### Step 2: Get Account Details

For each account, query the account name:

```gaql
SELECT customer.id, customer.descriptive_name, customer.currency_code, customer.time_zone
FROM customer
WHERE customer.id = [account_id]
```

Use MCP tool: `mcp__google-ads__execute_gaql` for each account.

### Step 3: Suggest Aliases

For each account, suggest:
- Official name
- Abbreviated version
- Short nickname

Example:
```
Account: 9882330267
Name: "The Scottish Shutter Company"

Suggested aliases:
- "Scottish Shutter Company"
- "Scottish Shutters"
- "SSC"
```

### Step 4: Ask User for Preferences

Present all accounts and ask:

```
I found X Google Ads accounts:

1. [Account Name] (ID: XXXXXXXXXX)
   Suggested aliases: ["Name", "Short", "Abbrev"]

   What would you like to call this account?
   (Enter aliases separated by commas, or press Enter to use suggestions)

2. [Account Name] (ID: XXXXXXXXXX)
   ...
```

### Step 5: Create accounts.json

Check if `.claude/accounts.json` exists in the current project.

If YES:
- Read existing file
- Add new accounts or update existing ones
- Preserve user's custom fields

If NO:
- Create new file with the structure from accounts_template.json

### Step 6: Write the File

Create/update `.claude/accounts.json`:

```json
{
  "account_key_1": {
    "id": "XXXXXXXXXX",
    "aliases": ["Alias 1", "Alias 2", "Alias 3"],
    "name": "Official Account Name",
    "currency": "USD",
    "timezone": "America/New_York"
  }
}
```

### Step 7: Confirm

```
✅ Account aliases set up!

Created .claude/accounts.json with X accounts:

1. account_key_1 → ["Alias 1", "Alias 2"]
2. account_key_2 → ["Alias 3", "Alias 4"]

You can now use these friendly names in queries:
- "Show campaigns for Alias 1"
- "Get metrics for Alias 3"

Try it: "Show me all campaigns for [first alias]"
```

## Key Generation

Generate clean keys from account names:
- Lowercase
- Replace spaces with underscores
- Remove special characters
- Example: "The Scottish Shutter Company" → "scottish_shutter_company"

## Best Practices

1. **Include variations**: Full name, short name, abbreviation
2. **Ask the user**: Don't assume - let them customize
3. **Preserve existing**: Don't overwrite user's custom aliases
4. **Add metadata**: Currency and timezone from API when available

## Error Handling

### No accounts found
→ "I couldn't find any Google Ads accounts. Check your MCP connection with /test-google-ads-connection"

### Account query failed
→ Fall back to just account ID, ask user for details

### Invalid JSON in existing file
→ Offer to fix it or create backup

## Example Interaction

```
User: /setup-account-aliases