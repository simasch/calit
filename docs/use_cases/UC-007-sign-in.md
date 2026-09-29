# Use Case: Sign In

## Overview

**Use Case ID:** UC-007  
**Use Case Name:** Sign In  
**Primary Actor:** Owner  
**Secondary Actors:** Google, Identity Provider  
**Requirements:** FR-016, FR-017, FR-018  
**Goal:** The Owner proves who they are, with a password, a Google account or the organisation's single sign-on, to reach their management pages.  
**Status:** Implemented

## Preconditions

- The instance has been bootstrapped (UC-005).
- The Owner has an account, or sign-up is enabled for automatic creation.

## Main Success Scenario

1. Owner opens the sign-in page.
2. System shows the password form and, when configured, "Sign in with Google" and single sign-on buttons.
3. Owner enters username and password and optionally ticks "remember me".
4. System verifies that the account exists, is not locked and the password matches.
5. System records a successful sign-in in the audit log and signs the Owner in.
6. System shows the Owner's dashboard.

## Alternative Flows

### A1: Wrong Credentials

**Trigger:** The account does not exist, is locked, has no password, or the password is wrong (step 4)  
**Flow:**

1. System records a failed sign-in in the audit log.
2. System shows the sign-in page with a generic error.
3. Use case continues at step 3.

### A2: Sign In with Google

**Trigger:** Owner chooses "Sign in with Google" (step 3)  
**Flow:**

1. System sends the Owner to Google to choose an account.
2. Google returns the account's identity and whether its email is verified.
3. System finds the account already linked to that Google identity, or else the one account whose settings email equals the verified Google email and links it.
4. System checks that the account is not locked (A10 otherwise).
5. Use case continues at step 5.

### A3: Sign In with Single Sign-On

**Trigger:** Owner chooses single sign-on (step 3)  
**Flow:**

1. System sends the Owner to the organisation's identity provider.
2. The identity provider returns the identity, email and group memberships.
3. System finds or links the account as in A2 and grants or withdraws administrator rights according to the configured administrator group.
4. System checks that the account is not locked (A10 otherwise).
5. Use case continues at step 5.

### A4: No Matching Account

**Trigger:** During A2 or A3, no account matches the external identity (step 3)  
**Flow:**

1. If sign-up is enabled, system creates a passwordless regular account whose username is derived from the email (BR-008); use case continues at step 5.
2. If sign-up is disabled, system returns to the sign-in page stating that sign-up is disabled.
3. Use case ends.

### A5: Ambiguous Email

**Trigger:** During A2 or A3, more than one account has the external identity's email (step 3)  
**Flow:**

1. System returns to the sign-in page stating the email is ambiguous.
2. Use case ends.

### A6: External Sign-In Fails

**Trigger:** During A2 or A3, the external provider reports an error or the round trip expired (step 3)  
**Flow:**

1. System returns to the sign-in page stating the sign-in could not be completed.
2. Use case ends.

### A7: Setup Not Finished

**Trigger:** The Owner has not completed first-login setup (step 6)  
**Flow:**

1. System redirects to the first-login setup (UC-009).
2. Use case ends.

### A8: Already Signed In

**Trigger:** The Owner is already signed in (step 1)  
**Flow:**

1. System shows the dashboard directly.
2. Use case ends.

### A9: Sign Out

**Trigger:** A signed-in Owner chooses to sign out (step 1)  
**Flow:**

1. System ends the Owner's sign-in and shows the sign-in page.
2. Use case ends.

### A10: Locked Account Signs In Externally

**Trigger:** During A2 or A3, the matched account is locked (step 3)  
**Flow:**

1. System refuses the sign-in and records a failed sign-in in the audit log, as in A1.
2. System shows the sign-in page with a generic error; the external identity stays linked to the account.
3. Use case ends.

## Postconditions

### Success Postconditions

- The Owner is signed in on this browser; with "remember me" the sign-in survives closing the browser for 30 days.
- The sign-in is recorded in the audit log.

### Failure Postconditions

- The Owner is not signed in; a failed attempt is recorded in the audit log.

## Business Rules

### BR-001: Locked Accounts

A locked account cannot sign in, whether with a password, Google or single sign-on (A1, A10), and an existing sign-in of an account that is locked or deleted stops working on its next request.

### BR-002: Stateless Sign-In

A sign-in is valid on every replica of the instance without shared session storage.

### BR-003: Verified Email for Linking

An external identity is linked to an existing account by email only when the provider states the email is verified, and only if exactly one account has that email.

### BR-004: External Round Trip Lifetime

An external sign-in must be completed within 10 minutes; the one-time proof that finishes it is valid for 2 minutes and usable once.

### BR-005: Administrator Group

With single sign-on, membership of the configured administrator group grants administrator rights and leaving it withdraws them at the next sign-in; administrator rights granted locally are never withdrawn by the identity provider. Withdrawal therefore never leaves the instance without an administrator, because UC-015 BR-008 counts only locally granted rights.

### BR-006: No Lockout

Repeated failed sign-ins do not lock the account; they are only recorded.

### BR-007: Audit Log

Successful and failed sign-ins, like the account actions of UC-015 and UC-018, are written to the operator's audit log, an application log stream. They are not stored as booking or account data.

### BR-008: Derived Username

A username derived from an email is the part before "@", lower-cased and reduced to letters, digits and single hyphens; "user" is used when nothing valid remains. When the result is taken, reserved or belonged to a deleted account (UC-005 BR-001, UC-015 BR-007), a suffix -2, -3, and so on is appended within the 64-character limit.
