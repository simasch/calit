# Use Case: Expire Unanswered Booking Requests

## Overview

**Use Case ID:** UC-020  
**Use Case Name:** Expire Unanswered Booking Requests  
**Primary Actor:** Scheduler  
**Secondary Actors:** Invitee, Owner  
**Requirements:** FR-053  
**Goal:** The Scheduler declines booking requests the Owner has not answered in time, so held slots are released and Invitees are not left waiting.  
**Status:** Implemented

## Preconditions

- Pending bookings exist (UC-001 A10).

## Main Success Scenario

1. Scheduler wakes up every minute.
2. System selects up to 50 pending bookings whose deadline has passed, oldest first.
3. System ensures no other replica handles the same booking or group.
4. System checks the booking is still pending and marks it declined, which frees the slot.
5. System removes its unsent reminder and queues a declined notice to the Invitee and to the Owner unless the Owner turned off booking emails; guests receive nothing (BR-002).
6. System notifies the Owner's notification channels.

## Alternative Flows

### A1: Already Answered

**Trigger:** The Owner approved or declined meanwhile (step 4)  
**Flow:**

1. System leaves the booking unchanged.
2. Use case continues at step 2.

### A2: Multi-Host Request

**Trigger:** The booking belongs to a multi-host group (step 4)  
**Flow:**

1. System declines every Host's share of the group, including shares a Host already approved, and removes their unsent reminders.
2. System queues one declined notice to the Invitee and to every Host who has not turned off booking emails.
3. Use case continues at step 6.

## Postconditions

### Success Postconditions

- Expired requests are declined, their slots are free and participants are informed.

### Failure Postconditions

- Requests not processed stay pending and are picked up at the next run.

## Business Rules

### BR-001: Approval Deadline

A pending booking expires 24 hours (configurable) after it was first requested, or at its start time if that is earlier. The deadline is not reset when an Invitee reschedule sends a booking back for approval (UC-002 A4): a booking requested more than 24 hours earlier is declined at the next run. A booking counts as expired up to 30 seconds before its deadline.

### BR-002: Guest Notices

Guests receive nothing on expiry: neither guests of a booking that was never confirmed nor guests who were invited before an Invitee reschedule sent the booking back for approval (UC-002 A4). An expiry differs here from a decline by the Owner (UC-014 A2).
