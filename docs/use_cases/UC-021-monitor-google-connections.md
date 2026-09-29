# Use Case: Monitor Google Connections

## Overview

**Use Case ID:** UC-021  
**Use Case Name:** Monitor Google Connections  
**Primary Actor:** Scheduler  
**Secondary Actors:** Owner, Google Calendar  
**Requirements:** FR-054  
**Goal:** The Scheduler detects connected Google accounts whose access was revoked and tells their Owners once, so they can restore their paused booking pages.  
**Status:** Implemented

## Preconditions

- At least one Owner has a connected Google account (UC-017).

## Main Success Scenario

1. Scheduler wakes up every hour (configurable).
2. System claims up to 50 connected accounts not checked within the last half interval.
3. System verifies each account's access with Google.
4. System marks accounts that work as healthy.
5. System claims up to 50 accounts needing reconnection whose Owner has not been told yet.
6. System emails each such Owner that Google is disconnected and their booking page is paused, with a link to reconnect.

## Alternative Flows

### A1: Access Revoked

**Trigger:** Google reports the access as no longer valid (step 3)  
**Flow:**

1. System marks the account as needing reconnection.
2. Use case continues at step 5.

### A2: Temporary Error

**Trigger:** Google is temporarily unreachable or overloaded (step 3)  
**Flow:**

1. System leaves the account's state unchanged.
2. Use case continues at step 5.

## Postconditions

### Success Postconditions

- Every account's health is current and each affected Owner is informed once per outage.

### Failure Postconditions

- Accounts not checked are picked up at the next run.

## Business Rules

### BR-001: One Notice per Outage

An Owner is told once per outage; a successful reconnect or check re-arms the notice.

### BR-002: Always Sent

The disconnection notice is sent even if the Owner turned off booking emails.

### BR-003: Recovery

A later successful check clears the reconnection mark without Owner action.
