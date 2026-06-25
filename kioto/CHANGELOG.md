# kioto changelog

## 3.11.12 — 2025-06-25

### New modules

#### json — Full JSON parser, navigator, and builder (`e1a7d5a`)
```mire
set data = json::get(response, "user.name")
set age  = json::get(response, "user.age")    # "30"
set ok   = json::is_valid("{...}")
set t    = json::type_of(data, "items")       # "object" | "array" | "string" | ...
set keys = json::keys(obj, "")                # newline-separated key names
set n    = json::len("[1,2,3]", "")           # 3
set out  = json::object(pairs_vec)            # {"k":"v",...}
set out  = json::array(items_vec)             # [a,b,c]
set safe = json::quoted("hello \"world\"")    # escaped + quoted
```
Supports dot-path navigation (`user.tags.0.name`), array indexing, nested objects,
all JSON types (string, number, bool, null, object, array), and string escape/unescape.

#### maybe — Option type (`071d7d3`)
```mire
set m = maybe::some("hello")
if maybe::is_some(m) { use dasu(maybe::unwrap(m)) }
set v = maybe::unwrap_or(maybe::none(), "fallback")
set m2 = maybe::map(m, "mapped")
set m3 = maybe::and_then(m, next_maybe)
```
Tagged-string encoding (`"some:value"` / `"none"`).

#### result — Result type (`071d7d3`)
```mire
set r = result::ok("success")
set e = result::err("fail")
if result::is_ok(r) { use dasu(result::unwrap(r)) }
set v2 = result::unwrap_or(e, "default")
set em = result::unwrap_err(e)
set r2 = result::map(r, "mapped")
set r3 = result::map_err(e, "better error")
```
Tagged-string encoding (`"ok:value"` / `"err:message"`).

#### iter — Iterator utilities (`071d7d3`)
```mire
set parts = strings::split("a\nb\nc", "\n")
set n  = iter::count(parts)       # 3
set f  = iter::first(parts)       # "a"
set l  = iter::last(parts)        # "c"
set el = iter::nth(parts, 1)      # "b"
if iter::contains(parts, "b") { ... }
set idx = iter::index_of(parts, "b")
set rng = iter::range(0, 5)       # "0\n1\n2\n3\n4"
```
Operates on `vec[str]` (the standard Mire vector type).

#### ws — WebSocket client + server (`392067b`)
```mire
# Client (ws:// and wss:// with auto TLS)
set fd = ws::connect("ws://echo.example.com/")
ws::send::text(fd, "hello")
set msg = ws::recv::all(fd)
ws::close(fd)

# Server
ws::server::accept(client_fd)
set msg = ws::server::recv(client_fd, 65536)
ws::server::send_text(client_fd, "echo: " + msg)
ws::server::close(client_fd)
```
Client frames automatically masked per RFC 6455. Server frames unmasked.
Server PAL per RFC 6455 SS5.2–5.3 (handshake, frame encode/decode, XOR masking,
base64 encoding).

#### net::http server — Request parser + response builder (`757abc2`)
```mire
set method = net::http::req_method(raw)       # "GET" / "POST"
set path   = net::http::req_path(raw)         # "/api/data"
set qs     = net::http::req_query(raw)        # "a=1&b=2"
set host   = net::http::req_header(raw, "Host")
set body   = net::http::req_body(raw)         # after \r\n\r\n
set ct     = net::http::server_mime("style.css")  # MIME type
set resp   = net::http::resp_200(body, "text/html")
set notf   = net::http::resp_404()
set rd     = net::http::resp_302("/new-location")
net::http::serve_file(fd, "index.html")
```

### Changes

- **ws:** Server-side PAL functions added (`pal_ws_server_accept/send_text/recv/close`)
  with corresponding LLVM IR declarations. Client PAL rewired to use existing
  `pal_net_*` / `pal_tls_*` functions. (`418f48f` in mire)
- **ws:** Fixed `owl.toml` to export `server` submodule. (`d5cde03`)
- **maybe/result/iter:** Replaced dict-based stubs with tagged-string implementations
  that avoid LLVM codegen edge cases.
- **json:** Moved from planned `core/json` to `ext/json` to match export path.
  All helpers use `&str` borrows to avoid Mire ownership conflicts.
- **http server:** Flattened into `net::http` module to avoid 3-level module
  nesting (`kioto::net::http::server`). Functions prefixed (`server_mime`,
  `req_method`, `resp_200`, etc.) to avoid conflicts with existing client API.

### Design notes

- **Module nesting > 2 levels** requires `owl.toml` in every intermediate directory.
  When possible, flatten to 2 levels or use prefixed function names.
- **`&str` everywhere:** Mire's borrow checker is conservative — passing owned `str`
  to helper functions that consume it prevents further use of the value in the caller.
- **`str mut` building:** Long inline string concatenation can trigger codegen issues.
  Using intermediate variables or delegating to builder functions is more reliable.
- **No binary data in Mire strings:** Mire `str` is UTF-8/C-string. WebSocket frame
  construction requires C PAL functions for binary headers and XOR masking.
- **`rt_vec_get_str` spacing:** `rt_vec_get_str(v(i+1))` is parsed as function call.
  Use `rt_vec_get_str(v, i+1)` or `rt_vec_get_str(v(i+1))` (no space before `(`).
