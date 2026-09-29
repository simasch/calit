# Use Case: Administer Users

## Overview

**Use Case ID:** UC-018  
**Use Case Name:** Administer Users  
**Primary Actor:** Site Administrator  
**Requirements:** FR-047, FR-048, FR-049, FR-050  
**Goal:** The Site Administrator invites, promotes, locks, unlocks and deletes Owner accounts of the instance.  
**Status:** Implemented

## Preconditions

- The Site Administrator is signed in and holds administrator rights.

## Main Success Scenario

1. Site Administrator opens the user list.
2. System lists all accounts, oldest first, with their status pending, active or locked (BR-006).
3. Site Administrator invites a new user with username and email.
4. System validates the username and email.
5. System creates an account without password, records the invitation in the audit log and sends an invitation with an activation link valid for 48 hours (UC-008).
6. System shows the updated list.

## Alternative Flows

### A1: Invalid Entries

**Trigger:** The username breaks UC-005 BR-001 or the email is not valid (step 4)  
**Flow:**

1. System shows the list with the error.
2. Use case continues at step 3.

### A2: Resend Invitation

**Trigger:** Site Administrator resends the invitation of a pending user (step 2)  
**Flow:**

1. If the account is not pending (BR-006) or has no email, system shows that the user is not pending and sends nothing.
2. Otherwise system sends a new activation link valid for 48 hours and records it in the audit log.
3. Use case continues at step 2.

### A3: Grant or Revoke Administrator Rights

**Trigger:** Site Administrator grants or revokes administrator rights (step 2)  
**Flow:**

1. System refuses to revoke locally granted rights while only one unlocked administrator with locally granted rights remains (UC-015 BR-008).
2. Otherwise system changes the locally granted rights and records it in the audit log; rights granted by the identity provider (UC-007 BR-005) are not affected.
3. Use case continues at step 2.

### A4: Lock or Unlock

**Trigger:** Site Administrator locks or unlocks an account (step 2)  
**Flow:**

1. System refuses to lock the administrator's own account or the last unlocked administrator whose rights were granted locally (UC-015 BR-008).
2. Otherwise system changes the state and records it; a locked user is signed out on their next request.
3. Use case continues at step 2.

### A5: Delete User

**Trigger:** Site Administrator deletes another account (step 2)  
**Flow:**

1. System asks the Site Administrator to type the account's username.
2. System deletes the account as in UC-015 A2 and records it in the audit log.
3. Use case continues at step 2.

### A6: Deletion Refused

**Trigger:** The typed username does not match, the account is the administrator's own, or it is the last unlocked administrator whose rights were granted locally (step 2)  
**Flow:**

1. System refuses with the reason; nothing is deleted.
2. Use case continues at step 2.

## Postconditions

### Success Postconditions

- The account is created, changed or deleted as requested and the action is recorded in the audit log.

### Failure Postconditions

- Accounts are unchanged.

## Business Rules

### BR-001: Administrators Only

Only site administrators can open the user list; other Owners are refused.

### BR-002: Invited Accounts Start Dormant

An invited account has no password until the user activates it through the link. It never has administrator rights through the invitation; activating only sets the password, and rights are granted separately (A3).

### BR-003: Last Administrator

Revoking, locking or deleting must never leave the instance without an unlocked site administrator whose rights were granted locally (UC-015 BR-008).

### BR-004: Own Account

A Site Administrator cannot lock or delete their own account here; self-deletion is done in the settings (UC-015).

### BR-005: Invitation Email Check

The invitation email must contain one "@", a domain with a dot and no spaces.

### BR-006: Account Status

An account without a password that has never signed in with Google is shown as pending; otherwise it is shown as active when unlocked and locked when locked. Pending is shown even for a locked account. An account that signs in only through single sign-on has neither a password nor a Google sign-in, so it is shown as pending too and can be sent an activation link.
