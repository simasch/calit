# Use Case: Send Reminders and Retry Mail

## Overview

**Use Case ID:** UC-019  
**Use Case Name:** Send Reminders and Retry Mail  
**Primary Actor:** Scheduler  
**Secondary Actors:** Invitee, Owner  
**Requirements:** FR-051, FR-052  
**Goal:** The Scheduler reminds participants of upcoming meetings and delivers messages that could not be sent at first, so no notice is silently lost.  
**Status:** Implemented

## Preconditions

- The instance is running; any replica may perform this work.

## Main Success Scenario

1. Scheduler wakes up every minute.
2. System claims the reminders that are due, so no other replica sends them too.
3. System marks each reminder sent and queues the reminder message to the Invitee and, if they want booking emails, the Hosts; guests receive no reminder.
4. System notifies the Hosts' notification channels.
5. System claims up to 20 queued messages that are due for delivery.
6. System sends each message and marks it delivered.

## Alternative Flows

### A1: Delivery Fails

**Trigger:** A queued message cannot be delivered (step 6)  
**Flow:**

1. System records the failure and schedules the next attempt with increasing delay.
2. Use case continues at step 6 with the next claimed message.

### A2: Attempts Exhausted

**Trigger:** A message has failed 10 times (step 6)  
**Flow:**

1. System stops retrying; the message counts as undeliverable, and every Owner's dashboard warns about undelivered email until it is purged (UC-014 step 2, UC-022).
2. Use case continues at step 6 with the next claimed message.

### A3: Message Outdated

**Trigger:** The message's usefulness deadline has passed, such as an expired reset link (step 6)  
**Flow:**

1. System stops trying without sending, recording that the deadline passed; the message then counts as undeliverable like one in A2.
2. Use case continues at step 6 with the next claimed message.

### A4: Reminder Cannot Be Prepared

**Trigger:** The reminder message cannot be prepared (step 3)  
**Flow:**

1. System logs the problem and keeps the reminder marked as sent.
2. Use case continues at step 4.

## Postconditions

### Success Postconditions

- Due reminders are sent once and queued messages are delivered.

### Failure Postconditions

- Undelivered messages remain queued for retry or are marked undeliverable.

## Business Rules

### BR-001: Reminder Timing

A reminder is scheduled when a booking is confirmed, approved or rescheduled, at 1440 minutes before its start (configurable by the operator for the whole instance; Owners see it read-only, UC-015). It is not created if that moment has passed, and it is removed when the booking is cancelled, declined or returns to pending. A multi-host booking has one reminder.

### BR-002: Messages Never Block Bookings

A message that cannot be sent immediately is queued; a booking never fails because of email.

### BR-003: Retry Schedule

Failed messages are retried after 2, 4, 8, 16 and 32 minutes and then hourly, up to 10 attempts in total.

### BR-004: At-Least-Once Delivery

Work is shared between replicas without a leader; in rare failure cases a message may be delivered twice but never lost silently.

### BR-005: Due Window

A reminder counts as due up to 30 seconds (configurable) before its time.

### BR-006: Instance-Wide Mail Warning

The undelivered-mail warning counts every undeliverable message of the instance, not only the viewing Owner's, and is shown on every Owner's dashboard.
