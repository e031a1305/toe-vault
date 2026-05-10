# Video Intelligence System — Architecture Blueprint
*TOE Vault · Master Project Document*

---

## Project Summary

A locally-hosted web application for esoteric video analysis — pattern recognition, symbology, subliminal detection, and TOE vault integration. Runs on ARM64 hardware, accessed via browser on the local intranet. No recurring API cost. Videos never leave the local network.

---

## Hardware Inventory

| Device | Role | Notes |
|---|---|---|
| Raspberry Pi 5 (8GB) | Web server, task queue, OpenCV Stage 1, file management | Passive cooling — marine environment. Do not run sustained LLM inference here. |
| Google Pixel (GrapheneOS) | LLM inference via Termux + llama.cpp | Tensor chip with NPU. Best free local option. |
| Oracle Cloud Free Tier (optional) | Remote LLM inference | 4 ARM cores, 24GB RAM. Genuinely free forever. Best quality option. |

---

## The Four Options

### Option A — RPi5 Only
- Model: Moondream2 (1.8B, ~1.5GB) or LLaVA 7B Q4 (~4.5GB)
- Cost: $0
- Speed: 5–15 min/video
- Risk: Sustained CPU heat on passive-cooled marine hardware
- Verdict: Viable for occasional use. Not for batch processing 200 videos.

### Option B — RPi5 + Pixel (Recommended Free Option)
- RPi5: web UI, OpenCV frame extraction, task queue, vault output
- Pixel: runs llama.cpp via Termux, receives flagged frames over local network HTTP
- Model: LLaVA 7B Q4 or Moondream2 on Termux
- Cost: $0
- Speed: 2–5 min/video (Tensor NPU assists)
- Privacy: 100% local, no cloud dependency
- Verdict: Best balance of cost, quality, and thermal safety for the RPi5.

### Option C — RPi5 + Oracle Cloud Free Tier (Recommended Quality Option)
- RPi5: web UI, OpenCV, task queue, vault output
- Oracle: runs Ollama + LLaVA 13B (24GB RAM enables larger models)
- RPi5 sends flagged frames to Oracle via HTTPS, receives analysis JSON
- Cost: $0 forever (Oracle ARM free tier is permanent)
- Speed: Fast — larger model, more RAM, dedicated instance
- Tradeoff: Requires internet connection during analysis. Frames leave local network.
- Verdict: Best analysis quality at zero cost.

### Option D — RPi5 + RunPod GPU (Best Quality, Small Cost)
- RPi5: web UI, OpenCV, task queue
- RunPod: spins up on-demand GPU instance, runs LLaVA 34B or Llama 3.2 Vision 11B
- Cost: ~$0.20–0.40/hr. 200 videos ≈ $1.50–3.00 total. Well under $10/month.
- Speed: Fastest. Highest quality output.
- Verdict: Best for large batch processing. Pay only when running.

---

## Recommended Architecture

**Primary: Option B (RPi5 + Pixel)**
**Fallback/Batch: Option C (Oracle Free Tier)**

Use the Pixel for day-to-day single video analysis. Switch to Oracle for batch runs of many videos. RPi5 never runs inference — protecting the passive cooling setup.

---

## System Architecture

```
Browser (any device on LAN)
        │
        ▼
[ RPi5 — Flask Web Server :5000 ]
        │
        ├── /upload        → receives video file
        ├── /analyze       → triggers pipeline
        ├── /status        → SSE progress stream
        └── /vault         → browse/download markdown output
        │
        ▼
[ Stage 1 — OpenCV on RPi5 ] (lightweight, no model)
        │
        Detects:
        ├── Scene cuts & flash events
        ├── Geometric contours & shapes
        ├── Face/eye regions
        ├── Color dominance shifts
        ├── Optical flow anomalies
        └── Brightness spikes (subliminal candidates)
        │
        Outputs: flagged_frames[] with timestamps & detection_type
        │
        ▼
[ Stage 2 — Vision LLM ] (on Pixel or Oracle)
        │
        Receives: flagged frames only (not full video)
        Prompt: TOE esoteric analysis system prompt
        Returns: JSON — symbols, patterns, hidden messages,
                         core message, TOE connections, tags
        │
        ▼
[ Markdown Generator on RPi5 ]
        │
        Writes: YYYY-MM-DD - [title].md
        Format: TOE Tagging Standard frontmatter
        Path: vault output folder (synced to Google Drive)
        │
        ▼
[ Browser — Results Display ]
```

---

## Technology Stack

| Layer | Technology | Reason |
|---|---|---|
| Web server | Python + Flask | Lightweight, RPi5-appropriate, simple SSE support |
| Task queue | Redis + RQ | Non-blocking video processing, progress tracking |
| Video decode | ffmpeg + PyAV | ARM64-optimized, handles all formats |
| CV analysis | OpenCV 4.x (ARM64) | Native ARM build, fast on RPi5 |
| LLM inference | llama.cpp (Termux) / Ollama (Oracle) | ARM64-optimized, runs vision models |
| Vision model | LLaVA 7B Q4 (Pixel) / LLaVA 13B (Oracle) | Best vision+language balance per hardware |
| Frontend | Vanilla HTML/CSS/JS | No build step, no dependencies, fast |
| Vault output | Markdown + YAML frontmatter | TOE Tagging Standard compliant |
| Sync | rclone → Google Drive | Scheduled or manual push from RPi5 |

---

## File Structure on RPi5

```
/home/e031a/toe-vision/
├── app.py                  # Flask server
├── pipeline/
│   ├── extractor.py        # ffmpeg frame extraction
│   ├── cv_analyzer.py      # OpenCV Stage 1
│   ├── llm_client.py       # HTTP client → Pixel or Oracle
│   └── markdown_gen.py     # TOE vault note generator
├── static/                 # Frontend HTML/CSS/JS
├── templates/
├── vault_output/           # Generated .md files (rclone syncs this)
├── video_inbox/            # Uploaded videos (temp)
└── config.yaml             # Inference target, model, paths
```

---

## LLM Inference Setup

### Option B — Pixel (GrapheneOS + Termux)
```bash
# In Termux on Pixel
pkg install clang cmake git
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp && make -j4 LLAMA_CLBLAST=1
# Download LLaVA 7B Q4 GGUF
# Run as HTTP server on port 8080
./llama-server -m llava-7b-q4.gguf --port 8080 --host 0.0.0.0
```

### Option C — Oracle Cloud
```bash
# On Oracle ARM instance
curl -fsSL https://ollama.com/install.sh | sh
ollama pull llava:13b
ollama serve  # accessible via public IP + firewall rule
```

---

## TOE Analysis System Prompt (Core)

The LLM receives this system prompt with every frame batch:

> You are an expert in symbology, esoteric traditions, subliminal analysis, and the Theory of Everything knowledge framework. Analyze these video frames for: recurring patterns, symbols (occult/sacred/corporate/governmental), subliminal elements, hidden messages, core overt and covert messaging, and connections to TOE domains (consciousness, cosmology, astrology, sacred-geometry, numerology, power-structures, simulation-theory, frequency-vibration, mythology, esoteric-traditions). Return structured JSON only.

---

## Vault Output Format

Every analyzed video produces one markdown file:

```yaml
---
title: "[AI-generated descriptive title]"
date: YYYY-MM-DD
type: video-analysis
tags: [symbology, hidden-knowledge, power-structures, ...]
source_file: "video.mp4"
duration_seconds: 2700
frames_analyzed: 34
significance: high|medium|low
vault_worthy: true
---
```

Saved to: `vault_output/YYYY-MM-DD - [title].md`
Synced to: `My Drive/toe/03 - Projects/Video-Intelligence-System/`

---

## Cost Summary

| Scenario | Cost |
|---|---|
| 200 videos × 45 min (Option B — Pixel) | $0 |
| 200 videos × 45 min (Option C — Oracle) | $0 |
| 200 videos × 45 min (Option D — RunPod) | ~$1.50–3.00 |
| Monthly ongoing (any option) | < $5 |

---

## Build Order (Phase Sequence)

1. **Phase 1** — RPi5 Flask server + file upload + ffmpeg extraction
2. **Phase 2** — OpenCV Stage 1 (scene detection, flash detection, contour/shape, face regions)
3. **Phase 3** — LLM client (configurable target: Pixel or Oracle)
4. **Phase 4** — Markdown generator (TOE Tagging Standard)
5. **Phase 5** — Frontend (drag-drop upload, progress, results, vault preview)
6. **Phase 6** — rclone sync to Google Drive vault

---

## Open Questions

- [ ] Confirm Pixel LAN IP is static (set in GrapheneOS network settings)
- [ ] Decide primary inference target: Pixel (Option B) or Oracle (Option C)
- [ ] Confirm vault sync method: rclone scheduled cron, or manual trigger from UI
- [ ] Video storage: how much space is available on RPi5? SSD or SD card?

---

*Blueprint created: 2026-05-10*
*Project: Video Intelligence System*
*Status: Pre-development — awaiting build phase approval*
