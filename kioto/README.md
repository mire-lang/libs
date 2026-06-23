# Kioto — Mire Standard Library

**Version:** 3.11.11 — **Language:** Mire (Avenys) — **License:** GPL v3.0

Kioto is the standard library for the Mire programming language. It provides
core data structures, operating system abstractions, networking, and browser
bridge primitives — **242 public functions across 22 modules**.

```
User code (.mire)
     │
     ▼
┌─────────────────────┐
│  kioto (this repo)  │  core/ + ext/  —  pure Mire wrappers
└─────────┬───────────┘
          │ extern fn pal_*  /  extern fn rt_*
          ▼
┌─────────────────────┐
│  Mire PAL + RT (C)  │  pal/linux/ + runtime/  —  OS syscalls + managed types
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  Operating System    │  Linux (POSIX)
└─────────────────────┘
```

---

## Quick Start

```mire
load kioto

fn main: () {
    # HTTP GET
    set res = net::http::get("https://httpbin.org/get")
    use dasu(res)

    # HTTP POST
    set r2 = net::http::post("https://httpbin.org/post", "hello=world", "application/x-www-form-urlencoded")

    # File I/O
    fs::write("log.txt", res)

    # Math
    set x = math::sin(math::pi() / 4.0)
    use dasu(strings::from_i64(x :i64))
}
```

Compile:

```bash
mire run main.mire
# or via owl:
owl run
```

---

## Module Index

### Core (`kioto::core/`)

| Module | Path | Functions | Description |
|--------|------|:---------:|-------------|
| `strings` | `core/strings` | 22 | Case, split, join, replace, trim, substr, pad, parse |
| `lists` | `core/lists` | 26 | push, pop, get, slice, concat, sort, reverse, map |
| `dicts` | `core/dicts` | 11 | get, set, keys, values, merge, remove |
| `time` | `core/time` | 5 | unix_ms, unix_ns, elapsed, mark |
| `fs` | `core/fs` | 17 | read, write, copy, move, mkdir, list, walk |
| `env` | `core/env` | 6 | get, set, cwd, chdir, args, all |
| `proc` | `core/proc` | 18 | run, spawn, capture, shell, wait, kill |
| `async` | `core/async` | 9 | spawn, join, ready, failed, task::done |
| `mem` | `core/mem` | 8 | used, total, free, percent, process, format |
| `cpu` | `core/cpu` | 11 | time_ms, count, freq, cycles, loadavg |
| `gpu` | `core/gpu` | 2 | available, snapshot |
| `math` | `core/math` | 33 | trig, pow, sqrt, stats, random, range |
| `term` | `core/term` | 4 | style, hr, clear, bar |
| `net` | `core/net` | 11 | TCP connect, HTTP/HTTPS GET+POST, TLS |

### Extensions (`kioto::ext/`)

| Module | Path | Functions | Description |
|--------|------|:---------:|-------------|
| `ws` | `ext/ws` | 5 | WebSocket ws:// and secure wss:// |
| `json` | `ext/json` | 4 | get, get_number, get_bool, has |
| `types` | `ext/types` | 5 | is_int, is_str, is_bool, type_name |
| `maybe` | `ext/maybe` | 8 | Option pattern: Some/None, map, bind |
| `result` | `ext/result` | 6 | Result pattern: Ok/Err, map_err, unwrap |
| `tuple` | `ext/tuple` | 4 | pair, triple, first, second |
| `iter` | `ext/iter` | 6 | head, tail, take, skip, collect |

---

## API Reference

### strings — `load kioto::strings`

String case, slicing, searching, formatting.

| Function | Signature | Description |
|----------|-----------|-------------|
| `upper` | `(s :&str) :str` | Uppercase |
| `lower` | `(s :&str) :str` | Lowercase |
| `strip` | `(s :&str) :str` | Trim whitespace from both ends |
| `trim` | `(s :&str) :str` | Alias for strip |
| `ltrim` | `(s :&str) :str` | Left trim |
| `rtrim` | `(s :&str) :str` | Right trim |
| `split` | `(s :&str, sep :&str) :vec[str]` | Split on separator |
| `join` | `(parts :&vec[str], sep :&str) :str` | Join with separator |
| `replace` | `(s :&str, old :&str, new :&str) :str` | Replace all occurrences |
| `replace_first` | `(s :&str, old :&str, new :&str) :str` | Replace first occurrence |
| `contains` | `(s :&str, sub :&str) :bool` | Check if contains substring |
| `startswith` | `(s :&str, prefix :&str) :bool` | Check prefix |
| `endswith` | `(s :&str, suffix :&str) :bool` | Check suffix |
| `len` | `(s :&str) :i64` | String length |
| `substr` | `(s :&str, start :i64, len :i64) :str` | Extract substring |
| `pad_left` | `(s :&str, w :i64, pad :&str) :str` | Left-pad to width w |
| `pad_right` | `(s :&str, w :i64, pad :&str) :str` | Right-pad to width w |
| `repeat` | `(s :&str, n :i64) :str` | Repeat n times |
| `is_empty` | `(s :&str) :bool` | Check if empty |
| `index_of` | `(s :&str, sub :&str) :i64` | Position of first occurrence, -1 if not found |
| `from_i64` | `(value :i64) :str` | Convert i64 to string |
| `to_i64` | `(s :&str) :i64` | Parse string to i64 |

### lists — `load kioto::lists`

Mutable ordered collections.

| Function | Signature | Description |
|----------|-----------|-------------|
| `len` | `(list :&list) :i64` | Element count |
| `push` | `(list, value)` | Append value to end |
| `pop` | `(list) :i64` | Remove and return last as i64 |
| `append` | `(list, value)` | Alias for push |
| `remove` | `(list, index :i64)` | Remove at index |
| `delete` | `(list, index :i64)` | Alias for remove |
| `clear` | `(list)` | Remove all elements |
| `join` | `(list :&list, sep :&str) :str` | Join i64 elements with separator |
| `contains` | `(list :&vec[i64], value :i64) :bool` | Check if contains value |
| `index_of` | `(list :&vec[i64], value :i64) :i64` | First index of value, -1 if not found |
| `first` | `(list :&list) :i64` | First element |
| `last` | `(list :&list) :i64` | Last element |
| `get` | `(list :&list, index :i64) :i64` | Element at index (generic list → i64) |
| `get` | `(list :&vec[str], index :i64) :str` | Element at index (vec[str] → str) |
| `get_str` | `(list :&vec[str], index :i64) :str` | String element at index |
| `slice` | `(list :&list, start :i64, end :i64) :vec[i64]` | Sub-list [start, end) |
| `concat` | `(list, other)` | Append all elements from other |
| `flatten` | `(list :&list) :vec[i64]` | Flatten nested list |
| `reverse` | `(list :&vec[i64]) :vec[i64]` | New reversed list |
| `sort` | `(list)` | Sort in-place |
| `unique` | `(list :&vec[i64]) :vec[i64]` | New list with duplicates removed |
| `is_empty` | `(list :&list) :bool` | Check if empty |
| `map` | `(list, func) :vec[i64]` | Apply func to each element (stub) |
| `filter` | `(list, func) :vec[i64]` | Keep elements where func true (stub) |
| `fold` | `(acc :i64, list, func) :i64` | Reduce with accumulator (stub) |

### dicts — `load kioto::dicts`

String-keyed maps.

| Function | Signature | Description |
|----------|-----------|-------------|
| `len` | `(dict) :i64` | Entry count |
| `keys` | `(dict) :vec[str]` | All keys |
| `values` | `(dict) :vec[i64]` | All values |
| `has` | `(dict, key :str) :bool` | Check if key exists |
| `get` | `(dict, key :str) :str` | Get value by key, "" if missing |
| `set` | `(dict, key :str, value)` | Insert/update entry |
| `remove` | `(dict, key :str)` | Remove entry |
| `delete` | `(dict, key :str)` | Alias for remove |
| `entries` | `(dict) :i64` | Opaque handle to entries |
| `merge` | `(dict, other)` | Merge all entries from other dict |
| `is_empty` | `(dict) :bool` | Check if empty |

### time — `load kioto::time`

Timestamps and durations.

| Function | Signature | Description |
|----------|-----------|-------------|
| `unix_ms` | `() :i64` | Unix timestamp in milliseconds |
| `unix_ns` | `() :i64` | Unix timestamp in nanoseconds |
| `elapsed` | `(start :i64) :i64` | Milliseconds elapsed since start |
| `elapsed_ns` | `(start :i64) :i64` | Nanoseconds elapsed since start |
| `mark` | `() :i64` | High-resolution monotonic mark |

### fs — `load kioto::fs`

Filesystem operations.

| Function | Signature | Description |
|----------|-----------|-------------|
| `read` | `(path :&str) :str` | Read entire file |
| `write` | `(path :&str, data :&str)` | Write data (overwrites) |
| `append` | `(path :&str, data :&str)` | Append data |
| `is_file` | `(path :&str) :bool` | Check if regular file |
| `exists` | `(path :&str) :bool` | Check if path exists |
| `size` | `(path :&str) :i64` | File size in bytes |
| `copy` | `(src :&str, dst :&str)` | Copy file |
| `move` | `(src :&str, dst :&str)` | Move/rename file |
| `drop` | `(path :&str)` | Delete file |
| `list` | `(path :&str) :vec[str]` | List directory contents |
| `mkdir` | `(path :&str)` | Create directory |
| `rmdir` | `(path :&str)` | Remove empty directory |
| `join` | `(a :&str, b :&str) :str` | Join path segments |
| `dir` | `(path :&str) :str` | Directory part of path |
| `name` | `(path :&str) :str` | Filename (basename) |
| `ext` | `(path :&str) :str` | File extension (with dot) |
| `walk` | `(path :&str) :vec[str]` | Recursive directory listing |

### env — `load kioto::env`

Environment and arguments.

| Function | Signature | Description |
|----------|-----------|-------------|
| `get` | `(key :&str) :str` | Read env var, "" if not set |
| `set` | `(key :&str, value :&str)` | Set env var |
| `all` | `() :map[str str]` | All env vars as map |
| `args` | `() :vec[str]` | CLI arguments |
| `cwd` | `() :str` | Current working directory |
| `chdir` | `(path :&str)` | Change directory |

### proc — `load kioto::proc`

Process spawning and management.

| Function | Signature | Description |
|----------|-----------|-------------|
| `run` | `(cmd :str, args :vec[str]) :i64` | Spawn process, return PID |
| `spawn` | `(cmd :str, args :vec[str]) :i64` | Alias for run |
| `spawn_shell` | `(cmd :str) :i64` | Spawn via shell |
| `capture` | `(cmd :str, args :vec[str]) :str` | Run and capture stdout |
| `pipe` | `(commands :vec[str]) :str` | Pipe commands (stub) |
| `shell` | `(cmd :str) :str` | Run via shell, return output |
| `read` | `() :str` | Read stdin line |
| `stdin` | `() :str` | Alias for read |
| `write` | `(data :str)` | Write to stdout |
| `exit` | `(code :i64)` | Exit process |
| `err` | `() :str` | stderr capture (stub) |
| `eprint` | `(msg :str)` | Print to stderr |
| `exec` | `(cmd :str, args :vec[str]) :i64` | Alias for run |
| `exec_bg` | `(cmd :str, args :vec[str]) :i64` | Spawn in background |
| `kill` | `(pid :i64)` | Terminate process |
| `wait` | `(pid :i64) :i64` | Wait for exit, return code |
| `exists` | `(pid :i64) :bool` | Check if running |
| `on` | `(signal :str, handler)` | Signal handler (stub) |

### async — `load kioto::async`

Background tasks and async primitives.

| Function | Signature | Description |
|----------|-----------|-------------|
| `ready` | `(value :str) :map[str str]` | Create resolved task |
| `failed` | `(message :str) :map[str str]` | Create failed task |
| `task::done` | `(task :map[str str]) :bool` | Check if task completed |
| `task::error` | `(task :map[str str]) :bool` | Check if task failed |
| `value` | `(task :map[str str], fallback :str) :str` | Extract task value |
| `error` | `(task :map[str str]) :str` | Extract error message |
| `spawn` | `(cmd :str) :i64` | Spawn background process, return PID |
| `join` | `(pid :i64) :i64` | Wait for background task, return exit code |
| `exists` | `(pid :i64) :bool` | Check if task is alive |

Usage pattern:

```mire
load kioto::async

fn main: () {
    # Spawn a background HTTP request via curl
    set pid = async::spawn("curl -s https://httpbin.org/get")
    use dasu("Task running...")
    set code = async::join(pid)
    use dasu("Done: " + strings::from_i64(code))
}
```

### mem — `load kioto::mem`

System memory statistics.

| Function | Signature | Description |
|----------|-----------|-------------|
| `used` | `() :i64` | Used memory (bytes) |
| `total` | `() :i64` | Total memory (bytes) |
| `free` | `() :i64` | Free memory (bytes) |
| `available` | `() :i64` | Available memory (bytes) |
| `percent` | `() :i64` | Usage percent (0-100) |
| `process` | `() :i64` | Process memory (bytes) |
| `snapshot` | `() :map[str i64]` | Full memory snapshot |
| `format` | `(bytes :i64) :str` | Human-readable byte format |

### cpu — `load kioto::cpu`

CPU metrics.

| Function | Signature | Description |
|----------|-----------|-------------|
| `time_ns` | `() :i64` | CPU time nanoseconds |
| `time_ms` | `() :i64` | CPU time milliseconds |
| `mark` | `() :i64` | High-resolution mark |
| `elapsed` | `(mark :i64) :i64` | Milliseconds since mark |
| `elapsed_ms` | `(mark :i64) :i64` | Alias for elapsed |
| `elapsed_ns` | `(mark :i64) :i64` | Nanoseconds since mark |
| `count` | `() :i64` | Logical CPU cores |
| `freq_mhz` | `() :i64` | CPU frequency MHz |
| `cycles_est` | `(mark :i64) :i64` | Estimated cycles since mark |
| `loadavg` | `() :vec[f64]` | System load averages |
| `snapshot` | `() :map[str i64]` | CPU metrics snapshot |

### gpu — `load kioto::gpu`

GPU monitoring.

| Function | Signature | Description |
|----------|-----------|-------------|
| `available` | `() :bool` | GPU monitoring available |
| `snapshot` | `() :map[str i64]` | GPU metrics snapshot |

### math — `load kioto::math`

**Constants:**

| Function | Signature | Returns |
|----------|-----------|---------|
| `pi` | `() :f64` | π (3.14159...) |
| `e` | `() :f64` | e (2.71828...) |
| `tau` | `() :f64` | τ = 2π |

**Basic:**

| Function | Signature | Description |
|----------|-----------|-------------|
| `abs` | `(n :i64) :i64` | Absolute value |
| `min` | `(a :i64, b :i64) :i64` | Smaller of a, b |
| `max` | `(a :i64, b :i64) :i64` | Larger of a, b |
| `clamp` | `(n :i64, min :i64, max :i64) :i64` | Clamp to [min, max] |
| `round` | `(value :f64) :i64` | Round to nearest integer |
| `floor` | `(value :f64) :i64` | Round down |
| `ceil` | `(value :f64) :i64` | Round up |

**Trigonometry:**

| Function | Signature | Description |
|----------|-----------|-------------|
| `sin` | `(value :f64) :f64` | Sine (radians) |
| `cos` | `(value :f64) :f64` | Cosine (radians) |
| `tan` | `(value :f64) :f64` | Tangent (radians) |
| `asin` | `(value :f64) :f64` | Arcsine |
| `acos` | `(value :f64) :f64` | Arccosine |
| `atan2` | `(y :f64, x :f64) :f64` | Arctan with quadrant |
| `hypot` | `(x :f64, y :f64) :f64` | sqrt(x² + y²) |

**Power & Log:**

| Function | Signature | Description |
|----------|-----------|-------------|
| `sqrt` | `(value :f64) :f64` | Square root |
| `pow` | `(base :f64, exp :f64) :f64` | base^exp |
| `log` | `(value :f64) :f64` | Natural logarithm |
| `log10` | `(value :f64) :f64` | Base-10 logarithm |
| `exp` | `(value :f64) :f64` | e^value |

**Statistics & Ranges:**

| Function | Signature | Description |
|----------|-----------|-------------|
| `sum` | `(list :&vec[i64]) :i64` | Sum all elements |
| `mean` | `(list :&vec[i64]) :f64` | Arithmetic mean |
| `avg` | `(list :&vec[i64]) :f64` | Alias for mean |
| `variance` | `(list :&vec[i64]) :f64` | Population variance |
| `stddev` | `(list :&vec[i64]) :f64` | Standard deviation |
| `median` | `(list :&vec[i64]) :f64` | Median |
| `min_list` | `(list :&vec[i64]) :i64` | Minimum in list |
| `max_list` | `(list :&vec[i64]) :i64` | Maximum in list |
| `range` | `(end :i64) :vec[i64]` | [0, 1, ..., end-1] |
| `range_between` | `(start :i64, end :i64) :vec[i64]` | [start, ..., end-1] |
| `range_step` | `(start :i64, end :i64, step :i64) :vec[i64]` | Range with step |

### term — `load kioto::term`

Terminal styling and output.

| Function | Signature | Description |
|----------|-----------|-------------|
| `style` | `(s :str, style :str) :str` | Apply ANSI style |
| `hr` | `(ch :str, len :i64) :str` | Horizontal rule of ch repeated |
| `clear` | `() :str` | Clear screen escape sequence |
| `bar` | `(name :str, elapsed_ms :i64, phase_ms :i64, total_ms :i64) :str` | Progress bar string |

### net — `load kioto::net`

TCP sockets and HTTP/HTTPS client. See full documentation in the module source.

| Function | Signature | Description |
|----------|-----------|-------------|
| `connect` | `(host :&str, port :i64) :i64` | TCP connect, returns fd or -1 |
| `connect::timeout` | `(host :&str, port :i64, timeout_ms :i64) :i64` | TCP connect with timeout |
| `send` | `(fd :i64, data :&str) :bool` | Send raw data on TCP socket |
| `recv` | `(fd :i64, max_bytes :i64) :str` | Receive up to max_bytes |
| `recv::all` | `(fd :i64) :str` | Receive up to 64 KiB |
| `close` | `(fd :i64)` | Close TCP/TLS socket |
| `poll` | `(fd :i64, timeout_ms :i64) :i64` | Poll for readability |
| `socket::nonblock` | `(fd :i64, nonblock :i64)` | Non-blocking (1) or blocking (0) |
| `resolve` | `(host :&str) :str` | DNS-resolve hostname to IPv4 |
| `http::get` | `(url :&str) :str` | HTTP/HTTPS GET, returns body |
| `http::post` | `(url :&str, body :&str, content_type :&str) :str` | HTTP/HTTPS POST |

Usage:

```mire
load kioto::net

fn main: () {
    # HTTP (plain)
    set r1 = net::http::get("http://httpbin.org/get")

    # HTTPS (auto-detected, uses OpenSSL TLS)
    set r2 = net::http::get("https://httpbin.org/get")

    # POST with JSON
    set r3 = net::http::post(
        "https://httpbin.org/post",
        "{\"key\": \"value\"}",
        "application/json"
    )
    use dasu(r3)
}
```

### ws — `load kioto::ws`

WebSocket client (RFC 6455). Auto-detects `ws://` vs `wss://` scheme.

| Function | Signature | Description |
|----------|-----------|-------------|
| `connect` | `(url :&str) :i64` | Connect to ws:// or wss://, returns fd |
| `send::text` | `(fd :i64, message :&str)` | Send masked text frame (opcode 0x1) |
| `recv` | `(fd :i64, max_bytes :i64) :str` | Receive one text frame |
| `recv::all` | `(fd :i64) :str` | Receive up to 64 KiB |
| `close` | `(fd :i64)` | Send close frame (0x8) and shutdown |

Usage:

```mire
load kioto::ws

fn main: () {
    set fd = ws::connect("wss://echo.websocket.org/")
    if fd < 0 {
        use dasu("Connection failed")
        return
    }
    ws::send::text(fd, "ping")
    set reply = ws::recv::all(fd)
    use dasu("Received: " + reply)
    ws::close(fd)
}
```

### json — `load kioto::json`

Lightweight JSON field extraction.

| Function | Signature | Description |
|----------|-----------|-------------|
| `get` | `(text :&str, field :&str) :str` | Extract string field |
| `get_number` | `(text :&str, field :&str) :i64` | Extract numeric field as i64 |
| `get_bool` | `(text :&str, field :&str) :bool` | Extract boolean field |
| `has` | `(text :&str, field :&str) :bool` | Check if field exists |

### types — `load kioto::types`

Runtime type introspection.

| Function | Signature | Description |
|----------|-----------|-------------|
| `is_int` | `(value) :bool` | Check if type is i64 |
| `is_str` | `(value) :bool` | Check if type is str |
| `is_bool` | `(value) :bool` | Check if type is bool |
| `to_str` | `(value) :str` | Convert any value to string |
| `type_name` | `(value) :str` | Runtime type name |

### maybe — `load kioto::maybe`

Option type pattern. Uses maps internally (`{"tag": "some"/"none"}`).

| Function | Signature | Description |
|----------|-----------|-------------|
| `some` | `(value)` | Wrap value in Some |
| `none` | `()` | Create None |
| `is_some` | `(m) :bool` | Check if Some |
| `is_none` | `(m) :bool` | Check if None |
| `map` | `(m, next_value)` | Transform value if Some |
| `bind` | `(m, next_maybe) :map[str anything]` | Chain maybes |
| `or_else` | `(m, fallback)` | Unwrap with fallback |
| `unwrap` | `(m)` | Force unwrap, return raw value |

### result — `load kioto::result`

Result type pattern. Uses maps internally (`{"tag": "ok"/"err"}`).

| Function | Signature | Description |
|----------|-----------|-------------|
| `ok` | `(value)` | Create Ok result |
| `err` | `(message :str)` | Create Err result |
| `is_ok` | `(r) :bool` | Check if Ok |
| `is_err` | `(r) :bool` | Check if Err |
| `map_err` | `(r, next_error :str)` | Transform error message |
| `unwrap` | `(r)` | Extract value, exit(1) on Err |

### tuple — `load kioto::tuple`

Lightweight n-element tuples via vec.

| Function | Signature | Description |
|----------|-----------|-------------|
| `pair` | `(a, b)` | Create 2-element tuple |
| `triple` | `(a, b, c)` | Create 3-element tuple |
| `first` | `(t :vec[str]) :str` | First element |
| `second` | `(t :vec[str]) :str` | Second element |

### iter — `load kioto::iter`

Basic iterator combinators over lists.

| Function | Signature | Description |
|----------|-----------|-------------|
| `size` | `(items :&list) :i64` | Element count |
| `head` | `(items :&list) :i64` | First element as i64 |
| `tail` | `(items :&list) :vec[i64]` | All but first |
| `take` | `(items :&list, n :i64) :vec[i64]` | First n elements |
| `skip` | `(items :&list, n :i64) :vec[i64]` | Skip n, return rest |
| `collect` | `(items :vec[i64]) :vec[i64]` | Identity (pass-through) |

---

## Importing

```mire
# Load the entire standard library (all modules available with prefixes)
load kioto

# Load specific modules only
load kioto::net
load kioto::ws

# Then call functions with module prefix
set r = net::http::get("https://example.com")
set fd = ws::connect("ws://127.0.0.1:9877/")

# use calls a function as a statement (discards return value):
use dasu("hello")     # call dasu (print)
use proc::exit(1)     # call exit
```
Note: `use` is always an expression call, `load` always imports a module. Use `load` to import, `use` to execute for side effects.

---

## Project Structure

```
kioto/
  mod.mire           # Root: loads all sub-modules
  owl.toml           # Package manifest
  core/
    strings/mod.mire   # String manipulation (22 fns)
    lists/mod.mire     # List operations (26 fns)
    dicts/mod.mire     # Dictionary operations (11 fns)
    time/mod.mire      # Time and timestamps (5 fns)
    fs/mod.mire        # Filesystem (17 fns)
    env/mod.mire       # Environment variables (6 fns)
    proc/mod.mire      # Process management (18 fns)
    async/mod.mire     # Background tasks (9 fns)
    mem/mod.mire       # Memory stats (8 fns)
    cpu/mod.mire       # CPU metrics (11 fns)
    gpu/mod.mire       # GPU info (2 fns)
    math/mod.mire      # Math functions (33 fns)
    term/mod.mire      # Terminal styling (4 fns)
    net/mod.mire       # TCP + HTTP/HTTPS (11 fns)
  ext/
    ws/mod.mire        # WebSocket client (5 fns)
    wasm/mod.mire      # WASM browser bridge (15 fns)
    json/mod.mire      # JSON field extraction (4 fns)
    types/mod.mire     # Type introspection (5 fns)
    maybe/mod.mire     # Option pattern (8 fns)
    result/mod.mire    # Result pattern (6 fns)
    tuple/mod.mire     # Tuples (4 fns)
    iter/mod.mire      # Iterator combinators (6 fns)
```

---

## Documentation

- [PAL & ABI Reference](https://github.com/mire-lang/mire/blob/main/docs/PAL-ABI.md) — How to add platform functions
- [Mire Libraries Guide](https://github.com/mire-lang/mire/blob/main/docs/LIBRARIES.md) — How `load`/`use`/`owl.toml`/`exports` work
- [Mire Language Syntax](https://github.com/mire-lang/mire/blob/main/SYNTAX.md)

---

## Changelog

See [CHANGELOG.md](./CHANGELOG.md)
