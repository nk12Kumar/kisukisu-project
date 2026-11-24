# Tables and sessions

A **table** is not an account and not a room that lives forever. Someone opens it, people sit
down, and when the host ends it, everything said at it is meant to be gone.

## The lifecycle

| Step | What happens |
|---|---|
| Open | A code like `KUSA-7F2Q` is generated and shown. Nothing carries over from a previous table. |
| Join | A name and the code. The name is unique at that table while it's live, and bound to the joining device. |
| Live | Messages are encrypted on each device and relayed. The server holds ciphertext, not text. |
| End | The host ends it. Every device drops its copy; the server purges the room's events and files. |
| After | Nothing to reopen. There is no history, no archive, and no invite to revisit. |

## Why a session rather than a room

Conventional messengers treat a conversation as a growing archive: history accumulates, an
account persists, and the provider can see who talks to whom indefinitely. For a small group
that is mostly unnecessary risk.

A session also maps honestly onto the costs:

- **Offline delivery is bounded.** Messages reach people who are reachable while the table is
  open. There is no backlog to catch up on later, because there is nothing kept.
- **A new device starts blind.** It joins the conversation from that point, not from the
  beginning.
- **Screenshots are outside the model.** No system can recall a copy someone made.

## What "deleted" means

- **Deleted from the server**: verifiable. The host can inspect the database and see the events
  are gone — the advantage of owning the machine.
- **Deleted from other devices**: each client drops its in-memory copy and that session's
  cryptographic state. A modified client could keep its own copy; no protocol can prevent that.
- **Deleted from screenshots, photos, notes**: impossible. The confirmation dialog says so
  rather than implying otherwise.
- **Keys already handed out**: cannot be recalled. What protects the group is that the
  ciphertext those keys could unlock no longer exists anywhere.

## Retention as a backstop

A host may vanish without ending the table — a closed laptop, a dead disk, a forgotten app. A
retention policy (starting at seven days) purges tables nobody closed. Both mechanisms are
needed: an explicit end is immediate, retention is the safety net, and neither is trusted alone.

## Backups deliberately exclude session data

Backups cover server configuration and account records. Room events are not backed up, because
a backup would resurrect a conversation that was supposed to be deleted.
