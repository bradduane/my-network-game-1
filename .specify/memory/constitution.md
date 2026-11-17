# Campus Connect Constitution

## Core Principles

### I. Technical Truth Is a Release Requirement

The game MUST never reward a technically incorrect answer. Grading MUST be reproducible from a
documented rule and the challenge inputs. Unsupported or ambiguous content is an authoring error:
the game MUST show that the challenge is unavailable and MUST NOT grade it.

Model being taught (simplified and stated openly):

- **Wireless signal:** RSSI (dBm) is computed from a documented indoor log-distance path-loss
  model plus a fixed attenuation per wall type. A zone is "covered" when its RSSI meets the
  documented design threshold. In 2.4 GHz, only channels 1, 6, and 11 are non-overlapping; APs on
  overlapping channels that can hear each other reduce effective capacity.
- **Congestion:** each user type adds a documented demand (Mbps). AP load is the sum of its
  associated users' demand, limited by effective capacity; building load is limited by uplink
  capacity. Utilization above 100% is congestion, and each user's share shrinks proportionally.
- **Availability:** a building with a single uplink loses connectivity when that uplink fails; a
  redundant uplink keeps it online at reduced capacity.
- **Exclusions:** no TCP/IP dynamics, roaming, band steering, multipath, MU-MIMO/OFDMA, 5/6 GHz
  channel planning (unless added to the spec), vendor features, or security. All constants,
  thresholds, and sources live in `SPEC.md`.

Simplifications MUST appear in the level briefing and README wherever they affect what the player
can conclude. Theme and animation MAY illustrate a rule and MUST NOT change it.

**Evidence:** hand-checked correct, incorrect, and boundary cases for RSSI, channel overlap,
AP/uplink utilization, and uplink failure; a source link and section for each networking rule;
review of all shipped briefings, ticket texts, and explanations; expected results recorded
independently of the implementation under test.

### II. Every Decision Teaches

The player's main actions MUST exercise the target skills: placing APs, choosing channels, sizing
uplinks, adding redundancy, and diagnosing tickets. A concept MUST be introduced before it is
tested, and the values needed to reason about it MUST be visible: RSSI per zone, utilization per
AP and uplink, channel per AP, and active users.

- A wrong diagnosis MUST explain the specific misconception and point to a useful next step; a
  correct diagnosis MUST explain why it works.
- The end-of-wave review MUST explain every score change in terms of the model.
- Learning sequence: Level 1 Foundation (coverage) → Level 2 Application (capacity) → Level 3
  Transfer (event surge combining channel interference and an uplink failure).
- Hints reveal a method first, then a worked step. Worked solutions are available when stuck;
  viewing one is recorded as assistance, never as independent mastery.

**Evidence:** a teammate can connect each player action and ticket type to a learning outcome,
and can describe what they learned from a deliberately wrong diagnosis.

### III. Pressure Is Fair and the Rules Are Explicit

*Amendment to the starter rule:* "no lives, countdown, or speed bonus" is replaced for this game,
which needs a running clock and a firing condition. The protections below take its place.

- **Real time, always pausable.** The clock runs in real time; the player MUST be able to pause at
  any moment by keyboard or button. While paused, the player can inspect, build, configure, and
  work tickets, and the clock MUST NOT advance. An optional setting auto-pauses on ticket arrival
  and at each wave end.
- **Authored waves.** Users arrive in waves defined in level data with stated size and
  composition; the next wave MUST be previewed before it starts.
- **Fired only at the end-of-wave review.** The review compares documented metrics (campus
  satisfaction and open-ticket age) against stated thresholds; budget limits purchases but MUST
  NOT get the player fired. A firing MUST name the metric, its value, the threshold, and the
  network cause. Immediate level retry from its start, with no carry-over penalty, MUST be
  offered. Getting fired is a level outcome, not a judgment of mastery.
- **Tickets.** A ticket closes only on a valid, correct diagnosis and fix. A wrong diagnosis
  leaves it open, with an explanation and no score deduction. Empty or malformed submissions give
  input guidance and do not count as attempts. Repeated Submit actions MUST NOT close a ticket or
  award score twice.
- **Score.** Computed from documented components (users served, satisfaction, tickets resolved,
  budget remaining), each shown in the review. Hints carry no score penalty but are recorded.
- **Session rules.** The specification MUST state exactly what ends a wave, completes a level,
  fires the player, and resets a session.

**Evidence:** pause/resume, wrong-diagnosis, repeated-submit, hint, wave-review, firing, retry,
level-completion, and reset checks match the specification.

### IV. Access Is Part of the Core Game

All core actions MUST be usable by keyboard with visible focus, readable labels, and an
understandable order: map navigation, placing and configuring equipment, pausing, and working
tickets. Success, failure, coverage, and congestion MUST be conveyed with text and numbers as well
as color; heat-map color is never the only signal.

- A text equivalent of the campus map MUST exist: a labeled zone table showing RSSI, serving AP,
  channel, users, and utilization.
- Reduced-motion preferences MUST be respected; animation MUST NOT delay feedback or controls.
- Real-time pressure MUST NOT be the only way to experience the game; pause and auto-pause make
  every decision readable without time pressure.
- The game MUST be usable on a typical laptop and in a narrow window. Dense data MAY scroll within
  its own labeled container, without hiding pause or action buttons.

**Evidence:** a keyboard-only playthrough including pause and one ticket, a narrow-window check, a
reduced-motion check, and a check that the zone table matches the map. Record what was actually
checked; do not claim full accessibility certification.

### V. Deterministic and Self-Contained First Version

*Amendment:* real-time play is allowed, but the simulation stays deterministic.

- The simulation MUST advance in fixed time steps independent of frame rate.
- Arrivals, ticket triggers, and failures come from level data, or from a random generator whose
  seed is stored in level data. Same level + same actions at the same steps + same seed MUST
  produce the same results and score.
- The game MUST require no account, backend, live device connection, model, or API key.
- AI MAY help write code and assets during development; its output receives the same review as any
  other contribution. Runtime AI tutoring, generated tickets, and free-text AI grading are stretch
  work and require an explicit amendment to this constitution, the specification, and the
  verification approach before implementation.

**Evidence:** a clean browser session plays using only the project's static files; a scripted
replay of the same inputs produces identical wave reviews and scores; no external service decides
an answer.

### VI. Content and Logic Are Reviewed Separately

Levels, campus layouts, wall types, equipment specs, user demand, waves, tickets, hints, sources,
and thresholds MUST be stored in editable data. Domain rules (signal, channel overlap, load,
availability, ticket evaluation) MUST be separate from rendering, the simulation clock, and
progress tracking. Data MUST be validated before a level is presented.

Feedback MUST agree with the evaluator's reasoning. A hand-authored answer key MUST NOT silently
override the rule engine; a ticket's correct answer is derived from, or checked against, model
state. Adding a level or ticket that uses existing mechanics SHOULD require only a data change and
its checks, not a new scoring path or screen.

**Evidence:** inspect one level's data, trace one zone's RSSI and one AP's utilization through the
evaluator, and add a trial ticket without modifying the rule engine.

### VII. Done Is Demonstrated

Ship only the agreed scope. Requirements, plan, tasks, and README MUST describe the same game. A
feature is complete when its acceptance criteria pass and its actual status is recorded; a
screenshot, a checked task box, or an agent saying "done" is insufficient by itself.

When time is short, reduce scope explicitly following the cut order. Incorrect teaching and broken
core interactions MUST be fixed before visual effects are added. Failed and untested checks MUST
remain visible.

**Evidence:** reproducible run/test commands, recorded verification results, a fresh-start
playthrough, and a README another attendee can follow.

## Purpose, Scope & Cut Order

**Purpose:** help new network engineers and IT support staff practice diagnosing and designing for
Wi-Fi coverage, network congestion, and availability by building out a campus network ahead of
growing crowds and resolving the trouble tickets they generate.

**Genre:** Campus Connect is an original facility-management sim. It MAY use the genre's general
mechanics (build ahead of demand, handle incidents, risk being fired). It MUST NOT copy any
existing game's names, characters, art, music, levels, humor, or look.

**First release:** a real-time, pausable campus-builder with two connected activities and three
short levels:

1. **Build:** place APs, choose their channels, and connect buildings with uplinks on a grid map.
2. **Tickets:** diagnose and fix trouble tickets raised by users on campus.

**Cut order** (60-minute workshop budget): Level 3, then visual and animation polish, then
persistence. Correct grading, explanations, pause, and the end-of-wave review MUST NOT be cut.
This budget changes only by updating the agreed scope.

## Development Workflow & Evidence

- Each principle's **Evidence** list is its quality gate; a feature touching that principle is not
  done until the relevant evidence is recorded.
- Specs, plans, and tasks MUST include a Constitution Check against Principles I–VII; any
  deviation is recorded with its justification rather than silently accepted.
- Expected values for model checks are computed by hand (or from an independent source) and
  recorded before comparing against the implementation.
- Verification results, including failures and unrun checks, are recorded honestly in the README
  and task status.

## Governance

This constitution supersedes other project practices. For any material change: record the reason,
update the affected spec and plan, and rerun analysis (`/speckit-analyze`). A conflict with this
constitution is resolved openly; the build agent MUST NOT weaken a principle merely to make its
implementation pass. Amendments to Principles III and V for real-time play are recorded in-line.

Versioning follows semantic versioning: MAJOR for removing or redefining a principle, MINOR for
adding a principle or section or materially expanding guidance, PATCH for clarifications and
wording. Every plan and review MUST verify compliance with the current version.
`StarterKit/CONSTITUTION.md` is the workshop source text; this file is the governing copy for
Spec Kit commands and MUST be kept in agreement with it when either changes.

**Version**: 1.0.0 | **Ratified**: 2026-11-17 | **Last Amended**: 2026-11-17
