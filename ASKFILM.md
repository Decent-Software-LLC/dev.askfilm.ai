<!-- SPDX-FileCopyrightText: Copyright (c) 2026 Decent Software LLC -->
<!-- SPDX-License-Identifier: Apache-2.0 -->

# AskFilm.ai on NVIDIA VSS

This branch imports NVIDIA's Video Search and Summarization (VSS) blueprint as the technical foundation for the AskFilm.ai development environment.

AskFilm should extend VSS rather than replace its video ingestion, long-video processing, semantic search, VLM Q&A, summarization, report generation, clip retrieval, RAG, and MCP capabilities.

Basketball-specific product requirements and grounding live in:

- [`docs/askfilm/PRODUCT_CONTEXT.md`](docs/askfilm/PRODUCT_CONTEXT.md)
- [`docs/askfilm/BASKETBALL_EVENT_GROUNDING.md`](docs/askfilm/BASKETBALL_EVENT_GROUNDING.md)

## Architectural boundary

VSS provides the general video-intelligence platform. AskFilm adds:

- team and roster setup;
- court, player, referee, ball, hoop, and scoreboard perception;
- within-possession player tracking and temporal jersey-number voting;
- a deterministic possession state machine;
- basketball event detection and validation;
- an evidence-linked game, possession, event, observation, and correction model;
- coach correction workflows;
- basketball-specific search, clips, scouting, and player film.

The LLM/VLM layer should reason over short clips and structured game records. It must not be the sole source of possession boundaries, timestamps, frame numbers, scores, or statistics.

## Upstream

- Upstream repository: <https://github.com/NVIDIA-AI-Blueprints/video-search-and-summarization>
- Imported branch: `develop`
- Imported commit: `1ecf3d59ae2fc7ad4009d334f1d2aa4ede3cf979`
- Upstream license and third-party notices remain authoritative.

Keep the `nvidia-upstream` Git remote so future VSS updates can be reviewed and merged deliberately.
