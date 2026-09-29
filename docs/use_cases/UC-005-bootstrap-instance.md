# Use Case: Bootstrap Instance

## Overview

**Use Case ID:** UC-005  
**Use Case Name:** Bootstrap Instance  
**Primary Actor:** Operator  
**Requirements:** FR-014  
**Goal:** The Operator creates the first site administrator of a fresh installation, so the instance can be used.  
**Status:** Implemented

## Preconditions

- The instance is installed and reachable.

## Main Success Scenario

1. Operator opens any page of the fresh instance.
2. System redirects to the setup page (public legal pages and the product page stay reachable).
3. Operator enters a username and password.
4. System validates the username and password.
5. System creates the account as site administrator with placeholder settings.
6. System redirects to the sign-in page.

## Alternative Flows

### A1: Invalid Username

**Trigger:** The username is invalid, reserved or previously deleted (step 4)  
**Flow:**

1. System shows the setup form again with an error.
2. Use case continues at step 3.

### A2: Blank Password

**Trigger:** The password is blank, meaning empty or only whitespace (step 4)  
**Flow:**

1. System shows the setup form again with an error.
2. Use case continues at step 3.

### A3: Instance Already Set Up

**Trigger:** At least one account exists (step 1)  
**Flow:**

1. System shows a not-found page for the setup page.
2. Use case ends.

## Postconditions

### Success Postconditions

- A site administrator account exists and must complete first-login setup (UC-009).

### Failure Postconditions

- No account is created; while no account exists, the instance keeps redirecting to setup.

## Business Rules

### BR-001: Username Rules

A username is lower-case, 2 to 64 characters of letters and digits with single hyphens between them. Reserved words (me, login, logout, signup, setup, forgot-password, reset-password, booking, api, q, health, calit, index, og, privacy, terms), taken usernames and usernames of deleted accounts are refused.

### BR-002: One-Time Setup

Setup is available only while no account exists; afterwards it behaves as if it did not exist (A3).

### BR-003: No Default Password

The instance ships without any default account or password.

### BR-004: Password Required

The first administrator must enter a password that is not blank (empty or only whitespace); no strength rule applies.
