# Athlink - AI Football Video Intelligence

> **Public overview** - The main Athlink CV service repository is private and contains proprietary service code, API keys, and production infrastructure. This repository provides a high-level technical overview for portfolio purposes.

## Overview

Athlink is an end-to-end computer vision pipeline for turning football match footage into structured tactical data. The system is designed around detection, tracking, pitch mapping, ball-state estimation, event detection, and coach-facing reporting.

The public repository is intentionally an overview: it explains the architecture, engineering trade-offs, and evaluation approach without exposing private service code, model weights, credentials, or production infrastructure.

## What This Project Shows

| Area | Evidence |
|------|----------|
| Computer vision systems | Multi-stage football perception pipeline from video frames to tactical events |
| ML engineering | Detector, tracker, ReID, calibration, and event-state components designed as separate validation surfaces |
| Product thinking | Outputs aimed at coaches and analysts, not just model demos |
| Infrastructure awareness | Queue-based processing and GPU offloading considered for long match videos |
| Evaluation discipline | Emphasis on provenance, tracking identity quality, and report-level validation |

## Pipeline Architecture

### 1. Detection
- Player, ball, referee, and staff detection
- Broadcast-video challenges: camera motion, occlusion, small ball size, variable lighting
- Detector outputs treated as provenance-bearing inputs to downstream tracking

### 2. Tracking (BoT-SORT + ReID)
- BoT-SORT with appearance embeddings for identity persistence across occlusions
- ReID gallery built per-match from high-confidence crops
- Handles player re-identification after out-of-play, set-pieces, and substitutions

### 3. Pitch Mapping (Homography Calibration)
- Keypoint detection (penalty spots, corner arcs, centre circle, line intersections)
- DLT-based homography estimation with RANSAC outlier rejection
- Maps broadcast coordinates to a canonical 105m x 68m top-down pitch model
- Per-frame adaptive calibration for camera zoom/pan changes

### 4. Ball State Estimation
- Three-class classification per frame: `possession` / `loose` / `dead`
- Temporal smoothing with hidden Markov model for phase consistency
- Team assignment via spatial clustering of nearest tracked players

### 5. Game Phase Belief State
- Per-frame classification: `live`, `set-piece`, `transition`, `stoppage`
- Enables downstream analytics to separate tactical signals from tracking noise
- Critical for possession metrics, pressing intensity, formation analysis

### 6. Event Detection
- Automated detection of passes, shots, tackles, interceptions, set pieces
- Spatial-temporal validation against tracking + ball state
- Output: structured event stream with timestamps, actors, locations, outcomes

### 7. Coaching Reports
- ElevenLabs Voice API integration for narrated tactical summaries
- Async job queue (Railway) with GPU offloading (Modal) for 90-min processing
- Report templates: match summary, player profile, team analysis, set-piece review

## Tech Stack

| Layer | Technology |
|-------|------------|
| CV/ML | PyTorch, Ultralytics YOLO11, BoT-SORT, OpenCV |
| API/Backend | FastAPI, Redis, PostgreSQL |
| Orchestration | Railway (job queue), Modal (GPU inference) |
| Voice | ElevenLabs API (multilingual, low latency) |
| Frontend | React, Three.js (pitch viz), Mapbox GL |
| Identity | PlayerBook (cross-match player linking via ReID) |

## Deployment Considerations

- API and worker separation for long-running video jobs
- GPU offloading for expensive inference stages
- Queue health, inference latency, and failure-state monitoring as first-class production concerns

## Links

- **Public overview (this repo):** `github.com/Liyrs58/athlink`
- **Main service repo:** Private (contains proprietary code, configs, secrets)
- **Demo videos:** Available on request

## Status

Active private development. This public repository is a portfolio overview rather than the production codebase.

---

*This overview is for portfolio and recruitment purposes. The production service, model weights, API keys, and infrastructure configs remain in the private repository.*
