# Local Intelligence Platform — Master Blueprint
*TOE Vault · Master Project Document · Living Document*
*Version 4.0 — Updated 2026-05-10*

---

## Vision Statement

A private, locally-hosted intelligence platform running on ARM64 hardware. Its purpose is to reverse-engineer the hidden structural, esoteric, and geometric frameworks used by long-lasting institutions — royal families, governments, religions, Fortune 500 companies — and apply those findings to personal and operational decisions. Sacred geometry is the meta-framework connecting all modules.

---

## Core Thesis

> If an institution has lasted — it works. Study what it does. Extract the pattern. Apply it.

The triangle is the foundational structure. This platform finds it everywhere and makes it actionable.

---

## Hardware

| Device | Role |
|---|---|
| Raspberry Pi 5 (8GB) | Docker host — all services. Web server, task orchestration, OpenCV, audio analysis, AdGuard network-wide DNS. No LLM inference. |
| Seagate SSD (2TB, USB) | Primary active storage — vault output, inbox, models, database |
| WD HDD (2TB, 12V external) | Archive — original source files (video, books, audio) |
| SD card (115G, 67G free) | OS + Docker + application code only |
| Google Pixel (GrapheneOS) | Primary LLM inference — Termux + llama.cpp. Same LAN. |
| Spare Android phone | Secondary LLM inference — dedicated to RESONANCE module |
| Oracle Cloud Free Tier | Batch inference fallback — 4 ARM cores, 24GB RAM, free forever |
| Samsung Smart TV | Read-only dashboard display via browser |

---

## Storage Layout

| Mount | Device | Contents |
|---|---|---|
| `/` | SD card | OS, Docker engine, app code |
| `/mnt/ssd` | Seagate SSD | vault_output, inbox, LLM models, Redis data |
| `/mnt/archive` | WD HDD | Original source files — video, books, audio |

---

## Network Architecture

**AdGuard Home** runs in Docker on the RPi5, acting as DNS server for the entire network. All devices point to RPi5's static IP as their DNS server. Ads and trackers blocked network-wide — including the Samsung TV.

```
Router (DHCP)
    │
    ├── RPi5 static IP (e.g. 192.168.1.10)
    │       ├── AdGuard Home :3000 (admin UI)
    │       ├── DNS :53
    │       ├── TOE Platform :5000
    │       └── Redis :6379 (internal only)
    │
    ├── Pixel static IP (e.g. 192.168.1.11)
    │       └── llama.cpp inference server :8080
    │
    ├── Spare Android static IP (e.g. 192.168.1.12)
    │       └── llama.cpp inference server :8081
    │
    └── Samsung TV
            └── Accesses TOE Platform at 192.168.1.10:5000
                (read-only dashboard mode, TV-optimized layout)
```

All services accessed by internal IP — no domain names required.

---

## Docker Compose Stack

All services defined in one `docker-compose.yml`. Single command starts everything.

```yaml
services:

  adguard:
    image: adguard/adguardhome:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "3000:3000/tcp"
    volumes:
      - /mnt/ssd/adguard/work:/opt/adguardhome/work
      - /mnt/ssd/adguard/conf:/opt/adguardhome/conf
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    volumes:
      - /mnt/ssd/redis:/data
    restart: unless-stopped

  toe-worker:
    build: .
    command: rq worker
    volumes:
      - /mnt/ssd:/mnt/ssd
      - /mnt/archive:/mnt/archive
    depends_on:
      - redis
    restart: unless-stopped

  toe-platform:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - /mnt/ssd:/mnt/ssd
      - /mnt/archive:/mnt/archive
    depends_on:
      - redis
    restart: unless-stopped
```

---

## The Five Modules

| Module | Name | Function |
|---|---|---|
| 1 | VISION | Video analysis — patterns, symbology, subliminal, hidden messages |
| 2 | CODEX | Book ingestion — transcription, OCR, image extraction, analysis |
| 3 | ORACLE | Astrology + numerology engine — timing, charts, cycles |
| 4 | SOVEREIGN | Business intelligence — institutional dossiers, reverse-engineering |
| 5 | RESONANCE | Music analysis — audio, lyrics, artwork, cadence, frequency, symbolism |

---

## MODULE 1 — VISION (Video Analysis)

**Input:** Any video file (MP4, MOV, AVI, MKV, WebM)

**Pipeline:**
1. Upload via drag-and-drop
2. OpenCV Stage 1 on RPi5 — scene cuts, flash events, contours, color shifts, face/eye regions, brightness spikes
3. Flagged frames → Pixel LLM
4. LLM returns structured JSON analysis
5. Unified vault note generated → `/mnt/ssd/vault_output/vision/`
6. Original video archived → `/mnt/archive/video/`

---

## MODULE 2 — CODEX (Book Ingestion)

**Input:** PDF, EPUB, MOBI, DJVU, CBZ/CBR, DOCX, RTF, TXT, HTML, AZW3

**Pipeline:**
1. Upload via drag-and-drop
2. Format detection → parser
3. Text → Markdown with TOE frontmatter
4. Images → WebP (lossy 75 photos, lossless diagrams/geometry)
5. Two output files: `[Title].md` + `[Title] - Analysis.md`
6. Original archived → `/mnt/archive/books/`

**Living document schedule:** Weekly re-analysis Sunday 02:00. Manual override in UI.

---

## MODULE 3 — ORACLE (Astrology & Numerology)

**Capabilities:**
- Natal/founding charts — any date, time, location
- Transit tracking — current planets overlaid on any chart
- Electional astrology — optimal timing windows for specific actions
- Mundane astrology — planetary cycles mapped to world/market events
- Numerology — Pythagorean + Chaldean, names and dates
- Sacred geometry overlays — grand trines, T-squares, Star of David, yods

**Weekly output:** `Astro-Week-YYYY-MM-DD.md` every Sunday
**Backend:** pyswisseph (Swiss Ephemeris — fully offline, ARM64)

---

## MODULE 4 — SOVEREIGN (Business Intelligence)

**Purpose:** Observe and dissect long-lasting institutions. Extract what works. Apply it.

**Dossier files per entity:**
- `Profile.md` — founding date, structure, key figures, history
- `Chart.md` — natal/founding astrology + interpretation
- `Numerology.md` — name, date, address analysis
- `Sacred-Geometry.md` — logo, architecture, org structure triangles
- `Patterns.md` — timing of major moves, acquisitions, launches
- `Esoteric-Signals.md` — symbols, affiliations, ceremonial behavior
- `Synthesis.md` — what works, why, how to apply (living document)

**Seed entities:** British Royal Family, Vatican, Rothschild enterprises, Rockefeller institutions, BlackRock/Vanguard/State Street, Freemasonic lodges, long-running nation-states

---

## MODULE 5 — RESONANCE (Music Analysis)

**Input:**
- Drag-and-drop audio file (MP3, FLAC, WAV, AAC, OGG)
- YouTube URL → yt-dlp extracts audio + metadata + thumbnail
- Album artwork auto-fetched from file metadata or uploaded separately

**Inference:** Spare Android phone dedicated to this module

**Audio layer (librosa — no LLM):**
- BPM, musical key, scale
- Frequency spectrum, dominant tones
- Solfeggio proximity (396, 417, 432, 528, 639, 741, 852 Hz)
- Silence/pause geometric patterns
- Repetition and loop structures
- Dynamic range, compression
- Ad lib timing and placement

**Lyrics layer:**
- Whisper offline transcription (ARM64)
- LLM: symbolism, numerology, hidden messaging, repetition patterns, esoteric references

**Artwork layer:**
- OpenCV + LLM vision
- Sacred geometry in composition
- Color symbolism, hidden symbols, sigils, typography numerology

**Artist dossier (optional):** Same SOVEREIGN structure — natal chart, numerology, discography pattern analysis

---

## Unified Output Standard

Every module produces identical frontmatter and section structure.

### Frontmatter

```yaml
---
title: ""
date: YYYY-MM-DD
module: VISION | CODEX | RESONANCE | SOVEREIGN | ORACLE
type: video-analysis | book-analysis | music-analysis | dossier | astro-report
source: ""
source_url: ""
tags: []
significance: high | medium | low
vault_worthy: true | false
confidence: 0.00
---
```

### Body sections (all modules)

```
## Core Message
## Patterns Identified
## Symbolic Elements
## Hidden / Subliminal Content
## Sacred Geometry
## Numerological Findings
## Astrological Connections
## TOE Vault Connections
## Synthesis
## Suggested Tags
```

---

## TV Dashboard

The Samsung Smart TV accesses `http://192.168.1.10:5000` in its browser.

**TV mode activates automatically** when the browser viewport is ≥ 1280px and user-agent contains `SmartTV` or `Tizen`. Falls back to large-screen layout detection.

**TV dashboard features:**
- Read-only — no upload, no forms
- Large text and tap targets (minimum 72px)
- D-pad navigable (full keyboard focus management)
- High contrast dark theme
- Displays: recent analyses, weekly astro report, current transits, vault activity feed
- Auto-refreshes every 5 minutes

---

## Technology Stack

| Layer | Technology |
|---|---|
| Containerization | Docker + Docker Compose |
| DNS / Ad-blocking | AdGuard Home (Docker) |
| Web server | Python + Flask |
| Task queue | Redis + RQ |
| Video decode | ffmpeg + PyAV |
| CV analysis | OpenCV 4.x (ARM64) |
| Book parsing | PyMuPDF, ebooklib, python-docx, Tesseract |
| Image compression | Pillow → WebP |
| Audio analysis | librosa |
| Lyrics transcription | Whisper (offline, ARM64) |
| YouTube extraction | yt-dlp |
| LLM inference | llama.cpp (Pixel + spare Android) |
| Vision model | LLaVA 7B Q4 |
| Astrology | pyswisseph + Kerykeion |
| Numerology | Custom Python module (Pythagorean + Chaldean) |
| Frontend | Vanilla HTML/CSS/JS — no build step |
| TV layout | Responsive CSS — auto-detects TV viewport |
| Version control | Git on RPi5 |
| Vault sync | rclone → Google Drive |

---

## File Structure on RPi5

```
/home/e031a/toe-platform/
├── docker-compose.yml
├── Dockerfile
├── app.py
├── config.yaml
├── modules/
│   ├── vision/
│   ├── codex/
│   ├── oracle/
│   ├── sovereign/
│   └── resonance/
├── static/
└── templates/

/mnt/ssd/
├── vault_output/
│   ├── vision/
│   ├── codex/
│   ├── oracle/
│   ├── sovereign/
│   └── resonance/
├── inbox/
│   ├── video/
│   ├── books/
│   └── audio/
├── adguard/
├── redis/
└── models/
    ├── llava-7b-q4.gguf
    └── mmproj-llava.gguf

/mnt/archive/
├── video/
├── books/
└── audio/
```

---

## Build Phase Sequence

| Phase | Scope |
|---|---|
| 0 | Storage mount — SSD + HDD + static IP assignment |
| 1 | Docker install + AdGuard Home — network-wide ad blocking live |
| 2 | Core Flask + Redis + Docker Compose stack |
| 3 | VISION — OpenCV + LLM pipeline |
| 4 | CODEX — book parsing, OCR, WebP extraction |
| 5 | RESONANCE — audio, Whisper, yt-dlp, artwork |
| 6 | ORACLE — Swiss Ephemeris, charts, numerology |
| 7 | SOVEREIGN — dossier builder, synthesis |
| 8 | TV dashboard — responsive read-only layout |
| 9 | Scheduler — weekly automation |
| 10 | Cross-module synthesis engine |
| 11 | rclone sync + git versioning |

---

## Open Items

- [ ] RPi5 subnet — awaiting `ip route` + `hostname -I` output
- [ ] Router admin access — confirmed or not (required for DNS redirect to AdGuard)
- [ ] Spare Android model — determines inference capability for RESONANCE
- [ ] Personal natal chart data — add to ORACLE when ready
- [ ] Static IP reservations — set in router once subnet confirmed

---

*Blueprint version: 4.0*
*Updated: 2026-05-10*
*Status: Pre-development — awaiting subnet confirmation to begin Phase 0*
