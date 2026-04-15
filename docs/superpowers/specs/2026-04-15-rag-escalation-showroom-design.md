# RAG Escalation Lab — Showroom Design

**Date:** 2026-04-15
**Status:** Approved (pending user review of this spec)
**Source material:** [`rhai-workshops/EscalationLab`](https://github.com/rhai-workshops/EscalationLab) — 12-section Jupyter notebook workshop (00–11)
**Target:** RHDP Showroom catalog item built on the `rhpds/showroom_template_nookbag` template

## Purpose

Convert a slice of the existing Jupyter-based EscalationLab workshop into a Showroom lab-item that wraps — rather than replaces — the notebook experience. The workshop teaches an "escalation of effort must be justified by evidence" principle across Baseline → RAG → Scaling → Inference-Time Scaling → Synthetic Data Generation → QLoRA Fine-tuning → Evaluation.

## Audience

Data scientists and ML engineers. Their primary platform is JupyterLab; Showroom plays a supporting role, not a primary learning surface.

## Delivery mode

Self-paced primary, instructor-capable. The written content must carry all "why / what next" narrative that an instructor would otherwise provide verbally, while staying lean enough that a live speaker isn't slowed down. Instructor-led total runtime is **~5 hours**; self-paced runtime is expected to be longer and will be calibrated against a pilot run.

## Scope decisions (already agreed with user)

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Provisioning model | **Fully provisioned** by RHDP catalog item (OCP + RHOAI + pre-configured workbench with GPU, disk, notebooks cloned) | Adds more learner value than teaching RHOAI setup from scratch |
| Experience pattern | **Notebook drives, Showroom is a reference wrapper** | Audience lives in JupyterLab |
| Showroom content surface | **Bookend minimal** — Welcome/customer scenario, Environment tour, Workshop map, Synthesis capstone | Keeps Showroom tight; reserves expansion for later |
| Notebook-repo disposition | **Dual-sourced** — `00_Orientation`, `01_CustomerScenario`, and `11_Synthesis` notebooks remain in the notebooks repo with full content; Showroom has an adapted version of the same material | Accepts drift cost so notebook repo stays usable standalone outside RHDP |
| Provisioning ownership | **Out of scope** — owned by AgnosticV config in a separate repo | Showroom is pure content |

## Architecture

Showroom is a content-only artifact. It knows nothing about cluster provisioning, workbench CRs, or GPU scheduling — those are the AgnosticV config's responsibility in a separate repo / PR.

```
content/modules/ROOT/
├── nav.adoc                    # 4-item flat nav, no sub-sections
└── pages/
    ├── index.adoc              # Welcome + customer scenario  (adapts notebook 00+01)
    ├── environment.adoc        # Guided tour of the provisioned workbench
    ├── map.adoc                # Workshop map + escalation-ladder diagram
    └── synthesis.adoc          # Capstone narrative            (adapts notebook 11)
```

Repos in play:

- **This repo** (`RAG-Escalation-Lab-Item`) — Showroom content only. Output: an Antora site.
- **`rhai-workshops/EscalationLab`** — Jupyter notebook repo, pre-cloned into the workbench by the catalog item at launch. Also remains standalone-runnable.
- **AgnosticV config repo** (not in this spec) — catalog-item provisioning: OCP cluster, RHOAI install, workbench CR, notebook git-clone init.

## Page-by-page content

### `index.adoc` — Welcome + Customer Scenario (~400–600 words)

**Purpose:** Land learners in the story before they touch code.

**Sections:**
- `= RAG Escalation Lab` — title and one-line promise
- **What you'll learn** — the escalation ladder at a glance (Baseline → RAG → Scaling → ITS → SDG → Fine-tuning → Evaluation); the guiding principle: *"escalation of effort must be justified by evidence"*
- **The customer scenario** — adapted from the `01_CustomerScenario` notebook: customer brings a domain knowledge problem (Basic Fantasy RPG rulebook as stand-in), expects we'll "just fine-tune a model," and we use the lab to demonstrate why you climb the ladder rung by rung
- **Who this is for** — data scientists / ML engineers familiar with Python + Jupyter; no prior RAG or fine-tuning required
- **What's provisioned for you** — one-sentence teaser linking to `environment.adoc`
- **Time to complete** — ~5 hours instructor-led total; self-paced longer (calibrated against pilot)
- **Let's get started** — CTA linking to `environment.adoc`

### `environment.adoc` — Environment tour (~300–500 words)

**Purpose:** Orient learners inside the provisioned RHOAI workbench. Zero setup steps — the catalog item did it.

**Sections:**
- **What you have** — bulleted inventory: OCP cluster URL, RHOAI dashboard URL, pre-created DS project, pre-configured workbench (image, GPU type, disk), notebook repo already cloned to `~/EscalationLab`
- **Opening JupyterLab** — screenshot + link using `{rhoai_dashboard_url}` attribute → DS Project → workbench → Open
- **Finding the notebooks** — where `EscalationLab/` lives, recommended starting notebook (`02_Baseline/`)
- **Verifying GPU access** — one quick sanity check (run a single cell, expect GPU-visible output); only "check" in Showroom
- **Troubleshooting** — collapsible list: workbench won't start, GPU not allocated, Git LFS files missing, kernel dies OOM

### `map.adoc` — Workshop map (~400–600 words + diagram)

**Purpose:** Single-glance view of the nine hands-on notebook sections (02–10) so learners can plan their session.

**Sections:**
- **Escalation ladder diagram** — Mermaid, authored inline (the extension is already configured in `site.yml`). Visualizes the rungs, the "evidence gate" between them, and relative time.
- **Section table** — rows for notebooks 02–10: notebook name, one-line outcome, estimated time (TBD from pilot), depends-on
- **Recommended paths** — "90-minute tour" (02, 04, 05 + synthesis page), "half-day" (02–07 + synthesis page), "full lab" (02–10 + synthesis page). Useful for instructors.
- **Returning for synthesis** — reminder that `synthesis.adoc` is the capstone after notebook 10

### `synthesis.adoc` — Capstone (~500–800 words)

**Purpose:** Adapted from the `11_Synthesis` notebook. Pure narrative — reads well outside a notebook.

**Sections:**
- **What the evidence showed** — summary of the ladder's results: when RAG was enough, when ITS helped, when SDG+fine-tuning paid off (and when it didn't)
- **The escalation framework** — the durable takeaway learners should apply to their own customer engagements
- **When *not* to climb** — anti-patterns: fine-tuning as first response, skipping eval
- **Next steps** — pointers to Granite docs, OpenShift AI docs, RHDP catalog items for follow-on labs

## Learner flow (happy path)

1. Catalog item provisions → learner gets Showroom URL + workbench URL in the RHDP inbox
2. Learner lands on `index.adoc` → reads customer scenario → clicks "Get Started"
3. `environment.adoc` → opens JupyterLab in a new tab, runs GPU sanity cell
4. JupyterLab from here on — works through `02_Baseline` → `10_ComprehensiveEvaluation` at their own pace
5. Returns to Showroom tab → reads `synthesis.adoc` as capstone
6. (Optional) `map.adoc` consulted any time they want to re-orient

## Assets

- **Mermaid diagram** in `map.adoc` — inline, uses the already-configured `@sntke/antora-mermaid-extension`
- **Screenshots** in `environment.adoc`, stored under `content/modules/ROOT/assets/images/`:
  - RHOAI dashboard → Data Science Projects view
  - Workbench row with "Open" button
  - JupyterLab file browser showing cloned `EscalationLab/` repo
- **No custom branding** — inherits from the RHDP Summit 2025 UI bundle already pinned in `site.yml`

## Antora attributes

Rely on RHDP deployer conventions: `{rhoai_dashboard_url}`, `{openshift_console_url}`, `{user}`, `{password}`. Any net-new attributes must be declared in `content/antora.yml`.

## Validation (how we know it works)

- **Render check** — `podman run … ghcr.io/juliaaano/antora-viewer` locally; all 4 pages render, nav works, Mermaid renders
- **Link check** — all xrefs resolve; external links respond 200
- **Content review** — pass through the `showroom:verify-content` skill for Red Hat style and AsciiDoc conformance
- **Pilot run** — one self-paced end-to-end runthrough against the real provisioned env before publishing, to catch screenshot drift and instruction accuracy

## Out of scope

- AgnosticV / catalog-item provisioning config (separate repo)
- Notebook-repo edits (notebooks 02–10 stay as-is; 00/01/11 remain dual-sourced)
- Translation / i18n
- In-Showroom quizzes or live validation beyond the single GPU sanity cell
- Per-section time estimates — deferred to pilot measurement; spec uses "TBD"

## Open items (not blocking)

- **Per-section time estimates** — to be measured during pilot run and back-filled into `map.adoc`
- **Screenshot capture** — requires a provisioned environment to produce accurate shots; do during pilot
