# Creating Libraries for Mire (Avenys)

Libraries in Mire are plain `.mire` files organized in directories with an `owl.toml`
manifest. They are consumed via `load` in dependent projects.

## Structure

```
mylib/
  owl.toml          # package manifest
  mylib.mire        # root module (or mod.mire inside a module/)
  core/
    mod.mire        # mylib::core module
    utils.mire      # mylib::core::utils
  ext/
    mod.mire        # mylib::ext module
```

## owl.toml

```toml
[project]
name = "mylib"
version = "0.1.0"
entry = "mylib.mire"

[paths]
sources = "."

[dependencies]
kioto = { path = "../kioto" }
```

Key fields:
- `name` — how consumers reference this library in `owl.toml [dependencies]`
- `entry` — the root file loaded when a consumer writes `load mylib`
- `paths.sources` — where `.mire` source files live (relative to owl.toml)
- `[dependencies]` — other libraries this one depends on

## Module system

### Root module

The file named in `entry` (e.g. `mylib.mire`) is the root. It should declare `module mylib`:

```mire
module mylib

# Public API
pub fn hello: () :str {
    return "Hello from mylib"
}
```

### Submodules

Submodules are directories containing a `mod.mire` that declares its module name:

```
mylib/
  core/
    mod.mire     # module core
```

```mire
# core/mod.mire
module core

pub fn version: () :i64 {
    return 1
}
```

### Loading submodules

The root module must explicitly `load` any submodules it wants to re-export:

```mire
# mylib.mire
module mylib
load mylib::core     # makes mylib::core::version available

pub fn hello: () :str {
    set v = core::version()
    return "mylib v{v}"
}
```

## Exports

Only items declared with `pub` are visible to consumers:

```mire
# Internal — not exported
fn helper: (x :i64) :i64 { return x + 1 }

# Public — exported
pub fn process: (x :i64) :i64 { return helper(x) * 2 }
```

## Types

### Structs

```mire
pub struct Point {
    x :i64
    y :i64
}

impl Point {
    pub fn new: (x :i64, y :i64) :Point {
        return (Point x: x, y: y)
    }

    pub fn origin: () :Point {
        return (Point x: 0, y: 0)
    }
}
```

### Skills (Traits)

```mire
pub skill Printable {
    fn print: (self) :str
}

pub skill Size {
    fn size: (self) :i64
}

impl Printable for Point {
    fn print: (self) :str {
        return "({self.x}, {self.y})"
    }
}
```

### Enums

```mire
pub enum Option[T] {
    None
    Some(value: T)
}

pub enum Result[T, E] {
    Ok(value: T)
    Err(error: E)
}
```

## Extern functions (FFI)

```mire
extern lib "c" "libc.so.6"

# Private — only usable within this module
extern fn pal_custom_op: (x :i64) :i64 lib "c"

# Public — exported to consumers
pub extern fn pal_public_op: (x :i64) :i64 lib "c"

# Wrapper for safe usage
pub fn custom_op: (x :i64) :i64 {
    return pal_custom_op(x)
}
```

## PAL (Platform Abstraction Layer)

Libraries that need C interop define extern functions binding to PAL symbols in `_pal.mire`:

```mire
# _pal.mire (private submodule)
module _pal

extern fn pal_my_func: (arg :str) :str lib "c"
```

Then wrap in a public API:

```mire
# mod.mire
module mylib
load mylib::_pal

pub fn my_func: (arg :&str) :str {
    return pal_my_func(*arg)
}
```

The actual C implementation lives in `mire/src/pal/linux/`. Add your PAL function there
and declare it in `pal.h`.

## Consuming libraries

In the dependent project's `owl.toml`:

```toml
[dependencies]
mylib = { path = "../mylib" }
kioto = { path = "../kioto" }
```

In the source file:

```mire
load mylib
load kioto

pub fn main: () {
    use dasu(mylib::hello())
}
```

## Registry

Published libraries are listed in `registry.toml`:

```toml
[published]
mylib = "0.1.0"
```

The `mire-lang/libs` repository is the central registry. Submit a PR to add your library.

## Testing

Place test files in a `tests/` directory:

```mire
# tests/smoke.mire
load mylib

pub fn test_hello: () {
    set result = mylib::hello()
    assert_eq(result, "Hello from mylib")
}
```

Run with: `mire test tests/`
