# Use Case: Sign Up

## Overview

**Use Case ID:** UC-006  
**Use Case Name:** Sign Up  
**Primary Actor:** Visitor  
**Requirements:** FR-015  
**Goal:** A Visitor creates their own Owner account, so they can publish a scheduling page.  
**Status:** Implemented

## Preconditions

- The operator enabled self-service sign-up.

## Main Success Scenario

1. Visitor opens the sign-up page.
2. Visitor enters a username and password.
3. System validates the username.
4. System creates a regular (non-administrator) account with placeholder settings.
5. System redirects to the sign-in page.

## Alternative Flows

### A1: Sign-Up Disabled

**Trigger:** Self-service sign-up is disabled (step 1)  
**Flow:**

1. System shows a not-found page.
2. Use case ends.

### A2: Invalid Username

**Trigger:** The username breaks UC-005 BR-001 (step 3)  
**Flow:**

1. System shows the form again with an error.
2. Use case continues at step 2.

### A3: Empty Password

**Trigger:** The Visitor leaves the password empty (step 2)  
**Flow:**

1. The form is not submitted and asks for a password; no account is created.
2. Use case continues at step 2.

## Postconditions

### Success Postconditions

- A regular account exists and must complete first-login setup (UC-009).

### Failure Postconditions

- No account is created.

## Business Rules

### BR-001: Sign-Up Switch

Sign-up is off by default and is switched by the operator; changing it requires a restart. The same switch governs automatic account creation on first Google or single sign-on login (UC-007).

### BR-002: No Password Policy

The sign-up form requires a password to be entered, but no strength rule applies and the system does not check the password itself: a password of only whitespace is accepted.
