# Content Architecture & Setup

This document explains how Lx7.ca's content is structured, why it's organized this way, and how to fork, extend, or consume it.

---

## The Philosophy: Distributed Single Source of Truth

Lx7.ca is built on a **resilient, forkable architecture** where:

- **GitHub** = Canonical source for all content (episodes, artist data, metadata)
- **lx7.ca** = Consumer of that content, rendering it for humans
- **Forking** = The highest form of preservation and collaboration

If the website goes down, the content persists on GitHub. If GitHub changes, the website can be rebuilt from the data. This is **anti-fragility by design**.

---

## File Structure & Purpose

### Content Data (GitHub Root)

```
/episodes/
  └── toc.json                    → Episode catalog/index

/artists/
  └── Nigil-Caenaan/
      ├── profile.json            → Artist biography, links, stats
      └── discography.json        → Complete music release catalog
```

**Purpose**: The "encyclopedia" of the show and artist. This is the **single source of truth** for all content.

**Access**: Via raw GitHub URLs:
```
https://raw.githubusercontent.com/Lx7-CA/ref-web/main/episodes/toc.json
https://raw.githubusercontent.com/Lx7-CA/ref-web/main/artists/Nigil-Caenaan/profile.json
```

### Website Metadata (docs/metadata/)

```
/metadata/
  ├── INFO.json                   → Master index/router for the entire project
  ├── web-ring.json               → Trust network, relational links
  └── footer.json                 → Website UI data
```

**Purpose**: Website-specific presentation layer and the **master map** that tells LLMs and humans where everything is.

**Access**: Via lx7.ca URLs:
```
https://lx7.ca/metadata/INFO.json
https://lx7.ca/metadata/web-ring.json
```

---

## How It Works: The Data Flow

1. **Content lives on GitHub** (episodes, artist profiles, discography)
2. **Website consumes from GitHub** via JavaScript fetch to raw URLs
3. **INFO.json acts as the router** pointing to all data sources
4. **No CORS issues** because GitHub serves with `Access-Control-Allow-Origin: *`

**Example:**
```javascript
fetch('https://raw.githubusercontent.com/Lx7-CA/ref-web/main/artists/Nigil-Caenaan/discography.json')
  .then(res => res.json())
  .then(data => {
    // Render discography on lx7.ca
  });
```

---

## JSON Schema Conventions

All JSON files follow these patterns for consistency and LLM-navigability:

### Common Top-Level Fields
```json
{
  "version": "1.0",
  "lang": "en",
  "name": "Human-readable name",
  "short_name": "Abbreviated name",
  "description": "What this file contains",
  "updated": "YYYY-MM-DD"
}
```

### Fallback Emoji Pattern
Every image/icon has a fallback emoji for graceful degradation:
```json
{
  "thumbnail": "/path/to/image.jpg",
  "thumbnail_fallback": "🎬"
}
```

### URL Conventions
- **Relative paths** for website-hosted assets: `/images/logo.png`
- **Full URLs** for external resources: `https://nigilcaenaan.bandcamp.com`
- **Raw GitHub URLs** for canonical data: `https://raw.githubusercontent.com/...`

---

## How to Fork & Extend

### 1. Fork the Repository
```bash
git clone https://github.com/Lx7-CA/ref-web.git
cd ref-web
```

### 2. Add Your Own Episodes
Edit `/episodes/toc.json`:
```json
{
  "episodes": [
    {
      "id": "S26E002",
      "title": "Your Episode Title",
      "date": "2026-01-20",
      "status": "published",
      "thumbnail": "/episodes/S26E002/thumb.jpg",
      "thumbnail_fallback": "🎬"
    }
  ]
}
```

### 3. Create Artist Profiles
Add a new folder in `/artists/Your-Name/`:
- `profile.json` — Bio, links, social profiles
- `discography.json` — Music releases

Follow the existing structure in `/artists/Nigil-Caenaan/` as a template.

### 4. Modify the Web Ring
Edit `/docs/metadata/web-ring.json` to add your trusted nodes:
```json
{
  "nodes": [
    {
      "id": "your-site",
      "name": "Your Site Name",
      "url": "https://yoursite.com",
      "icon_fallback": "🌐",
      "why": "Why this site matters to you"
    }
  ]
}
```

### 5. Update INFO.json
Point to your new data files in `/docs/metadata/INFO.json`:
```json
{
  "navigation": {
    "git_data_files": {
      "episodes_index": "https://raw.githubusercontent.com/YOUR-USERNAME/ref-web/main/episodes/toc.json"
    }
  }
}
```

---

## For LLMs: How to Navigate This Project

**Start here**: `/docs/metadata/INFO.json`

This file contains:
- **`meta`** — Version, author, license, SEO keywords
- **`identity`** — Who created this, project lineage
- **`context`** — Philosophy, manifesto, empathy mapping
- **`navigation`** — URLs to all other data files
- **`entry_points`** — Where different visitor types should start
- **`resilience`** — How the project survives and can be rebuilt

From INFO.json, follow the `navigation.git_data_files` and `navigation.web_data_files` links to access:
- Episode catalog
- Artist profiles
- Discography
- Web ring

---

## The Resilience Model

This architecture ensures the project survives even if individual nodes fail:

| Node | Purpose | Backup |
|------|---------|--------|
| **lx7.ca** | Live website experience | Rebuildable from GitHub |
| **GitHub** | Canonical data source | Forkable by anyone |
| **Social (@vveerrgg)** | Relational identity | Independent of infrastructure |

**Philosophy**: "The project persists if either node survives. Forking is the highest form of preservation."

If you fork this repo, you get:
- All content data (episodes, artist info)
- All metadata schemas
- The entire relational structure
- Instructions to rebuild the website

---

## Key Principles

1. **Data is the artifact** — JSON files are as important as the videos
2. **Process is the product** — Show the work, not just the result
3. **Forkability is a feature** — Make it easy for others to build on this
4. **Resonance over reach** — Quality of connection matters more than quantity
5. **Sovereignty over convenience** — Own the data, control the narrative

---

## Questions?

- **Technical issues**: [Open an issue](https://github.com/Lx7-CA/ref-web/issues)
- **Collaboration**: [@vveerrgg on X](https://twitter.com/vveerrgg)
- **General info**: Start with [README.md](README.md) and [site-strategy.md](site-strategy.md)

---

**Last Updated**: 2026-01-18  
**Status**: Active Experiment  
**License**: CC BY-NC-ND 4.0
