# Use Case: Manage Own Booking

## Overview

**Use Case ID:** UC-002  
**Use Case Name:** Manage Own Booking  
**Primary Actor:** Invitee  
**Secondary Actors:** Owner, Guest, Google Calendar  
**Requirements:** FR-008, FR-009, FR-010  
**Goal:** The Invitee reschedules, edits or cancels their booking through its private manage link, without an account.  
**Status:** Implemented

## Preconditions

- The Invitee holds the private manage link from the confirmation page or email.
- The booking exists and its personal data has not been erased.

## Main Success Scenario

1. Invitee opens the manage link.
2. System shows the booking's time, meeting type and details, a slot grid for the booked length, an edit form, a cancel option, a calendar download and the personal-data options (UC-003).
3. Invitee selects a new slot.
4. System confirms the new slot is free for every Host, excluding the booking being moved.
5. System moves the booking, keeping its length, and updates the Google event.
6. System sends a reschedule notice to the Invitee, the Hosts and the guests and notifies the Hosts' notification channels.
7. System shows the updated booking.

## Alternative Flows

### A1: Unknown or Erased Booking

**Trigger:** The link does not match a booking, or the booking's data was erased (step 1)  
**Flow:**

1. System shows a not-found page.
2. Use case ends.

### A2: Host Locked

**Trigger:** A Host of the booking is locked (step 2)  
**Flow:**

1. System shows the booking with a notice that the Host no longer takes changes; rescheduling and editing are not offered, and a submitted change is ignored, so only cancelling (A6) and the calendar download (A8) remain.
2. Use case continues at step 3.

### A3: Slot Not Available

**Trigger:** The new slot is no longer free (step 4)  
**Flow:**

1. System rejects the move and the booking keeps its time.
2. Use case continues at step 3.

### A4: Approval Required

**Trigger:** The meeting type requires approval (step 5)  
**Flow:**

1. System moves the booking and sets it back to pending; for a multi-host booking every Host's share returns to pending.
2. System removes the Google event, whose attendees are told of the removal by Google, and the unsent reminder.
3. System sends the Invitee a "request sent" notice and every Host a new approval request (UC-014), and notifies the Hosts' notification channels; no reschedule notice is sent.
4. Guests still on the list receive nothing until the booking is approved (UC-014 step 7), declined (UC-014 A2) or expires (UC-020).
5. Use case continues at step 7.

### A5: Edit Details

**Trigger:** Invitee chooses to edit the meeting title, description or guest list (step 3)  
**Flow:**

1. Invitee submits the changed title, description and guests.
2. System validates the entries; blank title or description fall back to the meeting type's values.
3. System records new guests, cancels the invitation of removed guests and updates the Google event when one exists.
4. System sends an "updated" notice to the Invitee and the Hosts and an updated invitation to every guest on the list, also while the booking is still pending.
5. Use case continues at step 7.

### A6: Cancel Booking

**Trigger:** Invitee chooses to cancel (step 3)  
**Flow:**

1. System asks for confirmation.
2. Invitee confirms.
3. System marks the booking cancelled, frees the slot, removes the Google event and the pending reminder.
4. System sends a cancellation to the Invitee, the Hosts and the guests.
5. Use case ends.

### A7: Already Cancelled or Declined

**Trigger:** The booking is already cancelled or declined (step 2)  
**Flow:**

1. System shows that the booking is no longer active; changes are refused.
2. Use case ends.

### A8: Download Calendar Entry

**Trigger:** Invitee chooses to download the calendar entry (step 3)  
**Flow:**

1. System provides a calendar file for the booking.
2. Use case continues at step 2.

### A9: Invalid Details

**Trigger:** A title or description submitted through A5 breaks BR-004 (step 3)  
**Flow:**

1. System rejects the change with an error message; nothing is truncated.
2. Use case ends.

### A10: Calendar Temporarily Unavailable

**Trigger:** A connected Google account of a Host cannot be read while checking the new slot (step 4)  
**Flow:**

1. For a single-host booking, system shows that rescheduling is temporarily unavailable; for a multi-host booking, system treats the slot as not free (A3).
2. The booking keeps its time.
3. Use case ends.

### A11: Google Event Cannot Be Updated

**Trigger:** Updating or removing the Google event fails (step 5)  
**Flow:**

1. System shows an error and the booking keeps its time; no notice is sent.
2. Use case ends.

## Postconditions

### Success Postconditions

- The booking has its new time (or new details, or is cancelled), and its Google event matches it.
- The calendar invitation revision is increased so calendar clients replace the old entry.
- All parties have been informed.

### Failure Postconditions

- The booking is unchanged.

## Business Rules

### BR-001: The Link Is the Authority

Whoever holds the manage link may manage the booking; no sign-in is required. The link stops working once the booking's personal data is erased.

### BR-002: Rescheduling Keeps the Length

Rescheduling moves a booking, never resizes it. The new slot is checked with the same rules as a new booking (UC-001 BR-001 to BR-007). Choosing the same time with unchanged guests changes nothing.

### BR-003: Group Bookings Move Together

Rescheduling, editing or cancelling a multi-host booking applies to every Host's booking of the group and to the one shared Google event.

### BR-004: Title and Description Limits

The meeting title is at most 200 characters and the description at most 2000 characters.

### BR-005: Guest Changes

Guest lists follow UC-001 BR-011. Newly added guests receive an invitation; removed guests receive a cancellation.

### BR-006: No Cancellation Deadline

A booking can be cancelled at any time; there is no cut-off before the meeting.

### BR-007: Locked Host

When a Host is locked, the Invitee can still cancel but cannot reschedule or edit.

### BR-008: Guests of a Pending Booking

Guests of a pending booking receive an invitation only when the booking is confirmed, except that editing the details (A5) sends every listed guest an updated invitation straight away.
