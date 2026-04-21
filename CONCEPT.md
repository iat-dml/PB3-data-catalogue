# PB3 Data Catalogue — Concept

## Overview

This document outlines a proposed system for managing a catalogue of research data produced by the PB3 group. Each dataset is represented as an individual Markdown file containing structured metadata (in YAML front matter) and free-text description. The catalogue is managed as a Git repository.

## Why This Approach

- **Accessible to non-technical users.** Researchers create entries by copying a template and filling in fields in a plain text file. No database, no special software required.
- **Version-controlled.** Hosted on GitHub, every change is tracked with a full audit trail. Pull requests allow review before changes are accepted.
- **Automation-friendly.** GitHub Actions can automatically validate entries, check for missing fields, and generate summaries on every update.
- **Visual exploration with Obsidian.** The catalogue folder can be opened as an Obsidian vault. Tags, links between entries, and a network graph view make it easy to explore connections between datasets.
- **LLM-powered summaries.** An index file summarising the entire catalogue can be auto-generated using a language model, serving as a searchable knowledge base.

## Directory Structure

```
PB3-data-catalogue/
├── catalogue/                      # one Markdown file per data record
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
├── CONTRIBUTING.md                 # step-by-step guide for researchers
└── README.md
```

### File Naming Convention

Each catalogue entry follows the pattern:

```
YYYY-lastname-short-topic.md
```

For example: `2024-mueller-soil-moisture.md`. This keeps files sortable by year and identifiable at a glance.

## Metadata Fields

Each entry file contains a YAML front matter block with the following fields. Fields marked with **(mandatory)** must be filled in for the entry to pass validation. All other fields are recommended.

### Identity

| Field | Description | Required |
|-------|-------------|----------|
| `title` | Human-readable title for the dataset | Yes |
| `id` | Unique identifier, matching the filename | Yes |
| `schema_version` | Version of the metadata schema used (for future migration) | Yes |
| `status` | Entry lifecycle stage: `draft`, `complete`, or `archived` | Yes |
| `date_created` | Date the record was first created (YYYY-MM-DD) | Yes |
| `date_modified` | Date the record was last updated (YYYY-MM-DD) | Yes |

### People

| Field | Description | Required |
|-------|-------------|----------|
| `applicant` | The individual inserting/registering the data | Yes |
| `supervisor` | Supervising researcher | No |
| `contacts.project_lead` | Contact person responsible for the project | Yes |
| `contacts.data_manager` | Individual responsible for long-term data curation | Yes |
| `contacts.archive_steward` | Individual responsible for the archive (may be same as data manager) | Yes |

### Organisational Context

| Field | Description | Required |
|-------|-------------|----------|
| `working_group` | Working group or research area | No |
| `project.name` | Related project name | No |
| `project.number` | Related project number | No |
| `funding_sources` | List of funding organisations | No |

### Publications

| Field | Description | Required |
|-------|-------------|----------|
| `publications` | List of related publications, each with `title` and optionally `doi` | Yes |

### Discovery

| Field | Description | Required |
|-------|-------------|----------|
| `keywords` | List of keywords for search and categorisation | No |

### Data Description

| Field | Description | Required |
|-------|-------------|----------|
| `data.purpose` | Purpose of the data; relationship to the project | Yes |
| `data.collection_method` | How data were collected or produced; how existing data were re-used | Yes |
| `data.source` | Origin of the data | No |
| `data.format` | File formats (e.g., csv, netCDF, pdf) | Yes |
| `data.type` | Data types (e.g., numeric, textual, images, mixed media) | Yes |
| `data.volume` | Approximate size of the dataset | No |
| `data.coverage.spatial` | Geographic coverage | No |
| `data.coverage.temporal` | Time period covered | No |
| `data.identifiers` | Persistent identifiers such as DOIs, each with `type` and `value` | No |

### Documentation

| Field | Description | Required |
|-------|-------------|----------|
| `documentation.description` | Brief note on what documentation exists | Yes |
| `documentation.links` | List of links to documentation, codebooks, scripts, or repositories | Yes |

### Metadata Location

| Field | Description | Required |
|-------|-------------|----------|
| `metadata_location` | Where external metadata records can be found (if any) | Yes |

### Storage and Access

| Field | Description | Required |
|-------|-------------|----------|
| `storage.repository` | Name of the storage or archive service (e.g., Zenodo, BonaRes) | Yes |
| `storage.repository_url` | URL to the deposited dataset | No |
| `storage.license` | Licence under which data may be reused (e.g., CC-BY-4.0) | Yes |
| `storage.availability` | When and which data are available for reuse | Yes |
| `storage.retention` | How long the data will be preserved | No |
| `storage.access_restrictions` | Any restrictions on who may access the data | Yes |
| `storage.embargo.active` | Whether an embargo is in place (true/false) | No |
| `storage.embargo.reason` | Reason for embargo, if applicable | No |
| `storage.embargo.until` | Embargo end date, if applicable | No |

### Legal and Ethical Considerations

| Field | Description | Required |
|-------|-------------|----------|
| `legal_ethical.contains_personal_data` | Whether the data contain personal or sensitive data (true/false) | Yes |
| `legal_ethical.privacy_measures` | Measures taken to protect personal data (anonymisation, etc.) | Yes |
| `legal_ethical.gdpr_compliance` | How GDPR requirements are met, if applicable | No |
| `legal_ethical.ethical_approval` | Details of ethical approval, if applicable | No |
| `legal_ethical.data_ownership` | Who owns the data | Yes |
| `legal_ethical.ip_rights` | Intellectual property rights holder | No |

## Entry Lifecycle

Each catalogue entry has a `status` field reflecting where it is in its lifecycle:

```
draft  ──>  complete  ──>  archived
                │
                └──>  superseded (replaced by a newer entry)
```

- **draft** — The entry has been started but is incomplete. Validation is lenient: mandatory fields are flagged as warnings rather than errors.
- **complete** — All mandatory fields are filled and the data are deposited. Full validation applies.
- **archived** — The entry is retained for reference but the dataset is no longer actively maintained.

This allows researchers to register a dataset early (even before final deposit) and refine the record over time.

## Relationships Between Entries

Entries can reference each other in two ways:

1. **Structured relationships** in the YAML front matter, useful for automated processing:

   ```yaml
   related_entries:
     - id: "2023-weber-aqua-land-model"
       relationship: "derived-from"
   ```

   Supported relationship types: `derived-from`, `supplements`, `continues`, `replaces`.

2. **Wiki-style links** in the Markdown body, useful for Obsidian graph view:

   ```markdown
   This dataset was used as input for [[2023-weber-aqua-land-model]].
   ```

Both mechanisms can coexist. Structured relationships support automation; wiki links support visual browsing.

## Validation

A JSON Schema defines the expected structure and types for the YAML front matter. A validation script checks every entry against this schema.

Validation runs automatically via GitHub Actions on every pull request. An entry that fails validation (missing mandatory fields, wrong types) will be flagged before it can be merged.

For entries with `status: draft`, mandatory-field checks produce warnings instead of errors, allowing work-in-progress records.

## Auto-Generated Index

A script collects the YAML metadata from all catalogue entries and produces an `index.md` file containing:

- A summary table of all entries (title, applicant, status, keywords, year)
- Grouping by working group or research area
- Counts and statistics (entries per status, per year, etc.)

This index is regenerated automatically when changes are merged.

## LLM-Generated Summary

In addition to the structured index, a language model can be used to generate a narrative summary of the catalogue. This summary can:

- Group datasets by theme and highlight thematic connections
- Identify links and dependencies between datasets
- Flag potential gaps in documentation or metadata coverage

This serves as a human-friendly knowledge base overview and can be regenerated periodically as the catalogue grows.

## Obsidian Integration

The `catalogue/` folder can be opened directly as an Obsidian vault. Researchers who prefer a graphical interface can:

- Browse entries with live preview
- Use the graph view to see connections between datasets (via wiki links)
- Search and filter by tags (drawn from `keywords`)
- Use the Obsidian template plugin to create new entries from the template

No Obsidian-specific configuration is required; standard Markdown and YAML are used throughout.

## Workflow for Adding a New Entry

1. Copy `templates/new-entry.md` to `catalogue/YYYY-lastname-topic.md`
2. Fill in the YAML fields (start with mandatory ones; set `status: draft` if incomplete)
3. Add any free-text description in the Markdown body
4. Commit and open a pull request on GitHub
5. Automated validation runs and reports any issues
6. A reviewer checks the entry and merges the pull request
7. The index is regenerated automatically

## Alignment with FAIR Principles

This catalogue system supports the FAIR data principles:

- **Findable** — Each entry has a unique identifier, rich metadata, and keywords. The auto-generated index and LLM summary make datasets discoverable.
- **Accessible** — Storage location, access restrictions, and embargo details are explicitly recorded. The catalogue itself is openly hosted on GitHub.
- **Interoperable** — Structured YAML metadata uses consistent field names and controlled vocabularies (licences, relationship types). Standard formats (Markdown, JSON Schema) ensure broad tool compatibility.
- **Reusable** — Licence, ownership, provenance, and documentation links are mandatory fields, giving reusers the information they need.

## Future Considerations

- **Schema evolution.** A `schema_version` field in each entry allows the metadata structure to change over time. Migration scripts can update older entries when the schema is revised.
- **Web interface.** If the catalogue grows, a static site (e.g., built with Quarto or MkDocs) could provide a searchable, filterable web view generated directly from the Markdown files.
- **Web form for entry creation.** A simple form hosted on GitHub Pages could generate the YAML and open a pull request, further lowering the barrier for non-technical users.
- **Persistent identifiers.** Encourage minting DOIs (via Zenodo, BonaRes, etc.) for each dataset and recording them in the catalogue entry.

## Implementation Phases

1. **Template and schema** — Agree on final metadata fields, create the entry template and JSON Schema.
2. **Pilot entries** — Create 3-5 entries from real datasets to test and refine the template.
3. **CI validation** — Set up GitHub Actions to validate pull requests.
4. **Contributing guide** — Write clear instructions for researchers.
5. **Obsidian testing** — Verify graph view, tags, and links work as expected.
6. **Index generation** — Build the script to produce the summary table.
7. **LLM summary** — Add narrative summary generation once the catalogue has enough content.
8. **Website and advanced features** — As the catalogue matures.
