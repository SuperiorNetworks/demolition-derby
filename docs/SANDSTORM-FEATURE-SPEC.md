# Sandstorm — Feature Specification & Feedback Review

**Document:** SANDSTORM-FEATURE-SPEC.md  
**Revision:** 1.0  
**Date:** June 9, 2026  
**Source:** Dwain Henderson Jr. — field feedback session  
**Applies to:** `sandstorm-php/` (David's PHP web app) + `detonator-react/` (React editor)

---

## Overview

This document captures all feedback from the June 2026 review session, organized into actionable GitHub Issues with priority, category, and implementation notes for David. Each section maps directly to a GitHub Issue filed in this repo.

---

## 1. Naming Recommendations

### 1a. App Name: Keep "Sandstorm"

**Recommendation: Keep Sandstorm.** It is strong, field-appropriate, and already has brand recognition within the project. No change needed.

### 1b. Rename "Send Live Timeline" → "Launch Sequence"

"Send Live Timeline" is a developer-facing label that describes the mechanism, not the action. For an operator standing at a control station, the button should communicate what is about to happen.

| Current Label | Recommended Label | Rationale |
|---------------|------------------|-----------|
| Send Live Timeline | **Launch Sequence** | Describes the operator action, not the data transfer |
| Timeline | **Sequence** | Shorter, cleaner, industry-standard in pyrotechnics |
| Trigger Editor | **Sequence Editor** | Consistent with the new naming |
| Relay Monitor | **Live Monitor** | Clearer — "relay" is internal jargon |
| Timeline (page/tab) | **Sequences** | Plural for the list view |

**Rename all three editors consistently.** See Issue #7 for the full editor consistency requirement.

---

## 2. Launch Countdown — Full UX Specification

**Category:** Enhancement  
**Priority:** Phase 1  
**Labels:** `enhancement`, `ux`, `sandstorm-php`, `phase-1`

### Current Behavior
The "Send Live Timeline" button triggers a 30-second countdown with no on-screen feedback and no audio announcement.

### Required Behavior

#### 2a. On-Screen Countdown Display

When the operator clicks **Launch Sequence**, the following sequence must occur:

1. A full-screen or large modal overlay appears immediately, covering the control interface.
2. The overlay displays a large countdown timer (font size: readable from 10 ft away).
3. The overlay cannot be dismissed accidentally — it requires a deliberate abort action.

#### 2b. Audio Announcement Script

The system must play a synthesized or pre-recorded audio announcement at the following cue points:

| Time | Audio Cue | Display |
|------|-----------|---------|
| T-30 | *"Attention. Launch sequence initiated. System is armed. All systems are go. Stand clear of all blast zones."* | T-30 countdown begins |
| T-20 | *"T minus 20 seconds."* | T-20 on screen |
| T-10 | *"T minus 10 seconds."* | T-10 on screen |
| T-5 | *"T minus 5..."* | Large "5" |
| T-4 | *"4..."* | Large "4" |
| T-3 | *"3..."* | Large "3" |
| T-2 | *(silence — audience completes the count)* | Large "2" |
| T-1 | *(silence — audience completes the count)* | Large "1" |
| T-0 | *"Ignition."* or music starts | Sequence fires |

**Design intent:** T-5 through T-3 are announced by the system. T-2 and T-1 are left silent so the crowd naturally completes the countdown. This is a standard technique in live event production that builds audience participation and energy.

#### 2c. Pre-Launch Checklist Cards (30-Second Window)

During the 30-second countdown, a set of **checklist cards** must flash or cycle on screen. These are not blocking — the operator does not need to click them — but they serve as a visual reminder of what must be confirmed before ignition.

**Recommended checklist cards:**

| Card | Check |
|------|-------|
| 1 | All personnel clear of blast zones? |
| 2 | Fire suppression armed and accessible? |
| 3 | All relay connections confirmed (green)? |
| 4 | Video feed active and recording? |
| 5 | Radio check with field team complete? |
| 6 | Abort procedure confirmed with co-operator? |

Cards should cycle at ~4-second intervals, or display all simultaneously in a grid if screen size allows. Each card should have a visual state: **unchecked** (yellow) and **confirmed** (green, operator clicks). If any card is not confirmed by T-10, it flashes red as a warning — but does not block the sequence.

---

## 3. Manual Abort / Override

**Category:** Safety-Critical Feature  
**Priority:** Phase 1  
**Labels:** `enhancement`, `safety`, `sandstorm-php`, `phase-1`

During the 30-second countdown, the operator must be able to abort at any time. The abort mechanism must be:

- A large, clearly labeled **ABORT** button on the countdown overlay (red, prominent)
- A keyboard shortcut: `Escape` key triggers abort confirmation dialog
- A hardware E-stop button on the physical control station (cuts relay coil power — hardware, not software)

**Abort behavior:**
1. All relay outputs immediately de-energize (hardware E-stop handles this at the board level)
2. Software sends ALL RELAYS OFF command to ESP32
3. Countdown overlay closes
4. System returns to SAFE state
5. An abort event is logged in the audit log (see Issue #4) with timestamp, user, and time-remaining at abort

**Industry standard note:** In professional pyrotechnics, the person who physically initiates the abort is called the **Safety Officer**. This role must be defined in the system. See Issue #5 for role definitions.

---

## 4. Audit Log — Action History with User Attribution

**Category:** New Feature  
**Priority:** Phase 1  
**Labels:** `enhancement`, `sandstorm-php`, `phase-1`

### Requirement

Every action taken in Sandstorm must be logged with:

- Timestamp (ISO 8601, server time)
- Username of the operator who initiated the action
- Action type (sequence launched, relay fired, abort triggered, user logged in, config changed, etc.)
- Result (success / failed / aborted)
- Session ID

### Recommended Log Location

The audit log belongs on the **Sensors / Monitoring page** (the second page David has started). This keeps the primary control interface clean while making the log accessible to supervisors.

### Log Entry Examples

```
2026-07-10 21:34:02  |  dwain      |  SEQUENCE_LAUNCH   |  "Iron District Show 1"  |  SUCCESS
2026-07-10 21:34:45  |  dwain      |  RELAY_FIRE        |  R1 (BZ1)                |  SUCCESS
2026-07-10 21:35:10  |  david      |  ABORT             |  T-12s remaining         |  MANUAL_OVERRIDE
2026-07-10 21:35:11  |  SYSTEM     |  ALL_RELAYS_OFF    |  post-abort              |  SUCCESS
2026-07-10 21:40:00  |  dwain      |  CONFIG_CHANGE     |  R3 delay: 4.2s → 5.0s  |  SAVED
```

### Retention

Logs should be stored server-side (PHP + flat file or MySQL table) and exportable as CSV. Minimum retention: 90 days.

---

## 5. User Authentication and Role System

**Category:** New Feature  
**Priority:** Phase 1  
**Labels:** `enhancement`, `safety`, `sandstorm-php`, `phase-1`

### Login Requirement

Sandstorm must require login before any operator can access the control interface. Anonymous access must be disabled for all pages except the login screen.

### Role Definitions

The following roles are based on industry-standard pyrotechnics crew structure:

| Role | Name | Permissions | Industry Equivalent |
|------|------|-------------|---------------------|
| `admin` | **Show Director** | Full access — create sequences, manage users, view logs, initiate launch | Licensed Pyrotechnic Operator |
| `operator` | **Fire Operator** | Can arm system, initiate launch, trigger manual relays, view logs | Assistant Pyrotechnic Operator |
| `safety` | **Safety Officer** | Can view status, trigger abort, view logs — **cannot** initiate launch | Range Safety Officer (RSO) |
| `viewer` | **Observer** | Read-only — can view live monitor and logs — no control access | Spectator / Crew |

**Industry standard:** In professional pyrotechnics and demolition events, the person who physically pushes the fire button is the **Licensed Operator** (or "shooter"). A separate **Range Safety Officer (RSO)** must be present whose sole authority is to call a hold or abort. These two roles must never be the same person during a live sequence. Sandstorm should enforce this at the software level.

### Role Enforcement Rules

- A sequence cannot be launched unless at least one `operator` or `admin` AND one `safety` user are both logged in and have confirmed ready.
- The `safety` role user has a dedicated **HOLD** button that is always visible and always active during any countdown.
- The `admin` role is the only role that can create or modify sequences.

---

## 6. Multi-User Lockout and Dual-Key Authorization

**Category:** New Feature  
**Priority:** Phase 1  
**Labels:** `enhancement`, `safety`, `sandstorm-php`, `phase-1`

### Concept

When multiple users are logged in, the system must require **both the operator and the safety officer to confirm ready** before the launch sequence can begin. This is the software equivalent of the physical dual-key system used in nuclear launch protocols and professional pyrotechnics.

### UI Behavior

1. When the operator clicks **Launch Sequence**, the system does not immediately begin the countdown.
2. Instead, a **Ready Confirmation** screen appears for all logged-in users with `operator` or `safety` roles.
3. Each required user sees a **"Turn Key"** button (or equivalent — could be labeled "Confirm Ready" or "ARM").
4. The button is styled as a key icon that turns green when activated.
5. The countdown does not begin until **all required keys are green**.
6. If any required user does not confirm within 60 seconds, the launch request times out and must be re-initiated.

### Visual Design Recommendation

Each user's key should appear as a card on screen:

```
┌─────────────────────┐    ┌─────────────────────┐
│  🔑  OPERATOR       │    │  🔑  SAFETY OFFICER  │
│  dwain              │    │  david               │
│  [  CONFIRM ARM  ]  │    │  [  CONFIRM ARM  ]   │
│  ● WAITING          │    │  ● WAITING           │
└─────────────────────┘    └─────────────────────┘
         ↓ both green ↓
    COUNTDOWN BEGINS
```

When both cards are green, the countdown begins automatically. If either user clicks their key again (or presses Escape), the sequence is cancelled and both keys reset.

---

## 7. Editor Consistency — All Features Available in All Three Editors

**Category:** Enhancement / Bug  
**Priority:** Phase 1  
**Labels:** `enhancement`, `sandstorm-php`, `phase-1`

### Problem

David has built editing capability in three different places:
- The **Sequence view** (timeline/list view)
- The **Trigger Editor** (per-trigger detail editor)
- The **Relay Monitor** (live view)

Currently, not all editing features are available in all three editors. This creates confusion — an operator may not know which editor to use for a given task, and may miss features that exist in one editor but not another.

### Required Changes

Every editing action must be available in all three editors. The table below defines what must be consistent:

| Action | Sequence View | Trigger Editor | Live Monitor |
|--------|:---:|:---:|:---:|
| Add new trigger | ✅ | ✅ | ✅ |
| Edit trigger delay | ✅ | ✅ | ✅ |
| Edit relay assignment | ✅ | ✅ | ✅ |
| Delete trigger | ✅ | ✅ | ✅ |
| Reorder triggers | ✅ | ✅ | — |
| Fire single relay manually | — | — | ✅ |
| View relay status | ✅ | ✅ | ✅ |
| Save sequence | ✅ | ✅ | ✅ |

**Implementation note for David:** The cleanest approach is to build a single shared editing component (a React component or a PHP partial) that renders in all three contexts. This avoids maintaining three separate implementations of the same logic.

---

## 8. Live Video Feed Zone

**Category:** New Feature  
**Priority:** Phase 2  
**Labels:** `enhancement`, `sandstorm-php`, `phase-2`

### Requirement

The Sensors / Monitoring page should include a dedicated zone for a live video feed of the blast field. The video feed allows the operator to visually confirm field conditions before and during the sequence.

### Layout Recommendation

```
┌──────────────────────────────────────────────────────┐
│  LIVE VIDEO FEED                                      │
│  ┌────────────────────────────────────────────────┐  │
│  │                                                │  │
│  │         [  LIVE CAMERA FEED  ]                 │  │
│  │                                                │  │
│  └────────────────────────────────────────────────┘  │
│                                                       │
│  SENSOR DATA                    AUDIT LOG             │
│  ┌──────────────────┐           ┌──────────────────┐  │
│  │  Relay Status    │           │  Action History  │  │
│  │  R1 ● ARMED      │           │  21:34 dwain...  │  │
│  │  R2 ● ARMED      │           │  21:33 david...  │  │
│  └──────────────────┘           └──────────────────┘  │
└──────────────────────────────────────────────────────┘
```

### Camera Options (Phase 2)

- IP camera on the same WiFi hotspot as the control station (RTSP stream embedded via HLS.js or similar)
- Phone camera using a browser-based WebRTC stream (no app install required)
- YouTube Live embed if a public stream is used for the audience

**Phase 2 note:** Video feed is not required for Phase 1 (July 10). It is a Phase 2 feature. However, the page layout should reserve space for it now so the UI does not need to be restructured later.

---

## 9. Issue Classification Summary

The table below maps each feedback item to its GitHub Issue number, category, and priority.

| # | Issue Title | Category | Priority | Labels |
|---|------------|----------|----------|--------|
| 1 | Rename "Send Live Timeline" → "Launch Sequence" and standardize all labels | Enhancement | Phase 1 | `ux`, `sandstorm-php`, `phase-1` |
| 2 | Launch countdown: on-screen display, audio cues, T-minus announcement | Enhancement | Phase 1 | `ux`, `sandstorm-php`, `phase-1` |
| 3 | Pre-launch checklist cards during 30-second countdown window | Enhancement | Phase 1 | `ux`, `safety`, `sandstorm-php`, `phase-1` |
| 4 | Manual abort / override during countdown | Safety | Phase 1 | `safety`, `sandstorm-php`, `phase-1` |
| 5 | Audit log with user attribution on Sensors page | New Feature | Phase 1 | `enhancement`, `sandstorm-php`, `phase-1` |
| 6 | User login and role system (Show Director, Fire Operator, Safety Officer, Observer) | New Feature | Phase 1 | `enhancement`, `safety`, `sandstorm-php`, `phase-1` |
| 7 | Multi-user dual-key authorization before launch | Safety | Phase 1 | `safety`, `sandstorm-php`, `phase-1` |
| 8 | Editor consistency — all features in all three editors | Enhancement | Phase 1 | `enhancement`, `sandstorm-php`, `phase-1` |
| 9 | Live video feed zone on Sensors page | New Feature | Phase 2 | `enhancement`, `sandstorm-php`, `phase-2` |

---

*See also: [MOBILE-COMMAND-STATION.md](MOBILE-COMMAND-STATION.md) for the physical hardware specification.*  
*See also: [REPO-AUDIT.md](REPO-AUDIT.md) for the original repository audit.*
