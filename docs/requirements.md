# Requirements

Requirements catalog for **calit**, a self-hosted, multi-user scheduling application: every Owner publishes meeting
types on their own public page and Invitees book slots on them. Reverse-engineered from the use cases in
`docs/use_cases/`, the entity model, `CONTEXT.md`, the ADRs in `docs/adr/`, `README.md` and `CLAUDE.md`. Terms follow
`docs/glossary.md`.

All requirements below describe the behavior of the current implementation, so their status is `Implemented`.

## Functional Requirements

### Booking (Invitee and Guest)

| ID     | Title                        | User Story                                                                                                                                                         | Priority | Status      |
|--------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-001 | Browse Public Page           | As an Invitee, I want to see an Owner's bookable meeting types on their public page so that I can pick the kind of meeting I need.                                 | High     | Implemented |
| FR-002 | See Free Slots               | As an Invitee, I want to see the free slots of a meeting type up to its horizon in a chosen length so that I can pick a time that suits both of us.                | High     | Implemented |
| FR-003 | Book a Slot                  | As an Invitee, I want to book a slot with my name, email and answers to the booking questions, without an account, so that the meeting is agreed.                  | High     | Implemented |
| FR-004 | Add Guests                   | As an Invitee, I want to add up to 10 Guests to my booking so that other people receive the invitation too.                                                        | Medium   | Implemented |
| FR-005 | Request Approval             | As an Invitee, I want my booking of a meeting type that requires approval to hold its slot as a booking request so that nobody else takes the time while the Owner decides. | Medium   | Implemented |
| FR-006 | Book a Multi-Host Meeting    | As an Invitee, I want to book a meeting type with several Hosts so that one booking finds a time when all of them are free.                                        | Medium   | Implemented |
| FR-007 | Receive Confirmation         | As an Invitee, I want a confirmation with the location, a calendar entry and my manage link so that I can attend and change the booking later.                    | High     | Implemented |
| FR-008 | Reschedule Own Booking       | As an Invitee, I want to move my booking to another free slot through the manage link so that I can attend when my plans change.                                  | High     | Implemented |
| FR-009 | Edit Own Booking Details     | As an Invitee, I want to change the title, description and Guests of my booking through the manage link so that the meeting details stay current.                | Medium   | Implemented |
| FR-010 | Cancel Own Booking           | As an Invitee, I want to cancel my booking through the manage link at any time so that the Owner's time is freed.                                                 | High     | Implemented |
| FR-011 | Export Own Booking Data      | As an Invitee, I want to download the personal data a booking holds about me so that I can exercise my right of access.                                           | Medium   | Implemented |
| FR-012 | Erase Own Booking Data       | As an Invitee, I want to have my personal data erased from a booking so that I can exercise my right to erasure.                                                  | Medium   | Implemented |
| FR-013 | Decline Guest Invitation     | As a Guest, I want to decline an invitation through its decline link so that I stop receiving updates and the Invitee knows I cannot attend.                      | Medium   | Implemented |

### Accounts and Sign-In

| ID     | Title                          | User Story                                                                                                                                                  | Priority | Status      |
|--------|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-014 | Bootstrap Instance             | As an Operator, I want to create the first Site Administrator on a fresh instance so that the instance can be used without any default password.           | High     | Implemented |
| FR-015 | Sign Up                        | As a Visitor, I want to create my own Owner account when the Operator allows it so that I can publish a public page.                                      | Medium   | Implemented |
| FR-016 | Sign In with Password          | As an Owner, I want to sign in with my username and password, optionally remembered for 30 days, so that I can reach my management pages.                | High     | Implemented |
| FR-017 | Sign In with Google            | As an Owner, I want to sign in with my Google account so that I do not need a separate password.                                                           | Medium   | Implemented |
| FR-018 | Sign In with Single Sign-On    | As an Owner, I want to sign in through my organisation's identity provider so that my organisation controls access and administrator rights.             | Medium   | Implemented |
| FR-019 | Reset Password                 | As an Owner, I want to set a new password through an emailed link so that I can regain access when I forget it.                                           | High     | Implemented |
| FR-020 | Complete First-Login Setup     | As an Owner, I want to enter my display name, email and time zone on first sign-in so that my public page can go live with default working hours.        | High     | Implemented |
| FR-021 | Manage Account Settings        | As an Owner, I want to change my name, email, time zone, language, time format, booking-email preference, home redirect and retention period so that calit fits how I work. | Medium | Implemented |
| FR-022 | Export Account Data            | As an Owner, I want to download all data of my account without secrets so that I can exercise my right of access.                                          | Medium   | Implemented |
| FR-023 | Delete Own Account             | As an Owner, I want to delete my account and all its data so that calit no longer holds anything about me.                                                 | Medium   | Implemented |

### Meeting Types and Availability

| ID     | Title                        | User Story                                                                                                                                                             | Priority | Status      |
|--------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-024 | Create Meeting Type          | As an Owner, I want to create a meeting type with its allowed durations, buffers, minimum notice, horizon, cadence and location so that Invitees can book it.          | High     | Implemented |
| FR-025 | Configure Booking Form       | As an Owner, I want to define booking questions and whether the name and Guests fields are required, optional or hidden so that I collect what I need.               | Medium   | Implemented |
| FR-026 | Control Meeting Type Visibility | As an Owner, I want to mark a meeting type as secret or inactive so that I decide who can see and book it.                                                        | Medium   | Implemented |
| FR-027 | Require Approval             | As an Owner, I want a meeting type to require my approval so that I confirm each booking request before it becomes a meeting.                                          | Medium   | Implemented |
| FR-028 | Delete Meeting Type          | As an Owner, I want to delete a meeting type that has no upcoming bookings so that my list stays current.                                                             | Low      | Implemented |
| FR-029 | Set Working Hours            | As an Owner, I want to define weekly working hours, globally or for one meeting type, so that slots are offered only when I work.                                    | High     | Implemented |
| FR-030 | Set Date Overrides           | As an Owner, I want to replace my working hours for a specific date, or take the date off, so that exceptions are respected.                                        | High     | Implemented |

### Co-hosting

| ID     | Title                         | User Story                                                                                                                                                 | Priority | Status      |
|--------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-031 | Invite Co-hosts               | As a Creator, I want to invite up to 9 other Owners as Co-hosts of my meeting type so that a booking requires all of us to be free.                       | Medium   | Implemented |
| FR-032 | Remove Co-hosts               | As a Creator, I want to remove a Co-host, keeping or cancelling their upcoming bookings, so that the meeting type reflects who hosts it.                 | Medium   | Implemented |
| FR-033 | Respond to Co-host Invitation | As a Co-host, I want to accept or decline an invitation to co-host so that I only appear on meeting types I agree to.                                    | Medium   | Implemented |
| FR-034 | Configure Co-host Settings    | As a Co-host, I want to set my own working hours, date overrides, buffers and notification routing for a shared meeting type so that it fits my calendar. | Medium   | Implemented |
| FR-035 | Leave Meeting Type            | As a Co-host, I want to leave a shared meeting type so that I no longer host it.                                                                           | Low      | Implemented |

### Handling Bookings

| ID     | Title                        | User Story                                                                                                                                                   | Priority | Status      |
|--------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-036 | Review Upcoming Bookings     | As an Owner, I want a dashboard of my upcoming bookings, pending booking requests and mail problems so that I know what needs my attention.                | High     | Implemented |
| FR-037 | Approve or Decline Requests  | As an Owner, I want to approve or decline a booking request from the dashboard or with one click from the request email so that the Invitee gets an answer. | High     | Implemented |
| FR-038 | Reschedule a Booking         | As an Owner, I want to move a booking to another free slot without sending it back for approval so that I can resolve conflicts myself.                   | Medium   | Implemented |
| FR-039 | Edit a Booking               | As an Owner, I want to change a booking's title, description and Guests so that the meeting details stay current.                                        | Low      | Implemented |
| FR-040 | Cancel a Booking             | As an Owner, I want to cancel a booking so that the Invitee, the Guests and the Hosts are informed and the time is freed.                                 | High     | Implemented |

### Integrations and Notifications

| ID     | Title                          | User Story                                                                                                                                                           | Priority | Status      |
|--------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-041 | Connect Google Accounts        | As an Owner, I want to connect one or more Google accounts so that my Google calendars take part in scheduling.                                                     | Medium   | Implemented |
| FR-042 | Choose Busy Calendars          | As an Owner, I want to choose which calendars block my slots so that I am never booked over a commitment in another calendar.                                     | Medium   | Implemented |
| FR-043 | Choose Write Target            | As an Owner, I want to choose one write target, and a write override per meeting type, so that Google events land on the calendar I use.                          | Medium   | Implemented |
| FR-044 | Mirror Bookings to Google      | As an Owner, I want each confirmed booking mirrored as a Google event with a Google Meet link when the location asks for one so that my calendar stays complete.  | Medium   | Implemented |
| FR-045 | Manage Notification Channels   | As an Owner, I want to add chat or push notification channels and test them so that booking activity reaches me outside email.                                   | Low      | Implemented |
| FR-046 | Route Notifications            | As an Owner, I want to choose per meeting type which notification channels report its bookings so that each notice reaches the right place.                    | Low      | Implemented |

### Administration

| ID     | Title                          | User Story                                                                                                                                             | Priority | Status      |
|--------|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-047 | Invite Users                   | As a Site Administrator, I want to invite a new Owner by username and email so that they can activate their account with an emailed link.            | Medium   | Implemented |
| FR-048 | Manage Administrator Rights    | As a Site Administrator, I want to grant and revoke administrator rights so that more than one person can administer the instance.                   | Medium   | Implemented |
| FR-049 | Lock and Unlock Accounts       | As a Site Administrator, I want to lock and unlock accounts so that I can stop an account from signing in without deleting it.                       | Medium   | Implemented |
| FR-050 | Delete Accounts                | As a Site Administrator, I want to delete another Owner's account so that departed users leave no data behind.                                       | Medium   | Implemented |

### Background Work

| ID     | Title                          | User Story                                                                                                                                                         | Priority | Status      |
|--------|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------|
| FR-051 | Send Reminders                 | As an Invitee, I want a reminder before a confirmed booking starts so that I do not forget the meeting.                                                           | Medium   | Implemented |
| FR-052 | Retry Undelivered Mail         | As an Owner, I want messages that could not be sent to be retried and failures to be shown on my dashboard so that no notice is lost silently.                    | High     | Implemented |
| FR-053 | Expire Unanswered Requests     | As an Invitee, I want a booking request nobody answers within 24 hours to be declined so that I am not left waiting and the slot is released.                     | Medium   | Implemented |
| FR-054 | Monitor Google Connections     | As an Owner, I want to be told once when a connected account loses its access so that I can reconnect and unpause my public page.                               | Medium   | Implemented |
| FR-055 | Enforce Retention Period       | As an Owner, I want Invitee data of past meetings erased after my retention period so that I keep personal data no longer than needed.                          | Medium   | Implemented |
| FR-056 | Purge Technical Records        | As an Operator, I want delivered and undeliverable messages and expired reset and sign-in links purged after a fixed time so that the database does not keep them. | Low      | Implemented |

## Non-Functional Requirements

| ID      | Title                        | Requirement                                                                                                                                                                  | Category        | Priority | Status      |
|---------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|----------|-------------|
| NFR-001 | Owner Isolation              | No request of one Owner reads or writes a record of another Owner; a request for another Owner's booking or meeting type is answered with "not found".                     | Security        | High     | Implemented |
| NFR-002 | Stateless Replicas           | Any number of identical replicas serve the same instance; a sign-in made on one replica is valid on every other, and no state is kept outside the database.               | Scalability     | High     | Implemented |
| NFR-003 | Leaderless Background Work   | Every replica runs the Scheduler; a due reminder, message, request expiry or retention batch is processed by exactly one replica at a time, without leader election.       | Availability    | High     | Implemented |
| NFR-004 | No Double Booking            | Two pending or confirmed bookings of the same Host never overlap, also under concurrent requests on different replicas.                                                    | Availability    | High     | Implemented |
| NFR-005 | Works Without JavaScript     | Every feature, including booking, managing a booking and all management pages, is usable with JavaScript disabled; scripts only enhance.                                   | Usability       | High     | Implemented |
| NFR-006 | Languages                    | Every user-facing text is available in English, German and Hebrew, including right-to-left layout for Hebrew.                                                             | Usability       | Medium   | Implemented |
| NFR-007 | Password Storage             | Passwords are stored only as argon2id hashes; no default account or password ships with the instance.                                                                     | Security        | High     | Implemented |
| NFR-008 | Secrets Encrypted at Rest    | Google access grants and notification channel addresses are stored encrypted; notification channel addresses are never shown in full or written to logs.                 | Security        | High     | Implemented |
| NFR-009 | Capability Links             | Manage, decline, consent, approval and reset links carry an unguessable random token of at least 122 bits and render no link preview (ADR-0009).                          | Security        | High     | Implemented |
| NFR-010 | Mail Delivery                | A message that cannot be sent is retried after 2, 4, 8, 16 and 32 minutes and then hourly, up to 10 attempts; a booking never fails because of email.                    | Availability    | High     | Implemented |
| NFR-011 | Fail Closed on Calendar      | When a Host's connected account cannot be read, no slots are offered for that Host's meeting types rather than slots that may be busy.                                   | Availability    | High     | Implemented |
| NFR-012 | Abuse Protection             | Booking requires a solved human check when the Operator enables one, and one Invitee email makes at most 10 bookings per day by default.                                | Security        | Medium   | Implemented |
| NFR-013 | Sign-In Round Trip           | An external sign-in completes within 10 minutes; the one-time proof that finishes it is valid for 2 minutes and usable once.                                            | Security        | Medium   | Implemented |
| NFR-014 | Link Lifetimes               | A password reset link is valid for 30 minutes and an invitation link for 48 hours; each can be used once.                                                               | Security        | Medium   | Implemented |
| NFR-015 | Health Endpoints             | The instance exposes a liveness and a readiness endpoint for orchestrators.                                                                                               | Maintainability | Medium   | Implemented |
| NFR-016 | Audit Log                    | Every sign-in attempt and every account action (invite, grant, revoke, lock, unlock, delete) is written to the application log stream with the acting account.          | Security        | Medium   | Implemented |
| NFR-017 | Personal Data Inventory      | Every database column is classified in the personal data inventory, and a test fails when a column is added without classification (ADR-0010).                          | Maintainability | Medium   | Implemented |

## Constraints

| ID    | Title                  | Constraint                                                                                                                                                       | Category    | Priority | Status      |
|-------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|----------|-------------|
| C-001 | Runtime Platform       | The application runs on Quarkus 3 with Java 25 as a JVM image and as a GraalVM native image.                                                                   | Technical   | High     | Implemented |
| C-002 | Database               | All shared state lives in PostgreSQL; the schema is owned by versioned Flyway migrations and applied migrations are never edited.                               | Technical   | High     | Implemented |
| C-003 | Server-Rendered UI     | Pages are rendered on the server with Qute templates and styled by a self-hosted stylesheet; no asset is loaded from a runtime CDN.                             | Technical   | High     | Implemented |
| C-004 | Environment Config     | All production configuration is supplied through environment variables (12-factor); the session encryption key is at least 16 characters and identical on every replica. | Operational | High | Implemented |
| C-005 | Self-Hosted            | The Operator hosts the instance; calit depends on nothing hosted by its authors.                                                                               | Business    | High     | Implemented |
| C-006 | Optional Google        | Google Calendar, Google sign-in, single sign-on and the human check are optional; without them the instance runs in degraded mode.                | Technical   | High     | Implemented |
| C-007 | Container Distribution | Releases are published as multi-architecture container images to GitHub Container Registry and Docker Hub.                                                     | Operational | Medium   | Implemented |
| C-008 | Data Protection        | The instance supports the GDPR rights of access and erasure for Invitees and Owners without Operator involvement, unless the Operator disables self-service erasure. | Regulatory | High   | Implemented |
