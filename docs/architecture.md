# Architecture

## Shape of the system

```
┌──────────────────────────┐        ┌──────────────────────────┐
│  KusuKusu desktop        │        │  KusuKusu desktop        │
│  Windows / Linux         │        │  Windows / Linux         │
│                          │        │                          │
│  React interface         │        │  React interface         │
│  Matrix client layer  ───┼────────┼── Matrix client layer    │
│  Rust shell (Tauri 2)    │        │  Rust shell (Tauri 2)    │
└────────────┬─────────────┘        └─────────────┬────────────┘
             │  HTTPS (TLS), ciphertext only      │
             └──────────────┬────────────────────┘
                            │
                  ┌─────────▼──────────┐
                  │  Synapse           │  self-hosted, no federation,
                  │  + PostgreSQL      │  no public registration,
                  │  (Docker Compose)  │  session retention + purge
                  └────────────────────┘
```

The client is configured with a server address, so the same build works against a laptop on a
private network or a rented machine. Moving the server does not rebuild the client.

## Layers, and why

| Layer | Responsibility | Rule |
|---|---|---|
| Screens | Interaction and layout | Knows nothing about Matrix types |
| Client state | Sessions, formatting, preview data | Replaceable by the Matrix layer without touching screens |
| Matrix layer (Phase 3) | Session, sync, E2EE, verification | The only code that speaks to the homeserver |
| Rust shell | Native window, OS-protected key storage, file writes | No UI logic, no message parsing |

The seam that matters is between screens and messaging. Phase 1 keeps state in a single module so
Phase 3 can swap its body for a server-backed store without rewriting a component.

## Technology choices

- **Tauri 2 over Electron** — a native WebView gives a ~2 MB installer instead of ~120 MB, and
  the Rust side is available for key storage and file access.
- **React + TypeScript strict** — strictness catches the class of bug that costs privacy: a
  missing branch, an optional value used as if present.
- **Tailwind with CSS custom properties** — one token set drives both themes, no runtime
  CSS-in-JS, no external stylesheet requests.
- **Matrix + Synapse** — a maintained E2EE stack and a self-hostable server. Designing a
  protocol instead is how projects end up with unreviewable cryptography.
- **PostgreSQL** — Synapse's supported production database.

## Security posture in the shell

- Strict Content-Security-Policy: `default-src 'self'`, no inline script, no remote origins,
  `object-src 'none'`, `base-uri 'none'`, `frame-ancestors 'none'`.
- Capabilities limited to window basics — no filesystem or shell access from the interface.
- Fonts bundled locally; no third-party requests of any kind.
- No telemetry, no analytics, no crash reporting service.

## Where the server can live

| Option | Cost | Who sees metadata | Always on |
|---|---|---|---|
| Home machine, same network | 0 € | nobody | no |
| Home machine over a private mesh | 0 € | the mesh operator, not the relay | while the PC is |
| Free-tier cloud VM | 0 € | the cloud provider | yes |
| Small rented server | ~5 €/month | the host | yes |

Same Compose stack in all four; only the address in the client changes. Rationale and the
options that were ruled out: [decisions.md](decisions.md).
