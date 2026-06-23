# Kioto Changelog

## [3.11.11] — 2026-06-24

### Changed
- **Modularized `net`**: `http_get` → `net::http::get`, `http_post` → `net::http::post`,
  `connect_timeout` → `net::connect::timeout`, `recv_all` → `net::recv::all`,
  `set_nonblock` → `net::socket::nonblock`. All underscore-separated function
  names are now namespace-separated with `::`.
- **Modularized `ws`**: `send_text` → `ws::send::text`, `recv_all` → `ws::recv::all`.
- **Modularized `async`**: `is_done` → `async::task::done`, `is_error` → `async::task::error`.
- `extern fn` declarations are now **private by default** (not exported).
  Use `pub extern fn` to export.

### Removed
- **WASM module** (`ext/wasm/`): removed browser bridge.

## [3.11.10] — 2026-06-24

### Added
- **WebSocket module** (`ext/ws/`): `ws::connect(url)`, `send_text`, `recv`, `recv_all`, `close`.
  - Auto-detects `ws://` (plain TCP) vs `wss://` (TLS over OpenSSL).
  - Default ports: 80 for WS, 443 for WSS.
  - Frame encoding with RFC 6455 client-side masking.
  - WSS handshake: TCP connect → TLS handshake → WebSocket upgrade, all via `SSL_read`/`SSL_write`.
- **HTTPS support in net module**: `http_get` and `http_post` now auto-detect `https://` scheme
  and use TLS via OpenSSL (port 443 default).
- **WASM browser bridge** (`ext/wasm/`): `eval`, `get_element_by_id`, `set_inner_html`,
  `create_element`, `append_child`, `set_attribute`, `set_style`, `remove_element`,
  `add_event_listener`, `local_storage_get/set`, `fetch`, `console_log`, `alert`.
  - On native (non-WASM) targets, all functions are no-ops.

### Changed
- `net::http_post` refactored to use the same modular helpers as `http_get`
  (`http_connect`, `http_send`, `http_recv`, `http_close`, `extract_body`).
- `net` module: added `build_post_request` helper for POST body construction.
- All `:&i64` parameter types changed to `:i64` (Mire does not support `&i64` references).

### PAL (Platform Abstraction Layer) — added in Mire
- **Networking PAL** (`pal_net_*`): TCP connect, send, recv, close, poll, resolve
  (POSIX sockets + poll + DNS).
- **TLS PAL** (`pal_tls_*`): OpenSSL-based `SSL_connect`, `SSL_read`, `SSL_write`, `SSL_shutdown`.
- **WebSocket PAL** (`pal_ws_*` + `pal_wss_*`): RFC 6455 handshake, frame encode/decode,
  client-side masking, base64 key encoding.
- **WASM PAL** (`pal_wasm_*`): Emscripten `EM_ASM` JS bridge for browser targets.

### Fixed
- `\r` (carriage return) escape now supported in Mire string literals (required for HTTP/WS `\r\n` line endings).
- `ws::connect` no longer segfaults with module-level variables (was `&i64` type issue).

---

## [3.11.9] — 2026-05-01

### Added
- `net` module with TCP wrappers: `connect`, `send`, `recv`, `recv_all`, `close`, `poll`.
- `http_get(url)` and `http_post(url, body, content_type)` via raw TCP.
- URL parsing helpers: `extract_host`, `extract_port`, `extract_path`.

---

## [3.11.0] — 2026-03-15

### Added
- Initial stable release with core modules: strings, lists, dicts, fs, env, proc, time, math, async.
- Extension modules: types, maybe, result, tuple, iter, json.
- `owl.toml` package manifest with `[exports]` for module resolution.
