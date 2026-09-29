# Use Case: Participate as Co-host

## Overview

**Use Case ID:** UC-013  
**Use Case Name:** Participate as Co-host  
**Primary Actor:** Co-host  
**Requirements:** FR-033, FR-034, FR-035  
**Goal:** An invited Owner accepts or declines co-hosting a colleague's meeting type and, once accepted, sets their own hours, buffers and notifications for it, or leaves it.  
**Status:** Implemented

## Preconditions

- The Co-host was invited by the Creator (UC-012).

## Main Success Scenario

1. Co-host opens the consent link from the invitation email.
2. System shows the meeting type and the Creator's name.
3. Co-host accepts.
4. System records the acceptance and invalidates the consent link.
5. System makes the meeting type bookable under the Co-host's page too once every Host has accepted.
6. Co-host opens their settings for the shared meeting type.
7. System shows the Co-host's own weekly hours for the type (prefilled from their global week), date overrides, buffers, Google calendar (used only while this Co-host organizes the type's events, UC-012 BR-004) and notification routing.
8. Co-host adjusts these settings.
9. System saves them for this Co-host only.

## Alternative Flows

### A1: Respond While Signed In

**Trigger:** Co-host opens the list of pending invitations in their management pages (step 1)  
**Flow:**

1. System lists pending invitations with the Creator's name.
2. Co-host accepts there; a decline there follows A2.
3. Use case continues at step 4.

### A2: Decline

**Trigger:** Co-host declines, from the consent link or from the list of pending invitations (A1) (step 3)  
**Flow:**

1. System removes the invitation and invalidates the consent link; the Creator is not notified.
2. Use case ends.

### A3: Invalid Consent Link

**Trigger:** The consent link is unknown or already used (step 1)  
**Flow:**

1. System shows a not-found page.
2. Use case ends.

### A4: Leave the Meeting Type

**Trigger:** Co-host chooses to leave (step 6)  
**Flow:**

1. When the Co-host has upcoming bookings on this type, system asks whether to keep or cancel them.
2. System cancels them if asked, then removes the Co-host.
3. Use case ends.

## Postconditions

### Success Postconditions

- The Co-host is an accepted Host; slots of the meeting type require them to be free.
- The Co-host's own hours, overrides, buffers and routing for the type are stored.

### Failure Postconditions

- The Co-host's participation is unchanged.

## Business Rules

### BR-001: Single-Use Consent

The consent link can be used once; accepting or declining invalidates it.

### BR-002: Per-Host Settings

Weekly hours, date overrides and buffers are resolved per Host. A blank personal buffer means the Host sets no requirement of their own, so UC-001 BR-003 decides (the length's buffer, otherwise the meeting type's). A personal buffer that is not a whole number is treated as blank, and a negative one is saved as 0 without an error — unlike the meeting type's buffers, which are rejected (UC-010 BR-009).

### BR-003: Only Accepted Co-hosts Configure

Only an accepted Co-host may change their settings for the shared meeting type.

### BR-004: Creator Cannot Leave

The Creator cannot leave or be removed from their own meeting type (UC-012 A5).
