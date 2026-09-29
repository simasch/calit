# Use Case: Handle Bookings

## Overview

**Use Case ID:** UC-014  
**Use Case Name:** Handle Bookings  
**Primary Actor:** Owner  
**Secondary Actors:** Invitee, Guest, Google Calendar  
**Requirements:** FR-036, FR-037, FR-038, FR-039, FR-040  
**Goal:** The Owner reviews upcoming bookings and approves, declines, reschedules, edits or cancels them.  
**Status:** Implemented

## Preconditions

- The Owner is signed in and has completed setup (UC-009).

## Main Success Scenario

1. Owner opens the dashboard.
2. System shows upcoming confirmed bookings, the number of pending requests and a warning when the mail server is currently unreachable or when any message on the instance was given up or expired undelivered (UC-019 A2, A3) and has not yet been purged (UC-022).
3. Owner opens the pending requests.
4. System lists bookings waiting for approval.
5. Owner approves a request.
6. System confirms the booking and creates its Google event.
7. System sends the Invitee a confirmation, invites the guests, notifies the Owner's channels and schedules a reminder.

## Alternative Flows

### A1: Approve from Email

**Trigger:** Owner uses the one-click approve or decline link in the request email (step 3)  
**Flow:**

1. System asks the Owner to sign in if needed and checks the link belongs to one of the Owner's bookings (A9).
2. If the booking is no longer pending, system shows that it was already handled and the use case ends.
3. Use case continues at step 6 (or A2 step 1 for decline).

### A2: Decline

**Trigger:** Owner declines a request (step 5)  
**Flow:**

1. System marks the booking declined, which frees the slot, and removes its unsent reminder.
2. System informs the Invitee and the Owner's channels.
3. Guests who were already sent an invitation (the booking was confirmed before an Invitee reschedule returned it to pending, UC-002 A4, or its details were edited, UC-002 A5) receive a cancellation; other guests receive nothing.
4. Use case ends.

### A3: Multi-Host Approval, Other Hosts Pending

**Trigger:** The booking belongs to a multi-host meeting type and another Host's share is still pending (step 6)  
**Flow:**

1. System marks this Host's share of the group confirmed; the other shares stay pending and the whole group keeps holding the slot.
2. No Google event is created and nobody is informed yet.
3. Use case ends.

### A4: Multi-Host Decline

**Trigger:** One Host declines a multi-host booking (step 5)  
**Flow:**

1. System declines the booking for every Host of the group.
2. System informs the Invitee and every Host's channels.
3. Use case ends.

### A5: Reschedule

**Trigger:** Owner opens a booking and picks a new slot (step 2)  
**Flow:**

1. System checks the new slot as for the Invitee (UC-002 BR-002).
2. System moves the booking and updates the Google event; a single-host booking is not sent back for approval (BR-004).
3. System informs all parties that the Owner rescheduled.
4. Use case ends.

### A6: Edit Details

**Trigger:** Owner edits the title, description or guests of a booking (step 2)  
**Flow:**

1. System applies the same rules as UC-002 A5.
2. Use case ends.

### A7: Cancel

**Trigger:** Owner cancels a booking (step 2)  
**Flow:**

1. System marks the booking cancelled, frees the slot and removes the Google event.
2. System informs the Invitee, the guests and the Hosts that the Owner cancelled.
3. Use case ends.

### A8: Google Event Cannot Be Created

**Trigger:** Creating the Google event fails (step 6)  
**Flow:**

1. System keeps the booking pending and shows an error.
2. Use case ends.

### A9: Approval Link Not Valid

**Trigger:** The email link does not belong to one of the Owner's bookings, or its secret does not match the booking (step 3)  
**Flow:**

1. System shows a not-found page without revealing whether the booking exists.
2. The booking is unchanged.
3. Use case ends.

### A10: Last Host Approves

**Trigger:** The booking belongs to a multi-host meeting type and this Host's approval leaves no share pending (step 6)  
**Flow:**

1. System marks this Host's share confirmed, which confirms the whole group.
2. System creates the one shared Google event on the organizer's calendar (UC-001 A11).
3. Use case continues at step 7.

### A11: Rescheduled Slot Not Available

**Trigger:** The slot the Owner picked in A5 is no longer free, or a Host's connected calendar cannot be read (step 2)  
**Flow:**

1. System refuses the move; for a single-host booking whose calendar cannot be read it shows that rescheduling is temporarily unavailable.
2. The booking keeps its time and nobody is informed.
3. Use case ends.

### A12: Multi-Host Reschedule of an Approval Type

**Trigger:** The Owner reschedules a multi-host booking of a meeting type that requires approval (step 2)  
**Flow:**

1. System moves every Host's share; the rescheduling Host's share stays confirmed and every other Host's share returns to pending.
2. System removes the shared Google event, sends the Invitee a "request sent" notice and every Host a new approval request; no reschedule notice is sent.
3. Use case ends.

## Postconditions

### Success Postconditions

- The booking is confirmed, declined, moved, edited or cancelled, and its Google event matches it.
- All parties have been informed.

### Failure Postconditions

- The booking is unchanged.

## Business Rules

### BR-001: Own Bookings Only

An Owner sees and acts only on bookings in their own calendar; any other booking is reported as not found.

### BR-002: Approval Link

The approve and decline links in the request email work only for the signed-in Owner of that booking.

### BR-003: Declining Frees the Slot

Declined and cancelled bookings no longer block time.

### BR-004: Owner Reschedule Keeps Approval

A single-host booking rescheduled by the Owner stays confirmed, even for a meeting type that requires approval. For a multi-host booking only the rescheduling Host's share stays confirmed; the other Hosts must approve again (A12).

### BR-005: Notification Opt-Out

When a Host has turned off booking emails, routine notices are not emailed to them. Approval requests of a multi-host booking are emailed to every Host regardless. A single-host Owner who turned off booking emails receives no approval request email; the request appears in the pending list and on the notification channels.

### BR-006: Only Pending Requests

From the email link, only a pending booking can be approved or declined; a request that was already approved, declined or expired is reported as already handled and stays unchanged. From the pending list, a Host's share of a multi-host booking that is no longer pending stays unchanged, but a single-host booking is set to approved or declined whatever its current state.
