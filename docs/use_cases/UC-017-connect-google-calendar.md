# Use Case: Connect Google Calendar

## Overview

**Use Case ID:** UC-017  
**Use Case Name:** Connect Google Calendar  
**Primary Actor:** Owner  
**Secondary Actors:** Google Calendar  
**Requirements:** FR-041, FR-042, FR-043  
**Goal:** The Owner connects one or more Google accounts and chooses which calendars block their slots and where new Google events are written.  
**Status:** Implemented

## Preconditions

- The Owner is signed in.
- The operator has configured Google integration.

## Main Success Scenario

1. Owner opens the Google page and chooses to connect an account.
2. System sends the Owner to Google to grant calendar access.
3. Owner grants access.
4. System stores the connected account, or updates it if the same account was connected before.
5. System shows each connected account with its calendars.
6. Owner ticks the calendars that count as busy and picks exactly one write target.
7. System saves the selection, replacing the previous one.

## Alternative Flows

### A1: Access Refused or Expired

**Trigger:** Owner refuses access, or takes longer than 10 minutes (step 3)  
**Flow:**

1. System shows that the connection could not be completed.
2. Use case ends.

### A2: No Single Write Target

**Trigger:** No write target is picked, and no unreachable account keeps a saved one (A3) (step 7)  
**Flow:**

1. System refuses the save, asks to pick exactly one write target and stores nothing.
2. Use case continues at step 6.

### A3: Account Needs Reconnecting

**Trigger:** A connected account's access was revoked or its calendars cannot be loaded (step 5)  
**Flow:**

1. System shows the saved calendars of that account read-only with a reconnect banner.
2. System keeps that account's saved selection when the Owner saves.
3. Use case continues at step 6.

### A4: Disconnect Account

**Trigger:** Owner disconnects an account (step 5)  
**Flow:**

1. System removes the account and its calendars.
2. Use case continues at step 5.

### A5: Write Target Account

**Trigger:** The account to disconnect holds the write target while other accounts remain (step 5)  
**Flow:**

1. System refuses until another write target is chosen.
2. Use case continues at step 5.

## Postconditions

### Success Postconditions

- The connected accounts and their calendar selection are stored; slot calculation uses the busy calendars and new Google events go to the write target.

### Failure Postconditions

- Connections and selection are unchanged.

## Business Rules

### BR-001: One Write Target

While at least one account is connected, every saved selection has exactly one write target; with no connected account there is none. The write target always counts as busy, whether or not it was ticked.

### BR-002: Own Calendars Only

Only calendars of the Owner's own connected accounts can be selected.

### BR-003: Degraded Mode

Without any connected account every scheduling feature works; only Google events and Google busy times are absent, and messages carry calendar attachments instead.

### BR-004: Fail Closed

When a connected account cannot be read, the Owner's booking page is paused instead of offering times that may be busy (UC-001 BR-006).

### BR-005: Stored Ref

A booking's Google event stays on the calendar it was created on even if the write target changes later; if that account is gone, the write target is used.

### BR-006: Video Link Support

When a calendar refuses to create video links, the event is created without one and the calendar is remembered as not supporting them.

### BR-007: Protected Access

Google access grants are stored encrypted and refreshed automatically; a refresh failure marks the account as needing reconnection.
