# accounts.json Guide

The `accounts.json` file maps friendly names to your Google Ads account IDs, making it easier to work with multiple accounts.

## Location

Place this file in your **brain repo** (or any Claude Code project):

```
your-project/
└── .claude/
    └── accounts.json
```

## Format

```json
{
  "unique_key": {
    "id": "1234567890",
    "aliases": ["Friendly Name", "short", "nickname"],
    "name": "Official Account Name",
    "currency": "USD",
    "timezone": "America/New_York",
    "notes": "Any notes about this account"
  }
}
```

## Fields Explained

### Required Fields

- **`id`** (string) - The 10-digit Google Ads account ID (no dashes)
  - Find it: Top right in Google Ads UI, or in the URL
  - Example: `"1234567890"`

- **`aliases`** (array) - Friendly names you'll use in conversation
  - Example: `["SSC", "Scottish Shutters", "main"]`
  - You can say: "Show campaigns for SSC"
  - Claude Code will match to account ID `1234567890`

- **`name`** (string) - Official account name for reference
  - Helpful for documentation
  - Example: `"The Scottish Shutter Company (2025)"`

### Optional Fields

- **`currency`** - Account currency code
  - Examples: `"USD"`, `"GBP"`, `"EUR"`, `"AUD"`
  - Helps Claude Code format cost data correctly

- **`timezone`** - Account timezone
  - Examples: `"America/New_York"`, `"Europe/London"`, `"Australia/Melbourne"`
  - Useful for date/time queries

- **`notes`** - Any additional context
  - Client details, account purpose, special considerations

## Real Example

Here's a real-world setup:

```json
{
  "scottish_shutters": {
    "id": "9882330267",
    "aliases": ["SSC", "Scottish Shutters", "shutters", "main"],
    "name": "Rocknowe Interiors Ltd t/a The Scottish Shutter Company (2025)",
    "currency": "GBP",
    "timezone": "Europe/London",
    "notes": "Primary B2C account - Scotland market"
  },
  "mr_pool_man": {
    "id": "7435598264",
    "aliases": ["MPM", "Pool Man", "pools"],
    "name": "Mr Pool Man",
    "currency": "AUD",
    "timezone": "Australia/Melbourne",
    "notes": "Australian pool supplies - seasonal business"
  },
  "mcc_account": {
    "id": "5407170017",
    "aliases": ["MCC", "manager", "Mike Rhodes Ideas"],
    "name": "Mike Rhodes Ideas (MCC)",
    "currency": "USD",
    "timezone": "Australia/Melbourne",
    "notes": "Manager account - use for multi-account queries"
  }
}
```

## Usage Examples

With the example above, you can say:

```
Show me campaigns for SSC
```
→ Queries account `9882330267`

```
What's the ad spend for Pool Man this month?
```
→ Queries account `7435598264`

```
Compare performance between Scottish Shutters and MPM
```
→ Queries both accounts and compares

## How to Find Your Account IDs

### Method 1: Ask Claude Code

Once MCP is connected:
```
List my accessible Google Ads accounts
```

Claude Code will show all account IDs you can access.

### Method 2: Google Ads UI

1. Log into [Google Ads](https://ads.google.com/)
2. Look at top right corner - shows account ID
3. Or check the URL: `ads.google.com/aw/overview?ocid=1234567890`
4. Copy the 10-digit number (without dashes)

### Method 3: MCC Account

If you have an MCC (manager account):
1. Log into the MCC
2. Click "Accounts" in left sidebar
3. See all sub-account IDs listed

## Best Practices

### 1. Use Descriptive Keys

❌ Bad: `"a1"`, `"a2"`, `"account3"`
✅ Good: `"scottish_shutters"`, `"client_abc"`, `"main_account"`

### 2. Include Common Variations in Aliases

```json
{
  "aliases": [
    "ABC Corporation",  // Full official name
    "ABC Corp",         // Short version
    "abc",              // Quick reference
    "client1",          // Internal reference
    "ABC"               // Abbreviation
  ]
}
```

### 3. Keep Notes Updated

```json
{
  "notes": "Primary account. Seasonal: high in Q4. Budget: $50k/mo. Contact: john@abc.com"
}
```

### 4. Organize by Business Unit

```json
{
  "ecommerce_us": { ... },
  "ecommerce_uk": { ... },
  "ecommerce_au": { ... },
  "leadgen_dental": { ... },
  "leadgen_legal": { ... }
}
```

## Security

### Safe to Commit

✅ **accounts.json is safe to commit to git**
- Contains only account IDs and metadata
- No credentials or secrets
- Just organizational information

### What NOT to Commit

❌ `google-ads.yaml` - Contains OAuth credentials
❌ `client_secrets.json` - Contains OAuth client secret
❌ `.env` files with tokens

## Updating Accounts

### Add a New Account

1. Find the account ID (methods above)
2. Add to `accounts.json`:

```json
{
  "new_client": {
    "id": "1111111111",
    "aliases": ["New Client", "newclient"],
    "name": "New Client Name",
    "currency": "USD",
    "timezone": "America/New_York"
  }
}
```

3. Save the file
4. Start using the aliases immediately - no restart needed!

### Remove an Account

Just delete the entry from the JSON file.

### Rename an Account

Update the `aliases` array:

```json
{
  "aliases": ["Old Name", "New Name", "nickname"]
}
```

## Multiple Projects

Each Claude Code project can have its own `accounts.json`:

```
~/brain/.claude/accounts.json           # Your main brain repo
~/client-work/.claude/accounts.json     # Client work repo
~/experiments/.claude/accounts.json     # Experimental repo
```

This lets you organize accounts differently per project.

## Auto-Update Agent (Coming Soon)

We're building an agent that will:
- Watch your conversations for account references
- Detect when you use new account names
- Automatically update `accounts.json`
- Suggest better aliases based on usage patterns

Stay tuned!

## Troubleshooting

### "Account not found"

Check:
1. Is the account ID correct? (10 digits, no dashes)
2. Do you have access to this account?
3. Is the alias spelled correctly?

Test access:
```
List my accessible Google Ads accounts
```

### "Invalid JSON"

- Use a JSON validator: https://jsonlint.com/
- Common issues:
  - Missing comma after an entry
  - Trailing comma on last entry (remove it)
  - Quotes around numbers (account IDs should be strings: `"1234567890"`)

### Claude Code Not Finding Aliases

1. Make sure `accounts.json` is in `.claude/` directory
2. Check JSON syntax is valid
3. Try using the full account ID instead to test MCP connection

## Example: Complete Setup

```json
{
  "main_business": {
    "id": "1234567890",
    "aliases": ["main", "primary", "My Business"],
    "name": "My Business Inc.",
    "currency": "USD",
    "timezone": "America/Los_Angeles",
    "notes": "Main business account - ecommerce focus"
  },
  "client_abc": {
    "id": "2345678901",
    "aliases": ["ABC", "abc corp", "client1"],
    "name": "ABC Corporation",
    "currency": "GBP",
    "timezone": "Europe/London",
    "notes": "UK client - B2B SaaS. Monthly budget: £25k"
  },
  "client_xyz": {
    "id": "3456789012",
    "aliases": ["XYZ", "xyz company", "client2"],
    "name": "XYZ Company Ltd",
    "currency": "AUD",
    "timezone": "Australia/Sydney",
    "notes": "Australian client - local services"
  },
  "test_account": {
    "id": "9999999999",
    "aliases": ["test", "sandbox", "dev"],
    "name": "Test Account",
    "currency": "USD",
    "timezone": "America/New_York",
    "notes": "Test account - for experiments only"
  }
}
```

---

**Quick Start:**
1. Copy `accounts_template.json` to `.claude/accounts.json` in your project
2. Replace account IDs with your actual account IDs
3. Update aliases to match how you refer to accounts
4. Save and start using friendly names!

Happy querying! 🚀
