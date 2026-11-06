# Cloud Browser (GitHub Actions + Cloudflare Quick Tunnel)

A proof-of-concept remote Chromium desktop. Chromium runs on a GitHub-hosted Ubuntu runner; noVNC exposes the desktop through a Cloudflare Tunnel. The runner is temporary, not a permanent server.

## What this first release does

- Starts Chromium on an Xvfb virtual display.
- Opens three tabs (URLs can be changed in `BROWSER_URLS`).
- Exposes the desktop through noVNC + WebSocket over a temporary Cloudflare Quick Tunnel (`trycloudflare.com`).
- Uses VNC password authentication to access the desktop. The temporary public URL itself is not protected by Cloudflare Access.

## Setup

No domain, DNS record, named Cloudflare Tunnel, or Cloudflare account token is required. The workflow starts a temporary Cloudflare Quick Tunnel automatically and prints the generated `https://….trycloudflare.com` URL in the workflow run summary.

In GitHub repository **Settings → Secrets and variables → Actions**, add only:

- `VNC_PASSWORD`: a unique password of 6–8 characters (classic VNC authentication truncates longer passwords).

Never commit the password. Do not add or share a Cloudflare tunnel token for this Quick Tunnel setup.

## Start a browser session

1. Open **Actions → Cloud Browser Session → Run workflow**.
2. Wait for **Start Cloudflare Quick Tunnel** to finish.
3. Open the URL shown in the run summary, followed by `/vnc.html?autoconnect=true&resize=remote`.
4. Enter the VNC password when prompted.

The Quick Tunnel URL changes on each run. Anyone who obtains the URL can reach the noVNC page, but must still pass VNC authentication to control the desktop. Keep the URL private and stop the workflow when finished.

The workflow runs for up to about 5 hours, then GitHub terminates the runner and the session ends. Starting another run creates a fresh browser profile. This is a prototype, not a guaranteed always-on service.

## Change the initial tabs

Set the repository variable `BROWSER_URLS` to a comma-separated list of up to three URLs. If unset, the workflow opens three blank tabs.

## Android 4.4 note

This prototype uses noVNC in the device browser for a quick compatibility test. Android 4.4's browser may be too old for current noVNC JavaScript/WebSocket behavior. If that happens, the next step is a small native Android client using a compatible VNC/RFB implementation or an H.264 player; this workflow does not yet provide H.264/WebRTC streaming.

## Security

A remote browser can access websites using your session and may expose authenticated data. Keep Cloudflare Access enabled, use a unique VNC password, do not share the URL, and stop the workflow when finished. Do not use this prototype for banking or other high-risk accounts.
