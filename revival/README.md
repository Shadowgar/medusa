# Medusa Revival (Shared Runtime/Protocol)

This folder tracks shared runtime, renderer, and tooling revival work inside `medusa`.

## Scope in this repo

- Shared networking, GCQ protocol, world server/client protocol code
- Common runtime/libs consumed by server executables from other repos
- Security-sensitive defaults in shared client/server connectivity code

## Drift control rules

- Any server-impacting change in `medusa` must be logged in `DRIFT_LOG.md`.
- If the change alters runtime contracts (ports, protocol behavior, auth flags), mirror the note in:
  - `gamecq/revival/DRIFT_LOG.md`
  - `darkspace/revival/DRIFT_LOG.md`

## Current objective

Keep shared protocol/runtime stable while server components move into containerized deployment.

Current baseline status: `medusa` and `network` CMake bootstrap targets pass in Release (VS2022 generator). See `CMAKE_BOOTSTRAP.md`.

## Planning docs

- `GRAND_PLAN.md`: cross-repo strategy that ties `medusa`, `darkspace`, and `gamecq` together
- `REVIVAL_PLAN.md`: repo-specific audit and execution plan for engine, runtime, renderer, and tooling
- `MODERNIZATION_PLAN.md`: earlier tactical engine modernization notes retained for continuity
- `CMAKE_BOOTSTRAP.md`: current bootstrap status and commands

Primary orchestration contract currently lives in:

- `gamecq/revival/docker-compose.server.yml`
