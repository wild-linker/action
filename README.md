# Wild GitHub action

Depend on this action to use Wild when building Rust code for Linux or WebAssembly.

See the main [wild repo](https://github.com/wild-linker/wild) for more details about wild.

Example:

```yml
name: ci
on: [push, pull_request]

jobs:
  ci:
    name: CI
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: dtolnay/rust-toolchain@stable
      - uses: wild-linker/action@latest
      - run: cargo test
```

The above workflow will use the latest version of wild. If you'd like to pin to a specific version,
you can instead do:

```yml
      - uses: wild-linker/action@0.9.0
```

This action writes the following to `~/.cargo/config.toml`.
`/path/to/wild` is the `wild` binary unpacked under the runner temp directory.

```toml
[target.${target_arch}-unknown-linux-gnu]
linker = "clang"
rustflags = ["-Clink-arg=--ld-path=/path/to/wild"]

[target.${target_arch}-unknown-linux-musl]
linker = "clang"
rustflags = ["-Clink-arg=--ld-path=/path/to/wild"]
```

Wild 0.11.0 and newer also link WebAssembly. For `wasm32-unknown-unknown`, `wasm32-wasip1`, and `wasm32-wasip1-threads`, the action adds:

```toml
[target.wasm32-wasip1]
rustflags = ["-C", "linker=/path/to/wild", "-C", "linker-flavor=wasm-ld"]
```

The same `rustflags` are written for the other two Wasm targets.

An existing `[target.<triple>]` section is left unchanged. If you specify `rustflags` via a
`.cargo/config.toml` in your repository or by setting `RUSTFLAGS`, then that will override the
effect of this action.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `wild-version` | `0.11.0` | Wild version to install. |
| `download-retries` | `5` | Number of times to retry the download on transient failures (passed to `curl --retry`). curl uses exponential backoff between retries (1s, 2s, 4s, …), so the default of 5 retries spans roughly 30 seconds. |

Example overriding the retry count:

```yml
      - uses: wild-linker/action@latest
        with:
          download-retries: "6"
```
