# Use Case: Manage Meeting Types

## Overview

**Use Case ID:** UC-010  
**Use Case Name:** Manage Meeting Types  
**Primary Actor:** Owner  
**Requirements:** FR-024, FR-025, FR-026, FR-027, FR-028, FR-043  
**Goal:** The Owner creates, configures, deactivates and deletes the meeting types Invitees can book, including their lengths, location and booking questions.  
**Status:** Implemented

## Preconditions

- The Owner is signed in and has completed setup (UC-009).

## Main Success Scenario

1. Owner opens the list of meeting types.
2. System shows the Owner's meeting types, including secret ones, and a creation form.
3. Owner enters name, optional link name, default length, buffers, minimum notice, horizon, cadence, location, description, approval and secret flags, and how the name and guests fields are shown; optionally initial working hours and one date override.
4. System derives a unique link name, validates the entries and stores the meeting type as active.
5. Owner opens the meeting type's details.
6. System shows its settings, allowed durations, booking questions, working hours, date overrides, Hosts, Google calendar and notification routing.
7. Owner edits the settings, sets the allowed durations with optional per-length buffers, and adds or removes booking questions.
8. System validates and saves the changes.

## Alternative Flows

### A1: Invalid Length

**Trigger:** The default length is below 1 minute (step 4)  
**Flow:**

1. System shows "Duration must be at least 1 minute." and nothing is stored.
2. Use case continues at step 3.

### A2: Link Name Clash with Co-hosted Type

**Trigger:** The link name equals that of a meeting type the Owner co-hosts, or — when a shared type is renamed — is used by another Host of this type (step 4 or step 8)  
**Flow:**

1. System shows an error naming the conflicting link name and nothing is stored.
2. Use case continues at step 3 (or step 7).

### A3: Video Link Not Possible

**Trigger:** Google Meet is chosen but the calendar the type writes to (its write override, otherwise the Owner's write target) cannot create video links (step 4 or step 8)  
**Flow:**

1. System rejects the location with an explanation and stores nothing.
2. Use case continues at step 3 (or step 7).

### A4: Calendar Not Selected

**Trigger:** The chosen Google calendar is not one of the Owner's selected calendars (step 4 or step 8)  
**Flow:**

1. System shows "That calendar is not one of your selected Google calendars." and stores nothing.
2. Use case continues at step 3 (or step 7).

### A5: Calendar Changed with Upcoming Bookings

**Trigger:** The Owner changes the type's Google calendar while upcoming bookings exist on another calendar (step 8)  
**Flow:**

1. System saves the change and states how many upcoming bookings stay on the calendar they were created on.
2. Use case ends.

### A6: Deactivate or Reactivate

**Trigger:** Owner toggles a meeting type's active state (step 2)  
**Flow:**

1. System switches the meeting type between active and inactive.
2. Use case continues at step 2.

### A7: Delete Meeting Type

**Trigger:** Owner deletes a meeting type (step 2)  
**Flow:**

1. System checks that the meeting type has no upcoming pending or confirmed bookings for any Host.
2. System deletes the meeting type together with its past bookings.
3. Use case continues at step 2.

### A8: Upcoming Bookings Block Deletion

**Trigger:** Upcoming bookings exist when deleting (step 2)  
**Flow:**

1. System refuses and asks the Owner to cancel those bookings first.
2. Use case continues at step 2.

### A9: Manage Default Booking Questions

**Trigger:** Owner opens the default booking questions (step 1)  
**Flow:**

1. System shows the questions used by meeting types without their own.
2. Owner adds a question with label, key, answer type, required flag and position, or deletes one.
3. System saves the change.
4. Use case ends.

### A10: Invalid Scheduling Settings

**Trigger:** Minimum notice, a buffer, the horizon or the cadence is not a whole number or breaks BR-009 (step 4 or step 8)  
**Flow:**

1. System shows the form again with an error naming the setting and stores nothing.
2. Use case continues at step 3 (or step 7).

## Postconditions

### Success Postconditions

- The meeting type exists with the saved configuration and is reachable at the Owner's page under its link name.

### Failure Postconditions

- The meeting type is unchanged; a failed creation stores nothing (including its initial hours and override).

## Business Rules

### BR-001: Link Name

The link name is derived from the given text or the name: lower-case, accents removed, other characters replaced by single hyphens. It is made unique among the Owner's meeting types by appending -2, -3, and so on; an empty result becomes "meeting".

### BR-002: Default Length Is Allowed

The meeting type's default length is always one of its allowed durations. Every allowed length is at least 1 minute; per-length buffers are 0 or more or left unset. Duplicate lengths are merged.

### BR-003: Defaults

A new meeting type is active, has buffers of 0, a horizon of 60 days, and asks for the name as required and guests as optional. The guests field can be optional or hidden, never required.

### BR-004: Location

The location is Google Meet, phone, in person or custom text, and belongs to the meeting type, never to a booking or a Host. Google Meet is offered only when the type's calendar can create video links.

### BR-005: Write Override

A meeting type may write its Google events to a calendar other than the Owner's write target, chosen from the Owner's selected calendars. If that calendar is later unselected or its account disconnected, events go to the write target instead.

### BR-006: Booking Questions Scope

Questions of a meeting type replace the Owner's default questions entirely for that type. Answer types are short text, long text, email, phone and number.

### BR-007: Secret and Inactive

A secret meeting type is hidden from the Owner's public page but can be booked by its link. An inactive meeting type is hidden from the public page and cannot be booked, not even by its link (UC-001 A1).

### BR-008: Deletion Guard

A meeting type with upcoming pending or confirmed bookings cannot be deleted; deletion removes its past bookings as well.

### BR-009: Scheduling Settings Limits

Minimum notice and buffers are 0 minutes or more, the horizon is at least 1 day, and the cadence is blank (derived, UC-001 BR-004) or at least 1 minute.
