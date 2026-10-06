# Garrison branch

This branch is upstream anydoc **0.2.4** with changes for [Garrison](https://github.com/edgerunner-ai/Garrison), which installs the wheels from this fork's `garrison-v0.2.4+edgerunner.N` releases. The full write-up (symptoms, measurements, and how to update) is in Garrison's [`docs/utils/anydoc-fork.md`](https://github.com/edgerunner-ai/Garrison/blob/feat/anydoc-document-digestion/docs/utils/anydoc-fork.md).

## Changes

| Commit | Change |
|---|---|
| `4b7cbaf` | pdf-inspector bump (cherry-picked from firecrawl/anydoc#175). |
| `ae6834e`, `48ca86d`, `6985e74`, `97c10b7` | pdf-inspector 1.25.2 from [edgerunner-ai/pdf-inspector](https://github.com/edgerunner-ai/pdf-inspector/tree/garrison) via `[patch.crates-io]` in `Cargo.toml`, pinned by commit. That fork fixes PDF tables that lost numeric columns, rejected tables over 20 columns, and merged shaded header rows into the first data row; see its `GARRISON.md`. |
| `6c8af33` | `python/src/lib.rs`: conversions run under `catch_unwind`; a Rust panic raises `anydoc.ConvertError("internal error: anydoc panicked: ...")` instead of `pyo3_runtime.PanicException` (a `BaseException` that bypassed callers' `except Exception`). |
| `13c9627`, `de852b5` | Wheel version `0.2.4+edgerunner.N` in `python/Cargo.toml` (and its `Cargo.lock` entry). |

## Fork-only tooling

- `.github/workflows/garrison-wheels.yml` (`13c9627`, `a3f868a`): on a `garrison-v*` tag, runs `cargo test`, builds abi3 wheels for Linux x86_64/aarch64, macOS x86_64/arm64 and Windows x64, tests them, and publishes a GitHub Release. A manual run builds and tests without releasing; its `pdf_inspector` input builds against another pdf-inspector release for comparison.
- `.github/workflows/garrison-lock.yml`: runs `cargo update -p pdf-inspector` and uploads `Cargo.lock`, for updating the pin without a local Rust toolchain.
- Upstream's workflows need Firecrawl's Blacksmith runners and are disabled on this fork.

## Updating

Rebase onto the new upstream release, point the `[patch.crates-io]` rev at the current pdf-inspector fork commit, run *Garrison lock refresh* and commit the lock, bump `+edgerunner.N`, and push a `garrison-v...` tag. Then update the wheel URLs in Garrison's `pyproject.toml`.
