# Use Case: Manage Availability

## Overview

**Use Case ID:** UC-011  
**Use Case Name:** Manage Availability  
**Primary Actor:** Owner  
**Requirements:** FR-029, FR-030  
**Goal:** The Owner defines when they can be booked — weekly working hours and exceptions for specific dates — globally or for a single meeting type.  
**Status:** Implemented

## Preconditions

- The Owner is signed in and has completed setup (UC-009).

## Main Success Scenario

1. Owner opens the availability page.
2. System shows the global weekly grid and all working-hour windows.
3. Owner edits the weekly grid, with any number of windows per weekday.
4. System replaces the global weekly hours with the valid windows.
5. Owner opens the date overrides page.
6. System shows upcoming overrides soonest first and past overrides collapsed, based on today in the Owner's time zone.
7. Owner adds an override for a date, globally or for one meeting type, with up to three windows or none for a day off.
8. System stores the override.

## Alternative Flows

### A1: Hours for One Meeting Type

**Trigger:** Owner edits the weekly grid on a meeting type's detail page (step 3)  
**Flow:**

1. System prefills the grid with the global week when the type has no hours of its own.
2. Owner saves the grid.
3. System stores the hours for that meeting type only.
4. Use case continues at step 5.

### A2: Invalid Window Skipped

**Trigger:** A weekly window is incomplete or ends before it starts (step 4)  
**Flow:**

1. System skips that window and stores the rest.
2. Use case continues at step 5.

### A3: Remove Window or Override

**Trigger:** Owner deletes a working-hour window or a date override (step 2 or step 6)  
**Flow:**

1. System removes it.
2. Use case continues at step 2.

### A4: Meeting Type Not Owned

**Trigger:** The chosen meeting type was not created by the Owner (step 7); a Co-host sets per-type hours and overrides through UC-013 instead  
**Flow:**

1. System shows a not-found page and stores nothing.
2. Use case ends.

### A5: Invalid Override Windows

**Trigger:** An override window ends before it starts, or more than three windows are given (step 8)  
**Flow:**

1. System rejects the override with an error and stores nothing; incomplete windows are skipped as in A2.
2. Use case continues at step 7.

## Postconditions

### Success Postconditions

- The Owner's working hours and date overrides are stored and are used for the next slot calculation (UC-001 BR-001).

### Failure Postconditions

- Availability is unchanged.

## Business Rules

### BR-001: Weekly Hours Replace

Saving a weekly grid replaces all weekly hours of that scope (global or one meeting type).

### BR-002: Meeting-Type Hours Own the Week

As soon as a meeting type has any hours of its own, they replace the global weekly hours for that type.

### BR-003: One Override per Date and Scope

An Owner has at most one override per date for the global scope and one per meeting type; a global and a type-specific override may share a date.

### BR-004: Day Off

An override without windows makes the whole date unavailable.

### BR-005: Local Times

Working hours and overrides are expressed in the Owner's time zone.
