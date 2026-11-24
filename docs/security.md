# Security state

This file is the honest record, updated at every phase. It never describes something as done
before it has been tested.

## Today: Phase 1

**There is no encryption in this build, and no network connection.** The application is an
interface. Concretely:

| Claim | True today? |
|---|---|
| Messages are encrypted | **No** — there is no crypto code at all |
| Messages leave the device | **No** — there is no networking code |
| Messages are stored | **No** — they live in memory and vanish when the window closes |
| The server can read anything | There is no server |
| Safety numbers are real | **No** — the numbers shown are labelled *Sample* |
| Attachments are encrypted | **No** — attachments do not exist yet |

The interface states this in four places — the header pill, the conversation details, the
security screen, and the end-of-table dialog — so a screenshot cannot be mistaken for a claim.

The only data written to disk is two interface preferences (theme, motion).

## What the design is aiming at

### What the server will be able to see

Even perfect end-to-end encryption leaves a homeserver able to see **metadata**: which accounts
exist, which rooms they're in, when events were sent, sizes, and connecting IP addresses. That
is inherent to any relay and is stated rather than hidden. The goal is therefore:

- **content** unreadable to the server — including attachments;
- **metadata** on infrastructure the group owns, not a provider's;
- metadata minimised (no presence, no typing, no push services) and retained only as long as
  delivery requires.

### Encryption

- Matrix's established E2EE stack (Olm/Megolm via a maintained SDK). No primitives written by
  hand, no home-made protocol.
- Encryption on the sender's device before upload; decryption only inside an authorised,
  verified device.
- Attachments encrypted locally, uploaded as ciphertext.
- Private keys in the operating system's protected store — never in the repository, never in a
  log, never on the server.

### Device verification

Verification is a comparison, not a promise: the same safety number must appear on both
devices, checked in person or over a separate channel. Anything unverified is shown as
unverified everywhere that person appears. Revocation removes a device's ability to decrypt
future messages.

### Recovery and the "nothing stored" tension

A recovery key makes history readable on a new device; "no history kept" argues against having
one. KusuKusu resolves this by having no recovery key in the first release: lose the devices,
lose the history, and the interface says so before anyone relies on it.

### Logging

Never logged: message bodies, attachment names or contents, passwords, access tokens, key
material, room names. What may be logged: errors without payloads, connection failures, version
numbers — safe to paste into a support message.

## Testing standard

Before any security claim appears in the interface or these documents, the flow is exercised on
two separate machines with real accounts: send, receive offline, verify, revoke, re-verify,
restore from backup, and end a table and confirm the server no longer holds its events.
"It compiled" is not evidence.
