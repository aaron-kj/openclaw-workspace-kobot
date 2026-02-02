# TOOLS.md - Local Notes

Skills define *how* tools work. This file is for *your* specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:
- Camera names and locations
- SSH hosts and aliases  
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras
- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH
- home-server → 192.168.1.100, user: admin

### TTS
- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

### Vaultwarden
- URL: https://vault.wu5154.synology.me
- Access: via Tailscale (macOS app handles routing transparently)
- Account: ccw220@yahoo.com
- Note: Tailscale installed via macOS app (NOT Homebrew) - system-level network routing

### Network
- Tailscale tailnet active on this machine
- No need for Clawdbot to manage Tailscale - macOS handles it
- Private services accessible through Tailscale routing

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

Add whatever helps you do your job. This is your cheat sheet.
