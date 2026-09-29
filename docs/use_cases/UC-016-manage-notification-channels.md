# Use Case: Manage Notification Channels

## Overview

**Use Case ID:** UC-016  
**Use Case Name:** Manage Notification Channels  
**Primary Actor:** Owner  
**Requirements:** FR-045, FR-046  
**Goal:** The Owner adds chat or push destinations and chooses which meeting types report to them, so booking activity reaches them outside email.  
**Status:** Implemented

## Preconditions

- The Owner is signed in and has completed setup (UC-009).

## Main Success Scenario

1. Owner opens the settings page.
2. System lists the Owner's channels with their label, a hidden address, whether they are default channels, and their last delivery success and failure.
3. Owner adds a channel address with an optional label.
4. Owner sends a test message.
5. System delivers one test message and reports success.
6. Owner saves the channels.
7. System checks each address against the instance policy and stores the channels.
8. Owner opens a meeting type and chooses to report to all default channels or to selected channels only.
9. System saves the routing for this Owner.

## Alternative Flows

### A1: Address Not Allowed

**Trigger:** The address uses an unknown or disallowed kind of channel, misses a required part, or targets a private network where forbidden (step 7)  
**Flow:**

1. System shows the reason and does not store the channel.
2. Use case continues at step 3.

### A2: Test Fails

**Trigger:** The test message cannot be delivered (step 5)  
**Flow:**

1. System reports that the test failed; nothing is saved.
2. Use case continues at step 3.

### A3: Hidden Address Pasted

**Trigger:** The Owner pastes a hidden (masked) address as a new address (step 7)  
**Flow:**

1. System rejects it; an unchanged masked address keeps the stored one.
2. Use case continues at step 3.

### A4: Empty Selection

**Trigger:** Owner chooses selected channels but selects none (step 9)  
**Flow:**

1. System asks to pick at least one channel or choose all channels.
2. Use case continues at step 8.

### A5: Delete Channel

**Trigger:** Owner deletes a channel (step 2)  
**Flow:**

1. System removes the channel and its meeting type routing.
2. Use case continues at step 2.

## Postconditions

### Success Postconditions

- The channels are stored with their addresses protected, and each Host's routing determines where booking notices go.

### Failure Postconditions

- Channels and routing are unchanged.

## Business Rules

### BR-001: Routing

For a booking, a Host's channels selected for that meeting type are notified; if none are selected, the Host's default channels are. Without channels, notices go by email only.

### BR-002: Notified Events

Channels receive booking requested, confirmed, approved, declined, cancelled, rescheduled and updated, guest declined and removed, reminders and co-host invitations.

### BR-003: Channel Policy

The operator can restrict allowed channel kinds and forbid private network targets; the policy is checked when saving and again when sending.

### BR-004: Best-Effort Delivery

A channel is tried up to 3 times; a failure never affects the booking and is shown as the channel's last failure. The address is never shown in full or written to logs.

### BR-005: Labels

A label is at most 64 characters; a missing label defaults to the channel kind's name.

### BR-006: Default for New Types

A channel is a default channel unless the Owner turns that off; new channels are. Default channels are notified for every meeting type for which the Owner has selected no channels (BR-001).
