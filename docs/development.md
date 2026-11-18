<!-- SPDX-FileCopyrightText: 2026 Progmasoft <support@progmasoft.com> -->
<!-- SPDX-License-Identifier: MIT-0 -->

# Development

The scripts are POSIX shell and run on the second Progmasoft server as the `fuzz` user. Before installing a change,
run a script by hand as that user and check what it would push:

```sh
sudo -u fuzz /usr/local/libexec/progmasoft-fuzz/fuzz-build
sudo -u fuzz git -C /var/lib/progmasoft-fuzz/repo status
```

Installing is described in the [README](../README.md). A new target is a directory `<project>/<target>/` and an
instance `progmasoft-fuzz@<project>-<target>.service`.
