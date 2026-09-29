# Reverse-Engineering Notes

Things to know about `docs/use_cases.puml`, `docs/use_cases/UC-*.md` and `docs/entity_model.md`, which were derived from the code.

## Mapping choices in the entity model

- Time-of-day columns are `String(5)`, because the allowed type list has no time-only type.
- UUIDs are `String(36)`, and the stored booking answers are an unbounded `String`.
- Unbounded text has length `-`.
- Nullable foreign keys use `Optional`, and the reference goes in a Constraints line.
- Sign-in tickets, reset tokens, the email outbox and deleted usernames are included; delete them if you consider them purely technical.

## Endpoints not treated as use cases

Health checks, link-preview images, the language switch, legal pages, the product page, and the older JSON Google-calendar endpoints.

## Where the code looks inconsistent

The specs describe what the code does, except where the code is evidently wrong. There the spec states the
intended behaviour, and the gap is listed here, as a known defect in the code rather than a spec error:

- An inactive meeting type is hidden from the Owner's page but can still be booked by its direct link (spec: UC-010 BR-007, UC-001 A1).
- In-app approve and decline don't check that a single-host booking is still pending, so a declined or cancelled booking can be confirmed; the one-click email link does check (spec: UC-014 BR-006).
- Cancel through the manage link doesn't check status, so a cancelled or declined booking can be cancelled again and the cancellation emails go out again. Reschedule and edit are refused correctly (spec: UC-002 A7).
- A guest who was removed can still use their decline link, which flips them to declined and triggers emails (spec: UC-004 A3).
- Minimum notice, horizon, buffers and cadence have no server-side limits; negative values are stored, and a non-numeric cadence causes a server error (spec: UC-010 BR-009, A10).
- Sign-up accepts a blank or whitespace password; only the browser's `required` attribute stops it (spec: UC-006 A3).
- The first-login wizard saves a blank name or invalid email as-is; only the browser checks them (spec: UC-009 A4).
- Date override windows are not checked on the server: more than three are stored, and a window ending before it starts is stored (spec: UC-011 A5).
- A pending booking's approval deadline counts from `created_at`, so a booking an Invitee sends back for approval more than 24 hours after booking expires within a minute (spec: UC-020 BR-001).
- Automatic expiry doesn't send guests a cancellation after an Invitee reschedule sent a confirmed booking back for approval (spec: UC-020 BR-002).
- Invalid title or description edits through the manage link return a bare, untranslated error response instead of the manage page (spec: UC-002 A9).
- Calendar attachments always say "request", even on cancellation emails.
- Notification channels ignore the Owner's "no booking emails" setting.
- The retention setting is described in code as only able to shorten the period, but it can also lengthen it.
- A user created only through single sign-on shows as "Pending" in the user list and can be sent an invite.

## Review first

1. UC-014 (Handle Bookings) and UC-002 (Manage Own Booking): the multi-host approve, decline and reschedule behaviour is the hardest part to recover from code.
2. UC-007 (Sign In): the rules for linking and auto-creating accounts on Google and single sign-on.
3. UC-001 BR-001 to BR-007: the slot-calculation rules, which other use cases refer to.

Next step: run `/spec-review` for the semantic review; it will likely need a baseline because this is an existing project.
