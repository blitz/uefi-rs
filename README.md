# uefi-rs

[![Crates.io](https://img.shields.io/crates/v/uefi)](https://crates.io/crates/uefi)
[![Docs.rs](https://docs.rs/uefi/badge.svg)](https://docs.rs/uefi)
![Stars](https://img.shields.io/github/stars/rust-osdev/uefi-rs)
![License](https://img.shields.io/github/license/rust-osdev/uefi-rs)
![Build status](https://github.com/rust-osdev/uefi-rs/workflows/Rust/badge.svg)

## Description

[UEFI] is the successor to the BIOS. It provides an early boot environment for
OS loaders, hypervisors and other low-level applications. While it started out
as x86-specific, it has been adopted on other platforms, such as ARM.

This crate makes it easy to both:
  - Write UEFI applications in Rust (for `i686`, `x86_64`, or `aarch64`)
  - Call UEFI functions from an OS (usually built with a [custom target][rustc-custom])

The objective is to provide **safe** and **performant** wrappers for UEFI interfaces,
and allow developers to write idiomatic Rust code.

Check out [the UEFI application template](template) for a quick start.

[UEFI]: https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface
[rustc-custom]: https://doc.rust-lang.org/rustc/targets/custom.html

![uefi-rs running in QEMU](https://imgur.com/SFPSVuO.png)

## Project structure

This project contains multiple sub-crates:

- `uefi` (top directory): defines the standard UEFI tables / interfaces.
  The objective is to stay unopinionated and safely wrap most interfaces.

  **Optional crate features:**

  - `alloc`: Enables functionality requiring the `alloc` crate from the Rust standard library.
    - For example, this allows many convenient `uefi-rs` functions to operate on heap data (`Box`).
    - It is up to the user to provide a `#[global_allocator]`.
  - `global_allocator`: implements a `#[global_allocator]` using UEFI functions.
    - This allows you to use all abstractions from the `alloc` crate from the Rust standard library
      during runtime. Hence, `Vec`, `Box`, etc. will be able to allocate memory.
      **This is optional**, so you can provide a custom `#[global_allocator]` as well.
    - There's no guarantee of the efficiency of UEFI's allocator.
  - `logger`: logging implementation for the standard [`log`] crate.
    - Prints output to UEFI console.
    - No buffering is done: this is not a high-performance logger.

- `uefi-macros`: procedural macros that are used to derive some traits in `uefi`.

- `uefi-services`: provides a panic handler, and initializes the `alloc` / `logger` features.

- `uefi-test-runner`: a UEFI application that runs unit / integration tests.

[log]: https://github.com/rust-lang-nursery/log

## Documentation

The [uefi-rs book] contains a tutorial, how-tos, and overviews of some
important UEFI concepts.

Reference documentation can be found on docs.rs:
- [docs.rs/uefi](https://docs.rs/uefi)
- [docs.rs/uefi-macros](https://docs.rs/uefi-macros)
- [docs.rs/uefi-services](https://docs.rs/uefi-services)

For additional information, refer to the [UEFI specification][spec].

[spec]: http://www.uefi.org/specifications
[uefi-rs book]: https://rust-osdev.github.io/uefi-rs/HEAD

## Building and testing uefi-rs

Use the `cargo xtask` command to build and test the crate.

Available commands:
- `build`: build all the UEFI packages
  - `--release`: build in release mode
  - `--target {x86_64,ia32,aarch64}`: choose target UEFI arch
- `clippy`: run clippy on all the packages
  - `--target {x86_64,ia32,aarch64}`: choose target UEFI arch
  - `--warnings-as-errors`: treat warnings as errors
- `doc`: build the docs for the UEFI packages
  - `--open`: open the docs in a browser
  - `--warnings-as-errors`: treat warnings as errors
- `run`: build `uefi-test-runner` and run it in QEMU
  - `--ci`: disable some tests that don't work in the CI
  - `--disable-kvm`: disable hardware accelerated virtualization support in QEMU.
                     Especially useful if you want to run the tests under
                     [WSL](https://docs.microsoft.com/en-us/windows/wsl) on Windows.
  - `--example <NAME>`: run an example instead of the main binary.
  - `--headless`: run QEMU without a GUI
  - `--ovmf-code <PATH>`: path of an OVMF code file
  - `--ovmf-vars <PATH>`: path of an OVMF vars file
  - `--release`: build in release mode
  - `--target {x86_64,ia32,aarch64}`: choose target UEFI arch
- `test`: run unit tests and doctests on the host

The `uefi-test-runner` directory contains a sample UEFI app which exercises
most of the library's functionality.

Check out the testing project's [`README.md`](uefi-test-runner/README.md) for
prerequisites for running the tests.

## Building UEFI programs

For instructions on how to create your own UEFI apps, see the [BUILDING.md](BUILDING.md) file.

The uefi-rs crates currently require some [unstable features].
The nightly MSRV is currently 2022-08-08.

[unstable features]: https://github.com/rust-osdev/uefi-rs/issues/452

## Contributing

We welcome issues and pull requests! For instructions on how to set up a development
environment and how to add new protocols, check out [CONTRIBUTING.md](CONTRIBUTING.md).

## License

The code in this repository is licensed under the Mozilla Public License 2.
This license allows you to use the crate in proprietary programs, but any modifications to the files must be open-sourced.

The full text of the license is available in the [license file](LICENSE).
