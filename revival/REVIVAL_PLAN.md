# Medusa Revival Plan

This plan expands `MODERNIZATION_PLAN.md` with a repo-wide audit and execution order aligned to `GRAND_PLAN.md`.

## Repo Role

Medusa is the shared engine and tooling base for both `darkspace` and `gamecq`. If this repo stays platform-locked or tool-fragile, the other two repos cannot modernize cleanly.

## Audit Summary

- Build fragmentation is severe: the repo still carries large numbers of legacy VS project files, older makefiles, and only partial CMake coverage.
- Platform lock-in is still centered on Direct3D 9, Win32 windowing and input, DirectSound, and MFC-era tooling.
- `DisplayGL/` is not a usable alternative path; it only exists as project scaffolding.
- Threading and runtime primitives still rely on dangerous APIs such as `CreateThread`, `SuspendThread`, and `TerminateThread`, plus custom synchronization wrappers.
- Content authoring still depends on `Resourcer`, Port DLLs, a 3ds Max exporter, and an FBX SDK 2013 importer.
- Tests exist but are stranded: `UnitTest/` has useful low-level coverage, but it is not wired into CMake or CI.
- Repo boundaries are blurry: server-critical code lives beside client-only modules, increasing accidental coupling.

## Desired End State

- `medusa-core` and `medusa-server` build headlessly on Windows and Linux
- `medusa-client` exposes a clean renderer, input, and audio boundary
- a new RHI exists with D3D11 parity first, while D3D9 is only a temporary fallback
- legacy Port and Resourcer workflows are bridged by CLI tooling and validated asset builds
- unit tests and protocol smoke tests run automatically in CI

## Execution Plan

### Phase 1: Finish The Canonical Build

- Expand CMake beyond `medusa` and `network` to cover `world`, server-safe `gcq`, and the existing unit test target.
- Preserve existing output layout so `darkspace` and `gamecq` revival scripts keep working.
- Add warning profiles, static analysis, and CI artifact publishing.

### Phase 2: Split The Engine By Responsibility

- Formalize module boundaries: `core`, `network`, `world`, `client-render`, `client-platform`, and `tools`.
- Remove renderer, audio, and UI dependencies from server-linked targets.
- Document every cross-module dependency that `darkspace` and `gamecq` rely on.

### Phase 3: Replace High-Risk Runtime Internals

- Replace dangerous thread lifecycle APIs with `std::thread` or `std::jthread`, condition variables, and cooperative shutdown.
- Audit custom synchronization, manual string buffers, and unsafe file and path handling in touched code.
- Add regression tests around serialization, networking, and world tick behavior before deep refactors.

### Phase 4: Renderer Modernization

- Define an RHI that isolates device creation, command submission, resource lifetime, shaders, and frame pacing from gameplay code.
- Implement a D3D11 backend first for faster Windows parity.
- Keep the D3D9 path alive only as a short-lived fallback during migration.
- Move profiling and GPU diagnostics into the new renderer path.

### Phase 5: Toolchain Replacement

- Build a CLI asset cooker that can read legacy `.prt` data and emit versioned runtime bundles.
- Standardize authoring inputs around open formats where practical.
- Replace MFC-only tool dependencies incrementally, starting with asset conversion and validation.

### Phase 6: Automated Quality Gates

- Wire `UnitTest/` into CMake and CI.
- Add world and protocol smoke tests used by the other repos.
- Add sanitizer or equivalent debug validation for Linux and headless builds.

## Immediate Backlog

- extend CMake to `world` and `UnitTest`
- produce a dependency map of server-safe versus client-only modules
- remove `TerminateThread` and `SuspendThread` usage from the common runtime
- write the first RHI design note and D3D11 parity checklist
- define the asset manifest format that `darkspace` will consume

## Dependencies

- Needs `darkspace` to define gameplay and content schema requirements before the new asset cooker is finalized.
- Needs `gamecq` protocol and auth contracts frozen before shared networking changes land.
- Must keep current runtime artifacts compatible until the other repos switch over.

## Success Signal

Medusa is successful when `darkspace` and `gamecq` can consume it through automated builds, servers can run headlessly without client baggage, and the client has a credible path off Direct3D 9 without rewriting gameplay.
