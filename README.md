# Wild GitHub action

Depend on this action to use Wild when building rust code.

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

This action writes the following to `~/.cargo/config.toml`:

```toml
[target.${target_arch}-unknown-linux-(gnu|musl)]
linker = "clang"
rustflags = ["-Clink-arg=--ld-path=${{ github.action_path }}/wild"]
```

If you specify `rustflags` via a `.cargo/config.toml` in your repository or by setting `RUSTFLAGS`,
then that will override the effect of this action.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `wild-version` | `0.8.0` | Wild version to install. |
| `download-retries` | `5` | Number of times to retry the download on transient failures (passed to `curl --retry`). curl uses exponential backoff between retries (1s, 2s, 4s, …), so the default of 5 retries spans roughly 30 seconds. |

Example overriding the retry count:

```yml
      - uses: wild-linker/action@latest
        with:
          download-retries: "6"
```
