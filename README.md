# mire-registry

Official package registry for the [Mire](https://github.com/mire-lang) language ecosystem.

Repository: https://github.com/mire-lang/libs

## Packages

| Package | Version | Description | Repository |
|---------|---------|-------------|------------|
| kioto | 2.3.2 | Standard library — core types, collections, I/O, networking | https://github.com/mire-lang/Kioto |
| sdl | 1.0.1 | SDL2 + SDL3 bindings — video, rendering, audio, events | https://github.com/mire-lang/SDL |

## Toolchain repositories

These are installed via the [Avenys install script](https://github.com/mire-lang/Avenys-rust), not `owl install`:

| Repo | Description |
|------|-------------|
| https://github.com/mire-lang/Avenys-rust | Mire compiler (Avenys v3.24.3) |
| https://github.com/mire-lang/owl | Owl package & project manager (v0.29.0) |

## Structure

```
libs/
  registry.json        registry metadata (protocol, mirrors, key)
  registry.toml        package metadata (descriptions, versions)
  index.toml           package index — sync database
  README.md            this file
  LICENSE              license terms
  packages/            published tarballs (.tar.zst)
  docs/
    CREATING-LIBRARIES.md  guide for adding new packages
```

## Using the registry

```bash
# Add this registry to owl
owl load -Lu https://github.com/mire-lang/libs

# Sync registries
owl load -Ls

# Install a package
owl install kioto
owl install sdl
```

## Adding a package

1. Create a root-level repo with its own `owl.toml`
2. Add a `[[packages]]` entry to `index.toml`
3. Publish a tarball release on GitHub, update `tarball` and `sha256`
4. Open a PR to this repository

See [docs/CREATING-LIBRARIES.md](docs/CREATING-LIBRARIES.md) for the full guide.
