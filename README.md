# agentteams

The AgentTeams multi-agent stack as an OpenCharly box.

AgentTeams is a multi-agent runtime built on a Manager–Workers model: a Manager
plans and delegates, Workers execute, and they converse in shared Rooms (Matrix
chat spaces). A controller — a mini Kubernetes API server (kube-apiserver backed
by a kine SQLite store) plus a reconciler — manages four resource kinds (Manager,
Worker, Team, Human), spawns Manager/Worker containers through the container
runtime socket, and serves a REST API on `:8090`.

This repo packages that whole stack as a **CachyOS-based box**. Every service runs
as the image user (uid 1000) against its own home-relative volume — the rootless
charly architecture, no root anywhere.

## What it provides

| Entity | Kind | What it is |
|---|---|---|
| `agentteams` | candy | the top composition: minio + matrix + element + higress + controller |
| `charly-toolchain` | candy | the on-PATH `charly` CLI, declared at project scope so the snapshot bed's bare `charly` dependency resolves |
| `agentteams` | box | the deployable full-stack image (`base: cachyos-base`) |
| `agentteams-manager` | box | the controller's Manager runtime image (openclaw gateway + `agt` + `mc`) |
| `agentteams-worker` | box | the controller's Worker runtime image (openclaw gateway + `agt` + `mc` + the shared protocol libs) |
| `cachyos-base` | box | the minimal, digest-pinned CachyOS stage base the images compose |

Services: MinIO S3 (`9000`/`9001`), Tuwunel Matrix homeserver (`6167`), Element
Web (`8088`), Higress gateway (`8080`/`8001`), and the controller REST API
(`8090`). Host port mappings auto-allocate at deploy and resolve as
`${HOST_PORT:<port>}`.

## How to use it

Compose the published box, or reference the composition candy from your own box:

```yaml
my-agentteams:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/layer-agentteams:<tag>'
```

Then build and deploy it with the charly CLI:

```bash
charly box build agentteams
charly start agentteams
```

The disposable R10 beds `check-agentteams-pod` and `check-agentteams-snapshot`
prove the stack end to end (`charly check run check-agentteams-pod`); the
VM-substrate bed lives in `opencharly/charly`.

## Layout

- `charly.yml` — the project scope: the `agentteams` composition candy, the
  `charly-toolchain` closure candy, the `agentteams-skill:` skill entity, and the
  `check-agentteams-pod` / `check-agentteams-snapshot` beds.
- `box/agentteams/charly.yml`, `box/agentteams-manager/charly.yml`,
  `box/agentteams-worker/charly.yml`, `box/cachyos-base/charly.yml` — the images.
- `.github/workflows/deploy.yml` — the manifest gate (`charly box validate`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-agentteams:agentteams`
- CLI: `/charly-agentteams:agentteams-cli` (`charly agentteams`)
- Check beds: `/charly-check:check`
- VM substrate: `/charly-vm:vm`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
