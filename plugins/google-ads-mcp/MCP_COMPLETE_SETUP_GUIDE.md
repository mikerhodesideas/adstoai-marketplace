# Complete Google Ads MCP Setup Guide

**From scratch to working MCP server in ~20 minutes**

This guide walks you through setting up the Google Ads MCP server with a fresh GCP project to avoid any OAuth redirect URI issues.

---

## Prerequisites

- Google account with access to Google Ads
- Python 3.12+ installed
- `uv` or `pipx` for Python package management
- Terminal access

---

## Part 1: Create Fresh GCP Project (5 minutes)

### Step 1: Create New Project

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Click project dropdown at top → **"New Project"**
3. Project name: `google-ads-mcp` (or your choice)
4. Click **Create**
5. **Wait for creation, then SELECT the new project** from dropdown

### Step 2: Enable Google Ads API

1. Go to [APIs & Services > Library](https://console.cloud.google.com/apis/library)
2. Search: **"Google Ads API"**
3. Click it → Click **"Enable"**
4. Wait ~30 seconds for it to enable

---

## Part 2: Configure OAuth (10 minutes)

### Step 3: OAuth Consent Screen

1. Go to [OAuth consent screen](https://console.cloud.google.com/apis/credentials/consent)
2. Select **"External"** user type (unless you have Google Workspace)
3. Click **Create**

**App information:**
- **App name**: `Google Ads MCP` (or your choice)
- **User support email**: Your email
- **Developer contact email**: Your email

4. Click **Save and Continue**

**Scopes:**
5. Click **"Add or Remove Scopes"**
6. Search: `adwords`
7. Check: `https://www.googleapis.com/auth/adwords`
8. Click **Update** → **Save and Continue**

**Test users:**
9. Click **"+ Add Users"**
10. Add your Google Ads account email
11. Click **Add** → **Save and Continue**
12. Click **Back to Dashboard**

### Step 4: Create OAuth Client

**IMPORTANT: Must be Desktop app!**

1. Go to [Credentials](https://console.cloud.google.com/apis/credentials)
2. Click **"+ Create Credentials"** → **"OAuth client ID"**
3. Application type: **"Desktop app"** ⚠️ (NOT web app!)
4. Name: `Google Ads MCP Desktop`
5. Click **Create**
6. **Download JSON** or copy both:
   - Client ID
   - Client Secret
7. Save the JSON file or keep the popup open

**Why Desktop app?**
- No redirect URI configuration needed
- Works with localhost automatically
- No 400 errors from OAuth!

---

## Part 3: Get Google Ads Developer Token (2 minutes)

1. Go to [Google Ads](https://ads.google.com/)
2. Click **Tools & Settings** (wrench icon) → **Setup** → **API Center**
3. If no token exists:
   - Fill out the application
   - Test accounts get instant access
   - Production needs Google approval (~24-48 hours)
4. Copy your **Developer Token**

---

## Part 4: Clone & Setup MCP Server (3 minutes)

```bash
# Clone the repository
git clone https://github.com/google-marketing-solutions/google_ads_mcp.git
cd google_ads_mcp

# Install dependencies (uv will create a venv automatically)
uv pip sync

# Or if you don't have uv:
pip install google-ads
```

---

## Part 5: Generate Refresh Token (2 minutes)

### Download Authentication Script

```bash
curl -O https://raw.githubusercontent.com/googleads/google-ads-python/main/examples/authentication/generate_user_credentials.py
```

### Create Client Secrets File

Create `client_secrets.json` with your OAuth credentials:

```json
{
  "installed": {
    "client_id": "YOUR_CLIENT_ID.apps.googleusercontent.com",
    "client_secret": "GOCSPX-xxxxx",
    "auth_uri": "https://accounts.google.com/o/oauth2/auth",
    "token_uri": "https://oauth2.googleapis.com/token",
    "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
    "redirect_uris": ["http://localhost"]
  }
}
```

### Run OAuth Flow

```bash
python3 generate_user_credentials.py -c client_secrets.json
```

**What happens:**
1. Script prints a URL
2. Copy URL and paste in browser
3. Log in with your Google Ads account
4. Click **Allow**
5. Browser redirects and shows "Authorization successful"
6. Script outputs your **refresh token**
7. **Copy the refresh token!**

---

## Part 6: Create google-ads.yaml (1 minute)

Create `google-ads.yaml` in the `google_ads_mcp` directory:

```yaml
# OAuth2 Client ID from GCP
client_id: YOUR_CLIENT_ID.apps.googleusercontent.com

# OAuth2 Client Secret from GCP
client_secret: GOCSPX-xxxxx

# Refresh Token from authentication script
refresh_token: 1//0gxxxxx

# Developer Token from Google Ads API Center
developer_token: YOUR_DEVELOPER_TOKEN

# Required: Use proto-plus messages
use_proto_plus: True

# Optional: MCC account ID (no dashes)
# login_customer_id: 1234567890
```

**IMPORTANT:** Make sure `use_proto_plus: True` is included!

---

## Part 7: Test the Server

```bash
cd google_ads_mcp
uv run -m ads_mcp.server
```

**Success looks like:**
```
╭────────────────────────────────────────╮
│        Google Ads API                  │
│        FastMCP  2.0                    │
│        http://127.0.0.1:8000/mcp       │
╰────────────────────────────────────────╯

INFO: Started server process
INFO: Uvicorn running on http://127.0.0.1:8000
```

---

## Usage

### Using with Claude Code

Add to your Claude Code MCP settings (`~/.claude/mcp_settings.json`):

```json
{
  "mcpServers": {
    "GoogleAds": {
      "command": "uv",
      "args": [
        "run",
        "--directory",
        "/full/path/to/google_ads_mcp",
        "-m",
        "ads_mcp.server"
      ],
      "cwd": "/full/path/to/google_ads_mcp",
      "timeout": 30000
    }
  }
}
```

Replace `/full/path/to/google_ads_mcp` with your actual path.

### Using with Gemini CLI

```json
{
  "mcpServers": {
    "GoogleAds": {
      "command": "pipx",
      "args": [
        "run",
        "--spec",
        "git+https://github.com/google-marketing-solutions/google_ads_mcp.git",
        "run-mcp-server"
      ],
      "env": {
        "GOOGLE_ADS_CREDENTIALS": "/path/to/google-ads.yaml"
      },
      "timeout": 30000
    }
  }
}
```

### Available MCP Tools

The server provides these tools:

1. **list_accessible_accounts** - List all Google Ads accounts you can access
2. **execute_gaql** - Run Google Ads Query Language queries

### Example Queries

**List all campaigns:**
```
execute_gaql(
  query="SELECT campaign.id, campaign.name, campaign.status FROM campaign",
  customer_id="1234567890"
)
```

**Get campaign metrics:**
```
execute_gaql(
  query="SELECT campaign.name, metrics.clicks, metrics.impressions, metrics.cost_micros FROM campaign WHERE segments.date DURING LAST_30_DAYS",
  customer_id="1234567890"
)
```

---

## Troubleshooting

### "FileNotFoundError: Google Ads credentials YAML file is not found"
- Make sure `google-ads.yaml` is in the `google_ads_mcp` directory
- Or set environment variable: `export GOOGLE_ADS_CREDENTIALS=/path/to/google-ads.yaml`

### "ValueError: missing use_proto_plus key"
- Add `use_proto_plus: True` to your `google-ads.yaml`

### "OAuth 400 error: invalid_client"
- Make sure you created a **Desktop app** OAuth client, not Web app
- Web app requires redirect URI configuration and causes 400 errors

### "Invalid refresh token"
- Regenerate using the authentication script
- Make sure you're using the same Client ID/Secret

### "Developer token is invalid"
- Test accounts get instant access
- Production tokens require Google approval
- Check your token in Google Ads API Center

### "Access not configured"
- Make sure Google Ads API is enabled in your GCP project
- Check the OAuth consent screen has the `adwords` scope

---

## Security Notes

- **Never commit** `google-ads.yaml` or `client_secrets.json` to git
- Add to `.gitignore`:
  ```
  google-ads.yaml
  client_secrets.json
  *.json
  .env
  ```
- Refresh tokens don't expire but can be revoked
- Rotate credentials if compromised

---

## Quick Reference

| What | Where |
|------|-------|
| Create GCP project | https://console.cloud.google.com/ |
| Enable APIs | https://console.cloud.google.com/apis/library |
| OAuth credentials | https://console.cloud.google.com/apis/credentials |
| OAuth consent screen | https://console.cloud.google.com/apis/credentials/consent |
| Google Ads API Center | https://ads.google.com/aw/apicenter |
| Auth script | https://github.com/googleads/google-ads-python/blob/main/examples/authentication/generate_user_credentials.py |

---

## Summary Checklist

- [ ] Created fresh GCP project
- [ ] Enabled Google Ads API
- [ ] Configured OAuth consent screen with `adwords` scope
- [ ] Added yourself as test user
- [ ] Created **Desktop app** OAuth client
- [ ] Downloaded Client ID and Secret
- [ ] Got Google Ads Developer Token
- [ ] Cloned google_ads_mcp repo
- [ ] Created `client_secrets.json`
- [ ] Ran auth script and got refresh token
- [ ] Created `google-ads.yaml` with all credentials
- [ ] Added `use_proto_plus: True`
- [ ] Tested server with `uv run -m ads_mcp.server`
- [ ] Server started successfully!

---

**Time to complete:** ~20 minutes
**Difficulty:** Moderate
**Key insight:** Use Desktop app OAuth client to avoid redirect URI issues!

Good luck! 🚀
