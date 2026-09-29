# Use Case: Decline Guest Invitation

## Overview

**Use Case ID:** UC-004  
**Use Case Name:** Decline Guest Invitation  
**Primary Actor:** Guest  
**Secondary Actors:** Invitee, Owner, Google Calendar  
**Requirements:** FR-013  
**Goal:** A Guest added to a booking tells the system they cannot attend, so they stop receiving updates and the Invitee knows.  
**Status:** Implemented

## Preconditions

- The Guest received an invitation with a private decline link (UC-001, UC-002).

## Main Success Scenario

1. Guest opens the decline link from the invitation.
2. System shows the meeting and asks the Guest to confirm they cannot attend.
3. Guest confirms.
4. System marks the Guest as declined and removes them from the Google event's attendees.
5. System sends the Guest a cancellation for their calendar, informs the Invitee so they can reschedule if needed, and notifies the Hosts' notification channels (UC-016 BR-001).
6. System shows a confirmation.

## Alternative Flows

### A1: Already Declined

**Trigger:** The Guest has already declined (step 2)  
**Flow:**

1. System shows that the invitation was already declined.
2. Use case ends.

### A2: Unknown Link

**Trigger:** The decline link does not match an invitation (step 1)  
**Flow:**

1. System shows a not-found page.
2. Use case ends.

### A3: Guest Removed

**Trigger:** The Guest was removed from the booking's guest list (step 2)  
**Flow:**

1. System shows the same page as for an already declined invitation; the Guest's state is unchanged and no message is sent.
2. Use case ends.

### A4: Booking No Longer Active

**Trigger:** The booking was cancelled or declined, but the Guest's invitation was never withdrawn (step 2)  
**Flow:**

1. System asks for confirmation as in step 2; the booking's state is not checked.
2. On confirmation, system marks the Guest as declined, sends the Guest a cancellation, informs the Invitee and notifies the Hosts' notification channels; there is no Google event left to update.
3. Use case ends.

## Postconditions

### Success Postconditions

- The Guest is declined and no longer an attendee of the Google event.
- The Invitee has been informed and the Hosts' notification channels have been notified.

### Failure Postconditions

- The Guest's invitation is unchanged.

## Business Rules

### BR-001: Declining Is Final and Idempotent

Declining twice changes nothing. A Guest can only be invited again by the Invitee or Owner editing the guest list.

### BR-002: Calendar Replies Are Not Tracked

Only the decline link counts; accepting or declining within Google Calendar is not recorded.

### BR-003: Hosts Informed by Channel Only

A Guest's decline reaches the Hosts through their notification channels (UC-016 BR-002); no email is sent to the Hosts. Without channels, the Hosts are not informed.
