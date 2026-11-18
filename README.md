<!-- SPDX-FileCopyrightText: 2026 Progmasoft <support@progmasoft.com> -->
<!-- SPDX-License-Identifier: MIT-0 -->

# Fuzzing corpora

The inputs that Progmasoft's continuous fuzzing keeps, and the inputs that made a target crash. A project's CI fuzzes
for minutes on each pull request and every night; the continuous campaigns run without end on a Progmasoft server and
keep what they learn here, so every run starts from everything found before.

| Directory | What it holds |
| --- | --- |
| `<project>/<target>/corpus/` | The kept corpus of a target: the smallest set of inputs that reaches all the coverage found so far. |
| `<project>/<target>/crashes/` | Inputs that made the target crash, hang or run out of memory. Each becomes a regression test in its project and is fixed there. |
| `ops/` | The scripts and systemd units of the campaigns. |

Targets:

| Project | Targets |
| --- | --- |
| [Catch3](https://github.com/Progmasoft/catch3) | `TestSpecParser`, `ProjectConfig`, `RunnerJson`, `RunnerOptions` (libFuzzer, ASan and UBSan) |

## How the campaigns run

Each target runs as `progmasoft-fuzz@<project>-<target>.service` on the second Progmasoft server, at the lowest CPU and
I/O priority, at most 60 % of one CPU and 2 GB of memory each, so the backups and the build cache keep their share. A
run lasts six hours; systemd starts the next one with the binary that `progmasoft-fuzz-build.timer` builds every day
from the project's default branch. `progmasoft-fuzz-sync.timer` merges the working corpus into `corpus/` every day,
keeping only inputs that add coverage, copies any crash inputs, and pushes the result here with a deploy key that can
write to this repository and nothing else.

Installing on the server, as root:

```sh
dnf install clang compiler-rt cmake ninja-build git
useradd --system --create-home --home-dir /var/lib/progmasoft-fuzz --shell /sbin/nologin fuzz
install -d -m 0755 /usr/local/libexec/progmasoft-fuzz
install -m 0755 ops/fuzz-build ops/fuzz-run ops/fuzz-sync /usr/local/libexec/progmasoft-fuzz/
install -m 0644 ops/systemd/* /etc/systemd/system/
restorecon -R /usr/local/libexec/progmasoft-fuzz /etc/systemd/system
systemctl daemon-reload
```

Then, as the fuzz user, clone this repository to `/var/lib/progmasoft-fuzz/repo` with the deploy key in
`/var/lib/progmasoft-fuzz/.ssh/fuzz-corpora`, run `fuzz-build` once, and enable
`progmasoft-fuzz@catch3-<target>.service` for each target and the two timers.

## License

[MIT-0](LICENSE.txt). Participation follows the [Code of Conduct](CODE_OF_CONDUCT.md).
