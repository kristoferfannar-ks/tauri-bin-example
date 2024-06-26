# Example issue repo

This repo shows a minimal example for how using `{ artifact = "bin" }` for binary dependencies, 
used with `+nightly` & `-Z bindeps`, defined in [RFC-3028](https://github.com/rust-lang/rfcs/blob/master/text/3028-cargo-binary-dependencies.md), fails in Tauri applications.

Tauri fails when its `Cargo.toml` has a binary build-dependency:

```
[build-dependencies]
bin_crate = {path = "../bin_crate", artifact = "bin" }

```

## Reproducing

* rust nightly setup using rustup 1.27.
* MacOS 14.5. Not sure how behavior is elsewhere.
* Cargo / rustc 1.78


In this repository, run

```
cargo +nightly build -p tauri-app -Z bindeps

```

This results in this failure:
```
❯ cargo +nightly build -p tauri-app -Z bindeps
   Compiling tauri-app v0.0.0 (/<dirs>/tauri-bin-builder/crates/tauri-app)
error: failed to run custom build command for `tauri-app v0.0.0 (/<dirs>/tauri-bin-builder/crates/tauri-app)`

Caused by:
  process didn't exit successfully: `/<dirs>/tauri-bin-builder/target/debug/build/tauri-app-c16440941532fbd4/build-script-build` (exit status: 1)
  --- stdout
  cargo:rerun-if-env-changed=TAURI_CONFIG
  cargo:rerun-if-changed=tauri.conf.json
  cargo:rustc-cfg=desktop
  cargo:rustc-cfg=dev
  cargo metadata command exited with a non zero exit code: error: failed to parse manifest at `/<dirs>/tauri-bin-builder/crates/tauri-app/Cargo.toml`

  Caused by:
    `artifact = …` requires `-Z bindeps` (bin_crate)

```


However, running a non-tauri application:

```
cargo +nightly build -p non-tauri-app -Z bindeps
```

```
❯ cargo +nightly build -p non-tauri-app -Z bindeps
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.06s
```
