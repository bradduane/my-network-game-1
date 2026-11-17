# Campus Connect · Project constitution

**Purpose:** help new network engineers and IT support staff practice **diagnosing and designing for Wi-Fi coverage, network congestion, and availability** by **building out a campus network ahead of growing crowds and resolving the trouble tickets they generate**.

**Genre note:** Campus Connect is an original facility-management sim. It may use the genre's general mechanics: build ahead of demand, handle incidents, risk being fired. It must not copy any existing game's names, characters, art, music, levels, humor, or look.

**First release:** a real-time, pausable campus-builder with two connected activities and three short levels:

1. **Build:** place access points (APs), choose their channels, and connect buildings with uplinks on a grid campus map.
2. **Tickets:** diagnose and fix trouble tickets raised by the users on campus.

The 60-minute workshop budget is tight for this scope. If time runs short, cut in this order: Level 3, then visual and animation polish, then persistence. Never cut correct grading, explanations, pause, or the end-of-wave review. Change this budget only by updating the agreed scope.

## C1 · Technical truth is a release requirement

The game must never reward a technically incorrect answer. Grading must be reproducible from a documented rule and the challenge inputs. Unsupported or ambiguous content is an authoring error: show an explanation that the challenge is unavailable and prevent grading it.

**Model being taught (simplified and stated openly):**

- **Wireless signal:** received signal strength (RSSI, in dBm) is computed from a documented indoor path-loss model: loss grows with the log of distance, plus a fixed attenuation per wall type. A zone counts as "covered" when its RSSI meets the documented design threshold. In the 2.4 GHz band, only channels 1, 6, and 11 are treated as non-overlapping. APs on overlapping channels that can hear each other reduce effective capacity.
- **Congestion:** each user type adds a documented demand (in Mbps). An AP's load is the sum of its associated users' demand, limited by the AP's effective throughput capacity. A building's load is limited by its uplink capacity. Utilization above 100% means congestion, and each user's share of capacity shrinks proportionally.
- **Availability:** a building with a single uplink loses connectivity when that uplink fails. A redundant uplink keeps the building online at reduced capacity.
- **Exclusions:** no TCP/IP protocol dynamics, roaming algorithms, band steering, multipath, MU-MIMO/OFDMA, 5/6 GHz channel planning (unless added to the spec), vendor-specific features, or security. All constants, thresholds, and their sources live in `SPEC.md`.

Simplifications must appear in the level briefing and README wherever they affect what the player can conclude. The campus theme and animations may illustrate a rule; they must not change it.

**Evidence:** hand-checked correct, incorrect, and boundary cases for RSSI, channel overlap, AP/uplink utilization, and uplink failure. Each networking rule has a source link and section, such as an indoor propagation reference or the IEEE 802.11 channel plan. All shipped briefings, ticket texts, and explanations are reviewed. Expected results are recorded independently of the implementation under test.

## C2 · Every decision teaches

The player's main actions exercise the target skills: placing APs, choosing channels, sizing uplinks, adding redundancy, and diagnosing tickets. Introduce a concept before testing it, and show the values needed to reason about it: RSSI per zone, utilization per AP and uplink, channel per AP, and active users.

A wrong ticket diagnosis explains the specific misconception (for example, "this zone has strong signal; the AP is over capacity") and points to a useful next step. A correct diagnosis explains why it works. The end-of-wave review explains every score change in terms of the model.

Learning sequence: **Level 1 Foundation**, coverage (signal vs. distance and walls) → **Level 2 Application**, capacity (congestion as demand grows; AP and uplink sizing) → **Level 3 Transfer**, an event surge combining channel interference and an uplink failure (availability and redundancy).

Hints reveal a method first ("check the zone's RSSI before adding capacity"), then a worked step. Worked solutions are available when a learner is stuck. Viewing one is recorded as assistance, never as independent mastery.

**Evidence:** a teammate can connect each player action and ticket type to a learning outcome, and can describe what they learned from a deliberately wrong diagnosis.

## C3 · Pressure is fair and the rules are explicit (amended)

*Amendment:* the starter rule ("no lives, countdown, or speed bonus") is replaced for this game. The design needs a running clock and a firing condition. The fairness protections below take its place.

- **Real time, always pausable.** The simulation clock runs in real time. The player can pause at any moment by keyboard or button. While paused, the player can inspect everything, place and change equipment, and read or work tickets. The clock never advances while paused. An optional setting pauses automatically when a ticket arrives and at the end of each wave.
- **Authored waves.** Users arrive in waves defined in level data. Each wave has a stated size and composition, and the next wave is previewed before it starts.
- **Fired only at the end-of-wave review.** At the end of each wave, the review compares the documented metrics (campus satisfaction and open-ticket age; budget limits purchases but cannot get the player fired) against stated thresholds. Falling below a threshold means the player is fired. The review names the exact metric, its value, the threshold, and the network cause. The player can retry the level immediately from its start, with no carry-over penalty. Getting fired is a level outcome, not a judgment of mastery.
- **Tickets.** A ticket closes only on a valid, correct diagnosis and fix. A wrong diagnosis leaves the ticket open, with an explanation and no score deduction. Time keeps running unless the player pauses. Empty or malformed submissions give input guidance without counting as an attempt. Repeated Submit actions cannot close a ticket or award score twice.
- **Score.** The score is computed from documented components (users served, satisfaction, tickets resolved, budget remaining). The end-of-wave review shows each component. Hints have no score penalty, but they are recorded as assistance.
- **Session rules.** The specification must say exactly what ends a wave, completes a level, fires the player, and resets a session.

**Evidence:** pause/resume, wrong-diagnosis, repeated-submit, hint, wave-review, firing, retry, level-completion, and reset checks match the specification.

## C4 · Access is part of the core game

All core actions must be usable by keyboard with visible focus, readable labels, and an understandable order. This includes moving across the map, placing and configuring equipment, pausing, and working tickets. Explain success, failure, coverage, and congestion with text and numbers as well as color. Heat-map colors are never the only signal.

Provide a text equivalent for the campus map: a labeled table of zones showing RSSI, serving AP, channel, users, and utilization. Respect reduced-motion preferences. Animation must never delay feedback or controls. Real-time pressure must never be the only way to experience the game: pause and auto-pause make every decision readable without time pressure.

Keep the game usable on a typical laptop and in a narrow browser window. Dense data may scroll within its own labeled container, without hiding the pause or action buttons.

**Evidence:** a keyboard-only playthrough including pause and one ticket, a narrow-window check, a reduced-motion check, and a check that the zone table matches the map. Record what was actually checked; do not claim full accessibility certification.

## C5 · The first version is deterministic and self-contained (amended)

*Amendment:* real-time play is allowed, but the simulation stays deterministic.

The simulation advances in fixed time steps that don't depend on frame rate. Arrivals, ticket triggers, and failures come from level data, or from a random generator whose seed is stored in level data. The same level, the same player actions at the same simulation steps, and the same seed always produce the same results and score.

The game requires no account, backend, live device connection, model, or API key. AI may help write code and assets during development, and its output receives the same review as any other contribution. Runtime AI tutoring, generated tickets, and free-text AI grading are stretch work. Adding them requires an explicit change to this constitution, the specification, and the verification approach before implementation.

**Evidence:** a clean browser session can play using only the project's static files. A scripted replay of the same inputs produces identical wave reviews and scores. No external service decides an answer.

## C6 · Content and logic can be reviewed separately

Store levels, campus layouts, wall types, equipment specs, user demand, waves, tickets, hints, sources, and thresholds in editable data. Keep the domain rules (signal, channel overlap, load, availability, ticket evaluation) separate from rendering, the simulation clock, and progress tracking. Validate data before presenting a level.

Feedback must agree with the evaluator's reasoning. A hand-authored answer key cannot silently override the rule engine. A ticket's correct answer is derived from, or checked against, the model's state.

Adding another level or ticket that uses existing mechanics should require a data change and its checks, not a new scoring path or a new screen.

**Evidence:** inspect one level's data, trace one zone's RSSI and one AP's utilization through the evaluator, and add a trial ticket without modifying the rule engine.

## C7 · Done is demonstrated

Ship only the agreed scope. The requirements, plan, tasks, and README must describe the same game. A feature is complete when its acceptance criteria pass and its actual status is recorded. A screenshot, a checked task box, or the agent saying "done" is insufficient by itself.

When time is short, reduce scope explicitly, following the cut order above. Fix incorrect teaching and broken core interactions before adding visual effects. Keep failed and untested checks visible.

**Evidence:** reproducible run/test commands, recorded verification results, a fresh-start playthrough, and a README another attendee can follow.

## How we change these rules

For any material change, record the reason, update the affected spec and plan, and rerun analysis. A conflict with this constitution is resolved openly; the build agent must not weaken a principle merely to make its implementation pass. Amendments to C3 and C5 for real-time play are recorded above.
