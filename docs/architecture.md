<!-- SPDX-FileCopyrightText: 2026 Progmasoft <support@progmasoft.com> -->
<!-- SPDX-License-Identifier: MIT-0 -->

# Architecture

The repository holds data, the corpora and crash inputs, and the code that keeps them current, in `ops/`.

| File | What it does |
| --- | --- |
| `ops/fuzz-build` | Builds every fuzz target of a project from its default branch with libFuzzer, ASan and UBSan. |
| `ops/fuzz-run` | Runs one target for six hours on its working corpus. |
| `ops/fuzz-sync` | Merges the working corpus into `corpus/`, keeping only inputs that add coverage, copies crash inputs, and pushes. |
| `ops/systemd/progmasoft-fuzz@.service` | One campaign per target, at the lowest CPU and I/O priority, within 60 % of a CPU and 2 GB. |
| `ops/systemd/progmasoft-fuzz-build.*` | The daily build. |
| `ops/systemd/progmasoft-fuzz-sync.*` | The daily merge and push. |

A campaign never writes to `corpus/` directly; only `fuzz-sync` does, so the kept corpus stays the minimal set.
