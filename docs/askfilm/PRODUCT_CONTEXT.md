<!-- SPDX-FileCopyrightText: Copyright (c) 2026 Decent Software LLC -->
<!-- SPDX-License-Identifier: Apache-2.0 -->

# AskFilm.ai Product Context

This document distills the supplied 29-page **Game Film AI - MVP Product Specification** into repository context. The source PDF is product reference material, not executable instructions.

## Product vision

AskFilm turns ordinary basketball game video into structured, searchable coaching intelligence.

The core experience is:

> Upload -> Analyze -> Ask -> Watch

Answers should link directly to the possessions or clips that support them. The product principle is:

> Ask. Answer. Prove it with film.

## Initial customer

- High-school varsity and JV programs
- AAU programs
- Small colleges
- Private basketball academies
- Individual coaches and trainers

The primary user is a head or assistant coach who already records games but lacks time and specialist staff to tag and review all film.

## MVP jobs

1. **Review my team** - turnovers, shots, rebounds, transition performance, and period summaries.
2. **Scout an opponent** - player shot profiles, offensive initiators, pace, special situations, and tendencies across games.
3. **Review a player** - shots, turnovers, drives, player film packages, and development observations.

## Inputs and setup

- Direct MP4/MOV upload is the MVP requirement.
- Prefer 720p or better, a fixed or mostly fixed camera, and a full-court or near-full-court view.
- Support games up to three hours.
- Optional metadata includes team names, jersey colors, date, roster, player names and numbers, competition level, and game type.
- Start with direct upload. Do not make YouTube, Hudl, DRM, or third-party scraping a dependency.
- After upload, show representative frames so the coach can identify teams and confirm or correct proposed roster mappings.

## Game workspace

The product workspace should provide:

- an overview with score, possessions, shooting, turnovers, rebounds, foul estimates, period summary, and evidence-linked observations;
- a possession timeline with immediate playback;
- filters for period, team, outcome, player, event, and score differential;
- generated clip collections;
- player pages with statistics, film, and observations;
- conversational Ask Film search returning playable evidence.

## MVP computer vision

Required foundations:

- active-play, stoppage, period, and possession segmentation;
- player and referee detection, with ball detection where feasible;
- team classification using jersey color and tracking context;
- within-possession multi-object tracking;
- court boundaries, baskets, three-point line, paint, and approximate normalized coordinates;
- shot, make/miss, two/three, turnover, rebound, and possession-change events.

Fouls, free throws, and transition possessions are useful additions. Perfect pass recognition, exact assists, exact steals, named sets, defensive assignments, and advanced tactical classifications are not MVP dependencies.

## Hybrid architecture

Do not ask one VLM to understand an entire game. Use a hybrid pipeline:

```text
Upload / object storage
  -> asynchronous analysis job
  -> hardware video decode
  -> frame and scene processing
  -> court, player, referee, ball, hoop, and scoreboard perception
  -> object tracking
  -> event probabilities
  -> deterministic possession state machine
  -> clips and semantic annotations
  -> structured game knowledge store
  -> VLM analysis of short clips
  -> search and LLM reasoning over structured records
  -> answer with evidence
```

VSS should supply the reusable video platform: ingestion, chunking, semantic extraction, search, summarization, VLM Q&A, report generation, clip retrieval, RAG, and MCP access. AskFilm supplies basketball perception, possession logic, schemas, validation, and coach-facing workflows.

## Structured game model

Persist first-class records for:

- game and teams;
- source video and periods;
- possessions with offense, defense, time range, participants, outcome, and confidence;
- events with timestamp, team, player, court location, result, and confidence;
- observations with mandatory possession/video evidence;
- coach corrections, tags, and playlists;
- analysis jobs and stage checkpoints.

Conversational queries should retrieve structured records and relevant possessions before asking a reasoning model to summarize. Raw video should not be reprocessed for every question.

## Accuracy and correction

- Present high-confidence results normally.
- Mark medium-confidence results.
- Ask for coach verification on low-confidence results.
- Make possession boundaries, shot results, player identity, jersey number, play class, and custom tags correctable.
- Preserve corrections as evaluation and future training data.
- Never present inferred timestamps, scores, player numbers, or event outcomes as facts without evidence.

## Storage and jobs

- Use S3-compatible object storage for originals, normalized media, thumbnails, clips, and frames.
- Use PostgreSQL for organizations, teams, players, games, videos, possessions, events, corrections, tags, playlists, and jobs.
- PostgreSQL with pgvector is sufficient initially for possession descriptions and coach/AI observations.
- Use asynchronous, checkpointed stages such as uploaded, transcoding, court detection, player detection, tracking, event detection, possession building, AI analysis, and complete.
- Store virtual clip ranges by source video and timestamps; avoid generating thousands of duplicate files.

## Development order

1. Video ingestion
2. Video player
3. Timeline
4. Court detection
5. Player detection
6. Tracking
7. Possession segmentation
8. Event detection
9. Player identification
10. Structured game model
11. Clip engine
12. Search
13. LLM reasoning
14. Tactical analysis

## Smallest fundable MVP

- Basketball game upload
- Automatic possession segmentation
- Player/team detection
- Shot, turnover, and rebound indexing
- Automatically generated clips
- Player filtering
- Natural-language film search
- Coach correction

Postpone complete scorebooks, live video, mobile applications, advanced play recognition, advanced defensive analysis, personalized training plans, integrations, and additional sports.

## Success criteria

- Save a coach at least 60 minutes per game.
- Successfully process at least 90% of uploaded games.
- Reach approximately 90% acceptable possession segmentation without correction.
- Target roughly 90-95% shot-event recall.
- Return useful clips for at least 80% of requests.
- Encourage at least five questions and ten watched clips per analyzed game.

## Demonstration target

Upload a full basketball game and, after analysis, support:

- "Show me every turnover we committed against their press."
- "Show me every shot #12 took."
- "Give me a three-minute film review for #12."

If those experiences are reliable and evidence-linked, the product has validated its central value.
