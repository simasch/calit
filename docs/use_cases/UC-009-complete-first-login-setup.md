# Use Case: Complete First-Login Setup

## Overview

**Use Case ID:** UC-009  
**Use Case Name:** Complete First-Login Setup  
**Primary Actor:** Owner  
**Requirements:** FR-020  
**Goal:** A newly created Owner provides their name, email and time zone, so their scheduling page can go live.  
**Status:** Implemented

## Preconditions

- The Owner is signed in (UC-007) and has not completed setup.

## Main Success Scenario

1. Owner opens any management page.
2. System redirects to the setup wizard.
3. Owner enters display name, email and time zone.
4. System saves the Owner's settings.
5. System seeds default working hours, Monday to Friday 09:00 to 18:00.
6. System marks setup as complete and shows the dashboard.

## Alternative Flows

### A1: Password Change Required

**Trigger:** The account is marked as requiring a new password (step 3)  
**Flow:**

1. System additionally asks for a new password.
2. Owner enters a new password together with the settings.
3. If the password is blank (empty or only whitespace), system shows "Please choose a new password" and the form again, saving nothing; use case continues at step 3.
4. Otherwise system sets the new password; use case continues at step 4.

### A2: Unknown Time Zone

**Trigger:** The submitted time zone is not recognised (step 4)  
**Flow:**

1. System uses UTC instead.
2. Use case continues at step 5.

### A3: Working Hours Already Exist

**Trigger:** The Owner already has global working hours (step 5)  
**Flow:**

1. System leaves the existing hours untouched.
2. Use case continues at step 6.

### A4: Missing Name or Invalid Email

**Trigger:** The display name or email is left empty, or the email does not have the form of an email address (step 3)  
**Flow:**

1. System does not accept the form, points the Owner to the field to correct and saves nothing.
2. Use case continues at step 3.

## Postconditions

### Success Postconditions

- The Owner has settings and working hours, and their public page can show meeting types.

### Failure Postconditions

- Setup stays incomplete and every management page keeps redirecting to the wizard.

## Business Rules

### BR-001: Setup Gate

Until setup is complete, every management page redirects to the wizard; connecting Google remains reachable.

### BR-002: Default Working Hours

Default hours are seeded only on first completion and only when the Owner has no global working hours.

### BR-003: Required Profile Data

Display name and email are required, and the email must have the form of an email address as the Owner's browser recognises it; the form checks this before submitting (A4). The time zone must be a known time zone, otherwise UTC is used (A2).
