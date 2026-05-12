---
title: OBSIDIAN-EXTERNAL-DRIVE-DISCUSSION
date: 2026-05-11
module: CODEX
type: conversation-log
tags: [obsidian, external-drive, storage, rpi5, seagate, wd-hdd]
significance: medium
vault_worthy: true
source_url: https://claude.ai/chat/27457001-46c0-4ce5-a27f-20a12160f9f6
---

# OBSIDIAN-EXTERNAL-DRIVE-DISCUSSION

## Summary
Located prior conversation about using external drives for Obsidian vault storage on RPi5 platform.

## Key Decisions Confirmed
- Seagate SSD (2TB) → `/mnt/ssd` — all active data including Obsidian vault output
- WD HDD (2TB, 12V) → `/mnt/archive` — raw archive, originals
- SD card — OS only, never touched by data

## Storage Layout Confirmed
| Drive | Mount | Purpose |
|---|---|---|
| Seagate SSD (2TB) | `/mnt/ssd` | Active data, vault output, inbox, models |
| WD HDD (2TB, 12V) | `/mnt/archive` | Raw archive — video, books, audio |
| SD card (116G) | `/` | OS, Docker, app code |

## TOE Vault Connections
- Platform Blueprint — storage section
- Installation Guide — Phase 0

## Tags
`obsidian` `storage` `rpi5` `seagate` `wd-hdd` `infrastructure`
