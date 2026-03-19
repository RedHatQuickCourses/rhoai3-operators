# Red Hat OpenShift AI 3.x — Operator ecosystem & control plane (Phase 0)

**From shadow clusters to a governed AI factory floor**

> **The problem:** Ad-hoc OpenShift AI installs, mystery CSV failures, and `DataScienceCluster` toggles left at defaults—GPUs idle while teams debate who owns the next outage.  
> **The outcome:** A **repeatable operator baseline**—OLM subscriptions, NFD + GPU truth, TLS for serving, gateway/policy prerequisites, and an intentional `DataScienceCluster`—so downstream modules (storage, registry, catalog, serving, MaaS) run on **known infrastructure**.

This repository is a **course-in-a-box** (Antora) for platform engineers building **Red Hat OpenShift AI (RHOAI) 3.3** on OpenShift 4: architecture, GitOps-style manifests, labs, and troubleshooting aligned with the *industrialized AI token platform* narrative.

---

## What you will do

* Map **which operators** unlock which journey phases—without installing the entire catalog.
* Understand **control plane vs execution plane** (OLM, meta-operator, DSC, gateways).
* Install **Web Terminal**, **OpenShift AI**, **NFD**, **cert-manager**, **Connectivity Link**, **Leader Worker Set**, and activate **DataScienceCluster** with selective components.
* Complete a **readiness checklist** before handoff to storage, registry, and catalog courses.

---

## Prerequisites

* OpenShift cluster with **cluster-admin** (or equivalent) for operator installation
* Documentation bundle **Red Hat OpenShift AI Self-Managed 3.3** (PDFs)—local folder in this workspace is often named `rhoai33docs`
* Optional: GPU nodes and vendor GPU operator (follow *Working with accelerators* in product docs)

---

## Build the site locally

```bash
cd rhoai3-operators
npm ci
npm run build
npm run serve   # optional: preview build/site
```

Open the URL printed by `http-server` (default `http://127.0.0.1:8080`).

---

## Repository layout

| Path | Purpose |
|------|---------|
| `antora.yml` | Component name, title, version, navigation |
| `antora-playbook.yml` | Local Antora playbook |
| `modules/ROOT/` | Home page (includes chapter 1 intro) |
| `modules/chapter1/pages/` | Course pages (intro → labs → summary) |
| `supplemental-ui/` | Site chrome (e.g., issue link) |
| `ui-bundle/ui-bundle.zip` | Antora UI bundle |

---

## Publishing

GitHub Actions workflow **Publish to GitHub Pages** runs `npm ci` and `npm run build` on pushes to `main`. Enable Pages from the `gh-pages` branch in repository settings if not already configured.

---

## Related journey modules

This course is **Phase 0** for the broader RHOAI 3 journey: `rhoai3-storage`, `rhoai3-registry`, `rhoai3-catalog`, `rhoai3-hwprofiles`, `rhoai3-deploy`, `rhoai3-llmd`, `rhoai3-maas`, and validation content.

---

## Contributing

Use **Report Issues** in the rendered site header, or open an issue in this repository.

**See also**

* [Development using a Dev Space](./DEVSPACE.md)
* [Editing content](./USAGEGUIDE.adoc)
* [Training metadata](./README-TRAINING.md)
