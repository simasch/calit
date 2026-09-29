# Use Case: Access or Erase Booking Data

## Overview

**Use Case ID:** UC-003  
**Use Case Name:** Access or Erase Booking Data  
**Primary Actor:** Invitee  
**Secondary Actors:** Owner, Google Calendar  
**Requirements:** FR-011, FR-012  
**Goal:** The Invitee obtains a copy of the personal data a booking holds about them, or has it erased, exercising their data-protection rights.  
**Status:** Implemented

## Preconditions

- The Invitee holds the private manage link of the booking (UC-002).
- The booking's data has not been erased yet.

## Main Success Scenario

1. Invitee opens the manage page and chooses to erase their data.
2. System shows a confirmation page stating whether the booking is upcoming and how long the Owner keeps booking data.
3. Invitee confirms the erasure.
4. System cancels the booking first when it is still upcoming, informing the Owner as in a normal cancellation.
5. System removes the Google event and anonymises the booking: name, email, answers, title, description and video link are cleared; guests, unsent reminders and queued messages are deleted.
6. System shows a report of what was erased and which copies it cannot reach (the Google trash, messages already delivered, notification channels used).

## Alternative Flows

### A1: Download Own Data

**Trigger:** Invitee chooses to download their data instead (step 1)  
**Flow:**

1. System provides a file with the booking's details, the Invitee's name, email, answers and guests.
2. Use case ends.

### A2: Erasure Disabled on This Instance

**Trigger:** The operator disabled self-service erasure (step 1)  
**Flow:**

1. System does not offer erasure and shows the operator's privacy contact instead.
2. Use case ends.

### A3: Past Booking

**Trigger:** The booking already took place (step 4)  
**Flow:**

1. System does not cancel; it removes the Google event only if the Owner's account is still connected.
2. Use case continues at step 5.

### A4: Google Unreachable

**Trigger:** The Google event cannot be removed (step 5)  
**Flow:**

1. System erases the data anyway and reports that the Google copy could not be removed.
2. Use case continues at step 6.

### A5: Invitee Keeps the Booking

**Trigger:** Invitee chooses to keep the booking instead of confirming the erasure (step 3)  
**Flow:**

1. System returns to the manage page; nothing is cancelled or erased.
2. Use case ends.

## Postconditions

### Success Postconditions

- The booking keeps only its time slot and meeting type; no personal data of the Invitee or guests remains.
- The manage link and every other Invitee link of the booking stop working.

### Failure Postconditions

- The booking and its data are unchanged (A5).

## Business Rules

### BR-001: Erasure Keeps the Time Slot

Erasure anonymises the booking instead of deleting it, so the Owner keeps a record that the time was booked.

### BR-002: Group Erasure

Erasing a multi-host booking erases every Host's booking of the group together.

### BR-003: Erasure Is Idempotent

Erasing an already erased booking changes nothing.

### BR-004: Honest Report

The report states which copies calit cannot reach: messages already delivered, notification channel messages, and a Google event kept in Google's trash for about 30 days.

### BR-005: Retention Statement

The confirmation page states after how many days the booking's links stop working, using the applicable retention period of UC-022 BR-001 (the Owner's period, otherwise the instance default). When neither is set, it states that the links keep working until the booking is removed.

### BR-006: Export Content

The export contains only the booking's own data and never data of other bookings or of the Owner.
