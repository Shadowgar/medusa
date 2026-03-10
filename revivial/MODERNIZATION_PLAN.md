# Medusa Modernization Plan

This plan defines how to modernize the `medusa` engine while keeping the live server revival moving.

## Principles

- Keep gameplay/protocol behavior stable while modernizing internals.
- Separate "build/runtime modernization" from "feature changes".
- Ship in small reversible slices with regression tests.
- Prefer compatibility layers over hard rewrites in early phases.

## Target Outcomes

- Reproducible cross-platform builds (CI + containerized toolchain).
- Engine code on modern C++ standard with stricter warnings.
- Replaced or isolated legacy platform locks (DirectX9-era assumptions, legacy project metadata).
- Automated coverage for protocol, serialization, and world-server critical paths.

## Progress snapshot (2026-03-10)

- Phase 1 started:
  - Added initial `CMakeLists.txt` at repo root for `medusa` + `network`.
  - Added bootstrap notes in `CMAKE_BOOTSTRAP.md`.
- Phase 1 compile unblocks completed:
  - x64 compatibility fixes applied in debug exception/error handling paths.
  - third-party source set constrained to library-only re2/zlib units.
  - deprecated/partial mirror server units excluded from baseline `network` target.
  - `medusa` + `network` now build successfully in Release via VS2022 CMake generator.
- Not started yet:
  - CI matrix and artifact publishing.
  - `world` and stable `gcq` CMake targets.

## Phase 0: Baseline Freeze

- Tag a known-good baseline for server revival.
- Capture current protocol contracts and serialized formats.
- Freeze public network message IDs and DB schema contracts for the first modernization cycle.

Exit criteria:

- Baseline tag created.
- Protocol/serialization contract docs checked in.

## Phase 1: Build System Modernization

- Introduce CMake alongside existing project files.
- Preserve current output layout so existing startup scripts continue to work.
- Add CI matrix for Linux and Windows build verification.
- Normalize compiler warnings and fail on new warnings for touched files.

Exit criteria:

- Core server-linked medusa libs build from CMake on Linux and Windows.
- CI produces artifacts consumed by server stack.

## Phase 2: Dependency and Platform Decoupling

- Inventory hard platform locks in engine subsystems:
  - rendering interfaces
  - audio
  - input/system wrappers
- Split interfaces from legacy implementations.
- Keep legacy implementations available behind compile switches until parity is validated.

Exit criteria:

- Server-critical medusa modules compile without client-only platform dependencies.
- Platform abstraction boundaries documented and enforced.

## Phase 3: Language/Runtime Upgrade

- Move to modern C++ (target C++20 unless blocked).
- Replace unsafe or custom legacy patterns where high risk:
  - raw ownership-heavy allocations in critical paths
  - ad-hoc threading primitives where standard primitives are safer
- Add sanitizers in CI for Linux debug builds.

Exit criteria:

- C++ standard uplift complete for server-critical targets.
- Sanitizer-clean on selected test suites.

## Phase 4: Engine Quality Hardening

- Expand automated tests for:
  - protocol encoding/decoding
  - auth/session flow
  - world server login and player state transitions
- Add deterministic integration test harness for server startup path.

Exit criteria:

- Repeatable integration test pass for core server loop.
- Regression suite gates merges.

## Immediate Workstream for Revival

While modernization proceeds, keep these parallel tracks:

- Track A: "Revival Runtime"
  - keep server stack running in containers.
- Track B: "Engine Modernization"
  - migrate build system and engine internals behind compatibility layers.

Do not block Track A on full engine rewrite.

## Risks

- Protocol drift during refactors.
- Hidden coupling between client-era rendering code and shared engine modules.
- Build migration churn across three repos.

## Mitigations

- Contract tests before refactors.
- Feature flags and compile-time compatibility switches.
- Per-repo drift logs already established in `revivial/`.
