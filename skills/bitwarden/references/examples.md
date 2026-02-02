# Bitwarden CLI Examples

## Quick Start

```bash
# Check if installed
bw --version

# Configure for Vaultwarden
bw config server https://vault.wu5154.synology.me

# Login
bw login clawdbot@example.com

# Unlock and capture session
export BW_SESSION=$(bw unlock --raw)

# Check status
bw status
```

## Retrieve Credentials

```bash
# Get password for a service
PASSWORD=$(bw get password "Rakuten")
echo "Password: $PASSWORD"

# Get username
USERNAME=$(bw get username "Rakuten")

# Get both username and password
ITEM=$(bw get item "Rakuten")
USERNAME=$(echo $ITEM | jq -r '.login.username')
PASSWORD=$(echo $ITEM | jq -r '.login.password')

# Get notes
NOTES=$(bw get notes "Rakuten")

# Get TOTP code (if configured)
TOTP=$(bw get totp "Rakuten")
```

## Search and Filter

```bash
# Search by name
bw list items --search rakuten

# Search in specific folder
bw list items --search github --folderid abc123

# Get all items in a folder
bw list items --folderid abc123

# Get items not in any folder
bw list items --folderid null
```

## Create New Items

```bash
# Create a simple login
bw get template item | \
  jq '.name="New Service" | .login.username="user@example.com" | .login.password="securepass123"' | \
  bw encode | \
  bw create item

# Create login with URI
bw get template item | \
  jq '.name="GitHub" | .login.username="user" | .login.password="pass" | .login.uris=[{match:null, uri:"https://github.com"}]' | \
  bw encode | \
  bw create item

# Create secure note
bw get template item | \
  jq '.type=2 | .secureNote.type=0 | .name="API Keys" | .notes="AWS_KEY=abc\nAWS_SECRET=xyz"' | \
  bw encode | \
  bw create item
```

## Password Generation

```bash
# Generate strong password (default: 14 chars)
bw generate

# Custom password
bw generate --length 32 --uppercase --lowercase --number --special

# Memorable passphrase
bw generate --passphrase --words 5 --separator _

# Password without special chars (for systems that don't support them)
bw generate --length 20 --uppercase --lowercase --number
```

## Update Items

```bash
# Change password
ITEM_ID="abc-123-def"
bw get item $ITEM_ID | \
  jq '.login.password="new-secure-password"' | \
  bw encode | \
  bw edit item $ITEM_ID

# Add/update notes
bw get item $ITEM_ID | \
  jq '.notes="Updated notes content"' | \
  bw encode | \
  bw edit item $ITEM_ID
```

## Automation Example

```bash
#!/bin/bash
# Script to retrieve credentials securely

# Ensure we're logged in and unlocked
STATUS=$(bw status | jq -r '.status')

if [ "$STATUS" = "unauthenticated" ]; then
  echo "Please login first: bw login"
  exit 1
fi

if [ "$STATUS" = "locked" ]; then
  echo "Vault is locked. Unlocking..."
  export BW_SESSION=$(bw unlock --raw)
fi

# Get credentials
RAKUTEN_USER=$(bw get username "Rakuten")
RAKUTEN_PASS=$(bw get password "Rakuten")

# Use them (example: pass to another command)
echo "Logging in as: $RAKUTEN_USER"
# some-command --user "$RAKUTEN_USER" --pass "$RAKUTEN_PASS"

# Lock when done
bw lock
```

## Using with Browser Automation

```bash
# Retrieve credentials for browser login
export BW_SESSION=$(bw unlock --raw)

USERNAME=$(bw get username "Rakuten Okta")
PASSWORD=$(bw get password "Rakuten Okta")

# Pass to Clawdbot browser automation
# (Clawdbot can use these variables to fill login forms)

bw lock
```

## Vault Management

```bash
# List all folders
bw list folders

# Create folder
bw get template folder | jq '.name="Work Accounts"' | bw encode | bw create folder

# Move item to folder
bw get item $ITEM_ID | jq '.folderId="folder-id-here"' | bw encode | bw edit item $ITEM_ID

# Sync vault
bw sync

# Export vault (backup)
bw export --output ~/backups/bitwarden-export.json --format json

# Export as CSV
bw export --output ~/backups/bitwarden-export.csv --format csv
```

## Secure Notes for Configuration

```bash
# Store API configuration as secure note
bw get template item | \
  jq --arg config "$(cat config.json)" \
  '.type=2 | .secureNote.type=0 | .name="App Config" | .notes=$config' | \
  bw encode | \
  bw create item

# Retrieve configuration
bw get notes "App Config" > restored-config.json
```

## Session Management Tips

```bash
# Save session to file (for reuse across terminals)
bw unlock --raw > ~/.bw-session
export BW_SESSION=$(cat ~/.bw-session)

# Auto-unlock function (add to ~/.bashrc or ~/.zshrc)
bw-unlock() {
  if [ -f ~/.bw-session ]; then
    export BW_SESSION=$(cat ~/.bw-session)
    if bw status | jq -e '.status == "unlocked"' > /dev/null; then
      echo "✅ Session restored"
      return 0
    fi
  fi
  
  echo "Unlocking vault..."
  export BW_SESSION=$(bw unlock --raw)
  echo $BW_SESSION > ~/.bw-session
  echo "✅ Vault unlocked"
}

# Lock and clear session
bw-lock() {
  bw lock
  rm -f ~/.bw-session
  unset BW_SESSION
  echo "🔒 Vault locked"
}
```
