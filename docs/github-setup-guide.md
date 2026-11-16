# PB3 Data Catalogue — GitHub set-up guide

## What we are building, in one paragraph
Every dataset in PB3 gets one small text file that describes it: who made it, where it is stored, what licence it has, and so on. All these files live together in one shared folder on GitHub. When someone adds or changes a file, GitHub automatically checks that nothing important is missing. A maintainer then has a quick look and accepts the change. People who don't use GitHub can fill in a Microsoft Form instead, and their answers are turned into one of these files automatically.

## Words you will come across

| Term | What it means here |
|------|--------------------|
| **Git** | Software that keeps a full history of every change to a set of files, so you can always see who changed what and go back. |
| **GitHub** | A website that stores Git projects online and adds collaboration tools: reviews, discussions and automation. |
| **Repository ("repo")** | One project folder on GitHub with its full history. Our catalogue is one repo. |
| **Organisation ("org")** | A shared GitHub account for a group. It owns the repo, so the repo does not belong to any one person who might leave. |
| **Owner** | Someone with full control over the organisation. We want at least two. |
| **Team** | A named group of people inside the org (for example "catalogue-maintainers"). Permissions are given to teams, not individuals. |
| **Role / permission** | What someone may do. *Read* = look only. *Triage* = also manage labels and issues. *Write* = also propose and accept changes. *Maintain* = also manage settings that don't destroy anything. *Admin* = everything. |
| **Two-factor authentication (2FA)** | Logging in needs a password **and** a code from your phone. GitHub requires this. |
| **Markdown (.md)** | A plain text file with light formatting: `#` for headings, `*` for bullet points. Easy to read even without special software. |
| **YAML** | A simple way to write structured information as `field: value` lines. Indentation matters: use spaces, never tabs. |
| **Front matter** | The YAML block at the very top of a Markdown file, between two `---` lines. This is where the dataset's metadata goes. |
| **Entry / record** | One Markdown file describing one dataset, for example `catalogue/2024-mueller-soil-moisture.md`. |
| **Template** | A ready-made empty entry that you copy and fill in. |
| **Schema (JSON Schema)** | The rulebook for entries. It says which fields must exist, what type each one is (text, date, yes/no, list) and which values are allowed. It is written in JSON, a format computers read easily. |
| **schema_version** | A field in each entry saying which version of the rulebook it was written against, so we can update old entries when the rules change. |
| **Validation** | Automatically checking every entry against the schema and a few extra rules. |
| **Status: draft / complete / archived** | *Draft* means started but not finished; missing fields only give warnings. *Complete* means all mandatory fields are filled; missing fields are errors. *Archived* means kept for reference only. |
| **main** | The official, accepted version of the catalogue. |
| **Branch** | A separate working copy where you make changes without touching `main` until they are accepted. |
| **Commit** | Saving a change, with a short message describing it. |
| **Pull request (PR)** | A request to bring the changes on a branch into `main`. It shows exactly what changed, runs the automatic checks, and lets someone review and comment. |
| **Review / approve** | A maintainer reads the PR and either asks for changes or approves it. |
| **Merge** | Accepting a PR, so that its changes become part of `main`. |
| **Squash merge** | Merging a PR as one tidy commit, however many small saves went into it. |
| **Issue** | A discussion item or to-do on GitHub, like a ticket. |
| **Issue form** | An issue with a fill-in form instead of a blank text box. |
| **PR template** | Text that appears automatically when someone opens a PR, such as a checklist. |
| **Label** | A coloured tag on a PR or issue, for example `new-entry` or `from-form`. |
| **GitHub Actions** | GitHub's built-in automation. It runs small programs when something happens, such as when a PR is opened. |
| **Workflow** | One automation recipe for GitHub Actions, stored as a YAML file in `.github/workflows/`. |
| **Status check** | The result of a workflow on a PR: green tick ✅ (passed) or red cross ❌ (failed). |
| **Ruleset / branch protection** | Settings that stop anyone, including admins, from changing `main` directly or merging a PR whose checks failed. |
| **CODEOWNERS** | A file that says who must review changes to which folders. |
| **Pre-commit hook** | An optional check that runs on your own computer before you save a commit, so you find errors even earlier. |
| **Microsoft Forms** | ZALF's Microsoft 365 tool for questionnaires. |
| **Power Automate / flow** | Microsoft 365 automation. A *flow* is a recipe like "when a form is submitted, do X". |
| **Connector** | Power Automate's link to another service, such as GitHub. |
| **Repository dispatch** | A signal sent from outside (here, from Power Automate) that tells a GitHub workflow to start, together with some data. |
| **Payload** | The data that travels with that signal, here the form answers. |
| **Token / service account** | A token is a kind of password that lets an automation act on GitHub. A service account is a shared GitHub account used by automations, rather than a personal one. |

## How a normal contribution works (the big picture)
1. Copy the template and fill in a new entry, either in the browser or on your computer.
2. Save it on a **branch** and open a **pull request**.
3. GitHub Actions **validates** it within about a minute and shows ✅ or ❌. If there is a ❌, click **Details** to see what is wrong. The problems are also marked on the file itself.
4. A maintainer **reviews** it, asks for fixes if needed, and **approves**.
5. The maintainer **merges** it, and the entry is now in the catalogue.

---

## Step-by-step set-up

> Steps marked 🔑 need an organisation owner. Menu names are those GitHub uses in 2026. If a label has moved, use the search box in Settings.

### Step 1: Create the organisation 🔑
1. Log in to GitHub. Click your profile picture (top right) → **Your organizations** → **New organization**.
2. Choose the **Free** plan.
3. Give it a name (for example `zalf-pb3`) and use a shared ZALF contact email.
4. Optional: check whether ZALF qualifies for GitHub's free plans for academic institutions (education.github.com). That would unlock merge protection on **private** repos.

### Step 2: Secure it and add people 🔑
1. **Org → Settings → Authentication security** → tick **Require two-factor authentication**. Everyone must set up 2FA first, otherwise they will be removed.
2. **Org → Settings → Member privileges → Base permissions** → set to **Read**, so people only get more access through teams.
3. **Org → People → Invite member** → invite everyone. Make a second person **Owner**.
4. **Org → Teams → New team**. Create:
   - `catalogue-maintainers`: the research assistants.
   - `catalogue-leads`: PI and lead RA. They approve schema changes.
   - `contributors`: researchers who add entries.

### Step 3: Put the repo in the organisation 🔑
- **If the repo already exists elsewhere:** **Repo → Settings → General → Danger Zone → Transfer ownership** → choose the new org. GitHub redirects the old links.
- Then **Repo → Settings → Collaborators and teams → Add teams**:
  - `catalogue-maintainers` → **Maintain**
  - `catalogue-leads` → **Admin**
  - `contributors` → **Write**

### Step 4: Merge settings
**Repo → Settings → General → Pull Requests**:
- Untick **Allow merge commits** and **Allow rebase merging**. Keep **Allow squash merging**.
- Tick **Automatically delete head branches**, which tidies up branches after merging.

### Step 5: Labels
**Repo → Issues → Labels → New label**. Create:
- `new-entry`
- `update-entry`
- `schema-change`
- `from-form`
- `needs-info`

### Step 6: Add the validation files
Add these files to the repo. Each one is shown in full below.

```
requirements.txt
schemas/metadata-schema.json
scripts/validate.py
.github/workflows/validate.yml
.github/pull_request_template.md
.github/CODEOWNERS
```

**`requirements.txt`** lists the Python packages the check needs:
```
python-frontmatter>=1.1
jsonschema>=4.20
```

**`schemas/metadata-schema.json`** is the rulebook. This is a *starter* version showing the pattern. Every field from CONCEPT.md still needs to be added (this is an action item).
- The top `required` list applies to **all** entries, including drafts.
- The `if`/`then` block adds the extra mandatory fields that only apply once an entry is `complete` (or `archived`).
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "PB3 catalogue entry",
  "type": "object",
  "required": ["title", "id", "schema_version", "status", "date_created", "date_modified", "applicant"],
  "properties": {
    "title":          { "type": "string", "minLength": 1 },
    "id":             { "type": "string" },
    "schema_version": { "type": "string" },
    "status":         { "enum": ["draft", "complete", "archived", "superseded"] },
    "date_created":   { "type": "string", "pattern": "^\\d{4}-\\d{2}-\\d{2}$" },
    "date_modified":  { "type": "string", "pattern": "^\\d{4}-\\d{2}-\\d{2}$" },
    "applicant":      { "type": "string" },
    "keywords":       { "type": "array", "items": { "type": "string" } },
    "storage": {
      "type": "object",
      "properties": {
        "repository": { "type": "string" },
        "license":    { "type": "string" }
      }
    }
  },
  "if":   { "properties": { "status": { "enum": ["complete", "archived"] } } },
  "then": {
    "required": ["contacts", "publications", "data", "documentation",
                 "metadata_location", "storage", "legal_ethical"],
    "properties": {
      "storage": { "required": ["repository", "license", "availability", "access_restrictions"] }
    }
  }
}
```

**`scripts/validate.py`** checks every entry.
- It reports **errors** for real problems.
- For drafts, it reports **warnings** that list what is still needed before the entry can be marked complete.
- It also checks things the schema can't:
  - the filename matches the `id`;
  - the filename follows the naming pattern;
  - the dates are in the right order;
  - linked entries exist.
```python
"""Check every catalogue entry against schemas/metadata-schema.json.

Usage: python scripts/validate.py catalogue
Exits with 1 if any entry has an error. For draft entries, fields still
needed before 'complete' are reported as warnings only.
"""
import json
import re
import sys
from pathlib import Path

import frontmatter
from jsonschema import Draft202012Validator

ROOT = Path(__file__).resolve().parent.parent
SCHEMA = json.loads((ROOT / "schemas" / "metadata-schema.json").read_text(encoding="utf-8"))
FILENAME = re.compile(r"^\d{4}-[a-z]+-[a-z0-9-]+$")


def report(level, path, message):
    # This format makes GitHub show the message on the PR's "Files changed" tab.
    print(f"::{level} file={path.as_posix()}::{message}")


def schema_problems(validator, meta):
    return {f"{'/'.join(map(str, e.path)) or 'entry'}: {e.message}" for e in validator.iter_errors(meta)}


def main(folder):
    validator = Draft202012Validator(SCHEMA)
    files = sorted(Path(folder).glob("*.md"))
    known_ids = {f.stem for f in files}
    errors = 0

    for path in files:
        try:
            post = frontmatter.load(path)
        except Exception as exc:
            report("error", path, f"YAML front matter could not be read: {exc}")
            errors += 1
            continue
        # YAML turns 2024-05-01 into a date object; the schema expects text.
        meta = json.loads(json.dumps(post.metadata, default=str))

        problems = schema_problems(validator, meta)
        if "id" in meta and meta["id"] != path.stem:
            problems.add(f"id '{meta['id']}' does not match the filename '{path.stem}'")
        if not FILENAME.match(path.stem):
            problems.add("filename should look like YYYY-lastname-short-topic.md (lowercase, hyphens)")
        if meta.get("date_created") and meta.get("date_modified") and meta["date_modified"] < meta["date_created"]:
            problems.add("date_modified is earlier than date_created")
        related = meta.get("related_entries")
        if isinstance(related, list):
            for rel in related:
                if isinstance(rel, dict) and rel.get("id") not in known_ids:
                    problems.add(f"related entry '{rel.get('id')}' does not exist in the catalogue")

        for message in sorted(problems):
            report("error", path, message)
        errors += len(problems)

        if meta.get("status") == "draft":
            still_needed = schema_problems(validator, {**meta, "status": "complete"}) - problems
            for message in sorted(still_needed):
                report("warning", path, f"needed before 'complete': {message}")

    print(f"Checked {len(files)} entries, {errors} error(s).")
    return 1 if errors else 0


if __name__ == "__main__":
    sys.exit(main(sys.argv[1] if len(sys.argv) > 1 else "catalogue"))
```

**`.github/workflows/validate.yml`** runs the check on GitHub for every PR and every change to `main`:
```yaml
name: Validate catalogue
on:
  pull_request:
  push:
    branches: [main]
  workflow_dispatch:          # adds a "Run workflow" button for manual runs

permissions:
  contents: read

jobs:
  validate:                   # this name becomes the required check in Step 7
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: pip
      - run: pip install -r requirements.txt
      - run: python scripts/validate.py catalogue
```

**`.github/pull_request_template.md`** is the checklist that appears in every new PR:
```markdown
## What does this PR do?
- [ ] New entry
- [ ] Update to an existing entry
- [ ] Archive an entry
- [ ] Schema / tooling change

**Entry file(s):** `catalogue/...`
**Status set to:** draft / complete

## Checklist
- [ ] Filename follows `YYYY-lastname-short-topic.md` and matches `id`
- [ ] `date_modified` is today's date
- [ ] Licence and access restrictions are filled in
- [ ] The personal / sensitive data question is answered
- [ ] The project lead / data owner knows about this entry
- [ ] Related entries are linked (in YAML and as `[[wiki-links]]`) where relevant

## Notes for the reviewer
```

**`.github/CODEOWNERS`** sets who is asked to review what. Replace `ORG` with the organisation name.
```
catalogue/   @ORG/catalogue-maintainers
templates/   @ORG/catalogue-maintainers
schemas/     @ORG/catalogue-leads
scripts/     @ORG/catalogue-leads
.github/     @ORG/catalogue-leads
```

### Step 7: Protect `main` 🔑
Do this *after* the validate workflow has run at least once. Until then, GitHub won't offer "validate" as a check you can pick.
1. **Repo → Settings → Rules → Rulesets → New ruleset → New branch ruleset.**
2. Fill in the basics:
   - Name: `protect-main`
   - Enforcement status: **Active**
   - Target branches: **Add target → Include default branch**
3. Tick these rules:
   - **Restrict deletions**
   - **Block force pushes**
   - **Require a pull request before merging**. Set required approvals to **1** and tick **Require review from Code Owners**.
   - **Require status checks to pass**. Click **Add checks** and choose **validate**.
4. Click **Create**.

> On a **private** repo with the Free plan, GitHub won't enforce these rules. Then the rule is simply "never merge with a red ❌".

### Step 8 (optional): Check on your own computer before pushing
For anyone editing in VS Code or on the command line:
```bash
pip install -r requirements.txt pre-commit
pre-commit install
```
Then add `.pre-commit-config.yaml` to the repo:
```yaml
repos:
  - repo: local
    hooks:
      - id: validate-catalogue
        name: Validate catalogue entries
        entry: python scripts/validate.py catalogue
        language: python
        additional_dependencies: [python-frontmatter, jsonschema]
        pass_filenames: false
        files: ^(catalogue|schemas)/
```
From then on, every commit that touches the catalogue is checked first.

### Step 9: Test it
1. Make a branch with three test entries:
   - one complete and correct;
   - one draft with missing fields;
   - one with a typo in a field name or a wrong date.
2. Open a PR. Expected results:
   - The correct entry passes.
   - The draft shows yellow warnings.
   - The broken one shows a red ❌ with a message on the file.
3. Check that the **Merge** button is blocked while the check is red.

---

## How a researcher adds an entry in the browser (no installation)
1. Open the repo on GitHub → open `templates/new-entry.md` → click the **copy** icon (Copy raw file).
2. Go to the `catalogue` folder → **Add file → Create new file**.
3. Name it `YYYY-lastname-short-topic.md` (for example `2025-schmidt-crop-yield.md`) and paste in the template.
4. Fill in the fields. If something isn't known yet, set `status: draft`.
5. Click **Commit changes…**
   - Choose **Create a new branch for this commit and start a pull request**.
   - Write a short message such as "Add crop yield dataset".
   - Click **Propose changes**.
6. Fill in the PR checklist → **Create pull request**.
7. Wait for the ✅ or ❌. If there is a ❌, click **Details**, fix the file (✏️ pencil icon on the "Files changed" tab), and commit again. The check reruns automatically.

---

## The Microsoft Form route (for people who don't use GitHub)

### How it works
```
Researcher fills in MS Form
   ↓
Power Automate notices the new answer
   ↓
Power Automate sends the answers to GitHub ("repository dispatch")
   ↓
A GitHub workflow turns the answers into a catalogue file (always status: draft)
   ↓
The workflow opens a pull request labelled "from-form"
   ↓
A maintainer checks it, completes missing details with the researcher, and merges
```
Power Automate only *passes the answers on*. All the logic that turns answers into a file lives in the repo (`scripts/form_to_entry.py`), where it is versioned and can be tested. This also means the form only needs the **standard** GitHub connector, so no premium licence is required.

### Set-up steps
**A. Prepare GitHub** 🔑
1. Create a shared **service account** on GitHub (for example `pb3-catalogue-bot`, using a shared ZALF mailbox) and add it to the org with **Write** access to the repo. Power Automate will act as this account, so nothing breaks when an RA leaves.
2. **Org → Settings → Third-party access → OAuth app policy.** If access is restricted, approve the Microsoft Power Platform GitHub connector when it asks.
3. **Repo → Settings → Actions → General → Workflow permissions** → tick **Allow GitHub Actions to create and approve pull requests**.
4. Add `.github/workflows/form-intake.yml`:
```yaml
name: Form intake
on:
  repository_dispatch:
    types: [catalogue-form-submission]

permissions:
  contents: write
  pull-requests: write

jobs:
  intake:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: pip
      - run: pip install -r requirements.txt
      - name: Build the entry from the form answers
        env:
          PAYLOAD: ${{ toJson(github.event.client_payload.record) }}
        run: python scripts/form_to_entry.py
      - name: Validate (result goes into the PR description; does not stop the PR)
        run: python scripts/validate.py catalogue > "$RUNNER_TEMP/validation.txt" 2>&1 || true
      - uses: peter-evans/create-pull-request@v7
        with:
          branch: form/${{ github.run_id }}
          add-paths: catalogue/*.md
          commit-message: "Add catalogue entry from form submission"
          title: "New catalogue entry (from form)"
          labels: from-form
          body-path: ${{ runner.temp }}/validation.txt
```
5. Write `scripts/form_to_entry.py` (an action item for after the pilot). It should:
   - read the JSON answers from the `PAYLOAD` environment variable;
   - map each form question to its schema field, using a mapping file in the repo;
   - split one-per-line answers into lists;
   - set `status: draft`, today's dates and the current `schema_version`;
   - build the filename and write `catalogue/<id>.md` from the template.

> **Good to know:** PRs created by a workflow don't start other workflows, so the normal validate check won't run on them by itself. That is why the intake workflow validates too. As soon as a maintainer edits the file on that PR, the normal check runs as usual.

**B. Build the form** (forms.office.com)
1. **New Form**, titled "PB3 Data Catalogue – register a dataset".
2. **Settings (⋯ → Settings):** **Only people in my organization can respond** and **Record name**. The responder's identity can then fill `applicant`.
3. For each mandatory field, add a question and mark it **Required** where the field is mandatory for `complete`.
4. Choose question types:
   - **Choice** for fixed answers: licence, data type, personal data yes/no.
   - **Date** for dates.
   - **Long text** with "one per line" in the hint for lists such as keywords, publications and links.
5. Keep a table in the repo (for example `schemas/form-mapping.md`) that pairs each question with its schema field. **Whenever the schema changes, update the form and this table at the same time.**

**C. Build the flow** (make.powerautomate.com)
1. **Create → Automated cloud flow.** Name it "PB3 catalogue form → GitHub". Trigger: **When a new response is submitted** (Microsoft Forms) → pick the form.
2. **+ New step → Get response details** (Microsoft Forms). Use the same form, and choose *Response Id* from the dynamic content.
3. **+ New step → GitHub → Create a repository dispatch event.**
   - Sign in **as the service account** when asked.
   - Repository owner: the org name.
   - Repository name: the repo name.
   - Event name: `catalogue-form-submission` (must match the workflow exactly).
   - Event payload: a JSON object with **one key, `record`**, containing the answers. GitHub allows at most 10 top-level keys, so everything goes inside `record`. For example:
     ```json
     { "record": { "title": "<Title answer>", "applicant": "<Responder's email>", "keywords": "<Keywords answer>", "schema_version": "1.0" } }
     ```
     Insert each `<…>` from the dynamic content of *Get response details*.
4. Optional: **+ New step → Send an email (Office 365 Outlook)** to the responder, saying "Thanks, a maintainer will be in touch".
5. **Save**, then **Share → add co-owners** (the other RAs), so the flow doesn't depend on one person.
6. **Test** by submitting the form yourself. Within about a minute there should be a new PR labelled `from-form` in the repo.

### What maintainers do with a form PR
- Read the entry and the validation results in the PR description.
- Contact the researcher by email for anything that is missing.
- Edit the file on the PR branch (pencil icon). This also runs the normal validation.
- Switch `status` to `complete` when everything is there, then approve and merge.

---

## Before we put real data in: data protection
- The catalogue holds names, contact roles and sometimes information about embargoes or personal data. GitHub servers are in the US.
- Check with the ZALF data protection officer **before** the first real entries go in. Also decide then whether the repo is public or private.
- Never put the data itself, passwords or confidential details into an entry. Entries only *describe* the data and say where it lives.
