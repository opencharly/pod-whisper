# AGENTS.md — pod-whisper

The `pod-whisper` repo owns the **`whisper` image** (an OpenAI-compatible speech-to-text service on
the CachyOS base) and the **two check beds that prove it**. The org-wide rulebook in the umbrella
`AGENTS.md` applies in full and is not repeated here; this file adds only what is specific to this repo.

## What this repo is

A **pod-class** repo: it composes one service image and proves it.

- `charly.yml` — the project manifest: `repo:`, the `defaults:` (tag + registry), the `import:` of the
  CachyOS base namespace, and the two bed entities `check-whisper-pod` (keyless posture) and
  `check-whisper-auth` (keyed posture).
- `box/whisper/charly.yml` — the `whisper` image: `base: cachyos.cachyos`, the `layer-whisper` candy
  pinned by `@github` ref, the host-bind model store, and the box description.
- `README.md` — user overview only; never agent guidance.

**The candy stays in `opencharly/layer-whisper`.** This repo composes and proves it; it does not own
the install logic. A change to the service belongs in that repo, a pin bump here.

## Load these skills first (R0)

- `/charly-tools:whisper` — the owning skill for the whisper service.
- `/charly-distros:cachyos` — the base image this box composes.
- `/charly-check:check` — the check framework the two beds use.
- `/charly-image:image` — the box authoring surface.
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural gate (project, box, beds, imports).
- `charly box build whisper` — the image build.
- The authoritative R10 witnesses are the repo's own beds:
  `charly check run check-whisper-pod` and `charly check run check-whisper-auth`, both
  `disposable: true`. The keyed arm exists because a baked candy check cannot assert `401` on a
  keyless deployment.
- The merge gate is the **org-wide** `charly/pr-validator`; this repo has no per-repo gate.

## Modify this repo

- A base bump changes the `import:` ref in `charly.yml`; a candy bump changes the `@github` ref in
  `box/whisper/charly.yml`. Keep the two in step with their producers' released tags.
- The `base:` is the namespaced `cachyos.cachyos` — the `import:` must stay at the PROJECT root, not
  in the discovered box file (a namespace mounted by a discovered document does not resolve the box's
  own `base:`).
- The model store is a host bind (`~/.local/share/charly/models/whisper`); a bed that needs a warm
  store keeps it, and a cache that is re-downloaded on every re-create is a defect.

## Landing

- PR-only. Every change lands through a pull request; the org-required `charly/pr-validator` validates
  the diff and body and arms native auto-merge on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR body IS the
  changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in `opencharly/opencharly`.
