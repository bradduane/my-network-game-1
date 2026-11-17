# Campus Connect · First playable specification

## The pitch

**Build a campus Wi-Fi network ahead of the crowds, and keep it fast and available as they grow. Learn to tell weak signal from congestion from interference, or get fired.**

The player is the new network manager of a small college campus. Each level shows a top-down campus map with buildings, walls, users, and a live readout of signal strength (RSSI), channel, and utilization. The player places access points (APs), chooses channels, and buys building uplinks before and during waves of arriving users. When users suffer, trouble tickets appear, and the player must diagnose the cause.

A correct diagnosis earns points, and the explanation shows the values that prove it. A wrong diagnosis leaves the ticket open and explains which value rules that cause out. At the end of each wave, the Dean of IT reviews satisfaction and ticket age. Missing a threshold gets the player fired, with the reason explained. The session ends when the player finishes all three levels (final score shown) or chooses to stop after being fired.

## Who this is for

- **Learner:** new network engineers, help-desk staff, and IT students with little or no hands-on wireless design experience.
- **Assumed knowledge:** what Wi-Fi, an access point, and a network link are. No prior knowledge of dBm, channels, or capacity planning.
- **What the briefing teaches:** RSSI in dBm (closer to 0 is stronger), the coverage threshold, how distance and walls weaken signal, demand vs. capacity (Mbps) and utilization (%), the 2.4 GHz non-overlapping channels 1/6/11, and single vs. redundant uplinks.
- **Why interaction helps:** the misconception that "slow Wi-Fi means weak signal, so add more APs". Players see zones with strong signal that are still slow, because the AP, the uplink, or the channel is the bottleneck, and adding a badly placed or badly channeled AP can make things worse.
- **Session target:** about 10–15 minutes for a first playthrough. The clock can be paused at any time, so thinking time is unlimited.

## Learning outcomes and evidence

| ID | After playing, the learner can… | The game asks them to… | Evidence of success |
| --- | --- | --- | --- |
| LO1 | Predict whether a zone has usable coverage from distance and walls | Place APs so every occupied zone reaches the RSSI threshold | All occupied zones are covered at the Level 1 wave reviews; "Weak signal" tickets are diagnosed correctly |
| LO2 | Distinguish AP congestion from uplink congestion by comparing demand with capacity | Add APs or upgrade an uplink as demand grows; diagnose congestion tickets | Level 2 reviews pass; the player picks "AP overloaded" vs. "Uplink congested" correctly |
| LO3 | Diagnose co- and adjacent-channel interference and design for availability | Assign 2.4 GHz channels; add a redundant uplink before a scripted failure | Level 3 reviews pass after the uplink failure; "Channel interference" and "Uplink down" tickets are diagnosed correctly |

## The technical model

All constants live in level/equipment data. All comparisons use unrounded values. Displays round RSSI to 0.1 dBm and utilization to a whole percent.

**Map:** a grid of 5 m × 5 m cells. A *zone* is one cell inside a building. Distance is the straight-line distance between cell centers, with a minimum of 1 m. Walls lie on cell edges and have a type.

**Wireless signal (2.4 GHz only):**
- RSSI(dBm) = EIRP − PL(1 m) − 10·n·log₁₀(d) − Σ wall losses
- EIRP = 17 dBm per AP. PL(1 m) = 40 dB: free-space loss at 1 m and 2.437 GHz, 20·log₁₀(2437) − 27.55 ≈ 40.2 dB, rounded to 40 for the game. Path-loss exponent n = 3.0 (indoor office). These are game constants.
- Wall loss for each wall the straight line between the AP and zone centers crosses: drywall 3 dB, brick 8 dB, concrete 12 dB.
- **Covered:** RSSI ≥ −67.0 dBm. **Serving AP:** the AP with the highest RSSI at that zone; a tie goes to the lowest AP ID. Users in an uncovered zone receive 0 Mbps.

**Channels:** APs use 2.4 GHz channels 1–11 (US channel plan). Two APs *conflict* when each hears the other (RSSI from one at the other's cell ≥ −82.0 dBm) and their channel numbers differ by 0–4 (same or overlapping). Channels 1, 6 and 11 never conflict with each other. An AP's **effective capacity** is 100 Mbps ÷ (1 + number of conflicting APs).

**Congestion:**
- User demand: *Light* (web/email) 2 Mbps, *Video* 5 Mbps, *Event* 3 Mbps.
- AP utilization = sum of its users' demand ÷ effective capacity. Above 100% means overloaded, and each user receives a proportional share.
- Building traffic = sum of what its APs deliver. Uplink utilization = building traffic ÷ total working uplink capacity. Above 100% means congested, and every AP's delivery in that building is scaled down proportionally.
- Utilization of exactly 100% is not congested.

**Availability:** uplinks are 100 Mbps or 1 Gbps. A building's capacity is the sum of its working uplinks. A building with no working uplink delivers 0 Mbps. Level data can schedule uplink failures at a set simulation time.

**Satisfaction:** campus satisfaction = total Mbps delivered ÷ total Mbps demanded × 100. Zone satisfaction uses the same formula, applied to one zone.

**Excluded behavior:** 5/6 GHz, channel widths other than 20 MHz, per-AP client-count limits, roaming and band steering, multipath/fading, MU-MIMO/OFDMA, TCP behavior, spanning tree/LACP failover timing, wired switch capacity, security, and vendor features. The briefings say plainly: "This is a simplified model; real networks have more effects."

**Authoritative references** (record the exact section or table you verified):
- Free-space path loss formula and the log-distance indoor model: ITU-R Recommendation P.1238 (indoor propagation), section *[VERIFY]*. Supports the distance-power-loss approach and the indoor exponent around 3.
- The 2.4 GHz channel plan, 5 MHz channel spacing, and the non-overlapping set 1/6/11: IEEE 802.11-2020 (DSSS/HR-DSSS channel plan), clause *[VERIFY]*.
- −82 dBm as the 20 MHz OFDM clear-channel-assessment (CCA) threshold: IEEE 802.11-2020 OFDM PHY CCA requirements, clause *[VERIFY]*.
- −67 dBm as a coverage design target for voice/video: a vendor wireless design guide (cite one, e.g. Cisco or Aruba), section *[VERIFY]*. Wall loss values are approximate, illustrative game constants; cite the site-survey guide used, or label them "illustrative".

## The repeatable game loop

1. **Brief:** each level opens paused with a briefing on one new concept, worked through an example on a different map.
2. **Inspect:** the map and the zone table show RSSI, serving AP, channel, users, demand, AP utilization, and uplink utilization. A preview shows the next wave's size and user types.
3. **Build and choose:** while paused or running, the player places, moves, or removes APs, sets channels (Level 3), and buys or upgrades uplinks within budget. When a ticket appears, the player selects a diagnosis (Weak signal, AP overloaded, Uplink congested, Channel interference, or Uplink down) and submits it.
4. **Explain:** ticket feedback shows the zone's values and the rule that connects them. The end-of-wave review explains each score component and any failed threshold.
5. **Retry or advance:** a wrong diagnosis leaves the ticket open. Being fired offers an immediate level retry. Passing the final wave review completes the level.
6. **Reflect:** the level summary shows waves passed, tickets diagnosed (first try, after a hint, or after a worked solution), score, and budget left.

**Worked interaction (Level 2):** the dorm has three APs (channels 1, 6 and 11), each serving 12 Video users (60 Mbps, 60% of 100 Mbps), on one 100 Mbps uplink. A ticket appears: *"Dorm room 2B: Wi-Fi is really slow tonight."* Zone 2B shows RSSI −53.0 dBm. The player picks **Weak signal**. Feedback: *"Not this time. Zone 2B's RSSI is −53.0 dBm, well above the −67 dBm coverage threshold, so signal isn't the bottleneck. Check what the traffic passes through next."* The player picks **AP overloaded**: *"AP-5 is at 60% of its 100 Mbps, so it has spare capacity. Look further upstream."* The player picks **Uplink congested**: *"Correct. The dorm's three APs send 180 Mbps into a 100 Mbps uplink (180%), so every user gets about 56% of their demand. Upgrade the uplink to 1 Gbps or add a second link."* The ticket shows *Diagnosed – awaiting fix* and closes once zone 2B is fully served.

## Required stories and acceptance criteria

### US1 · Build ahead of demand — P1

As a learner, I want to place equipment and see how signal and capacity respond, so I can serve users before they arrive.

- **AC1:** Given a fresh session, when I start, then the Level 1 briefing appears with the clock paused before any user arrives.
- **AC2:** Given an AP placed 20 m from a zone with one drywall between them, when the model updates, then the zone shows RSSI −65.0 dBm and "Covered".
- **AC3:** Given a purchase that exceeds the remaining budget, when I try it, then it is refused with the cost and the remaining budget shown, and nothing changes.
- **AC4:** Given the game is paused, when I build, inspect, or work tickets, then the simulation clock, arrivals, and ticket ages do not advance.

### US2 · Diagnose trouble tickets — P1

As a learner, I want to diagnose why users are unhappy and understand why my answer is right or wrong.

- **AC5:** Given a zone with users whose satisfaction stays below 100% for 10 simulated seconds, then one ticket for that zone opens (at most one open ticket per zone).
- **AC6:** Given the worked-interaction ticket, when I submit **Uplink congested**, then the ticket becomes *Diagnosed – awaiting fix*, +25 points are awarded once, and the explanation shows 180 Mbps over 100 Mbps.
- **AC7:** Given that ticket, when I submit **Weak signal**, then the feedback cites RSSI −53.0 vs. −67 dBm, the ticket stays open, and no points change.
- **AC8:** Given no diagnosis is selected, when I submit, then input guidance appears and the attempt count does not change. Repeated Submit after a correct diagnosis has no effect.
- **AC9:** A hint first explains the method ("compare the signal, then the AP, then the uplink"). Further help shows a worked solution. Both are recorded as assistance and don't diagnose the ticket for the player.
- **AC10:** If more than one cause is true for a zone, any true cause is accepted, and the explanation lists every true cause. *(Default; confirm in clarify.)*

### US3 · Wave review, firing, and progression — P1

As a learner, I want clear rules for passing, losing, and scoring.

- **AC11:** At the end of each wave, the review shows campus satisfaction, the oldest open ticket's age, and the score components.
- **AC12:** If campus satisfaction is below 70.0%, or any ticket has been open longer than 90 simulated seconds, the player is fired. The review names the metric, its value, the threshold, and the network cause, and offers **Retry level**. Exactly 70.0% passes.
- **AC13:** Retry level restores the level's starting state (map, budget, score at level start) with no extra penalty.
- **AC14:** Passing the third wave review completes the level and opens the next briefing. Completing Level 3 shows the session summary with the final score.
- **AC15:** Restart asks for confirmation, then clears the session and returns to the Level 1 briefing. Cancel keeps the session.

### US4 · Play with accessible controls — P1

As a keyboard user, I want to do everything without a mouse and without time pressure.

- **AC16:** Every core action works by keyboard with visible focus and accessible names: moving a map cursor, placing or removing equipment, setting a channel, pausing or resuming (a single key, e.g. Space), and working tickets.
- **AC17:** Auto-pause when a ticket opens and at each wave review is available, and is on by default.
- **AC18:** The zone table matches the map's values. Feedback is text that stays on screen and is announced to assistive technology. Heat-map colors always have numbers beside them. Reduced motion removes the user-movement animation without hiding any information.

## Version-one content

**Three levels × three waves.** Each wave lasts 60 simulated seconds, with users arriving evenly during the wave. Speeds are 1× and 2×.

| Level | New concept | Prerequisite | Misconception to expose | Setup |
| --- | --- | --- | --- | --- |
| 1 · Orientation Week | Coverage: distance and walls (LO1) | None | "One AP in the middle covers the whole building" | Library with drywall and concrete walls. Waves of 20/40/60 Light users. 1 Gbps uplink pre-installed. Up to 3 APs, channels auto-assigned 1/6/11 and locked. Budget $2,000 |
| 2 · Dorm Move-In | Capacity: AP vs. uplink congestion (LO2) | Level 1 | "Slow means weak signal, so add APs" | Dorm on a 100 Mbps uplink. Waves of 20/40/50 Video users (100/200/250 Mbps). Up to 3 APs, channels auto 1/6/11. Budget $2,500 |
| 3 · Homecoming | Interference and availability (LO3) | Level 2 | "Any channel is fine" and "one link is enough" | Stadium and student center. Waves of Event users. Up to 6 APs with manual channels 1–11. The student center's uplink fails at 0:30 of wave 2 (previewed as "scheduled maintenance risk"). Budget $5,000 |

**Prices:** AP $500. 100 Mbps uplink $200. 1 Gbps uplink $800 (an upgrade costs the difference). Removing equipment refunds 100%. Channel changes are free.

**Scoring:** each wave review adds round(campus satisfaction %), worth up to 100 points. Each correct diagnosis adds 25 points; hints and worked solutions don't reduce it, but they are recorded. Completing a level adds floor(remaining budget ÷ 100). The session's high score is kept for the page session only.

**Session behavior:** progress is held for the current page session, and reloading starts again. Saving across reloads, and a persistent high score, are stretch features.

## Cases the implementation must satisfy

Expected values were computed by hand from the formulas above. **A second person must re-check them before implementation.** EIRP 17, PL(1 m) 40, n 3.0, threshold −67.0, CCA −82.0, AP capacity 100 Mbps.

| Case | Input state | Player action / event | Expected result | Why / source |
| --- | --- | --- | --- | --- |
| Coverage – correct | AP 20 m (4 cells straight) from the zone, 1 drywall | Model update | RSSI = 17−40−39.03−3 = **−65.0 dBm**, Covered | Signal model |
| Coverage – boundary in | AP offset (5, 3) cells = 29.15 m, no walls | Model update | RSSI **−66.9 dBm** (−66.94), Covered | ≥ −67.0, unrounded |
| Coverage – boundary out | AP offset (6, 0) cells = 30 m, no walls | Model update | RSSI **−67.3 dBm** (−67.31), Not covered; users get 0 Mbps | Rounding must not change grading |
| Weak signal | Zone 15 m from the only AP, 1 concrete wall | Ticket → "Weak signal" | RSSI **−70.3 dBm** (−70.28); correct | Wall loss |
| AP overloaded | Zone at −53.0 dBm; AP serves 25 Video users (125 Mbps), no conflicts | Ticket → "Weak signal" | Wrong: "RSSI −53.0 dBm is above −67; AP is at **125%** of 100 Mbps" | Common misconception |
| Uplink congested | 3 APs × 60 Mbps, channels 1/6/11, one 100 Mbps uplink | Ticket → "AP overloaded" | Wrong: each AP is at 60%; the uplink is at **180%**; each user gets **55.6%** of demand | Uplink model |
| Utilization boundary | Uplink 100 Mbps carrying exactly 100 Mbps | Model update | **100%, not congested**; satisfaction 100% | > 100% means congested |
| Channel conflict | AP-A ch 1, AP-B ch 3, 20 m apart, no walls (each hears the other at −62.0 ≥ −82) | Model update | Conflict. Each effective capacity **50 Mbps** | Channels differ by 2 ≤ 4 |
| Channel boundary | Same APs, ch 1 and ch 5 / ch 1 and ch 6 | Model update | 1 and 5: conflict (differ by 4). 1 and 6: **no conflict**, 100 Mbps each | Non-overlapping 1/6/11 |
| Redundancy | Building demand 180 Mbps on two 100 Mbps uplinks; one fails | Scheduled failure | Capacity 200 → 100. Uplink 180%; delivery 55.6% | Availability model |
| Single point of failure | Building on one 1 Gbps uplink; it fails | Scheduled failure | Building delivers **0 Mbps**; tickets → "Uplink down" | Availability model |
| Firing boundary | Campus demand 100 Mbps, delivered 70.0 / 69.9 | Wave review | 70.0% passes; 69.9% fired, with the reason shown | AC12 |
| Unsupported content | AP channel 13 in level data; a wall line passing exactly through a cell corner; a zone outside any building | Open level | Visible content error; the level does not start or grade | Authoring error |

## Experience direction

- **Visual metaphor:** a top-down campus with buildings and walls, users as dots, and a signal heat map with RSSI numbers that can be toggled on.
- **One memorable moment:** the scripted uplink failure in Level 3. The student center goes dark, and users' dots turn grey with "Uplink down" status, unless the player built a second link, in which case the building stays lit at reduced speed.
- **Always visible:** the clock and pause state, budget, score, campus satisfaction, the open tickets list, and the selected zone's RSSI, channel, utilization, and uplink load.
- **Feedback voice:** patient and specific, from a friendly senior engineer. Name the value that rules a cause in or out, then suggest the next place to look.
- **Layout:** the map in the center, the ticket queue and selected-zone details at the side, the zone table available as a tab. Never hover-only.
- **Originality:** campus art, characters (the Dean of IT and users), and humor are original. Nothing is copied from existing management games.

## Non-goals

No user accounts, multiplayer, backend, live infrastructure, runtime AI, persistent leaderboard, or level editor. The game does not simulate 5/6 GHz, roaming, client limits, wired switching, spanning tree, security, or vendor-specific features. Visual polish must fit the existing mechanics.

## Open decisions for clarify

- Multiple true causes for one ticket: accept any true cause (current default), or require a priority order (for example Uplink down → Weak signal → Channel interference → Uplink congested → AP overloaded)?
- Is campus satisfaction measured at the moment of the wave review (current default) or averaged across the wave?
- Should removing equipment during a wave still refund 100%?
- Ticket timing: a ticket opens after 10 s below 100% zone satisfaction, and the firing limit is 90 s open. Are these right for 60-second waves?
- Exact references: fill in every *[VERIFY]* section above, or relabel those constants as illustrative.

## Release evidence

All acceptance criteria have a recorded result. Every case above passes in automated checks. All three levels and every ticket explanation are checked against the references. A teammate completes the core flow using only the keyboard, including pause and one ticket. The README describes the implemented behavior, exact run/test commands, and any remaining limits.
