# homebrew-tap

Homebrew tap for the [`exo`](https://github.com/adaptive-scale/exo) CLI.

## Install

```sh
brew install adaptive-scale/tap/exo
```

Or tap first, then install:

```sh
brew tap adaptive-scale/tap
brew install exo
```

Upgrade later with `brew upgrade exo`.

## Layout

This directory is the content of the **separate** public tap repo
`github.com/adaptive-scale/homebrew-tap`. Homebrew requires a repo named
`homebrew-<tap>`, so `adaptive-scale/tap` resolves to `adaptive-scale/homebrew-tap`.

- `Formula/exo.rb` — the formula. **Generated** by `scripts/release-cli.sh` in
  the main repo; do not hand-edit. It installs prebuilt binaries served from the
  `exo.assets.adaptive.live` asset host
  (`/assets/exo/<version>/exo_<version>_<os>_<arch>.tar.gz`), covering
  darwin/linux × amd64/arm64.

## Releasing a new version

The `v*` git tag **must already exist** — it's the build source. The script
exports `cli/` at that tag with `git archive` and builds from the export, never
the working tree, and forces the exact Go toolchain pinned in `cli/go.mod`. So
the tarballs (and thus these SHA256s) are reproducible: anyone who rebuilds the
same tag gets the same checksums, and regenerating the formula later can never
drift from the published artifacts.

From the main repo:

```sh
# Build all platforms, package, checksum, render Formula/exo.rb,
# and publish the tarballs to the exo.assets.adaptive.live asset host:
scripts/release-cli.sh v0.2.219

# ...or regenerate the formula only, without publishing:
scripts/release-cli.sh v0.2.219 --no-publish

# Local iteration against the live working tree (NOT reproducible — never
# publish from this):
scripts/release-cli.sh v0.2.219 --no-publish --from-worktree
```

Then copy the regenerated `homebrew-tap/Formula/exo.rb` into the
`adaptive-scale/homebrew-tap` repo and commit it. The script prints the exact
`brew install` command and the next steps when it finishes.

To bump the build toolchain, edit the `toolchain` line in `cli/go.mod`; the
release script reads it as the single source of truth.
