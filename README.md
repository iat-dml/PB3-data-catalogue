# PB3 Data Catalogue

A lightweight, version-controlled catalogue for research datasets produced by the PB3 group at [ZALF – Leibniz Centre for Agricultural Landscape Research](https://www.zalf.de/en/Pages/ZALF.aspx).

---

## The Problem

The PB3 group generates many datasets — soils, crops, models, images, field measurements. Without a shared record system it is hard to answer basic questions:

- Where is this data stored, and who is responsible for it?
- What data do we have on a given topic?
- When a colleague leaves, how do we find their data?

Not every dataset needs to be published, but it should be findable and usable within the group.

---

## The Approach

> **One structured text file per dataset. Stored together in a shared folder. Managed with version control.**

Each dataset is represented by a single Markdown file containing:

- **YAML front matter** — a standardised set of labelled fields (title, owner, format, licence, etc.)
- **Free-text body** — a human-readable description in plain Markdown

Files are stored in a shared Git repository on GitHub. Every change is tracked, entries are reviewed via pull requests, and automated checks flag incomplete or invalid records.

A minimal example entry:

```yaml
---
title: "Soil Moisture Survey — Müncheberg 2024"
id: "2024-mueller-soil-moisture"
status: complete
applicant: "Mueller, Anna"
keywords: [soil, moisture, field, sensor]
data:
  format: [csv]
  coverage:
    spatial: "Müncheberg research farm"
    temporal: "2024-04 to 2024-09"
storage:
  repository: Zenodo
  license: CC-BY-4.0
---

## Description

Volumetric soil moisture measured at five depths using TDR sensors
across three field plots. Data collected weekly during the growing season.

Used as input for the crop water stress model in [[2024-schmidt-crop-yield]].
```

---

## Repository Layout

```
PB3-data-catalogue/
├── catalogue/                      # one Markdown file per dataset
│   ├── 2024-mueller-soil-moisture.md
│   ├── 2025-schmidt-crop-yield.md
│   └── ...
├── templates/
│   └── new-entry.md                # copy this to create a new record
├── schemas/
│   └── metadata-schema.json        # JSON Schema for validating entries
├── scripts/
│   ├── validate.py                 # validate all entries against the schema
│   └── build-index.py              # generate the catalogue index
├── index.md                        # auto-generated catalogue overview
├── .github/
│   └── workflows/
│       └── validate.yml            # CI: validate entries on every pull request
├── CONCEPT.md                      # full concept and design rationale
├── CONTRIBUTING.md                 # step-by-step guide for researchers
└── README.md
```

> **Note:** The `catalogue/`, `templates/`, `schemas/`, `scripts/`, and `CONTRIBUTING.md` items above are part of the planned structure documented in [CONCEPT.md](CONCEPT.md). The repository is currently in the concept and design phase.

### File Naming Convention

```
YYYY-lastname-short-topic.md
```

For example: `2024-mueller-soil-moisture.md`. Files stay sortable by year and identifiable at a glance.

---

## Adding a New Entry

1. Copy `templates/new-entry.md` to `catalogue/YYYY-lastname-topic.md`
2. Fill in the YAML fields — start with the mandatory ones; set `status: draft` if not yet complete
3. Write a short description in the Markdown body
4. Commit and open a pull request
5. Automated validation checks run and report any issues
6. A reviewer merges the pull request
7. The catalogue index is regenerated automatically

---

## Entry Lifecycle

```
draft  ──>  complete  ──>  archived
```

| Status | Meaning |
|--------|---------|
| `draft` | Started but incomplete — mandatory fields produce warnings, not errors |
| `complete` | All mandatory fields filled; data deposited; full validation applies |
| `archived` | Retained for reference; no longer actively maintained |

---

## Implementation Tiers

The system is designed to be adopted incrementally. Each tier adds value on its own.

| Tier | Features | Effort |
|------|----------|--------|
| **1 — Core** | Template + shared Git repo + contributing guide | Low |
| **2 — Automation** | Schema validation, auto-generated index, CI checks on pull requests | Medium |
| **3 — Advanced** | Obsidian graph view, AI-powered summaries, static web interface | Optional |

### Tier 1 — Core
A shared repository, a template file, and a contributing guide. Gives the group a single place to record datasets, consistent metadata, full edit history, and peer review — immediately.

### Tier 2 — Automation
A JSON Schema validates every entry. A script generates `index.md` — a summary table of all entries (title, owner, status, keywords, year). Both run automatically via GitHub Actions on every pull request.

### Tier 3 — Advanced
- **Obsidian integration** — open the `catalogue/` folder as an Obsidian vault to browse entries visually, follow wiki-style links (`[[entry-id]]`), and explore connections via the graph view.
- **LLM knowledge base** — a language model generates a narrative summary of the catalogue, enabling natural-language queries such as *"What datasets do we have on soil moisture?"*
- **Static website** — a searchable, filterable web view built directly from the Markdown files (e.g., with Quarto or MkDocs).

---

## FAIR Principles

This catalogue is designed for [FAIR data](https://www.go-fair.org/fair-principles/) compliance from the ground up.

| Principle | How it is supported |
|-----------|---------------------|
| **Findable** | Unique IDs, rich metadata, keywords, auto-generated index, LLM summaries |
| **Accessible** | Storage location, access restrictions, and embargo details explicitly recorded; catalogue hosted openly on GitHub |
| **Interoperable** | Consistent YAML field names, controlled vocabularies (licences, relationship types), standard formats (Markdown, JSON Schema) |
| **Reusable** | Licence, ownership, provenance, and documentation links are mandatory fields |

---

## Further Reading

- **[CONCEPT.md](CONCEPT.md)** — Full design rationale, metadata field reference, validation approach, and implementation phases
- **[concept-presentation.qmd](concept-presentation.qmd)** — Source for the concept slide deck (rendered with [Quarto](https://quarto.org/))
- **[concept-presentation.html](concept-presentation.html)** — Rendered slide deck (open in a browser)

---

## About

Developed within the [Innovation Center for Agricultural System Transformation (IAT)](https://www.zalf.de) at ZALF, Müncheberg, Germany.
