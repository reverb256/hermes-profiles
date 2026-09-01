# Hermes Profile System Design

> Source of truth: this repo (`reverb256/hermes-profiles`).
> Live deployment: `~/.hermes/profiles/` via `scripts/deploy-profiles.sh`.
> Last updated: 2026-09-01.

## The operating model

j_kro owns creative direction. SPOC (default profile) manages the system.
Bot profiles execute work. SPOC never executes heavy work directly.

```
j_kro (strategy, approvals)
  ↓
SPOC / default (manager — dispatch, verify, escalate)
  ├── Executive (4) — strategy, numbers, model-tier decisions
  ├── Production (10) — the content factory
  ├── Brand (5) — personal-media track
  └── Specialists (4) — cross-cutting execution
```

## Profile layers (23 profiles)

### Executive — strategy, numbers, model-tier decisions
| Profile | Role | KPI |
|---------|------|-----|
| cco | Chief Content Officer — voice, quality, editorial | output quality |
| cro | Chief Revenue Officer — offers, CTA, monetization | revenue per piece |
| coo | Chief Operations Officer — pipeline health | cards advanced / cycle time |
| caio | Chief AI & Infra Officer — routing, cost | cost per piece, uptime |

These stay SEPARATE (j_kro decision 2026-09-01). Each owns a different
decision; merging them would collapse four judgments into one.

### Production — the faceless content factory
| Profile | Role | Returns |
|---------|------|---------|
| oracle | opportunity finder (X arbitrage scan) | scored production cards |
| researcher | evidence packages | 3-7 verified claims + URLs |
| scriptwriter | retention-optimized scripts | TTS-paced script + visual notes |
| voicebot | TTS routing | narration audio |
| videobot | video routing (manim→comfyui→xai→stock) | MP4 |
| thumbnailbot | thumbnail variants | ≤3 words, feed-size |
| seobot | title/description/tags/chapters | metadata JSON |
| publishbot | YouTube upload + cross-post | video ID (private, review gate) |
| analyst | keep/test/stop with real numbers | performance report |
| storyteller | audio-drama pipeline (VoxCPM) | finished audio |

### Brand — personal-media track
| Profile | Role |
|---------|------|
| scout | signal scout — trends, angles, authority clips |
| strategist | angle briefs (human approves) |
| writer | long-form flagship pieces |
| distributor | platform asset rethinks |
| editor | review + publish approval |

### Specialists — cross-cutting execution
| Profile | Role |
|---------|------|
| web-designer | web design, media showcases, static sites |
| site-agency | consent-first local-business web agency (KEPT SEPARATE) |
| ops | infrastructure operations |
| maplespike-eng | MapleSpike platform development |

## Profile contract (every profile MUST have)

1. `SOUL.md` — role persona: owns / reads / returns / must not / done when
2. `distribution.yaml` — name, revision, description, skills, model, provider
3. `config.yaml` — pinned model/provider, scoped toolsets, fallback routers
4. Fallback routers: `kilo/kilo-auto/free` + `opencode-zen/openrouter/free` LAST
   (the free-model auto-routers, verified live 2026-09-01 — NOT specific :free models)
5. `skills.external_dirs: ["~/.hermes/skills"]` — shared SPOC tree, read-only

## Model tiering

| Tier | Model | Used by |
|------|-------|---------|
| Exec | longcat-2.0 / solar-pro4 (nous) | c-suite, oracle, strategist |
| Worker | laguna / stepfun / hy3 (nous) | production crew |
| Web | laguna-s-2.1 (nous) | web-designer |
| Router (LAST) | kilo-auto/free + openrouter/free | ALL profiles |

Never parallelize against quota-limited providers (xAI weekly reset).

## Skill curation policy

**Canonical store: nexus `/data/hermes/skills` (3.6TB bcache array, 1.9TB free).**
zephyr keeps a local working copy at `~/.hermes/skills` and syncs nightly
(`skills-sync-daily` cron, 3am, bidirectional rsync).

The SPOC skill tree (294 skills) is the shared library.
Worker profiles consume via `external_dirs` — they do NOT copy skills locally.

| Category | Count (approx) | Action |
|----------|----------------|--------|
| Cluster/infra (nixos-*, k3s-*, omarchy-*, cloudflare) | ~120 | ops + SPOC only |
| Web/design (claude-design, sketch, astro, ...) | ~40 | web-designer + site-agency |
| Content/creative (comfyui-*, video, writing, ...) | ~60 | production crew + storyteller |
| Finance/blockchain/product | ~40 | ARCHIVE (not in any workflow) |
| Hermes-internal (hermes-*) | ~50 | SPOC only |
| Duplicates/orphans (apple, gifs, searxng-mcp) | ~10 | ARCHIVE |

Curation runs weekly via cron. The curator archives, never deletes.

## Governance

- Canonical profiles live in THIS repo. `scripts/deploy-profiles.sh` deploys.
- Profile changes go through the repo (PR/branch flow), not hand-edits.
- Skill curation is a standing weekly cron.
- SPOC routes via kanban + profiles. Never delegate_task for heavy work.
