# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

KubeSkills **GROW** (GitHub Repository of Work): a template repo that students fork to build a public Kubernetes learning portfolio. There is no application code, test suite, or build beyond MkDocs — content is Markdown lab guides, Kubernetes manifests, and weekly reflection templates. Upstream is `kubeskills/grow` (older docs still say `kubeskills/student-notebook`).

## Commands

```bash
# Docs site (same toolchain as .github/workflows/deploy.yaml)
pip install mkdocs mkdocs-material
mkdocs serve                 # preview at http://localhost:8000
mkdocs build -d site --strict  # use --strict to surface broken nav/links; CI builds without it

# Lab environment
kind create cluster --name grow-lab
kind create cluster --config 04-services-ingress/kind-config.yaml   # Lab 04: maps host ports 80/443 for ingress
kubectl apply -f <NN-topic>/<manifest>.yaml
```

Pushing to `main` triggers `.github/workflows/deploy.yaml`, which builds MkDocs and deploys via GitHub Actions Pages (not the "branch: main, folder: /docs" method the README describes).

## Structure and conventions

- **Lab modules** are numbered folders `NN-topic/` (`00`–`06`), each with a `lab-guide.md` and its manifests living alongside it. New labs get the next number. `99-reflections/` holds `weekN.md` templates (one per lab week) with blank bullet sections students fill in.
- **Lab guide shape** (keep consistent when adding/editing labs): emoji-prefixed H1 title → 🎯 Objectives → 🛠 Prerequisites → `✅ Part N:` steps with `kubectl` commands → 🧠 Reflect in Your Notebook (embedded reflection template) → 📝 Commit → 📣 Share (`#KubeSkillsGROW`) → 🔁/⏩ Next Step linking to the following lab.
- **Some manifests are intentionally incomplete — they are exercises.** E.g., `01-kubernetes-fundamentals/nginx-pod.yaml` lacks a `securityContext` because Lab 01 Step 2 has the student add it. Before "fixing" a manifest, read its `lab-guide.md` so you don't pre-solve an exercise; if a fix is warranted, update the guide to match.
- Known gaps in existing manifests (not yet tied to exercises): unpinned/floating image tags (`busybox`, `nginx`, `:stable`), no `resources` blocks, no per-workload NetworkPolicy, and a plaintext `POSTGRES_PASSWORD` in `05-stateful-deploy/postgres-deployment.yaml`.

## MkDocs gotchas

`mkdocs.yml` has no `docs_dir`, so it defaults to `docs/` — but its `nav` points at repo-root paths (`00-getting-started/...`, `99-reflections/...`) that don't exist under `docs/`, plus files that don't exist anywhere (`02-gitops/fluxcd-installation.md`, `03-security/podsecuritypolicy-example.yaml`). A non-strict build only warns, so the deployed site currently renders little beyond `docs/index.md`. When adding a lab, add it to `nav` and make sure the path resolves relative to `docs/`.

## Commit / PR conventions (from CONTRIBUTING.md)

Commit messages and PR titles use `<type>: <summary>` with types `lab`, `reflection`, `fix`, `question`, `docs` (e.g. `lab: add Week 6 StatefulSet lab`). Students ask questions by opening a PR titled `question: <topic>` or via the "Ask a Question" issue template.
