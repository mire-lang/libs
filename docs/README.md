# mire-registry

Official package registry for the Mire language ecosystem (https://github.com/mire-lang/libs).

The registry follows the protocol described in the owl documentation.
It serves as both a local development index and an online sync database
(pacman-style `-Sy` / `-S` workflow available in owl v0.18+).

## Structure

```
libs/
  registry.json        registry metadata (protocol, mirrors, key)
  index.toml           package index — sync database
  index.toml.sig       Ed25519 signature over index.toml
  README.md            quick reference (this directory has full docs)
  LICENSE              license terms
  docs/
    README.md          this file
    CREATING-LIBRARIES.md guide for adding new packages
```

## Packages

| Package | Version | Description |
|---------|---------|-------------|
| kioto | 2.3.0 | Standard library — core types, collections, I/O, networking |
| owl | 0.26.0 | Package and project manager for Mire (Avenys) |
| sdl | 1.0.0 | SDL2 + SDL3 bindings — video, rendering, audio, events |

## Using the registry

```bash
# Add this registry to owl
owl registry add https://github.com/mire-lang/libs

# Sync and install
owl -Sy
owl -S kioto

# Or install from URL directly
owl install https://github.com/mire-lang/Kioto
owl install https://github.com/mire-lang/SDL
```

## Adding a package

1. Create a root-level repo with its own `owl.toml`
2. Add a `[[packages]]` entry to `index.toml`
3. Publish a tarball release on GitHub, update `tarball` and `sha256`
4. Open a PR to this repository
