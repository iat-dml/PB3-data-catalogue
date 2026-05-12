# PB3 Data Catalogue

A lightweight, version-controlled catalogue for research datasets produced by the PB3 group at [ZALF – Leibniz Centre for Agricultural Landscape Research](https://www.zalf.de/en/Pages/ZALF.aspx).

## Purpose

The PB3 group generates many datasets — soils, crops, models, images, field measurements. This catalogue provides a shared, structured record of those datasets so they remain findable and usable within the group, even as people move on.

## Proposed System

> **One structured text file per dataset. Stored together in a shared Git repository.**

Each entry is a Markdown file with a YAML front matter block (title, owner, format, licence, etc.) and a free-text description. Entries are added via pull requests, validated automatically, and kept under full version control. The catalogue is designed to be adopted incrementally — starting with just a template and a shared repo, with optional automation and advanced features added later.

See [CONCEPT.md](CONCEPT.md) for the full design rationale, metadata schema, and implementation plan.
