# Roadmap

Each phase ends with acceptance criteria that are demonstrated, not asserted. The next phase
does not begin on an unverified one.

## Phase 1 — Foundation and interface ✅

- Tauri 2 shell, React + TypeScript strict, Tailwind with one token set
- Original identity: ink canvas, lantern accent, peacock-feather mark, bundled fonts
- Screens: conversations, thread, details, people, security, settings
- Tables as sessions: open, share a code, end, and the conversation is gone
- Strict CSP, minimal capabilities, no third-party requests
- Windows installer and portable binary

**Acceptance:** every screen renders from 940 to 1600 px without clipping, both themes switch,
keyboard flow works without a mouse, and the security screen correctly states that no
encryption exists.

## Phase 2 — Homeserver and sessions

- Docker Compose: Synapse + PostgreSQL, bound to a private interface only
- A join service that mints a seat from a name + table code, no passwords
- Purge on end: table events and media deleted from the server
- Retention timer as the backstop for a host who disappears
- Federation off; public registration off; presence and typing off
- Log level with no identifiers; no session data in backups
- Reachability over a private mesh or a hardened public endpoint
- Encrypted backup procedure, restore rehearsed

**Acceptance:** two devices sit at one table; the server is unreachable from the public internet
as configured; ending the table leaves no readable event or file, verified by inspecting the
database; a restore from backup produces a working server.

## Phase 3 — Encryption and verification

- Matrix client layer: login, sync, room state, E2EE enabled at room creation
- Device keys in the OS-protected store
- Verification: safety number, compare, confirm, revoke
- No recovery key in the first release — losing the devices loses the history, and the
  interface says so before anyone depends on it

**Acceptance:** a message typed on machine A is unreadable in the server database and readable
on verified machine B; an unverified device is visibly blocked from decrypting; revocation
works.

## Phase 4 — Messaging

- Tables and whispers created from the client
- Offline queue and delivery on reconnect, bounded by the session
- Delivery and read receipts, replies, reactions
- Contact list from server-side account data

**Acceptance:** messages survive a network cut while the table is open; receipts and reactions
survive a restart; nothing survives the table ending.

## Phase 5 — Encrypted attachments

- Local encryption before upload, download and decrypt on demand
- Size limits, progress, cancel, failure handling
- Previews generated from decrypted bytes only

**Acceptance:** the server holds only ciphertext; a file shared between two machines opens
correctly and is unusable to anyone else, including the server operator.

## Phase 6 — Notifications, search, polish

- Desktop notifications for background messages, with no third-party push service
- Local search over decrypted history within the live session only
- Empty states, error states, keyboard navigation, accessibility pass

**Acceptance:** a notification never leaks message content on a lock screen; search touches
only locally decrypted data.

## Phase 7 — Release

- Unit and integration tests for the client layer
- Security review: CSP, capabilities, key handling, logging audit
- Windows installer, Linux package
- Install, update and backup guides written for non-technical readers

**Acceptance:** someone who has never seen the project can install it, open a table, and hold a
verified conversation by following the guides alone.

## Deliberately out of scope

- **Voice and video calls** — they widen the attack surface and metadata footprint well beyond
  what a few friends need.
- **Federation** — sharing metadata with other servers defeats the point of a private table.
- **Public registration** — tables are joined by code, never by signing up.
- **A custom protocol** — the one shortcut this project will not take.
