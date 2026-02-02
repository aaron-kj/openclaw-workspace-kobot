---
name: bitwarden
description: Set up and use Bitwarden CLI (bw). Use when installing the CLI, signing in, or reading/injecting secrets from Bitwarden or self-hosted Vaultwarden.
homepage: https://bitwarden.com/help/cli/
metadata: {"clawdbot":{"emoji":"🔐","requires":{"bins":["bw"]},"install":[{"id":"brew","kind":"brew","formula":"bitwarden-cli","bins":["bw"],"label":"Install Bitwarden CLI (brew)"}]}}
---

# Bitwarden CLI

Bitwarden CLI (`bw`) provides command-line access to Bitwarden and self-hosted Vaultwarden vaults.

## Installation

```bash
# macOS
brew install bitwarden-cli

# Verify installation
bw --version
```

## Configuration

### For Self-Hosted Vaultwarden

Before logging in, configure the server URL:

```bash
# Point to your Vaultwarden instance
bw config server https://vault.example.com
```

For official Bitwarden cloud, this step is not needed.

## Authentication & Session Management

### Login

```bash
# Interactive login (prompts for email and master password)
bw login

# Login with email (then prompts for password)
bw login user@example.com
```

After successful login, you must unlock to get a session key.

### Unlock

```bash
# Unlock vault and get session key
bw unlock
```

This returns export commands for BW_SESSION. Copy and run the appropriate one:

```bash
# Bash/ZSH
export BW_SESSION="your-session-key-here"

# PowerShell
$env:BW_SESSION="your-session-key-here"
```

### Using Session Keys

**Option 1: Environment variable (recommended)**
```bash
export BW_SESSION="session-key"
bw list items
```

**Option 2: Pass as option**
```bash
bw list items --session "session-key"
```

### Lock & Logout

```bash
# Lock vault (clears session)
bw lock

# Logout completely
bw logout
```

## Common Operations

### List Items

```bash
# List all items
bw list items

# List folders
bw list folders

# List collections
bw list collections

# Search items
bw list items --search github

# Filter by folder
bw list items --folderid <folder-id>
```

### Get Specific Item

```bash
# Get item by ID
bw get item <item-id>

# Get password by name/search
bw get password Github

# Get username by name/search
bw get username Github

# Get notes
bw get notes <item-id>

# Get TOTP code
bw get totp <item-id>
```

### Create Items

```bash
# Create a login item
bw get template item | jq \
  '.name="My Login" | .login.username="user@example.com" | .login.password="mypassword"' | \
  bw encode | \
  bw create item

# Create a folder
bw get template folder | jq '.name="My Folder"' | bw encode | bw create folder

# Create a secure note
bw get template item | jq \
  '.type=2 | .secureNote.type=0 | .name="My Note" | .notes="Note contents"' | \
  bw encode | \
  bw create item
```

### Edit Items

```bash
# Edit password
bw get item <item-id> | jq '.login.password="newpassword"' | \
  bw encode | \
  bw edit item <item-id>
```

### Delete Items

```bash
# Send to trash (default)
bw delete item <item-id>

# Permanent delete
bw delete item <item-id> --permanent
```

### Generate Passwords

```bash
# Default: 14 chars with uppercase, lowercase, numbers
bw generate

# Custom length and complexity
bw generate --length 20 --uppercase --lowercase --number --special

# Generate passphrase
bw generate --passphrase --words 4 --separator -
```

## Sync

```bash
# Sync vault from server
bw sync

# Check last sync time
bw sync --last
```

## Status

```bash
# Check vault status
bw status
```

Returns JSON with:
- `serverUrl`: Configured server
- `lastSync`: Last sync timestamp
- `userEmail`: Logged-in user
- `status`: "unlocked" | "locked" | "unauthenticated"

## Security Best Practices

1. **Never log or expose session keys** - They decrypt your entire vault
2. **Always lock when done** - Use `bw lock` to clear the session
3. **Use environment variables** - Store BW_SESSION in env, not in scripts
4. **Secure master password** - Never hardcode or log it
5. **Prefer `--nointeraction`** - For automation, use API keys or pre-set BW_SESSION

## Workflow Example

```bash
# 1. Configure server (if self-hosted)
bw config server https://vault.wu5154.synology.me

# 2. Login
bw login user@example.com

# 3. Unlock and set session
export BW_SESSION=$(bw unlock --raw)

# 4. Use the vault
PASSWORD=$(bw get password github)

# 5. Lock when done
bw lock
```

## Item Types

Use with `.type` in JSON:

- Login: `1`
- Secure Note: `2`
- Card: `3`
- Identity: `4`

## Troubleshooting

**"You are not logged in"**
```bash
bw login
```

**"Vault is locked"**
```bash
bw unlock
export BW_SESSION="<session-key>"
```

**"Invalid master password"**
- Verify correct server URL with `bw config server`
- Ensure correct account credentials

**Session expired**
```bash
bw unlock  # Re-unlock to get new session key
```

## References

- Official CLI docs: https://bitwarden.com/help/cli/
- API reference: https://bitwarden.com/help/vault-management-api/
- Vaultwarden (self-hosted): https://github.com/dani-garcia/vaultwarden
