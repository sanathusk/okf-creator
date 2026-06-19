---
name: okf-creator
description: Create, update, validate, and maintain Open Knowledge Format (OKF) bundles — portable markdown + YAML frontmatter knowledge catalogs. Use this skill whenever the user mentions OKF, knowledge bundles, concept documents, creating or updating markdown-based documentation catalogs, organizing metadata in markdown files with frontmatter, or needs to scaffold, validate, or maintain a portable knowledge base structured as a directory of markdown files. Also trigger on keywords like "knowledge bundle", "concept doc", "index.md", "frontmatter", "catalog metadata", or "progressive disclosure" in the context of documentation.
---

# OKF Creator

Create, update, validate, and maintain Open Knowledge Format (OKF) bundles.

## 1. Overview

OKF is a markdown+YAML-frontmatter format for portable knowledge bundles (see `skill/references/SPEC.md` for the full spec — read it before starting any workflow if not already in context).

## 2. Workflow Selection

| User says... | Workflow |
|---|---|
| "new bundle", "scaffold", "create bundle" | Scaffold bundle ([3](#3-scaffold-a-new-bundle)) |
| "add concept", "create concept", "new doc" | Create concept ([4](#4-create-a-concept)) |
| "update", "edit", "change", "modify" | Update concept ([5](#5-update-a-concept)) |
| "index", "regenerate index" | Generate index ([6](#6-generateupdate-indexmd)) |
| "log", "record change", "changelog" | Log entry ([7](#7-log-entry)) |
| "validate", "check", "verify", "conformance" | Validate bundle ([8](#8-validate-bundle-conformance)) |

When ambiguous, ask clarifying questions (path, type, scope). For multi-file changes, process files independently.

## 3. Scaffold a new bundle

1. **Ask**: bundle root path, purpose/domain. Confirm directory doesn't exist or is empty.
2. **Create directory structure**: root + any subdirectories the user specifies. Default: flat (root only).
3. **Create `index.md`** at root with `okf_version: "0.1"` in frontmatter (the only index.md that gets frontmatter — per §11) and body sections per [6](#6-generateupdate-indexmd) listing each top-level subdirectory's contents.
4. **Create `log.md`** at root with `# Directory Update Log` heading and a single `## <YYYY-MM-DD>` Initialization entry.
5. **Report** created files to the user.

## 4. Create a concept

1. **Determine** from user: concept path (e.g. `tables/orders.md`), `type`, `title`, `description`, `resource`, `tags`, and body content. If no path given, ask type first to suggest a subdirectory (e.g. BigQuery Table → `tables/`).
2. **Validate**: path must not be `index.md` or `log.md` ([3.1](#3-scaffold-a-new-bundle)). Reject and suggest alternative if reserved.
3. **Generate frontmatter** per [4.1](#4-create-a-concept). Always set `timestamp` to current UTC ISO 8601 (`YYYY-MM-DDTHH:MM:SSZ`).
4. **Write body**: start with `# Title` matching the concept title, then user-provided content. Use conventional sections per [4.2](#4-create-a-concept) when applicable.
5. **After writing**: update parent `index.md` (run [6](#6-generateupdate-indexmd)) and append log entry (run [7](#7-log-entry)).
6. **Cross-links**: add per [5](#5-update-a-concept) as requested.

## 5. Update a concept

1. **Read** the existing `.md` file. Parse frontmatter and body.
2. **Apply changes**: update specified frontmatter fields (bump `timestamp` to now unless user sets it explicitly). Replace/prepend/append body as directed. Delete optional fields by omitting them.
3. **Preserve unknown frontmatter keys** ([4.1](#4-create-a-concept) Extensions).
4. **After writing**: update parent `index.md` ([6](#6-generateupdate-indexmd)) and append log entry ([7](#7-log-entry)).
5. **Rename/move**: create at new path, delete old, update all cross-links (`](/old/path.md)` and `](./old/path.md)`) across the bundle. Update both old and new parent index files. Log both deletion and creation.

## 6. Generate/update index.md

1. **Identify target directory**. Default: bundle root or the directory containing the updated concept.
2. **Read current `index.md`** if exists. Root MAY have frontmatter (preserve it). Non-root MUST NOT have frontmatter ([6](#6-generateupdate-indexmd)) — strip if present.
3. **Scan directory** for all `.md` files except `index.md` and `log.md`.
4. **For each concept `.md`**: parse frontmatter (`title`, `description`, `type`). Generate entry per [6](#6-generateupdate-indexmd) format. Group entries by `type` as section headings. If frontmatter missing/unparseable, include as `* [filename](filename.md)` with no description.
5. **For each subdirectory**: check for `index.md` inside (recursive). Entry: `* [Subdirectory](subdir/) - description` using the subdirectory's first heading or "Subdirectory of <domain>" fallback.
6. **Assemble**: root — preserve frontmatter + `---` separator, then sections. Non-root — start directly with sections. Order: untyped concepts first, then alphabetical by type, then subdirectories. Within each section: alphabetical by title (or filename).
7. **Write file**. Notify user of changes.

## 7. Log entry

1. **Identify scope**: bundle root or the parent directory of the changed concept.
2. **Read existing `log.md`** or create if absent (with `# Directory Update Log`).
3. **Add entry** under today's `## YYYY-MM-DD` heading. Create heading at top if missing; otherwise insert under it (newest first within group).
4. **Entry format** per [7](#7-log-entry). Accepted actions: `Creation`, `Update`, `Deprecation`, `Deletion`, `Move`, `Initialization`. Include absolute links to affected concepts.
5. **Write file**. Preserve existing entries unchanged.

## 8. Validate bundle conformance

Perform [9](#9-requirements-fail-on-violation) conformance checks plus additional soft checks. Report all findings.

### 9. requirements (FAIL on violation)
- **[9.1](#9-requirements-fail-on-violation)** — Every non-reserved `.md` file must have parseable YAML frontmatter (first line `---`, closing `---`, valid YAML between).
- **[9.2](#9-requirements-fail-on-violation)** — Every frontmatter block must have a non-empty `type` field.
- **[9.3](#9-requirements-fail-on-violation)** — Reserved filenames follow spec: root `index.md` MAY have frontmatter (WARN if `okf_version` missing); non-root `index.md` MUST NOT have frontmatter; `log.md` date headings must be `YYYY-MM-DD`.

### Additional soft checks (WARN only)
- **Cross-links**: For each `[text](/path/to/concept.md)` in any body, check target file exists. Skip external URLs.
- **Index staleness**: For each entry in `index.md`, verify linked concept's `title`/`description` match the entry. WARN on mismatch.

### Report format
```
Validation results for <bundle path>
====================================
FAIL: <count> | WARN: <count> | PASS: <count>

FAIL details:
- [path]: message

WARN details:
- [path]: message

PASS: All <N> concept documents have valid frontmatter with non-empty type.
```
If no failures and no warnings: `PASS: Bundle is conformant with OKF v0.1.`
