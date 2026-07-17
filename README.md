```
 ██████╗ ██╗████████╗   ██████╗ ███████╗██████╗  ██████╗ ███████╗
██╔════╝ ██║╚══██╔══╝   ██╔══██╗██╔════╝██╔══██╗██╔═══██╗██╔════╝
██║  ███╗██║   ██║█████╗██████╔╝█████╗  ██████╔╝██║   ██║███████╗
██║   ██║██║   ██║╚════╝██╔══██╗██╔══╝  ██╔═══╝ ██║   ██║╚════██║
╚██████╔╝██║   ██║      ██║  ██║███████╗██║     ╚██████╔╝███████║
 ╚═════╝ ╚═╝   ╚═╝      ╚═╝  ╚═╝╚══════╝╚═╝      ╚═════╝ ╚══════╝
                                                                 
```

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![zshrs plugin](https://img.shields.io/badge/zshrs-native%20plugin-blue.svg)](https://github.com/MenkeTechnologies/zshrs)

### `[PARALLEL GIT-REPO FINDER — COMPILED]`

> *"find every repo, classify clean/dirty across threads, fzf-jump."*

## `[NATIVE ZSHRS PLUGIN]`

zsh-git-repo-cache ported to a **native [zshrs](https://github.com/MenkeTechnologies/zshrs) plugin**: scan the filesystem for every git repository, cache the list, and `fzf`-pick one to `cd` into — with clean/dirty filtering.

### [`zshrs`](https://github.com/MenkeTechnologies/zshrs) &middot; [`znative`](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZPM.md)

---

## Table of Contents

- [\[0x00\] Overview](#0x00-overview)
- [\[0x01\] Install](#0x01-install)
- [\[0x02\] Usage](#0x02-usage)
- [\[0x03\] How it works](#0x03-how-it-works)
- [\[0xFF\] License](#0xff-license)

---

## [0x00] OVERVIEW

The shell version does `sudo find / -name .git` plus a **sequential** `git status` per repo. The native version walks in-process and classifies clean/dirty **in parallel across threads**, which is the speedup.

Requires `git` and `fzf` on `PATH`. A repo is "clean" when `git diff-index --quiet HEAD` succeeds and there are no untracked files — the same test as the original.

---

## [0x01] INSTALL

```sh
znative load MenkeTechnologies/zshrs-git-repos
```

Put that one line in your `.zshrc`. [znative](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZPM.md), zshrs's package manager, installs the plugin on the first shell start — clones it, runs `cargo build --release`, and `zmodload -R`s the resulting `libgit_repos` — then loads it from the store, zero-network, on every start after. No separate install step.

### Manual build

```sh
cargo build --release
zmodload -R ./target/release/libgit_repos.dylib   # .so on Linux
gitrepos --regen && gitrepos
```

---

## [0x02] USAGE

```text
gitrepos              fzf-pick any cached repo and cd to it
gitrepos --clean      only repos with a clean working tree
gitrepos --dirty      only repos with uncommitted/untracked changes
gitrepos --list       print instead of fzf/cd
gitrepos --regen      rescan the filesystem and rebuild the cache
gitrepos --root DIR   scan root for --regen (default: $ZPWR_GIT_SCAN_ROOT or $HOME)
```

Cache file: `$ZPWR_ALL_GIT_DIRS` (default `~/.zsh-git-repo-cache`).

---

## [0x03] HOW IT WORKS

`--regen` walks the scan root once, records each `.git` parent in the cache file, and classifies clean/dirty across a thread pool instead of one-repo-at-a-time. Subsequent `gitrepos` calls read the cache and `fzf`-pick; the `cd` is delegated to the shell so `$PWD`/hooks stay correct.

---

## [0xFF] LICENSE

MIT. See [LICENSE](LICENSE).
