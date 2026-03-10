# Medusa Modernization Plan

This is now the critical path for the whole revival effort.

`medusa` is not just an engine repo. It is the unstable substrate under every server binary. The current plan is therefore a line-by-line runtime modernization pass with compile gates, not a loose build-and-debug loop.

## Current Reality

The failures already proved in code point to foundational runtime defects:

- 32-bit aliases used where the code assumes exact widths
- stack sizing that is incompatible with large formatting buffers
- `time_t` and libc misuse on 64-bit Linux
- unsafe thread and pointer ID handling
- legacy string and logging paths that are not safe on modern platforms

Until those are repaired, higher-level service and gameplay debugging will keep wasting time.

## New Objective

Turn `medusa` into a 64-bit-correct, server-safe runtime before doing more deep service bring-up.

That means:

- static code sweep first
- compile verification second
- fast smoke tests third
- no long runtime loops by default

## Rewrite Order

### Pass 1: Runtime Substrate

Review and modernize these areas line by line:

- `Standard/Types*`
- `Standard/Thread*`
- `Standard/Time*`
- `Standard/String*`
- `Debug/Log*`
- `Standard/Event*`
- `Standard/Process*`
- `Standard/Reference*`
- `Standard/Referenced*`

Focus:

- exact-width integer correctness
- pointer-width correctness
- stack safety
- libc API correctness
- thread lifecycle correctness

### Pass 2: Reflection, Serialization, and Utility Layers

- `Reflection/*`
- serialization helpers
- hashing and format-dependent helpers

Focus:

- binary layout assumptions
- duplicate type aliases
- unsafe conversions
- protocol-adjacent data handling

### Pass 3: Network and Protocol

- `Network/*`
- `GCQ/*`
- server-critical portions of `World/*`

Focus:

- resolver behavior
- socket setup
- packet serialization
- thread usage and time usage in network paths

### Pass 4: Build and Test Harness

- finish modern build coverage for server-critical targets
- add fast validation targets
- create smoke tests that fail quickly

## Engineering Rules For This Repo

- every touched file gets a 64-bit safety pass
- no new code should depend on implicit `long` width
- avoid large stack buffers in thread hot paths
- use thread-safe libc APIs where available
- keep server-critical code free of client-only dependencies whenever possible

## Definition Of Done For Medusa

`medusa` is considered repaired enough for the next stage when:

- server-critical libraries build cleanly on modern 64-bit Windows and Linux
- basic server startup no longer crashes inside runtime helpers
- network and protocol code can be debugged without first fighting substrate failures

## Immediate Backlog

- finish the runtime substrate sweep
- document each repaired defect class in `DRIFT_LOG.md`
- add compile gates for server-critical targets
- reduce dependence on legacy makefile behavior for correctness
