# Use Case: Reset Password

## Overview

**Use Case ID:** UC-008  
**Use Case Name:** Reset Password  
**Primary Actor:** Owner  
**Requirements:** FR-019  
**Goal:** An Owner who forgot their password, or who was invited by a site administrator, sets a new password through an emailed link.  
**Status:** Implemented

## Preconditions

- The Owner is not signed in.

## Main Success Scenario

1. Owner opens "forgot password" and enters their username.
2. System sends a reset link to the Owner's settings email, in the Owner's language.
3. System shows that a link was sent if the account exists and that it expires in 30 minutes.
4. Owner opens the link.
5. System shows the new-password form.
6. Owner enters a new password.
7. System sets the password, invalidates the link and redirects to sign-in.

## Alternative Flows

### A1: Unknown Account or No Email

**Trigger:** The account does not exist or has no email (step 2)  
**Flow:**

1. System sends nothing but shows the same message as in step 3.
2. Use case ends.

### A2: Invitation Link

**Trigger:** The Owner received an invitation from a site administrator (UC-018) (step 4)  
**Flow:**

1. The invitation link opens the same new-password form; it is valid for 48 hours.
2. Use case continues at step 5.

### A3: Blank Password

**Trigger:** The new password is blank, meaning empty or only whitespace (step 7)  
**Flow:**

1. System shows an error; the link stays valid.
2. Use case continues at step 6.

### A4: Invalid or Expired Link

**Trigger:** The link is unknown, expired or already used (step 5)  
**Flow:**

1. System shows that the link is invalid or expired and offers to request a new one.
2. Use case ends.

## Postconditions

### Success Postconditions

- The Owner's password is replaced and the link can no longer be used.

### Failure Postconditions

- The password is unchanged.

## Business Rules

### BR-001: No Account Disclosure

The response is identical whether or not the account exists.

### BR-002: Link Lifetime

A reset link is valid for 30 minutes, an invitation link for 48 hours; each can be used once. A message whose link has expired is not sent late.

### BR-003: Passwordless Accounts

An account created through Google or single sign-on can use this flow to add a password.

### BR-004: Password Protection

Passwords are stored only in a one-way protected form.
