# Athlink — AI Football Video Intelligence

Computer vision pipeline that transforms match footage into frame-level tactical data.

## Overview

Athlink processes football match video through a multi-stage CV pipeline:

- **Detection**: YOLO11n — player, ball, referee, staff detection per frame
- **Tracking**: BoT-SORT with ReID embeddings for identity persistence across occlusions
- **Pitch Mapping**: Homography-based calibration mapping broadcast footage to a canonical top-down model
- **Belief State**: Per-frame game-phase classification (live, set-piece, transition) — distinguishes tactical signals from tracking noise
- **Ball State**: Possession, loose-ball, and dead-ball classification per frame
- **Events**: Automated detection of passes, shots, tackles, and set pieces
- **Coaching Reports**: Narrated tactical summaries via ElevenLabs voice API

## Architecture

- Async job queue on Railway for match processing orchestration
- GPU-accelerated inference via Modal for full 90-min match runs
- FastAPI backend with Redis-backed job state management
- PlayerBook identity management for cross-match player linking

## Repos

- **[athlink-cv](https://github.com/Liyrs58/athlink-cv)** — Core CV pipeline (private, contains service implementation)
- **athlink** (this repo) — Public overview, documentation, and demos

## Status

Active development. Pre-release.
