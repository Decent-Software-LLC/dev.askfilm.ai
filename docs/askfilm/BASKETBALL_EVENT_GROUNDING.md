<!-- SPDX-FileCopyrightText: Copyright (c) 2026 Decent Software LLC -->
<!-- SPDX-License-Identifier: Apache-2.0 -->

# AskFilm Basketball Event Grounding

## Objective

Identify distinct basketball events visibly supported by video. Produce an event log, not a frame-by-frame caption stream. One real-world action should normally produce one event record.

## Event ontology and minimum evidence

### Start / Stop of Play

- **Start:** an inbound, jump ball, made free throw followed by live possession, or another clear resumption.
- **Stop:** a whistle reaction, referee signal, ball clearly out of bounds, players stopping for free throws, or another visible dead-ball state.
- Ordinary slowing is not a stop.

### Change of Possession

- Require visible control by the other team following a rebound, steal, turnover, made basket and inbound, jump ball, or another clear transfer.
- A loose or deflected ball alone is not a change.

### Shot Attempt

- Require a visible shooting motion and ball release toward the basket.
- Record the release once; do not repeat it during ball flight.
- Classify two, three, or free throw only when court position or context is clear.

### Scoring

- Require the ball passing through the basket, unmistakable net reaction, an attributable scoreboard change, or a clear immediate inbound after a make.
- When obscured, retain the attempt and mark the result uncertain rather than inventing a score.

### Rebound

- Require a missed shot followed by established player control.
- Offensive: the shooting team retains control.
- Defensive: the defending team gains control.
- A tip is not a rebound until control is established.

### Turnover

- Require the offense to lose possession without a shot producing the change.
- Examples include a steal, intercepted pass, offensive out-of-bounds, traveling, offensive foul, or visible violation.
- State a subtype only when supported.

### Steal

- Track a steal separately from its associated turnover and possession change when requested.
- A pass from one team caught and controlled by the opponent is an intercepted-pass steal.
- A direct strip becomes a steal when the defender establishes control.
- A tip, deflection, or uncontrolled loose ball is not yet a steal.
- Identify involved players by uniform color and legible jersey number when possible.

### Takeaway

- A takeaway is the broader team-possession event when the defense causes the loss and establishes control.
- It includes an interception, direct strip, or recovery of a defensively dislodged ball.
- An unforced error, offensive pass out of bounds, or uncontrolled loose ball is not a takeaway.
- Every confirmed steal is also a takeaway. A takeaway may lack a steal subtype when the exact mechanism is unclear.

### Jump Ball

- Require a visible referee toss between opponents or a clearly signaled held-ball situation leading to alternating possession.
- Distinguish an opening tip from a later held ball when context permits.
- Players reaching for a loose ball are insufficient.

### Foul

- Require a stoppage plus referee signal/report, free-throw setup, or strong contextual evidence. Contact alone is insufficient.
- Follow the sequence when visible: contact or violation, stop, referee report to the scorer's table, and resulting inbound or free throws.
- Use the reporting interval to determine the committing player's jersey number, foul type, and penalty only when signals are legible.
- Classify personal, shooting, offensive, technical, flagrant, or uncertain only when supported.
- Treat an `announced foul` as the visible referee report in the current visual path. Do not claim spoken information without an explicit audio transcript.

### Free Throw / Penalty Shot

- Track each free-throw attempt separately.
- Require dead-ball administration, the shooter at the line, and visible release.
- Record make/miss only when visible.
- Include shooter color, legible jersey number, and attempt count only when supported.
- Link to the preceding foul when visible.

### Transition / Fast Break

- Require rapid defense-to-offense advancement before the defense is set.
- Record once near its beginning; separately record any resulting attempt or score.

### Timeout

- Require a timeout signal, teams gathering at benches, or an extended organized stoppage distinct from an ordinary dead ball.

### Substitution

- Require a player reporting or entering while another leaves, or a clear lineup exchange during a dead ball.

### End of Quarter / Period

- Require horn/clock expiration plus players stopping or leaving. Do not infer from a video cut alone.

### End of Game

- Require clear final-game context distinct from the end of a non-final period.

## Temporal rules

1. Use a clip-relative timeline beginning at `00:00.000`.
2. Report the moment an event becomes identifiable, not every sampled frame depicting it.
3. Consolidate continuous action into one event. Never repeat the same description at fixed intervals.
4. Made-shot sequence: attempt -> scoring -> possession change -> inbound/resumption, with each event separately supported.
5. Missed-shot sequence: attempt -> rebound -> optional possession change.
6. Interception sequence: steal -> turnover -> possession change -> optional transition.
7. Defensive takeaway sequence: takeaway plus optional steal -> turnover -> possession change -> optional transition.
8. Foul sequence: contact -> stop -> referee report -> free throw or inbound -> resumption.
9. Jump-ball sequence: stop/start -> jump ball -> possession change when control is established.
10. Prefer omission over fabrication.
11. High confidence requires direct evidence; medium requires strong sequence evidence; low is plausible but obscured and should generally be omitted.

## Player and description rules

- Describe what visibly happened and why the classification is supported.
- Include uniform color, court location, direction, and outcome when clear.
- Include jersey numbers only when legible in multiple frames or exceptionally clear in one. Never guess.
- Good: `White #23 releases a shot from the right wing; the ball travels toward the rim.`
- Good steal: `Dark #7 intercepts a pass from white #12 near midcourt and establishes control.`
- Good foul: `Referee reports a shooting foul on dark #14; white #23 prepares for two free throws.`
- Good jump ball: `The referee tosses the opening tip between white #15 and dark #32 at center court.`
- Bad: `Shot attempt.`
- Bad: the same event repeated at regular one-second intervals.

## Output and validation

- Each record must include a clip-relative timestamp, event type, confidence, description, and visual evidence.
- The model should estimate coarse evidence intervals, not source-frame numbers.
- Deterministic code must calculate source timestamps and frames, merge overlap duplicates, and reject contradictions.
- Events at the first frames of an overlapping window require corroboration from the prior window.
- Scores and rebounds require a nearby attempt.
- Possession changes require nearby scoring, rebound, turnover, steal, takeaway, or jump-ball evidence.
- Free throws require nearby foul or dead-ball administration.
- If evidence is insufficient, return fewer events and state the limitation.
