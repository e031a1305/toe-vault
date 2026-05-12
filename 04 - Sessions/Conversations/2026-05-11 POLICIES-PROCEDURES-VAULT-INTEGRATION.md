# POLICIES-PROCEDURES-VAULT-INTEGRATION
*Date: 2026-05-11*
*Type: conversation-log*
*Tags: [policies, procedures, vault-integration, project-instructions, entropy, master-project]*

---

## Summary
Designed the artifact and instruction system for organizing TOE vault integration with the MASTER-PROJECT folder. Goal: reduce entropy.

## Problems Identified
1. Duplicate `_TOE Tagging Standard.md` files in vault
2. Project mapping outdated — `nicaragua-project` listed, `protein-group` missing, no `MASTER-PROJECT`
3. Each Claude project had slightly different integration instructions — no single template

## Solution Designed
Two-layer instruction system:

### Layer 1 — Claude Project Settings (static bootstrap)
- Tells Claude where to find live instructions
- Never needs to change
- One file per project

### Layer 2 — Vault Live Instructions (evolves automatically)
- `MASTER-PROJECT Live Instructions.md` → `00 - Index/`
- Sub-project templates → each project folder
- Claude archives old versions automatically to `Previous Versions/` folder
- Keeps max 3 previous versions, deletes oldest

## Files Created
- `MASTER-PROJECT Settings Bootstrap.md`
- `MASTER-PROJECT Live Instructions.md`
- `Sub-Project Settings Bootstrap Template.md`
- `Sub-Project Live Instructions Template.md`

## Project → Vault Folder Mapping Established
| Claude Project | Vault Folder |
|---|---|
| MASTER-PROJECT | `00 - Index/` + anywhere |
| astrology | `03 - Projects/Astrology/` |
| protein-group | `03 - Projects/Protein Group/` |

---
*Source: https://claude.ai/chat/483046ef-152f-4a54-8145-e6c07a4cc4c4*
