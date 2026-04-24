# Niveku Portfolio — Design Spec

**Date:** 2026-04-23
**Status:** Approved

---

## Context

Kevin Henao ("Niveku") is a remote-first technical professional with 8+ years of experience spanning geoscience, GIS/geospatial engineering, data engineering, cloud (AWS), and AI workflow automation. This portfolio serves as his primary public presence to gain visibility with the technical community (GIS, geospatial, data, AI) and secondarily with recruiters for remote roles.

Content source: `D:\Personal\Projects\ObsidianVaults\PersonalVault` — used as reference and draft, manually curated before publishing. The vault is private; only explicitly chosen content goes into the portfolio repo.

---

## Goals and Audience

**Primary:** Technical community — GIS, geospatial, data engineering, AI/automation practitioners. Goal: credibility through real work, not CV marketing.

**Secondary:** Recruiters and hiring managers for fully remote roles.

**Tertiary:** Freelance clients.

**Language:** English only.

**URL:** Free Vercel subdomain at launch (`niveku.vercel.app`). Custom domain deferred.

---

## Site Architecture

```
niveku.vercel.app/
├── /                  → Home
├── /about             → About
├── /projects          → Projects index
├── /projects/[slug]   → Project detail
├── /blog              → Blog index
├── /blog/[slug]       → Blog post
└── /contact           → Contact
```

### Page descriptions

**Home `/`**
Hero with tagline capturing the geosciences → data → AI arc. Preview of 3 featured projects. Latest 3 blog posts. Entry point for both audiences.

Tagline direction: *"Geoscientist turned data & GIS engineer. Building spatial workflows, AI tools, and automation systems."*

**About `/about`**
Three blocks derived from vault's `Master Profile` and `Career Narrative`:
1. Career narrative (evolution: geoscience → GIS → data → AI workflows)
2. Current stack (from `Tech Stack Inventory` — strong/marketable skills only)
3. Personal context: Colombia, remote-first, English C2, open to remote global roles

No generic bullet lists. Short, honest prose.

**Projects `/projects`**
Grid of project cards, filterable by category. Each card links to a detail page with the structure: problem → stack → role → outcome.

Initial project set (sourced from vault `Project Inventory`):
- AB InBev Invoice PDF Automation — Data Engineering / AWS
- Casaideas SAP-to-Cloud Pipeline — Data Engineering / AWS
- Coca-Cola Chile Distribution Data Flow — Data Engineering / AWS
- DIMAR COVID Geospatial Monitoring — Geospatial
- Fura Reporting & Licensing Maps — Geospatial
- OpenClaw — AI/Automation (personal project)

Categories: `Geospatial` | `Data Engineering` | `AI/Automation`

**Blog `/blog`**
Technical writing. Launch with 1–2 posts to avoid empty state. Topic direction: AI workflows with Claude Code/MCPs, or geospatial data engineering patterns. No fixed publishing cadence — quality over volume.

**Contact `/contact`**
Email + GitHub + LinkedIn links. No contact form (avoids backend or paid services).

---

## Content Strategy

Content lives in the Astro repo under `content/`. The vault is the drafting space; the repo is the publishing space. No symlinks, no scripts — manual curation is intentional (keeps private vault notes private).

When updating a project or writing a post: draft in vault, curate, copy to repo as MDX, push.

---

## Tech Stack

| Concern | Choice | Reason |
|---|---|---|
| Framework | Astro (static output) | Zero runtime, fast, great MDX support |
| Styling | Tailwind CSS v4 | Scaffold only — visual design deferred to Claude Design |
| Content | Astro Content Collections (MDX) | Typed frontmatter, simple file-based authoring |
| Hosting | Vercel (free tier) | Smooth GitHub integration, good DX, known in target community |
| Repo | `D:\Personal\Repo\Portafolio` | Existing directory, becomes the Git root |

No database, CMS, auth, external APIs, or analytics at launch. Zero operational cost.

---

## Content Schema

### `content/projects/[slug].mdx`

```yaml
---
title: string
summary: string          # 1-2 sentences, shown on card
date: YYYY-MM-DD
category: Geospatial | Data Engineering | AI/Automation
stack: string[]
featured: boolean        # true = appears on home preview
---
```

Body: MDX — problem → stack → role → outcome structure.

### `content/blog/[slug].mdx`

```yaml
---
title: string
summary: string          # 1-2 sentences, shown on listing
date: YYYY-MM-DD
tags: string[]
---
```

Body: MDX free-form.

---

## Deferred / Out of Scope

- Visual design (handled separately with Claude Design)
- Custom domain
- Analytics
- Contact form
- CV/resume page
- Multilingual support
- CMS integration
- Automated vault-to-site export (revisit when blog has 20+ posts)
