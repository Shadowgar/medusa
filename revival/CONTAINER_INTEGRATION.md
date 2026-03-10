# Container Integration Notes (medusa)

## Role in stack

`medusa` provides shared runtime/libs and protocol behavior used by containerized server executables from `gamecq` and `darkspace`.

## Container impact surface

- `GCQ/*Client.cpp` default endpoint selection behavior
- `World/WorldServer*` login/auth and meta-session handling
- Shared networking/runtime support linked into server binaries

## Drift policy

Any server-impacting protocol/default change in `medusa` must:

- be logged in `medusa/revival/DRIFT_LOG.md`
- include compatibility note for `gamecq` and `darkspace`
- be mirrored into `gamecq/revival/DRIFT_LOG.md` when runtime contract changes

## Runtime artifact contract (current)

`gamecq/revival/scripts/stage-medusa-runtime.ps1` stages these build outputs for server runtime:

- `Medusa.dll`
- `Network.dll`

Source baseline currently used:

- `medusa/out/cmake-bootstrap/Release`

Linux container runtime path also stages these legacy makefile outputs via `gamecq/revival/scripts/stage-server-binaries.ps1`:

- `libMedusa.so`
- `libNetwork.so`
- `libGCQ.so`
- `libRender3D.so`
- `libWorld.so`

Source baseline for Linux server artifacts:

- `medusa/out/server-bootstrap/Release`
