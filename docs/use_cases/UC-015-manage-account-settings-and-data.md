# Use Case: Manage Account Settings and Data

## Overview

**Use Case ID:** UC-015  
**Use Case Name:** Manage Account Settings and Data  
**Primary Actor:** Owner  
**Requirements:** FR-021, FR-022, FR-023  
**Goal:** The Owner maintains their profile and preferences, downloads their data, or deletes their account.  
**Status:** Implemented

## Preconditions

- The Owner is signed in and has completed setup (UC-009).

## Main Success Scenario

1. Owner opens the settings page.
2. System shows name, email, time zone, language, time format, booking-email preference, home redirect preference and retention period, plus the reminder lead time set by the operator (read-only, UC-019 BR-001).
3. Owner changes the settings.
4. System normalises unknown values to their defaults and saves the settings.
5. System shows the settings page in the chosen language.

## Alternative Flows

### A1: Download My Data

**Trigger:** Owner chooses to download their data (step 2)  
**Flow:**

1. System provides a file with the account, settings, meeting types, availability, date overrides, bookings with Invitee data and guests, notification channels with hidden addresses, and connected Google account emails.
2. Use case ends.

### A2: Delete Account

**Trigger:** Owner chooses to delete their account (step 2)  
**Flow:**

1. System asks the Owner to re-enter their password, or to type their username when the account has no password.
2. Owner confirms.
3. System checks the confirmation (A3 otherwise) and that the Owner is not the last administrator (A4 otherwise).
4. System cancels every upcoming pending or confirmed booking of the meeting types the Owner created, each multi-host booking once for its whole group, informing Invitees, guests and Co-hosts; an unreachable Google account does not stop the cancellation.
5. System removes the Owner as Host from meeting types they co-host; the Owner's own bookings of those types are deleted without notice, while the other Hosts' bookings of the group stay.
6. System reserves the username permanently, deletes the account and all its data (BR-006), records the deletion in the audit log and signs the Owner out.
7. Use case ends.

### A3: Confirmation Mismatch

**Trigger:** During A2, the re-entered password or username does not match (step 2)  
**Flow:**

1. System shows "That didn't match. Your account was not deleted."
2. Use case ends.

### A4: Last Administrator

**Trigger:** During A2, the Owner is the last unlocked site administrator whose rights were granted locally (BR-008) (step 2)  
**Flow:**

1. System refuses and asks to grant administrator rights to another account first.
2. Use case ends.

### A5: Missing Name or Invalid Email

**Trigger:** The display name or email is left empty, or the email does not have the form of an email address (step 3)  
**Flow:**

1. System does not accept the form, points the Owner to the field to correct and saves nothing.
2. Use case continues at step 3.

## Postconditions

### Success Postconditions

- The settings are saved; or the data file is delivered; or the account and all its data are gone and the username can never be reused.

### Failure Postconditions

- Settings and account are unchanged.

## Business Rules

### BR-001: Setting Defaults

An unknown time zone becomes UTC, an unsupported language becomes English, an unknown time format becomes automatic.

### BR-002: Time Format Scope

The Owner's 12/24-hour preference applies to their own pages and emails, never to Invitee-facing pages.

### BR-003: Home Redirect

When enabled (the default), a signed-in Owner visiting the instance home page is taken to their dashboard. The Owner's public page never redirects.

### BR-004: Retention Period

The Owner may set the number of days after a meeting before Invitee data is erased (UC-022). Blank or not positive means the instance default applies; values above 36500 days are capped.

### BR-005: Export Excludes Secrets

The export never contains passwords, Google access grants or notification channel addresses.

### BR-006: Account Deletion Scope

Deleting an account removes its settings, meeting types with their durations and booking questions, availability and date overrides, Host memberships, bookings, guests, reminders, Google connections and their calendars, notification channels, queued messages addressed on its behalf, and pending sign-in and password-reset links. Cancellation notices already queued for Invitees, guests and Co-hosts are still delivered. Access granted at Google is not revoked there. No confirmation email is sent.

### BR-007: Usernames Are Never Reused

The username of a deleted account is permanently reserved and cannot be chosen by setup, sign-up, invitation or external sign-in.

### BR-008: Last Administrator

The instance always keeps at least one unlocked site administrator whose rights were granted locally; rights granted by the identity provider (UC-007 BR-005) do not count.

### BR-009: Required Profile Data

Display name and email stay required when changed here, with the same check as UC-009 BR-003 (A5).
