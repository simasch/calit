# Use Case: Book a Meeting

## Overview

**Use Case ID:** UC-001  
**Use Case Name:** Book a Meeting  
**Primary Actor:** Invitee  
**Secondary Actors:** Owner, Google Calendar, Captcha Service  
**Requirements:** FR-001, FR-002, FR-003, FR-004, FR-005, FR-006, FR-007, FR-044  
**Goal:** The Invitee reserves a free slot of an Owner's meeting type, so that the meeting is agreed and both sides are informed.  
**Status:** Implemented

## Preconditions

- The Invitee has the address of an Owner's public page or of one of its meeting types.

## Main Success Scenario

1. Invitee opens the Owner's public page.
2. System lists the Owner's bookable meeting types with their lengths and location.
3. Invitee selects a meeting type.
4. System shows the meeting type with its free slots from today up to the booking horizon, in the Owner's time zone, for the default length.
5. Invitee optionally selects another allowed length.
6. System recalculates the free slots for that length.
7. Invitee selects a slot and enters their email, name, additional guests and answers to the booking questions.
8. Invitee passes the human check and submits the booking.
9. System validates the entries and confirms the slot is still free for every Host.
10. System stores the booking as confirmed, with a private manage link for the Invitee.
11. System creates the Google event on the Creator's calendar when a Google account is connected.
12. System sends a confirmation to the Invitee and the Owner, invitations to the guests, notifies the Owner's notification channels and schedules a reminder.
13. System shows the confirmation page with the location or video link and the manage link.

## Alternative Flows

### A1: Owner or Meeting Type Unknown

**Trigger:** The Owner does not exist or is locked (step 1), or the meeting type does not exist under the Owner's page (as Creator or accepted Co-host) or is inactive (step 3)  
**Flow:**

1. System shows a not-found page without revealing whether the account exists.
2. Use case ends.

### A2: Owner Has Not Finished Setup

**Trigger:** The Owner has not completed their settings (step 1)  
**Flow:**

1. System shows a page stating the booking page is not ready yet.
2. Use case ends.

### A3: A Host Has Not Accepted

**Trigger:** The meeting type has a Co-host who has not accepted yet, or a Host is locked (step 3)  
**Flow:**

1. System shows a page stating the meeting type is waiting for its hosts; no slots are offered.
2. Use case ends.

### A4: Calendar Temporarily Unavailable

**Trigger:** A connected Google account of a Host cannot be read (step 4)  
**Flow:**

1. System shows that booking is temporarily unavailable instead of offering possibly wrong slots.
2. Use case ends.

### A5: No Free Slots

**Trigger:** No slot satisfies the availability rules within the horizon (step 4)  
**Flow:**

1. System shows that no times are available.
2. Use case ends.

### A6: Invalid Entries

**Trigger:** Email, name, guests or answers violate a rule (step 9)  
**Flow:**

1. System shows the form again with the error message, keeping the chosen length.
2. Use case continues at step 7.

### A7: Human Check Failed

**Trigger:** The human check is missing or failed, or the hidden trap field was filled (step 9)  
**Flow:**

1. System rejects the submission and shows the form again with an error.
2. Use case continues at step 7.

### A8: Daily Limit Reached

**Trigger:** The Invitee's email has reached the daily booking limit (step 9)  
**Flow:**

1. System rejects the booking and explains that too many bookings were made today.
2. Use case ends.

### A9: Slot No Longer Free

**Trigger:** The slot was taken by someone else or became unavailable (step 9)  
**Flow:**

1. System shows the form again stating the time is no longer available.
2. Use case continues at step 4.

### A10: Approval Required

**Trigger:** The meeting type requires approval (step 10)  
**Flow:**

1. System stores the booking as pending; it holds the slot.
2. System tells the Invitee the request was sent and sends the Owner a request with one-click approve and decline links, unless the Owner turned off booking emails (UC-014 BR-005); guests are not invited yet.
3. For a multi-host meeting type, system stores one pending booking per Host, linked as a group, and sends every Host a request with approve and decline links for their own share, even a Host who turned off booking emails; each Host approves separately (UC-014 A3).
4. System notifies the Hosts' notification channels of the request.
5. System shows a "request sent" page with the manage link.
6. Use case ends.

### A11: Multi-Host Meeting Type

**Trigger:** The meeting type has Co-hosts (step 10)  
**Flow:**

1. System stores one booking per Host, linked as a group; guests belong to the Creator's booking.
2. System creates one shared Google event on the organizer's calendar: the Creator when the Creator has a connected Google account, otherwise the connected Host whose account is oldest; when no Host is connected, no Google event is created.
3. System sends every accepted Host a personal copy of the confirmation.
4. Use case continues at step 13.

### A12: Google Event Cannot Be Created

**Trigger:** Creating the Google event fails (step 11)  
**Flow:**

1. System discards the booking so no unmirrored booking remains.
2. System shows an error; the Invitee may try again.
3. Use case ends.

### A13: Email Cannot Be Delivered Now

**Trigger:** The mail system is unreachable (step 12)  
**Flow:**

1. System keeps the messages queued for later delivery (see UC-019).
2. System shows a warning on the confirmation page that the email may arrive late.
3. Use case continues at step 13.

## Postconditions

### Success Postconditions

- A booking exists with status confirmed (or pending when approval is required), the chosen length, the Invitee's answers, language and a private manage link.
- For a confirmed booking, each guest has an invitation with a private decline link; for a pending booking no guest is invited yet.
- A Google event mirrors the confirmed booking when the organizer (A11) has a connected account; a pending booking has no Google event.
- Confirmation or request messages are sent or queued; a reminder is scheduled for a confirmed booking only (UC-019 BR-001).

### Failure Postconditions

- No booking is stored and the slot stays free.
- No messages are sent.

## Business Rules

### BR-001: Working Hours

Slots are offered only inside a Host's working hours. A date override replaces the weekly hours for that date; an override without windows means the day is off. A meeting-type-specific override wins over a global one. If a meeting type has weekly hours of its own they replace the global weekly hours for the whole week.

### BR-002: Minimum Notice and Horizon

A slot must start no earlier than now plus the meeting type's minimum notice (default 0 minutes) and no later than the booking horizon (default 60 days).

### BR-003: Buffers

The protected time before and after the meeting must also be free. Per Host, the strictest buffer among the Host's own buffer and the chosen length's buffer applies; the meeting type's buffer applies only when neither is set.

### BR-004: Cadence

Slot starts are spaced by the meeting type's cadence, or by the shortest allowed length when no cadence is set. Changing the length does not move the offered start times.

### BR-005: All Hosts Must Be Free

For a multi-host meeting type a slot is offered only if it is free for every Host. The start times are anchored to the Creator's clock.

### BR-006: Busy Time

A Host is busy during their existing pending or confirmed bookings and, when a Google account is connected, during the busy times of the calendars selected for busy-checking. If a connected calendar cannot be read, no slots are offered.

### BR-007: No Double Booking

Two pending or confirmed bookings of the same Owner never overlap. A concurrent attempt for the same time is rejected.

### BR-008: Invitee Email

The email is required, at most 254 characters, contains exactly one "@" with text on both sides, a domain with a dot and no empty parts, and no spaces or commas.

### BR-009: Invitee Name

The name is at most 200 characters. When the meeting type makes it required a blank name is rejected; when it is optional and left blank, or hidden, the part of the email before "@" is used.

### BR-010: Booking Questions

Each answer is at most 2000 characters. Required questions must be answered. A meeting type with its own questions uses only those; otherwise the Owner's default questions apply.

### BR-011: Additional Guests

At most 10 guests per booking. Duplicates (ignoring case), the Invitee's own address and invalid addresses are silently dropped. When the meeting type hides the guests field, submitted guests are ignored.

### BR-012: Allowed Durations

The chosen length must be one the meeting type offers; an unknown length falls back to the default length. A length picker is shown only when more than one length is offered.

### BR-013: Human Check

When the instance enables a human check (Turnstile or a proof-of-work challenge), a booking without a valid solution is rejected. A filled hidden trap field is always rejected.

### BR-014: Daily Booking Limit

An Invitee email may make at most 10 bookings per day (configurable), counted across all Owners and regardless of status. The day runs from midnight to midnight in the time zone of the Owner whose meeting type is being booked. A multi-host booking counts once.

### BR-015: Approval

A booking of a meeting type that requires approval starts as pending and holds its slot until the Owner approves or declines it, or it expires (see UC-020).

### BR-016: Bookable Meeting Types

The public page lists active, non-secret meeting types, including multi-host types the Owner co-hosts. A secret meeting type is not listed but can be booked by its link. A multi-host type is bookable only when every Host has accepted and is not locked.

### BR-017: Google Event Mirror

The Google event mirrors the booking and never governs it. It lists the Invitee, the Hosts and the guests, and carries a video link only when the meeting type's location is Google Meet. For a multi-host meeting type it is written on the organizer's account as chosen in A11. When no Google account is connected, messages carry a calendar attachment instead.
