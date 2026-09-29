# Use Case: Share Meeting Type with Co-hosts

## Overview

**Use Case ID:** UC-012  
**Use Case Name:** Share Meeting Type with Co-hosts  
**Primary Actor:** Owner  
**Secondary Actors:** Co-host  
**Requirements:** FR-031, FR-032  
**Goal:** The Creator of a meeting type invites other Owners as Co-hosts, so a booking requires all of them to be free and appears on each Host's calendar.  
**Status:** Implemented

## Preconditions

- The Owner is signed in and is the Creator of the meeting type.

## Main Success Scenario

1. Owner opens the meeting type's details.
2. Owner types part of a colleague's username.
3. System suggests up to 20 matching usernames.
4. Owner selects a colleague and invites them.
5. System checks that the colleague is eligible and that the link name is free for them.
6. System adds the colleague as a pending Co-host, sends them an invitation email with a one-click consent link and notifies their notification channels.
7. System shows the meeting type with the pending Co-host; the type is not bookable until every Co-host accepts (UC-013).

## Alternative Flows

### A1: Colleague Not Eligible

**Trigger:** The colleague does not exist, is locked, has not completed setup, is the Creator or is already a Host (step 5)  
**Flow:**

1. System shows "No eligible user with that username".
2. Use case continues at step 2.

### A2: Too Many Hosts

**Trigger:** The meeting type already has 10 Hosts (step 5)  
**Flow:**

1. System shows that a meeting can have at most 10 hosts.
2. Use case ends.

### A3: Link Name Taken for Colleague

**Trigger:** The colleague already owns or co-hosts a meeting type with the same link name (step 5)  
**Flow:**

1. System shows which colleague already uses the link name.
2. Use case continues at step 2.

### A4: Remove Co-host

**Trigger:** Owner removes a Co-host (step 1)  
**Flow:**

1. When the Co-host has upcoming pending or confirmed bookings on this type, system asks whether to keep or cancel them.
2. If the Owner chooses to cancel, system cancels each of those group bookings as a whole — for every Host, with the usual cancellation notices (UC-014 A7).
3. System removes the Co-host. Kept group bookings stay unchanged for every Host, including the removed Co-host, whose booking still occupies their calendar.
4. When the last Co-host is removed, the meeting type becomes single-host again.
5. Use case ends.

### A5: Remove Creator

**Trigger:** Owner tries to remove themselves as Creator (step 1)  
**Flow:**

1. System refuses: the Creator cannot be removed from their own meeting type.
2. Use case ends.

## Postconditions

### Success Postconditions

- After an invitation: the Co-host is recorded as pending with a single-use consent link and has been notified by email and notification channel.
- After a removal (A4): the Co-host is no longer a Host; their upcoming group bookings of the type are either cancelled for all Hosts or kept unchanged, as the Owner chose.

### Failure Postconditions

- The meeting type's Hosts are unchanged.

## Business Rules

### BR-001: Host Limit

A meeting type has at most 10 Hosts, the Creator included.

### BR-002: Eligibility

Only an existing, unlocked Owner who has completed setup and is not yet a Host can be invited. Inviting an existing Host changes nothing.

### BR-003: Link Name Free Across Hosts

A shared meeting type's link name must be free in every Host's namespace, since it is bookable under each Host's page.

### BR-004: Creator Is the Organizer

Every Google event of a shared meeting type is written on the Creator's connected account. If the Creator has none, the connected Host whose account is oldest becomes the organizer; if no Host is connected, no Google event is created. Only the organizer's calendar choice applies: the Creator's write override, or that Co-host's own choice (UC-013 step 7) while that Co-host is the organizer; without a choice, the organizer's write target is used.

### BR-005: Suggestions Scope

Username suggestions are prefix matches, at most 20, and are offered only for the Owner's own meeting types.

### BR-006: Removing a Co-host Keeps or Cancels Whole Groups

Removing a Co-host never splits a group booking. Cancelling cancels the whole group for every Host and the Invitee; keeping leaves every Host's booking of the group, the Co-host's included, as it was.
