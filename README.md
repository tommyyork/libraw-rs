# libraw-rs

[![crates.io](https://img.shields.io/crates/v/libraw-rs.svg)](https://crates.io/crates/libraw-rs)
[![Documentation](https://docs.rs/libraw-rs/badge.svg)](https://docs.rs/libraw-rs)
[![Rustc Version 1.63.0+](https://img.shields.io/badge/rustc-1.63.0+-lightgray.svg)](https://blog.rust-lang.org/2022/08/11/Rust-1.63.0.html)
[![CI](https://github.com/paolobarbolini/libraw-rs/workflows/CI/badge.svg)](https://github.com/paolobarbolini/libraw-rs/actions?query=workflow%3ACI)
![Passively Maintained](https://img.shields.io/badge/Maintenance%20Level-Passively%20Maintained-yellowgreen.svg)

### Fork Note

I made this fork primarily to use Czkawka with the Adobe DNG SDK 1.7.1, which requires some 
fairly technical [build instructions](docs/INSTALL_WITH_CZKAWKA.md). The
only platform currently covered is Mac OS X 14.

Most DNG's can be processed by Czkawka, but there are some `smartpreview` files that are failing to hash. Further investigation is needed.

## Info

Bindings to the LibRaw C APIs.

This library is still in it's early days, feel free to open a PR if a feature is missing.
Contributions and suggestions are welcome on GitHub.

## License

Licensed under either of
 * Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you shall be dual licensed as above, without any
additional terms or conditions.
