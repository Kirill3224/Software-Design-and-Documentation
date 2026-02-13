## Part 4 — ADR

# ADR-001: Soft Delete Strategy for Moderated Content

## Status

Accepted

## Context

When a user reports a message and a moderator decides to ban it, we need to decide how to handle the data in the database. We can either permanently delete the record (Hard Delete) or mark it as hidden (Soft Delete).

## Decision

We will use **Soft Delete** mechanism.
When a message is banned, we will update its status to `HIDDEN` and clear the `content` payload for regular users, but keep the original data in a separate secure audit log or restrict access via flags.

## Consequences

### Positive

- **Audit Trail:** We preserve evidence of the violation in case of legal disputes or appeals.
- **Restoration:** False positives (mistaken bans) can be easily reversed by changing the flag back to `ACTIVE`.
- **Consistency:** References to this message ID in other tables (e.g., replies, threads) will not break.

### Negative

- **Storage:** The database grows even with "deleted" content.
- **Complexity:** All read queries must explicitly filter out `status = 'HIDDEN'`.
