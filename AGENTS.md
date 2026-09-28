# AGENTS.md — layer-agentteams

Standalone repo for the `agentteams` stack. Unlike a single-candy layer, this
repo declares a project scope: the `agentteams` top-composition candy (minio,
matrix, element, higress, controller) plus the image boxes under `box/`, the
embedded `skill:` entity projected as `/charly-agentteams:agentteams`, and two
R10 check beds. The `discover:` manifest in the root `charly.yml` pulls in
`candy/` and `box/` recursively.

Canonical files:

- `charly.yml` — the `agentteams:` candy, the `charly-toolchain:` project-scope
  candy, the `agentteams-skill:` skill entity, the `agentteams` top box, and the
  `check-agentteams-pod` / `check-agentteams-snapshot` beds.
- `box/agentteams/charly.yml`, `box/agentteams-manager/charly.yml`,
  `box/agentteams-worker/charly.yml`, `box/cachyos-base/charly.yml` — the images.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-agentteams:agentteams` — the owning skill: the Manager–Worker model,
  the five-service composition, volumes, ports, and both deploy substrates. Load
  before editing, building, deploying, or troubleshooting the stack.
- `/charly-agentteams:agentteams-cli` — the compiled-in `charly agentteams` REST
  CLI the snapshot bed drives. Load when touching the snapshot/hydrate path.
- `/charly-check:check` — the bed / ADE authoring surface. Load before editing
  the two `check-agentteams-*` beds.
- `/charly-image:layer` and `/charly-image:image` — the candy and box authoring
  references. Load before editing any entity field or plan step.

## Build / validate / test

- `charly box validate` at the repo root — the same structural gate CI runs.
  Keep the `version:` schema stamp within the installed charly's supported range.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has no
  per-repo candy gate.
- R10 beds (disposable; require a host with the rootless podman socket):
  `charly check run check-agentteams-pod` and
  `charly check run check-agentteams-snapshot`. Per-box ADE:
  `charly check box agentteams-manager` (and `-worker` / `agentteams` / `cachyos-base`).

## Modify this repo

- Edit the `agentteams:` candy entity AND the `agentteams-skill:` skill entity
  together. The skill is the projected usage source, so a composition or port
  change not mirrored in the skill leaves the corpus stale.
- A box that composes a bare candy (e.g. `charly`, the snapshot bed's overlay)
  resolves only against the **consuming project's closure**; declare such a
  dependency at project scope (as `charly-toolchain:` does) rather than
  composing it into a production image.
- Keep the CI `version:` schema stamp and the `plugin-deploy-pod` pin in step;
  the provider demands a matching schema and refuses an older project.
- Do not duplicate the VM-substrate bed — its single home is `opencharly/charly`.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
