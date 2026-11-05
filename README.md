# Cloud Browser (GitHub Actions + Cloudflare Tunnel)

A proof-of-concept remote Chromium desktop. Chromium runs on a GitHub-hosted Ubuntu runner; noVNC exposes the desktop through a Cloudflare Tunnel. The runner is temporary, not a permanent server.

## What this first release does

- Starts Chromium on an Xvfb virtual display.
- Opens three tabs (URLs can be changed in `BROWSER_URLS`).
- Exposes the desktop through noVNC + WebSocket over a Cloudflare Tunnel.
- Uses a VNC password. **Protect the public hostname with Cloudflare Access as well.**

## Cloudflare setup (one time)

1. In Cloudflare Zero Trust, create a Cloudflare Tunnel.
2. Add a Public Hostname, e.g. `browser.example.com`, with service `http://localhost:6080`.
3. Protect that hostname with a Cloudflare Access application/policy. Do not publish the VNC desktop without Access.
4. Copy the tunnel token.

## GitHub setup

In the repository, open **Settings → Secrets and variables → Actions → New repository secret** and add:

- `CLOUDFLARED_TUNNEL_TOKEN`: token for the tunnel above.
- `VNC_PASSWORD`: a VNC password of 6–8 characters (classic VNC authentication truncates longer passwords).

Never commit either value.

## Start a browser session

1. Open **Actions → Cloud Browser Session → Run workflow**.
2. Wait for the setup steps to finish.
3. Open your protected hostname, e.g. `https://browser.example.com/vnc.html?autoconnect=true&resize=remote`.
4. Enter the VNC password when prompted.

The workflow runs for up to about 5 hours, then GitHub terminates the runner and the session ends. Starting another run creates a fresh browser profile. This is a prototype, not a guaranteed always-on service.

## Change the initial tabs

Set the repository variable `BROWSER_URLS` to a comma-separated list of up to three URLs. If unset, the workflow opens three blank tabs.

## Android 4.4 note

This prototype uses noVNC in the device browser for a quick compatibility test. Android 4.4's browser may be too old for current noVNC JavaScript/WebSocket behavior. If that happens, the next step is a small native Android client using a compatible VNC/RFB implementation or an H.264 player; this workflow does not yet provide H.264/WebRTC streaming.

## Security

A remote browser can access websites using your session and may expose authenticated data. Keep Cloudflare Access enabled, use a unique VNC password, do not share the URL, and stop the workflow when finished. Do not use this prototype for banking or other high-risk accounts.
