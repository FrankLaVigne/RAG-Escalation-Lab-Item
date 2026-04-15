# RAG Escalation Lab Showroom — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the `showroom_template_nookbag` template scaffolding in this repo with four AsciiDoc pages (`index`, `environment`, `map`, `synthesis`) that serve as a lean wrapper around the `rhai-workshops/EscalationLab` Jupyter notebooks, configured for a fully-provisioned RHDP catalog item.

**Architecture:** Content-only Antora site. Four flat-nav pages under `content/modules/ROOT/pages/`, one Mermaid escalation-ladder diagram (extension already configured in `site.yml`), images already mirrored under `content/modules/ROOT/assets/images/`, runtime values injected through Antora attributes declared in `content/antora.yml`. No web terminal / no `role="execute"` code blocks — the learner's terminal is JupyterLab in a separate tab.

**Tech Stack:** Antora 3, AsciiDoc, `@sntke/antora-mermaid-extension`, `ghcr.io/juliaaano/antora-viewer` (local render), RHDP Summit 2025 UI bundle.

**Spec reference:** `docs/superpowers/specs/2026-04-15-rag-escalation-showroom-design.md` — re-read sections *Page-by-page content*, *Assets*, and *Antora attributes* before authoring each page.

**Source-material references (fetch these on-demand during relevant tasks):**

- 00 Orientation notebook — `curl -sSLf https://raw.githubusercontent.com/rhai-workshops/EscalationLab/main/00_Orientation/00_Orientation.ipynb` (actual filename may differ; first run `curl -sSLf https://api.github.com/repos/rhai-workshops/EscalationLab/contents/00_Orientation` to list)
- 01 Customer Scenario — same pattern under `01_CustomerScenario/`
- 11 Synthesis — same pattern under `11_Synthesis/`

Extract markdown cells using `python3 -c "import json,sys; nb=json.load(sys.stdin); [print(''.join(c['source'])) for c in nb['cells'] if c['cell_type']=='markdown']"` piped from `curl`.

**Branch / worktree:** This plan implements directly on `main` (continuation of current session). The user authorized SSH pushes to `git@github.com:FrankLaVigne/RAG-Escalation-Lab-Item.git`.

---

## File structure

| Path | Action | Responsibility |
|---|---|---|
| `content/modules/ROOT/nav.adoc` | Replace | Flat 4-item nav pointing at the 4 new pages |
| `content/modules/ROOT/pages/index.adoc` | Rewrite | Welcome + Customer Scenario (adapts 00+01 notebooks) |
| `content/modules/ROOT/pages/environment.adoc` | Create | Tour of the pre-provisioned RHOAI workbench |
| `content/modules/ROOT/pages/map.adoc` | Create | Workshop map + Mermaid escalation-ladder diagram + recommended paths |
| `content/modules/ROOT/pages/synthesis.adoc` | Create | Capstone narrative (adapts 11_Synthesis notebook) |
| `content/modules/ROOT/pages/*.adoc` (14 template docs) | Delete | Template self-documentation, irrelevant to this lab |
| `content/antora.yml` | Modify | Update `title`; add `rhoai_dashboard_url` attribute; keep existing placeholder defaults for local preview |
| `site.yml` | Modify | Update `site.title` and `start_page` (if not already `modules::index.adoc`) |
| `README.adoc` | Replace | Short lab-specific README (supersedes template's "Showroom Template" README) |

**Pages to delete** (14 files): `agnosticv-config.adoc`, `architecture.adoc`, `attribute-example.adoc`, `content-repo.adoc`, `contributing.adoc`, `deployer.adoc`, `images.adoc`, `nookbag.adoc`, `ocp4-role-reference.adoc`, `ocp-integration.adoc`, `quick-start.adoc`, `ui-config.adoc`, `user-data.adoc`, `vm-role-reference.adoc`.

---

## Task 1 — Scrub template scaffolding

**Files:**
- Delete: 14 template pages (list above) under `content/modules/ROOT/pages/`
- Modify: `content/modules/ROOT/nav.adoc` — replace entire content

- [ ] **Step 1.1: Verify the 14 deletion targets exist**

Run: `ls content/modules/ROOT/pages/{agnosticv-config,architecture,attribute-example,content-repo,contributing,deployer,images,nookbag,ocp4-role-reference,ocp-integration,quick-start,ui-config,user-data,vm-role-reference}.adoc | wc -l`
Expected: `14`

- [ ] **Step 1.2: Delete the 14 template docs pages**

```bash
git rm content/modules/ROOT/pages/agnosticv-config.adoc \
       content/modules/ROOT/pages/architecture.adoc \
       content/modules/ROOT/pages/attribute-example.adoc \
       content/modules/ROOT/pages/content-repo.adoc \
       content/modules/ROOT/pages/contributing.adoc \
       content/modules/ROOT/pages/deployer.adoc \
       content/modules/ROOT/pages/images.adoc \
       content/modules/ROOT/pages/nookbag.adoc \
       content/modules/ROOT/pages/ocp4-role-reference.adoc \
       content/modules/ROOT/pages/ocp-integration.adoc \
       content/modules/ROOT/pages/quick-start.adoc \
       content/modules/ROOT/pages/ui-config.adoc \
       content/modules/ROOT/pages/user-data.adoc \
       content/modules/ROOT/pages/vm-role-reference.adoc
```

- [ ] **Step 1.3: Replace `nav.adoc` with the 4-item flat nav**

Overwrite `content/modules/ROOT/nav.adoc` with exactly:

```asciidoc
* xref:index.adoc[Welcome]
* xref:environment.adoc[Your Environment]
* xref:map.adoc[Workshop Map]
* xref:synthesis.adoc[Synthesis]
```

- [ ] **Step 1.4: Commit**

```bash
git add content/modules/ROOT/nav.adoc content/modules/ROOT/pages/
git commit -m "Remove template docs pages; replace nav with 4-item lab nav"
```

---

## Task 2 — Update site and antora metadata

**Files:**
- Modify: `site.yml` — lines 1–5 (the `site:` block)
- Modify: `content/antora.yml` — `title` on line 2; add `rhoai_dashboard_url` to the `asciidoc.attributes` block (after line 17)

- [ ] **Step 2.1: Update `site.yml`**

Replace the `site:` block (top of file) with:

```yaml
site:
  title: RAG Escalation Lab
  url: https://github.com/FrankLaVigne/RAG-Escalation-Lab-Item
  start_page: modules::index.adoc
```

Leave `content:`, `ui:`, `antora:`, and `output:` blocks unchanged.

- [ ] **Step 2.2: Update `content/antora.yml`**

Change line 2 from `title: Showroom Collection` to `title: RAG Escalation Lab`.

Under `asciidoc.attributes`, add a new line **after** `openshift_cluster_ingress_domain`:

```yaml
    rhoai_dashboard_url: https://rhods-dashboard-redhat-ods-applications.apps.cluster-abc123.ocpv00.rhdp.net
    data_science_project_name: rag-escalation-lab
    workbench_name: escalation-workbench
```

These placeholder defaults let local previews render correctly; AgnosticV will override at catalog-item runtime.

- [ ] **Step 2.3: Verify local render still succeeds**

Run (in background, skip if Podman unavailable in dev env — fallback is Step 7):
```bash
podman run --rm --name antora-viewer -v $PWD:/antora:z -p 8080:8080 -d ghcr.io/juliaaano/antora-viewer
sleep 5
curl -sf http://localhost:8080/ >/dev/null && echo "OK render"
podman stop antora-viewer
```
Expected: `OK render`

- [ ] **Step 2.4: Commit**

```bash
git add site.yml content/antora.yml
git commit -m "Rebrand site/antora metadata for RAG Escalation Lab"
```

---

## Task 3 — Author `index.adoc` (Welcome + Customer Scenario)

**Files:**
- Create: `content/modules/ROOT/pages/index.adoc` (replaces the just-obsolete "What is Showroom?" content)

Target length: **400–600 words**. Audience: data scientists / ML engineers new to the lab.

- [ ] **Step 3.1: Fetch source markdown from the 01_CustomerScenario notebook**

```bash
curl -sSLf https://api.github.com/repos/rhai-workshops/EscalationLab/contents/01_CustomerScenario \
  | python3 -c "import json,sys; print('\n'.join(f['path'] for f in json.load(sys.stdin) if f['name'].endswith('.ipynb')))"
```
This prints the notebook path. Then:
```bash
curl -sSLf "https://raw.githubusercontent.com/rhai-workshops/EscalationLab/main/<path-from-above>" \
  | python3 -c "import json,sys; nb=json.load(sys.stdin); [print(''.join(c['source']),'\n---') for c in nb['cells'] if c['cell_type']=='markdown']"
```
Repeat for `00_Orientation` to capture the "escalation of effort must be justified by evidence" guiding principle.

- [ ] **Step 3.2: Write `content/modules/ROOT/pages/index.adoc`**

Structure (adapt prose from fetched cells; do NOT include the notebooks' environment-setup instructions — those belong in `environment.adoc`):

```asciidoc
= RAG Escalation Lab
:navtitle: Welcome

Your customer needs a specialist model that knows their domain cold. The obvious move — "just fine-tune it" — is expensive, slow, and often unnecessary. This lab walks you through an *escalation ladder*: start with the cheapest intervention, measure, and only climb the next rung if the evidence demands it.

NOTE: The guiding principle of this workshop is **escalation of effort must be justified by evidence**.

== What you'll learn

// Two-sentence summary of the journey: Baseline → RAG → Scaling → ITS → SDG → QLoRA Fine-tuning → Evaluation.

* How to benchmark a stock LLM against a domain the model has never seen
* When retrieval-augmented generation (RAG) is enough — and when it isn't
* How inference-time scaling (best-of-N) compares to training-time investment
* How to generate synthetic training data and parameter-efficiently fine-tune with QLoRA
* How to evaluate outcomes across all these approaches and choose the right rung for a real engagement

== The customer scenario

// Adapt the Basic Fantasy RPG framing from 01_CustomerScenario here. Keep it concrete.
// Emphasise: customer expects fine-tuning; our job is to prove that expectation wrong (or right) with evidence.

== Who this is for

This lab is built for *data scientists and ML engineers* comfortable with Python and JupyterLab. No prior RAG or fine-tuning experience is required — the notebooks walk through each stage end-to-end.

== What's provisioned for you

Your Red Hat Demo Platform catalog item has already built a complete environment: OpenShift cluster, Red Hat OpenShift AI, a GPU-attached JupyterLab workbench, and the lab notebooks pre-cloned into your home directory. No setup steps — xref:environment.adoc[see Your Environment] for the short tour.

== Time to complete

Instructor-led runtime: **~5 hours**. Self-paced runtime is longer; plan for a full day if you're working through every rung.

== Let's get started

xref:environment.adoc[Open your environment →]
```

Word count the prose sections (not the scaffolding) against the 400–600 target; trim or expand as needed.

- [ ] **Step 3.3: Verify AsciiDoc parses**

Run:
```bash
grep -c '^= ' content/modules/ROOT/pages/index.adoc
```
Expected: `1` (exactly one top-level title).

- [ ] **Step 3.4: Commit**

```bash
git add content/modules/ROOT/pages/index.adoc
git commit -m "Author index.adoc: welcome + customer scenario"
```

---

## Task 4 — Author `environment.adoc` (Environment tour)

**Files:**
- Create: `content/modules/ROOT/pages/environment.adoc`

Target length: **300–500 words**. Uses existing mirrored screenshots where useful; flags the three that don't yet exist as `TODO-PILOT` image attributes so they can be swapped in post-pilot without changing AsciiDoc structure.

- [ ] **Step 4.1: Write `content/modules/ROOT/pages/environment.adoc`**

```asciidoc
= Your Environment
:navtitle: Your Environment

Everything you need is already running. This page is a quick tour so you know where to click.

== What you have

* **OpenShift cluster console:** {openshift_console_url}
* **Red Hat OpenShift AI dashboard:** {rhoai_dashboard_url}
* **Pre-created Data Science Project:** `{data_science_project_name}`
* **Pre-configured workbench:** `{workbench_name}` — JupyterLab image with the lab's Python deps, GPU attached, disk sized for model artifacts
* **Notebooks pre-cloned** into your workbench home directory under `~/EscalationLab/`

== Opening JupyterLab

. Open the RHOAI dashboard: {rhoai_dashboard_url}
. Navigate to **Data Science Projects** → open **{data_science_project_name}**
. In the **Workbenches** tab, find **{workbench_name}** and click **Open**

// TODO-PILOT: replace with live screenshot once provisioned env is available
// image::TODO-workbench-open.png[Workbench row with Open button,link=self,window=blank,width=700]

JupyterLab opens in a new browser tab. You'll live here for the rest of the workshop.

== Finding the notebooks

In the JupyterLab file browser (left sidebar), open `~/EscalationLab/`. You'll see numbered directories:

* `02_Baseline/` — **start here** — establishes the stock-model baseline
* `03_Ingestion/` through `10_ComprehensiveEvaluation/` — one rung per directory
* The `00_Orientation/` and `01_CustomerScenario/` notebooks are informational; they duplicate the Showroom pages you've already read

// TODO-PILOT: replace with live screenshot of JupyterLab file browser showing EscalationLab/
// image::TODO-jupyter-filebrowser.png[JupyterLab file browser,link=self,window=blank,width=700]

== Verifying GPU access

Open a JupyterLab terminal (File → New → Terminal) and run:

[source,bash]
----
nvidia-smi
----

You should see a GPU listed (L40S or L4). If you see `command not found` or no GPU, see *Troubleshooting* below.

image::00_Orientation/terminal1.png[JupyterLab terminal,link=self,window=blank,width=700]

== Troubleshooting

[%collapsible]
.Workbench won't start
====
Check the workbench events in the RHOAI dashboard. Most often this is transient GPU-scheduling contention — wait 60 seconds and retry **Open**.
====

[%collapsible]
.`nvidia-smi` returns "command not found"
====
The workbench image was started without a GPU accelerator attached. Stop the workbench, edit it, set **Accelerator** to the expected GPU profile, and restart.
====

[%collapsible]
.Notebooks directory is missing or Git LFS files are empty
====
The clone-init container may have failed. Open a terminal and run:
`cd ~ && git clone https://github.com/rhai-workshops/EscalationLab.git && cd EscalationLab && git lfs pull`
====

[%collapsible]
.Kernel dies with out-of-memory during fine-tuning
====
Expected on L4 (24 GB). Either move to an L40S workbench or reduce batch size in the notebook's training config cell.
====

== Next step

xref:map.adoc[See the workshop map →] to pick a path, or jump straight into `~/EscalationLab/02_Baseline/` in JupyterLab.
```

- [ ] **Step 4.2: Verify image reference resolves**

Run:
```bash
test -f content/modules/ROOT/assets/images/00_Orientation/terminal1.png && echo "OK image"
```
Expected: `OK image`

- [ ] **Step 4.3: Commit**

```bash
git add content/modules/ROOT/pages/environment.adoc
git commit -m "Author environment.adoc: provisioned-env tour + troubleshooting"
```

---

## Task 5 — Author `map.adoc` (Workshop map + Mermaid ladder)

**Files:**
- Create: `content/modules/ROOT/pages/map.adoc`

Target length: **400–600 words plus one Mermaid block**. Uses the Mermaid extension already configured in `site.yml`.

- [ ] **Step 5.1: Write `content/modules/ROOT/pages/map.adoc`**

```asciidoc
= Workshop Map
:navtitle: Workshop Map

This lab has nine hands-on notebook sections. You don't have to do them all — pick a path based on your time budget.

== The escalation ladder

Each rung is an increase in effort and cost. We climb only when the evidence from the rung below says we must.

[mermaid]
----
flowchart TD
    B["02 · Baseline<br/>stock model, no context"]
    I["03 · Ingestion<br/>PDF → Markdown"]
    R["04 · RAG<br/>vector search + retrieval"]
    E1{"05 · Evaluation<br/>is RAG enough?"}
    S["06 · Scaling<br/>multi-document corpus"]
    ITS["07 · Inference-Time Scaling<br/>best-of-N sampling"]
    SDG["08 · Synthetic Data Gen"]
    FT["09 · Model Adaptation<br/>QLoRA fine-tuning"]
    E2["10 · Comprehensive Evaluation<br/>cross-architecture"]

    B --> I --> R --> E1
    E1 -- "no, escalate" --> S --> ITS --> E1
    E1 -- "still no" --> SDG --> FT --> E2
    E1 -- "yes, stop" --> STOP["Ship with RAG"]
----

== Section guide

[cols="1,3,1,2",options="header"]
|===
|Notebook | One-line outcome | Time | Depends on

|`02_Baseline`        | Stock-model answers on a domain it has never seen              | ~   | —
|`03_Ingestion`       | PDF rulebook converted to structured Markdown with Docling     | ~   | 02
|`04_RAG`             | ChromaDB vector store + retrieval-augmented answers            | ~   | 03
|`05_Evaluation`      | Baseline vs. RAG side-by-side, scored                          | ~   | 04
|`06_Scaling`         | Multi-document ingestion and index growth                      | ~   | 05
|`07_InferenceTimeScaling` | Best-of-N sampling; cost vs. quality curve                | ~   | 06
|`08_SyntheticDataGen` | Training examples produced by SDG Hub                         | ~   | 07
|`09_ModelAdaptation` | QLoRA fine-tune via Training Hub / Unsloth                     | ~   | 08
|`10_ComprehensiveEvaluation` | All approaches benchmarked on the held-out set          | ~   | 09
|===

NOTE: Time estimates will be filled in after the first pilot run. The `~` placeholders are intentional; don't guess.

== Recommended paths

=== 90-minute tour
Run `02_Baseline`, `04_RAG`, `05_Evaluation`, then jump to xref:synthesis.adoc[the Synthesis page]. You'll see the "is RAG enough" decision without climbing further.

=== Half-day
Everything from 02 through 07 plus xref:synthesis.adoc[Synthesis]. Adds scaling and inference-time tactics — the full "before you fine-tune" toolkit.

=== Full lab
Run 02 through 10 in order, then xref:synthesis.adoc[Synthesis]. This is what the instructor-led ~5-hour session covers.

== Returning for synthesis

After the last notebook you visit, come back here and open xref:synthesis.adoc[Synthesis] for the capstone discussion of what the evidence showed and how to apply it to real customer engagements.
```

- [ ] **Step 5.2: Verify Mermaid block syntax**

Run:
```bash
grep -c '^\[mermaid\]' content/modules/ROOT/pages/map.adoc
```
Expected: `1`

- [ ] **Step 5.3: Render locally to verify Mermaid extension picks it up**

```bash
podman run --rm -v $PWD:/antora:z -p 8080:8080 -d --name antora-viewer ghcr.io/juliaaano/antora-viewer
sleep 5
curl -sf http://localhost:8080/rag-escalation-lab/v1.5.6/map.html | grep -c 'class="mermaid"'
podman stop antora-viewer
```
Expected: `1` or higher (a `<div class="mermaid">` wrapping the graph). If the URL path differs, find it with `curl -sf http://localhost:8080/ | grep -oE 'href="[^"]*map[^"]*"' | head -1`.

- [ ] **Step 5.4: Commit**

```bash
git add content/modules/ROOT/pages/map.adoc
git commit -m "Author map.adoc: escalation-ladder Mermaid + section guide + paths"
```

---

## Task 6 — Author `synthesis.adoc` (Capstone)

**Files:**
- Create: `content/modules/ROOT/pages/synthesis.adoc`

Target length: **500–800 words**. Adapted from the `11_Synthesis` notebook markdown cells.

- [ ] **Step 6.1: Fetch source markdown from the 11_Synthesis notebook**

```bash
curl -sSLf https://api.github.com/repos/rhai-workshops/EscalationLab/contents/11_Synthesis \
  | python3 -c "import json,sys; print('\n'.join(f['path'] for f in json.load(sys.stdin) if f['name'].endswith('.ipynb')))"
```
Then fetch the notebook and extract markdown cells with the same pipeline as Task 3.1.

- [ ] **Step 6.2: Write `content/modules/ROOT/pages/synthesis.adoc`**

Structure (fill prose sections from the fetched source; preserve the source's conclusions and ordering — this is the *capstone*, so faithful adaptation matters more than creative rewrite):

```asciidoc
= Synthesis
:navtitle: Synthesis

You've climbed the ladder. Here's what the evidence says, and how to apply it outside this lab.

== What the evidence showed

// Summarise the measured results from 05_Evaluation and 10_ComprehensiveEvaluation:
// - Where did RAG close the gap to fine-tuning?
// - Where did inference-time scaling help, and where did it plateau?
// - Did QLoRA fine-tuning beat RAG by a margin worth the training cost?
// Use concrete framing from the source 11_Synthesis notebook.

== The escalation framework

// The durable takeaway: the decision rubric for customer engagements.
// - Start at the cheapest rung.
// - Measure before climbing.
// - Name the metric that would justify the next rung *before* you climb.

== When *not* to climb

Anti-patterns to watch for in real engagements:

* **Fine-tuning as first response.** The customer expects it; your evidence doesn't yet support it. Slow them down.
* **Skipping evaluation between rungs.** Without a measured baseline, you can't tell whether the next rung helped.
* **Over-indexing on a single benchmark.** If the eval set drifts from the customer's actual queries, rung gains are illusory.
* **Ignoring inference-time tactics.** Best-of-N or tool-use can often close gaps that look like "we need to fine-tune."

== Next steps

* **Granite documentation:** https://www.ibm.com/granite (model family, licensing, fine-tune guidance)
* **Red Hat OpenShift AI docs:** https://docs.redhat.com/en/documentation/red_hat_openshift_ai
* **Docling:** https://github.com/DS4SD/docling
* **SDG Hub / Training Hub:** see the `utils/` directory of `rhai-workshops/EscalationLab`
* **Follow-on labs:** search the RHDP catalog for related AI / RAG items
```

- [ ] **Step 6.3: Verify**

Run:
```bash
wc -w content/modules/ROOT/pages/synthesis.adoc
```
Expected: between 500 and 800 words in the final draft (scaffolding comments should be removed or replaced by real prose before commit).

- [ ] **Step 6.4: Commit**

```bash
git add content/modules/ROOT/pages/synthesis.adoc
git commit -m "Author synthesis.adoc: capstone adapted from 11_Synthesis notebook"
```

---

## Task 7 — Replace the README

**Files:**
- Replace: `README.adoc`

- [ ] **Step 7.1: Overwrite `README.adoc` with lab-specific content**

```asciidoc
= RAG Escalation Lab

Showroom lab item wrapping the https://github.com/rhai-workshops/EscalationLab[rhai-workshops/EscalationLab] Jupyter notebook workshop, which teaches the principle that *escalation of effort must be justified by evidence* across Baseline → RAG → Inference-Time Scaling → Synthetic Data Generation → Fine-Tuning → Evaluation.

This repo is the Showroom content artifact only. The catalog item itself (OpenShift + RHOAI + workbench provisioning) is configured in a separate AgnosticV repo.

== Local preview

[source,sh]
----
podman run --rm --name antora -v $PWD:/antora:z -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer
----

Open http://localhost:8080.

== Repo layout

* `content/modules/ROOT/pages/` — the four lab pages (welcome, environment, map, synthesis)
* `content/modules/ROOT/assets/images/` — screenshots mirrored from the source notebook repo
* `docs/superpowers/` — design spec and implementation plan
* `site.yml`, `content/antora.yml`, `ui-config.yml` — Antora and Showroom configuration

== Related

* Source notebooks: https://github.com/rhai-workshops/EscalationLab
* Design spec: `docs/superpowers/specs/2026-04-15-rag-escalation-showroom-design.md`
```

- [ ] **Step 7.2: Commit**

```bash
git add README.adoc
git commit -m "Replace template README with lab-specific README"
```

---

## Task 8 — End-to-end local render + link check

**Files:** none modified; this is a verification task.

- [ ] **Step 8.1: Build and serve with antora-viewer**

```bash
podman run --rm -v $PWD:/antora:z -p 8080:8080 -d --name antora-viewer ghcr.io/juliaaano/antora-viewer
sleep 8
```

- [ ] **Step 8.2: Confirm all 4 pages render**

```bash
BASE=$(curl -sf http://localhost:8080/ | grep -oE 'href="rag-escalation-lab/[^"]*/index\.html"' | head -1 | sed 's|href="\(.*\)/index.html"|\1|')
echo "Base: $BASE"
for p in index environment map synthesis; do
  code=$(curl -sf -o /dev/null -w '%{http_code}' "http://localhost:8080/$BASE/$p.html")
  echo "$p: HTTP $code"
done
```
Expected: all 4 return `HTTP 200`.

- [ ] **Step 8.3: Verify no broken xrefs**

Grep the rendered HTML for `<a class="xref unresolved"`:
```bash
for p in index environment map synthesis; do
  curl -sf "http://localhost:8080/$BASE/$p.html" | grep -c 'xref unresolved' || true
done
```
Expected: each prints `0`.

- [ ] **Step 8.4: Verify Mermaid renders on map page**

```bash
curl -sf "http://localhost:8080/$BASE/map.html" | grep -c 'class="mermaid"'
```
Expected: `1` or higher.

- [ ] **Step 8.5: Verify referenced images are embedded**

```bash
curl -sf "http://localhost:8080/$BASE/environment.html" | grep -oE '_images/[^"]+terminal1\.png' | head -1
```
Expected: a path containing `terminal1.png`.

- [ ] **Step 8.6: Stop the viewer**

```bash
podman stop antora-viewer
```

- [ ] **Step 8.7: No new files to commit for this task** — if any fixes were needed during verification, they should have been committed with the file they touched. If the verification surfaced a genuinely cross-cutting fix, commit it with a descriptive message.

---

## Task 9 — Content review with `showroom:verify-content` skill

**Files:** possibly multiple page edits based on feedback.

- [ ] **Step 9.1: Invoke the skill**

Run via the Skill tool: `showroom:verify-content`

Point it at `content/modules/ROOT/pages/{index,environment,map,synthesis}.adoc`.

- [ ] **Step 9.2: Fix any Red Hat style / AsciiDoc conformance issues it surfaces**

Treat actionable feedback seriously; ignore feedback that conflicts with explicit spec decisions (e.g., it might suggest adding setup steps — the spec says environment is fully provisioned, so decline).

- [ ] **Step 9.3: Commit fixes**

If any content changed:
```bash
git add content/modules/ROOT/pages/
git commit -m "Address verify-content feedback: <summary>"
```

---

## Task 10 — Push

**Files:** none.

- [ ] **Step 10.1: Confirm clean working tree**

```bash
git status
```
Expected: `nothing to commit, working tree clean`.

- [ ] **Step 10.2: Review the commit sequence**

```bash
git log --oneline main..origin/main 2>/dev/null || git log --oneline -15
```
Confirm the commits from Tasks 1–9 are present and messages are sensible.

- [ ] **Step 10.3: Push**

```bash
git push origin main
```
Expected: clean push (SSH remote, no credential prompt).

---

## Post-completion (not a task)

Open items that intentionally survive this plan — they require things we don't have:

1. **Three live-env screenshots** for `environment.adoc` — the `TODO-PILOT` markers. Capture during the first provisioned-environment pilot; swap in and commit.
2. **Per-section time estimates** in `map.adoc` — the `~` placeholders. Measure during pilot; back-fill.
3. **`map.adoc` time-to-complete calibration** for the three recommended paths — refine after a self-paced runthrough confirms the numbers.
4. **AgnosticV catalog-item config** (separate repo) — this plan assumes the deployer will inject `rhoai_dashboard_url`, `data_science_project_name`, `workbench_name`, `openshift_console_url` at runtime; those attribute names are now locked in and must match.

## Self-review notes

- **Spec coverage:** Welcome+customer scenario → Task 3; environment tour → Task 4; map+Mermaid → Task 5; synthesis → Task 6; screenshots — already pulled (prior commit); nav — Task 1; configs — Task 2; README — Task 7; validation (render + link check + content review) → Tasks 8–9; out-of-scope items → listed in *Post-completion*.
- **Placeholders:** `TODO-PILOT` image comments and `~` time cells are *intentional* placeholders for pilot-time data; the plan explicitly surfaces them rather than hiding them.
- **Type consistency:** Antora attributes `{rhoai_dashboard_url}`, `{data_science_project_name}`, `{workbench_name}`, `{openshift_console_url}` declared once in Task 2's `content/antora.yml` edit; referenced identically in Tasks 3–4. Page filenames (`index`, `environment`, `map`, `synthesis`) consistent across nav (Task 1), xrefs (Tasks 3, 5, 6), and verification (Task 8).
