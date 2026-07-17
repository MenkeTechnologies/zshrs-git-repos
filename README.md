# zshrs-git-repos

zsh-git-repo-cache ported to a **native
[zshrs](https://github.com/MenkeTechnologies/zshrs) plugin**: scan the
filesystem for every git repository, cache the list, and `fzf`-pick one to
`cd` into — with clean/dirty filtering.

The shell version does `sudo find / -name .git` plus a **sequential**
`git status` per repo. The native version walks in-process and classifies
clean/dirty **in parallel across threads**, which is the speedup.

```text
gitrepos              fzf-pick any cached repo and cd to it
gitrepos --clean      only repos with a clean working tree
gitrepos --dirty      only repos with uncommitted/untracked changes
gitrepos --list       print instead of fzf/cd
gitrepos --regen      rescan the filesystem and rebuild the cache
gitrepos --root DIR   scan root for --regen (default: $ZPWR_GIT_SCAN_ROOT or $HOME)
```

Cache file: `$ZPWR_ALL_GIT_DIRS` (default `~/.zsh-git-repo-cache`). Requires
`git` and `fzf` on `PATH`. A repo is "clean" when
`git diff-index --quiet HEAD` succeeds and there are no untracked files —
the same test as the original.

## Install

```sh
zpm load MenkeTechnologies/zshrs-git-repos
```

Put that one line in your `.zshrc`.
[zpm](https://github.com/MenkeTechnologies/zshrs/blob/main/docs/ZPM.md),
zshrs's package manager, installs the plugin on the first shell start — clones
it, runs `cargo build --release`, and `zmodload -R`s the resulting
`libgit_repos` — then loads it from the store, zero-network, on every start
after. No separate install step.

### Manual build

```sh
cargo build --release
zmodload -R ./target/release/libgit_repos.dylib   # .so on Linux
gitrepos --regen && gitrepos
```

## License

MIT. See [LICENSE](LICENSE).
