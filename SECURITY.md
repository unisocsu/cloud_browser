# Security policy

This project exposes a remote browser desktop. Anyone who can reach an unprotected VNC/noVNC endpoint may control the browser and access its authenticated sessions.

- Always protect the Cloudflare hostname with Cloudflare Access.
- Keep `CLOUDFLARED_TUNNEL_TOKEN` and `VNC_PASSWORD` in GitHub Actions secrets.
- Never commit cookies, browser profiles, passwords, or tunnel tokens.
- Use a dedicated browser profile and avoid sensitive accounts.
- Stop the workflow when the session is no longer needed.
