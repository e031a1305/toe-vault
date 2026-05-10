# Local Intelligence Platform — Master Blueprint
*TOE Vault · Master Project Document · Living Document*

---

## Vision Statement

A private, locally-hosted intelligence platform running on ARM64 hardware. Its purpose is to reverse-engineer the hidden structural, esoteric, and geometric frameworks used by long-lasting institutions — royal families, governments, religions, Fortune 500 companies — and apply those findings to personal and operational decisions. Sacred geometry is the meta-framework connecting all modules.

---

## Core Thesis

> If an institution has lasted — it works. Study what it does. Extract the pattern. Apply it.

The triangle is the foundational structure: stable, hierarchical, load-distributing. It appears in governance (executive/legislative/judicial), religion (trinity), Freemasonry, corporate structure (board/management/operations), and sacred geometry. This platform is built to find it everywhere and make it actionable.

---

## Hardware

| Device | Role |
|---|---|
| Raspberry Pi 5 (8GB) | Web server, task orchestration, file management, OpenCV. No LLM inference. |
| Google Pixel (GrapheneOS) | Primary LLM inference via Termux + llama.cpp. Local network only. |
| Oracle Cloud Free Tier | Secondary inference for batch jobs. 4 ARM cores, 24GB RAM. Free forever. |

---

## The Four Modules

### MODULE 1 — VISION (Video Analysis)
Analyze video for esoteric patterns, symbology, subliminal messaging, hidden structure, and TOE vault connections.

**Pipeline:**
1. Upload video via drag-and-drop
2. OpenCV Stage 1 on RPi5 — scene cuts, flash events, geometric contours, color shifts, face/eye regions, brightness spikes
3. Flagged frames sent to LLM (Pixel or Oracle)
4. LLM returns: symbols, patterns, hidden messages, core message, TOE connections, suggested tags
5. Markdown vault note generated — saved and optionally synced to Google Drive

---

### MODULE 2 — CODEX (Book Ingestion & Transcription)

**Accepted formats:** PDF, EPUB, MOBI, DJVU, CBZ/CBR, DOCX, RTF, TXT, HTML, AZW3

**Pipeline per book:**
1. Drag-and-drop upload
2. Format detection → appropriate parser
3. Text extracted → cleaned → converted to Markdown with TOE frontmatter
4. Images extracted → converted to **WebP** (best compression, lossless option for diagrams/sacred geometry illustrations)
5. Two files created per book:
   - `[Title].md` — full transcription (living document)
   - `[Title] - Analysis.md` — esoteric/structural analysis (living document)
6. Analysis file examines: sacred geometry references, numerology in dates/names, structural frameworks used, TOE connections

**Living Document Schedule:**
- Weekly automated re-analysis pass (every Sunday 02:00)
- Manual override: "Re-analyze now" button in UI
- Version history tracked via git on RPi5

**Image storage strategy:**
- WebP lossy (quality 75) for photographs → typically 60–80% smaller than JPEG
- WebP lossless for diagrams, charts, sacred geometry illustrations → preserves precision
- Stored in `/vault_output/books/[Title]/images/`

---

### MODULE 3 — ORACLE (Astrology & Numerology Engine)

**Purpose:** Calculate, interpret, and apply astrological and numerological data to timing, analysis, and decision-making. Feed results into SOVEREIGN module and personal planning.

**Astrological capabilities:**
- **Natal/Founding charts** — enter any date, time, location to generate a full chart (personal or corporate)
- **Transit tracking** — current planetary positions overlaid on any natal chart
- **Electional astrology** — identify optimal windows for specific actions (launching, signing, hiring, publishing)
- **Mundane astrology** — planetary ingresses, eclipses, major conjunctions mapped to world/market events
- **Solar returns, progressions** — longer-term cycle analysis
- **Synastry** — relationship/compatibility between two charts (person-to-person or person-to-company)

**Numerology capabilities:**
- Name numerology (Pythagorean and Chaldean)
- Date numerology — life path, expression, soul urge
- Company name + founding date analysis
- Address/location numerology

**Sacred geometry layer:**
- Chart wheels rendered with geometric overlays (grand trines, T-squares, Star of David patterns, yods)
- Identify triangular configurations (trines, grand trines) — the stable 120° structure
- Flag cardinal cross patterns, kites, mystic rectangles
- Connect geometric patterns in charts to structural patterns found in SOVEREIGN dossiers

**Astro-calendar (weekly):**
- Every Sunday: auto-generate the week's significant transits
- Flag: void-of-course moons, Mercury retrograde windows, major aspect exact dates
- Electional recommendations for the coming week
- Pushed to TOE vault as `Astro-Week-YYYY-MM-DD.md`

**Backend:** Swiss Ephemeris (pyswisseph) — accurate, offline, free, ARM64 compatible

---

### MODULE 4 — SOVEREIGN (Business Intelligence)

**Purpose:** Observe, study, and dissect successful long-lasting institutions — royal families, governments, religions, Fortune 500 companies, billionaire-controlled entities — to extract the structural, esoteric, and operational frameworks that make them work. Apply findings to personal operations.

**This module is observational. No personal business operations tracked here — pure intelligence gathering.**

**Dossier structure per entity:**
```
Entity Name
├── Profile.md          — founding date, key figures, structure, history
├── Chart.md            — natal/founding astrology chart + interpretation
├── Numerology.md       — name, date, address numerology analysis
├── Sacred-Geometry.md  — logo analysis, building/layout geometry, org structure triangles
├── Patterns.md         — what they do repeatedly (timing of launches, acquisitions, etc.)
├── Esoteric-Signals.md — symbols used, affiliations, ceremonial behavior, public rituals
└── Synthesis.md        — what works, why, and how it can be applied (living document)
```

**Research pipeline:**
1. Enter company/institution name + founding date (+ HQ location if known)
2. System auto-generates: numerology report, founding chart, chart interpretation
3. Web research task queued: pulls public information (leadership, key dates, milestones)
4. LLM analyzes gathered data through esoteric/structural lens
5. All six dossier files created and saved to vault
6. Weekly re-analysis checks for new public information and updates Synthesis.md

**What to look for (standing analysis criteria):**
- Use of triangular org structures (triad leadership, three-tier hierarchy)
- Numerologically significant founding dates, IPO dates, product launches
- Astrological timing of major moves (acquisitions, leadership changes, public offerings)
- Sacred geometry in branding (logos, architecture, spatial layout of HQ)
- Masonic, Rosicrucian, or other esoteric affiliations of founders/leadership
- Cyclical patterns — do they operate on 7-year, 19-year, Saturn-return cycles?
- Ceremonial behavior disguised as corporate ritual (annual meetings, product launches as events)

**Entities of interest (seed list — expand as research grows):**
- British Royal Family
- Vatican / Catholic Church
- Rothschild family enterprises
- Rockefeller institutions
- Freemasonic lodges and affiliated foundations
- BlackRock / Vanguard / State Street
- Fortune 500 with esoteric founding histories
- Long-running nation-states (UK, Vatican City, Switzerland)

---

## Cross-Module Intelligence Flow

```
VISION (video)  ──┐
CODEX (books)   ──┼──→ TOE VAULT ←──→ ORACLE (astrology)
SOVEREIGN       ──┘         │
                            ▼
                    Weekly Synthesis Report
                    (auto-generated Sunday)
                    Connections across all modules
                    New patterns flagged for review
```

---

## Technology Stack

| Layer | Technology |
|---|---|
| Web server | Python + Flask |
| Task queue | Redis + RQ (non-blocking, progress streaming) |
| Video decode | ffmpeg + PyAV |
| CV analysis | OpenCV 4.x (ARM64) |
| Book parsing | Calibre CLI, PyMuPDF, ebooklib, python-docx, Tesseract OCR |
| Image compression | Pillow → WebP conversion |
| LLM inference | llama.cpp (Termux/Pixel) + Ollama (Oracle) |
| Vision model | LLaVA 7B Q4 (Pixel) / LLaVA 13B (Oracle) |
| Astrology engine | pyswisseph (Swiss Ephemeris — offline, ARM64) |
| Chart rendering | Kerykeion (Python astrology charts) |
| Numerology | Custom Python module (Pythagorean + Chaldean) |
| Frontend | Vanilla HTML/CSS/JS — no build step |
| Version control | Git on RPi5 — tracks all living document changes |
| Vault sync | rclone → Google Drive (on-demand or scheduled) |

---

## File Structure on RPi5

```
/home/e031a/toe-platform/
├── app.py
├── config.yaml
├── modules/
│   ├── vision/
│   │   ├── extractor.py
│   │   ├── cv_analyzer.py
│   │   └── llm_client.py
│   ├── codex/
│   │   ├── parser.py
│   │   ├── image_extractor.py
│   │   └── markdown_gen.py
│   ├── oracle/
│   │   ├── ephemeris.py
│   │   ├── chart_engine.py
│   │   ├── numerology.py
│   │   └── electional.py
│   └── sovereign/
│       ├── dossier_builder.py
│       ├── researcher.py
│       └── synthesis.py
├── static/
├── templates/
├── vault_output/
│   ├── video-analysis/
│   ├── books/
│   ├── astro/
│   └── sovereign/
├── video_inbox/
└── scheduler/
    └── weekly_jobs.py
```

---

## Scheduled Automation (Weekly — Sunday 02:00)

1. Re-analyze all living documents for new connections
2. Generate astro-week forecast for coming 7 days
3. Update SOVEREIGN dossiers with any new cross-module patterns
4. Generate weekly synthesis report — what connected, what emerged
5. Push all updates to Google Drive vault via rclone

Manual override available from UI at any time.

---

## Build Phase Sequence

| Phase | Scope |
|---|---|
| 1 | Core Flask server, file upload, routing, progress streaming |
| 2 | VISION — OpenCV + LLM pipeline + markdown output |
| 3 | CODEX — Book parsing, OCR, WebP image extraction, transcription |
| 4 | ORACLE — Swiss Ephemeris, natal charts, numerology, electional calendar |
| 5 | SOVEREIGN — Dossier builder, research pipeline, synthesis |
| 6 | Scheduler — weekly automation, living document updates |
| 7 | Cross-module synthesis — connections engine, weekly report |
| 8 | rclone Google Drive sync, git versioning |

---

## Open Questions

- [ ] Confirm Pixel LAN IP is static
- [ ] RPi5 storage available (SSD or SD card, free space)
- [ ] Primary inference target confirmed: Pixel (Option B)
- [ ] Personal natal chart data — to be added to ORACLE when ready
- [ ] Seed list of entities for SOVEREIGN — to be expanded

---

*Blueprint version: 2.0*
*Updated: 2026-05-10*
*Status: Pre-development — awaiting Phase 1 build approval*
*Next action: Confirm storage situation, then begin Phase 1*
