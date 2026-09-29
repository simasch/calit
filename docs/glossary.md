# Glossary

One name per domain concept. Requirements, use cases, test cases and code use the **Term**; a word in **Avoid** is
not used for that concept. Definitions follow `CONTEXT.md` and the ADRs in `docs/adr/`; where this file and
`CONTEXT.md` differ, `CONTEXT.md` wins and this file is corrected.

## Roles

| Term               | Definition                                                                                                                                                             | Avoid                                   |
|--------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
| Owner              | The signed-in user whose time is booked and who configures meeting types, availability and settings. Every tenant record belongs to exactly one Owner.               | host user                       |
| Invitee            | The person who books an Owner's time. Has no login; everything calit holds about them lives on the booking.                                                        | booker, customer                |
| Guest              | An extra person the Invitee (or a Host) adds to a booking. Receives the invitation and may decline it, but did not make the booking.                                 | additional attendee, co-invitee         |
| Host               | An Owner who takes part in a meeting type and whose calendar a booking must fit: the Creator or an accepted Co-host. Availability and buffers are resolved per Host. |                                         |
| Creator            | The Host who owns a meeting type: it lives in their namespace, they configure it and they invite its Co-hosts.                                                      | primary host, main host                 |
| Co-host            | A Host invited onto another Owner's meeting type, which makes the type bookable under their page too and requires them to be free.                                  | guest host, secondary host              |
| Organizer          | The Host on whose connected account a booking's Google event is written. Always the Creator (ADR-0007); when the Creator has no connected account there is none and no Google event exists. |                   |
| Site Administrator | An Owner who additionally holds administrator rights and manages the accounts of the instance. Rights are granted locally or by the identity provider.               | superuser, root                         |
| Operator           | The person who installs and configures the instance (environment settings, mail, Google and sign-in integration). Not necessarily an account holder.                | sysadmin                                |
| Scheduler          | The background worker, run on every replica, that performs time-driven work: reminders, mail retries, request expiry, Google health checks and data retention.    | cron job, batch job                     |
| Visitor            | An anonymous person on the instance who is not yet an Owner, for example before signing up.                                                                        |                                         |

## Scheduling

| Term              | Definition                                                                                                                                                                        | Avoid                               |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| Meeting type      | A bookable offering of its Creator: allowed durations, cadence, availability, location, booking questions and approval policy. What an Invitee picks before choosing a slot.     | event type                          |
| Allowed durations | The set of lengths a meeting type may be booked at. The meeting type's own duration is its default and always a member (ADR-0003).                                               | duration options, duration range    |
| Slot              | A candidate start time of a meeting type, derived from working hours, date overrides, buffers, minimum notice, horizon and existing bookings. Computed, never stored.            | timeslot, opening                   |
| Cadence           | The spacing between consecutive slot starts of a meeting type, independent of the meeting length and anchored to the Creator's clock (ADR-0008).                                | granularity                         |
| Buffer            | Protected time a Host requires directly before or after a meeting. A constraint: the strictest applicable buffer governs (ADR-0002).                                             | padding, turnaround                 |
| Minimum notice    | The shortest time between now and the earliest start a meeting type offers.                                                                                                       |                                     |
| Horizon           | How many days ahead a meeting type offers slots.                                                                                                                                  | booking window                      |
| Working hours     | An Owner's weekly windows of availability per weekday, either global or specific to one meeting type. A meeting type's own working hours replace the global ones for that type. | office hours, opening hours         |
| Date override     | A replacement of the working hours for one calendar date, global or for one meeting type; an override without windows makes the date a day off.                                 | exception date, blackout            |
| Location          | Where a meeting happens: Google Meet, phone, in person or custom text. A property of the meeting type, never of a booking or a Host (ADR-0005).                                   | venue, room                         |
| Booking question  | A custom question an Invitee answers when booking, defined for one meeting type or as an Owner's default for types without their own.                                           | custom field                        |
| Secret meeting type | A meeting type that is not listed on the Owner's public page but can be booked through its link.                                                                              | hidden type, private type           |
| Approval          | The Owner's decision to confirm or decline a booking request of a meeting type that requires it.                                                                                 |                                     |

## Bookings

| Term              | Definition                                                                                                                                                                 | Avoid                           |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| Booking           | An agreement between an Owner and an Invitee to meet at a specific time, carrying its own length. The sole source of truth about whether a meeting exists (ADR-0001).    | appointment                     |
| Booking request   | A booking in the pending state: it holds its slot while it waits for approval, and expires if nobody answers in time.                                                    | tentative booking               |
| Group booking     | The set of per-Host bookings created by one booking of a multi-host meeting type. They share one Google event and are moved, cancelled and erased together.          | multi-booking                   |
| Manage link       | The private capability link that lets whoever holds it reschedule, edit, cancel, export or erase one booking without signing in (ADR-0009).                              | edit link, booking URL          |
| Decline link      | The private capability link that lets one Guest decline their invitation to a booking.                                                                                   | unsubscribe link                |
| Consent link      | The single-use link in a Co-host invitation that accepts co-hosting.                                                                                                     |                                 |
| Approval link     | The one-click link in a booking request email that approves or declines the request for the signed-in Owner.                                                            |                                 |
| Reminder          | A message sent to the participants of a confirmed booking a configured time before it starts.                                                                            |                                 |
| Erasure           | Anonymising a booking: the Invitee's and Guests' personal data are removed while the time slot and meeting type are kept.                                                | deletion (of a booking)         |
| Retention period  | The number of days after a meeting ends before its Invitee data is erased automatically; the Owner's period when set, otherwise the instance default, otherwise none.   | TTL                             |

## Google Calendar

| Term              | Definition                                                                                                                                                                      | Avoid                                 |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------|
| Google event      | The calendar entry calit creates in Google to mirror a booking. A mirror, never an authority over the booking (ADR-0001).                                                       |                                       |
| Connected account | One Google account an Owner has authorised, together with its access grant. An Owner may connect several.                                                                     |                                       |
| Busy calendar     | A calendar of a connected account that the Owner selected for busy-checking: its events block slots. The write target is always a busy calendar.                              | blocking calendar                     |
| Write target      | The one calendar, across an Owner's connected accounts, on which the Owner's new Google events are created by default.                                                        | primary calendar, default calendar    |
| Write override    | The Creator's choice of another selected calendar for one meeting type's Google events instead of the write target. Unset means the write target.                             | per-type calendar, type calendar      |
| Dangling override | A write override naming a calendar the Creator no longer has selected, or whose account was disconnected. It never fails a booking: the write falls back to the write target. | broken override, stale calendar       |
| Stored ref        | The calendar a booking's Google event was actually created on, recorded on the booking so later writes address the same calendar after the write target moves.               | original calendar, event calendar     |
| Degraded mode     | Running with no Google configured or connected. Every scheduling feature works; only Google events and Google busy times are absent, and messages carry calendar attachments. | no-Google mode, offline mode          |

## Accounts and Operations

| Term                 | Definition                                                                                                                                                  | Avoid                    |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Instance             | One installation of calit, run as one or more identical replicas that share one database.                                                                  | tenant                   |
| First-login setup    | The wizard a new Owner completes (display name, email, time zone) before their management pages and public page work.                                     | onboarding               |
| Locked account       | An account that may not sign in; its existing sign-ins stop working on their next request.                                                               | banned, suspended        |
| Notification channel | A chat or push destination (a webhook address) that receives an Owner's booking notices in addition to email.                                             | webhook                  |
| Default channel      | A notification channel that is notified for every meeting type for which the Owner selected no channels.                                                  |                          |
| Public page          | The Owner's page at `/{username}` listing their bookable meeting types. Never redirects.                                                                  | landing page             |
| Home                 | The instance entrance at `/`. A signed-in Owner is sent from here to their dashboard unless they turned that off (ADR-0011).                              |                          |
| Audit log            | The Operator's application log stream recording sign-ins and account actions. Not stored as account or booking data.                                      |                          |
| Outbox               | The queue of outgoing email messages, delivered and retried by the Scheduler.                                                                             | mail spool               |
