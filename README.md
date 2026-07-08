# Athlink — AI Football Video Intelligence

> **Public overview** — The main Athlink CV service repository is private and contains proprietary service code, API keys, and production infrastructure. This repository provides a high-level technical overview for portfolio purposes.

## Overview

Athlink is an end-to-end computer vision pipeline that transforms raw football match footage into frame-level tactical data. The system processes full 90-minute matches through a multi-stage pipeline: detection, tracking, pitch mapping, ball-state estimation, event detection, and automated coaching reports.

## Pipeline Architecture

### 1. Detection (YOLO11n)
- Custom-trained YOLO11n for player, ball, referee, and staff detection
- Optimized for broadcast camera angles and varying lighting conditions
- Runs at 25+ FPS on consumer GPUs

### 2. Tracking (BoT-SORT + ReID)
- BoT-SORT with appearance embeddings for identity persistence across occlusions
- ReID gallery built per-match from high-confidence crops
- Handles player re-identification after out-of-play, set-pieces, and substitutions

### 3. Pitch Mapping (Homography Calibration)
- Keypoint detection (penalty spots, corner arcs, centre circle, line intersections)
- DLT-based homography estimation with RANSAC outlier rejection
- Maps broadcast coordinates → canonical 105m×68m top-down pitch model
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

## Deployment

- Production: Railway (API + workers) + Modal (batch GPU inference)
- CI/CD: GitHub Actions → Docker → Railway
- Monitoring: Prometheus + Grafana for queue health, inference latency, error rates

## Links

- **Public overview (this repo):** `github.com/Liyrs58/athlink`
- **Main service repo:** Private (contains proprietary code, configs, secrets)
- **Demo videos:** Available on request

## Status

Active development. Pre-release.

---

*This overview is for portfolio and recruitment purposes. The production service, model weights, API keys, and infrastructure configs remain in the private repository.*
