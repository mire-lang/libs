# mire-registry

Official package registry for the [Mire](https://github.com/mire-lang) language ecosystem.

Repository: https://github.com/mire-lang/libs

## Packages

| Package | Version | Method | Description | Repository |
|---------|---------|--------|-------------|------------|
| mire | 0.0.7 | clone | Core library — vec, map, str, maybe, result, arr, std macros | https://github.com/mire-lang/mire-lib |
| kioto | 2.4.7 | release | Standard library — core types, collections, I/O, networking, crypto | https://github.com/mire-lang/Kioto |
| sdl | 1.0.1 | release | SDL2 + SDL3 bindings — video, rendering, audio, events | https://github.com/mire-lang/SDL |
| avenys | 3.24.27 | clone | Mire compiler — compiler binary and runtime/PAL sources | https://github.com/mire-lang/Avenys-rust |
| owl | 0.31.0 | clone | Package & project manager for Mire | https://github.com/mire-lang/owl |

Two install methods are supported by `owl install`:

- `method = "clone"` — owl git-clones the repo into `~/.owl/libs/<name>` (pinned
  by `ref` and `commit`).
- `method = "release"` — owl downloads the signed release asset from GitHub,
  verifies its SHA-256, and extracts it.

## Structure

```
libs/
  registry.json        registry metadata (protocol, mirrors, key)
  registry.toml        package metadata (descriptions, versions)
  index.toml           package index — sync database
  index.toml.sig       Ed25519 signature over index.toml
  README.md            this file
  LICENSE              license terms
  packages/            published tarballs (.tar.zst)
  docs/
    CREATING-LIBRARIES.md  guide for adding new packages
```

## Using the registry

```bash
# Add this registry to owl (trusts the registry signing key)
owl load -Lu https://github.com/mire-lang/libs

# Sync registries (fetches + verifies index.toml.sig)
owl load -Ls

# Install a package
owl install mire
owl install kioto
owl install sdl
```

The registry `index.toml` is signed with the registry Ed25519 key; owl verifies
`index.toml.sig` on every sync and refuses to use an unsigned or invalid index.

## Adding a package

1. Create a root-level repo with its own `owl.toml`
2. Add a `[[packages]]` entry to `index.toml` (method `clone` or `release`)
3. For `method = "release"`: publish a tarball release on GitHub, set `asset` and `sha256`
4. Re-sign `index.toml` and update `registry.json` (`serial`, `packages`, `expires`)
5. Open a PR to this repository

See [docs/CREATING-LIBRARIES.md](docs/CREATING-LIBRARIES.md) for the full guide.
