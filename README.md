# mire-lang/libs

Official package repository for the mire language ecosystem.

Packages in this repo can be installed via `owl`:

```sh
owl sync kioto   # install the standard library
owl sync owl     # install the owl build system
```

## Packages

| Package | Description |
|---------|-------------|
| `kioto` | Standard library (core types, collections, I/O, concurrency) |
| `owl`   | Build system and project manager CLI |

## Adding a package

1. Create a directory with your package name at the repo root
2. Include an `owl.toml` with `[project]` and `[exports]` sections
3. Add an entry to `registry.toml`
4. Open a PR
