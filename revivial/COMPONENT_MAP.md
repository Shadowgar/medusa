# Component Map (medusa)

## Server-impacting shared modules

- `GCQ/`
  - Meta/Master client connectivity defaults and shared protocol behavior.
- `World/`
  - World server login/auth and server-side gameplay protocol.
- `Network/`
  - Shared transport/network abstractions used by server binaries.
- `SelfUpdate/`
  - Mirror/update endpoint defaults used by server-side utilities.

## Build/link role

`medusa` provides shared libraries referenced by server projects in `gamecq` and `darkspace`.

## Out-of-scope for current phase

- Client UX/presentation work
- Non-server third-party demo/test projects
