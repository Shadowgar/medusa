# CMake Bootstrap (Phase 1)

## What exists now

`medusa/CMakeLists.txt` introduces an initial modern build entry point for server-critical shared libraries:

- `medusa` (always)
- `network` (default ON)
- `gcq` (default OFF, experimental due to legacy MFC assumptions)

## Current status (2026-03-10)

- Configure: PASS (Visual Studio 2022 generator)
- Build targets:
  - `medusa`: PASS
  - `network`: PASS
  - `gcq`: not validated in this pass

Validated command:

```powershell
& "C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\cmake.exe" `
  --build d:\DarkSpace\medusa\out\cmake-bootstrap --config Release --target medusa network
```

## Why this scope

- `medusa` and `network` are prerequisites for backend/server modernization.
- `gcq` and `world` involve additional legacy coupling (notably MFC and broader dependency graph) and are intentionally staged after baseline CMake adoption.

## Configure examples

From `d:\DarkSpace\medusa`:

```powershell
cmake -S . -B out/cmake-vs2022 -G "Visual Studio 17 2022"
cmake --build out/cmake-vs2022 --config Release --target medusa network
```

Optional experimental GCQ target:

```powershell
cmake -S . -B out/cmake-vs2022 -G "Visual Studio 17 2022" -DMEDUSA_BUILD_GCQ=ON
cmake --build out/cmake-vs2022 --config Release --target gcq
```

## Known limitations

- Source enumeration currently uses controlled `GLOB` patterns for fast bootstrap.
- `gcq` may require additional MFC-specific setup and is not considered baseline-pass yet.
- `world` is not yet mapped into CMake in this phase.
- `network` intentionally excludes legacy partial units that fail against current headers:
  - `Network/MirrorServerDepreciated.cpp`
  - `Network/MirrorServerVersionControl.cpp`
  These files appear to be deprecated/partial implementations and are not required for baseline server runtime bootstrap.
