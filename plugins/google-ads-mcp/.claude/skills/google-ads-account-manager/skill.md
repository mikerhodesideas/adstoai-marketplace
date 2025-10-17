# Google Ads Account Manager Skill

## Description
Manages the `.claude/accounts.json` file intelligently by detecting account references in conversation, suggesting updates, and keeping account aliases current.

## When to Use
- User mentions a Google Ads account by ID but no alias exists
- User refers to an account by a name not in accounts.json
- User asks to add, update, or remove account aliases
- Detecting confusion between account references

## Capabilities
1. **Auto-detect** account mentions in conversation
2. **Suggest aliases** based on how user refers to accounts
3. **Update accounts.json** with user approval
4. **Validate** account IDs against accessible accounts
5. **Improve usability** by learning user's naming patterns

## How It Works

### 1. Monitor Conversations

Watch for patterns like:
- "Show me campaigns for account 1234567890"
- "What's the performance for ABC Corp?"
- "Check my Scottish Shutters account"

### 2. Check accounts.json

- Does this account ID exist?
- Does this alias exist?
- Is there a mismatch?

### 3. Suggest Updates

If not found:
```
I notice you mentioned "ABC Corp" (account 1234567890).
Would you like me to add this to your accounts.json for easier reference?

Suggested entry:
{
  "abc_corp": {
    "id": "1234567890",
    "aliases": ["ABC Corp", "abc"],
    "name": "ABC Corporation"
  }
}

Should I add this?
```

### 4. Implement Changes

With approval, update `.claude/accounts.json` and confirm.

## Workflow

```
User mentions account → Check if exists in accounts.json
                           ↓
                    Not found?
                           ↓
                Query actual Google Ads for account name
                           ↓
                Suggest adding to accounts.json
                           ↓
                User approves
                           ↓
                Update file and confirm
```

## Example Interactions

### Example 1: New Account Detection

**User:** "Show me campaigns for 9882330267"

**Skill:**
```
I see you're querying account 9882330267. Let me check the account name...

This is "The Scottish Shutter Company" account. Would you like me to add
it to your accounts.json with a friendly alias like "Scottish Shutters"
or "SSC" for easier reference in future?
```

**User:** "Yes, use SSC"

**Skill:**
```
Added to accounts.json:
{
  "scottish_shutters": {
    "id": "9882330267",
    "aliases": ["SSC", "Scottish Shutters"],
    "name": "The Scottish Shutter Company"
  }
}

From now on, you can just say "Show campaigns for SSC" instead of the
account number.
```

### Example 2: Alias Suggestion

**User:** "What's the spend for my shutters account?"

**Skill:**
```
I don't have "shutters account" in your aliases yet. I found these accounts:
1. scottish_shutters (9882330267) - "The Scottish Shutter Company"
2. pool_man (7435598264) - "Mr Pool Man"

Did you mean "scottish_shutters"? Should I add "shutters account" as
an alias for it?
```

**User:** "Yes"

**Skill:**
```
Updated aliases for scottish_shutters:
- SSC
- Scottish Shutters
- shutters account (new)

You can now use any of these names to refer to this account.
```

### Example 3: Account Validation

**User:** "Add my client XYZ with account 1234567890"

**Skill:**
```
Checking access to account 1234567890...

Error: You don't have access to account 1234567890.

Your accessible accounts are:
- 9882330267
- 7435598264
- 5407170017

Please check the account ID or request access from the account owner.
```

## Commands

### Explicit Commands

Users can directly ask:

```
Add account 1234567890 as "Main Account"
Update aliases for SSC
Remove account abc_corp
Show all my account aliases
Validate my accounts.json
```

### Implicit Detection

Skill activates automatically when:
- Unknown account ID mentioned
- Unknown alias used
- Account confusion detected

## Implementation

The skill should:

1. **Read** `.claude/accounts.json`
2. **Monitor** conversation for account mentions
3. **Query** MCP to validate account IDs
4. **Suggest** updates proactively
5. **Update** file only with user approval
6. **Confirm** changes clearly

## Rules

### Always Ask Before Updating
Never modify accounts.json without explicit user approval.

### Validate Account Access
Always verify the user has access to an account ID before adding it.

### Suggest Smart Aliases
Based on:
- Official account name
- How user refers to it
- Common abbreviations
- Existing pattern in their accounts.json

### Handle Errors Gracefully
If accounts.json is invalid JSON, offer to fix it rather than breaking.

## File Location

Always work with: `.claude/accounts.json` in the current project root.

## Future Enhancements

- **Learn patterns**: Track which aliases users actually use
- **Auto-cleanup**: Remove unused aliases
- **Bulk import**: "Add all my accessible accounts"
- **Smart suggestions**: "You often say 'main' - should I add that alias?"
- **Currency/timezone auto-fill**: Query from Google Ads API

## MCP Tools Used

- `mcp__google-ads__list_accessible_accounts` - List accounts
- `mcp__google-ads__execute_gaql` - Get account details
- `Read` - Read accounts.json
- `Edit` - Update accounts.json

## Success Metrics

- Reduced friction in account references
- Fewer "account not found" errors
- More natural conversation flow
- Users learn to use aliases quickly

## Example accounts.json Structure

```json
{
  "scottish_shutters": {
    "id": "9882330267",
    "aliases": ["SSC", "Scottish Shutters", "shutters", "main"],
    "name": "The Scottish Shutter Company",
    "currency": "GBP",
    "timezone": "Europe/London"
  },
  "mr_pool_man": {
    "id": "7435598264",
    "aliases": ["MPM", "Pool Man", "pools"],
    "name": "Mr Pool Man",
    "currency": "AUD",
    "timezone": "Australia/Melbourne"
  }
}
```

---

**This skill makes Google Ads account management invisible - users just talk naturally and the system adapts.**
