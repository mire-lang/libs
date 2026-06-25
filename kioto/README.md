# kioto — Mire standard library

Kioto is the core library for the Mire language ecosystem.
It loads via `load kioto` and provides all user-facing APIs.

## Core modules

| Module | Description | Key functions |
|--------|-------------|---------------|
| `strings` | String manipulation | `len`, `substr`, `split`, `join`, `contains`, `startswith`, `endswith`, `trim`, `replace`, `index_of`, `from_i64`, `to_i64`, `upper`, `lower` |
| `lists` | List operations | `len`, `push`, `pop`, `get`, `first`, `last`, `remove`, `sort`, `contains`, `reverse`, `unique`, `slice`, `flatten`, `is_empty` |
| `dicts` | Dictionary (map) | `len`, `keys`, `values`, `has`, `get`, `set`, `remove`, `entries`, `merge`, `is_empty` |
| `fs` | Filesystem I/O | `read`, `write`, `append`, `exists`, `is_file`, `size`, `copy`, `move`, `drop`, `list`, `mkdir`, `rmdir`, `join`, `dir`, `name`, `ext` |
| `net` | TCP + HTTP/HTTPS | `connect`, `send`, `recv`, `close`, `bind`, `accept`, `poll`, `resolve` |
| `net::http` | HTTP client + server | Client: `get`, `post`. Server: `req_method`, `req_path`, `req_query`, `req_header`, `req_body`, `server_mime`, `resp_200`, `resp_404`, `resp_302`, `serve_file` |
| `net::event` | Non-blocking I/O | `accept_one`, `has_data`, `wait` |
| `ws` | WebSocket client + server | Client: `connect`, `send::text`, `recv`, `recv::all`, `close`. Server: `server::accept`, `server::send_text`, `server::recv`, `server::recv_all`, `server::close` |
| `proc` | Process management | `run`, `exit` |
| `env` | Environment variables | `get`, `set` |
| `time` | Time utilities | `now`, `sleep` |
| `math` | Math functions | `abs`, `min`, `max`, `sqrt`, `pow`, `round`, `floor`, `ceil`, `random` |
| `log` | Logging | `info`, `warn`, `error` with `[LEVEL]` formatting |
| `cli` | CLI argument parsing | `args`, `flag`, `value` |
| `async` | Async runtime | Task spawning and coordination |
| `mem` | Memory allocation | `alloc`, `free`, `size_of` |
| `cpu` | CPU information | `cores`, `arch` |
| `term` | Terminal I/O | `print`, `read_line` |

## Extended modules

| Module | Description | Key functions |
|--------|-------------|---------------|
| `json` | JSON parser + builder | `get` (dot-path), `exists`, `type_of`, `keys`, `len`, `is_valid`, `quoted`, `number`, `bool_val`, `null_val`, `object`, `array`, `escape`, `unescape` |
| `maybe` | Option type | `some`, `none`, `is_some`, `is_none`, `unwrap`, `unwrap_or`, `map`, `and_then` |
| `result` | Result type | `ok`, `err`, `is_ok`, `is_err`, `unwrap`, `unwrap_or`, `unwrap_err`, `map`, `map_err` |
| `iter` | Iterator utilities | `range`, `count`, `nth`, `first`, `last`, `contains`, `index_of`, `is_empty` |

## Getting started

```mire
load kioto

pub fn main: () {
    # HTTP GET
    set data = net::http::get("https://httpbin.org/get")

    # JSON parsing
    set origin = json::get(data, "headers.Host")

    # File I/O
    fs::write("output.txt", origin)

    # Logging
    log::info("Saved: " + origin)

    # Result handling
    set r = result::ok("done")
    use dasu(result::unwrap(r))
}
```

## Version

**3.11.12** — See [CHANGELOG.md](CHANGELOG.md) for details.
