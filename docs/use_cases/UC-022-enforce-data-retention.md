# Use Case: Enforce Data Retention

## Overview

**Use Case ID:** UC-022  
**Use Case Name:** Enforce Data Retention  
**Primary Actor:** Scheduler  
**Requirements:** FR-055, FR-056  
**Goal:** The Scheduler erases Invitee data of past meetings after the retention period and purges expired technical records, so personal data is not kept longer than needed.  
**Status:** Implemented

## Preconditions

- The instance is running; any replica may perform this work.

## Main Success Scenario

1. Scheduler wakes up daily at 03:17 server time.
2. System claims up to 200 not yet erased bookings whose meeting ended longer ago than the applicable retention period.
3. System anonymises them as in UC-003.
4. System repeats with the next batch while full batches are found, for at most 5 minutes.
5. Scheduler wakes up daily at 03:37 server time.
6. System deletes messages delivered more than 30 days ago, undeliverable or expired messages queued more than 30 days ago, and reset and sign-in links one day after they expired.

## Alternative Flows

### A1: No Retention Configured

**Trigger:** Neither the Owner nor the instance defines a retention period (step 2)  
**Flow:**

1. System keeps the Owner's booking data until the Invitee erases it.
2. Use case continues at step 5.

### A2: Time Budget Exhausted

**Trigger:** 5 minutes have passed (step 4)  
**Flow:**

1. System stops and continues at the next daily run.
2. Use case continues at step 5.

## Postconditions

### Success Postconditions

- Bookings older than their retention period hold no personal data.
- Expired links and old messages are removed.

### Failure Postconditions

- Unprocessed records remain and are handled at the next run.

## Business Rules

### BR-001: Applicable Period

The Owner's retention period applies when set, otherwise the instance default; the instance default is capped at the same limit as the Owner's period (UC-015 BR-004). By default none is set and data is kept.

### BR-002: Messages Still Being Retried

Messages still inside their retry schedule are never purged.

### BR-003: Deleted Usernames Kept

Reservations of deleted usernames are never purged (UC-015 BR-007).
