# Medusa Revival (Shared Runtime/Protocol)

This folder tracks server-relevant revival work inside `medusa`.

## Scope in this repo

- Shared networking, GCQ protocol, world server/client protocol code
- Common runtime/libs consumed by server executables from other repos
- Security-sensitive defaults in shared client/server connectivity code

## Drift control rules

- Any server-impacting change in `medusa` must be logged in `DRIFT_LOG.md`.
- If the change alters runtime contracts (ports, protocol behavior, auth flags), mirror the note in:
  - `gamecq/revivial/DRIFT_LOG.md`
  - `darkspace/revivial/DRIFT_LOG.md`

## Current objective

Keep shared protocol/runtime stable while server components move into containerized deployment.

Current baseline status: `medusa` and `network` CMake bootstrap targets pass in Release (VS2022 generator). See `CMAKE_BOOTSTRAP.md`.

## Modernization track

- See `MODERNIZATION_PLAN.md` for engine upgrade phases and constraints.
- CMake bootstrap status and commands are tracked in `CMAKE_BOOTSTRAP.md`.

Primary orchestration contract currently lives in:

- `gamecq/revivial/docker-compose.server.yml`
