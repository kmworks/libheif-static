# libheif-static

Prebuilt static libraries of [libheif](https://github.com/strukturag/libheif), [libde265](https://github.com/strukturag/libde265) and [dav1d](https://code.videolan.org/videolan/dav1d), for linking into [kmrs](https://github.com/kmworks/kmrs).

They are built with [vcpkg](https://github.com/microsoft/vcpkg) at the commit in `vcpkg.commit`, with no encoders: x265, which is GPL, is deliberately left out.

| Target | vcpkg triplet |
|---|---|
| `x86_64-unknown-linux-gnu` | `x64-linux` |
| `aarch64-unknown-linux-gnu` | `arm64-linux` |
| `aarch64-apple-darwin` | `arm64-osx` |
| `x86_64-pc-windows-msvc` | `x64-windows-static-md` |

Linux archives are built on Ubuntu 24.04, so their glibc matches the kmrs release builds. macOS archives target macOS 11.0.

## Using the crate

This repository is also a drop-in replacement for the [`libheif-sys`](https://crates.io/crates/libheif-sys) crate, with the same version, features and bindings. Its build script downloads the archive for the target, verifies it against `SHA256SUMS` and links it statically, so `cargo build` needs neither `pkg-config`, vcpkg nor a system libheif.

```toml
[patch.crates-io]
libheif-sys = { git = "https://github.com/kmworks/libheif-static", rev = "<commit>" }
```

To build offline, extract an archive and point `LIBHEIF_STATIC_DIR` at it.

## Releasing

Update `vcpkg.commit`, then push a `v<libheif version>-<revision>` tag. The workflow builds every target and publishes the archives, the sources and `SHA256SUMS`.

Then copy the release's `SHA256SUMS` to the repository root and update `ARCHIVE_TAG` in `build.rs`. To follow a new `libheif-sys` release, replace `src/bindings` and the version in `Cargo.toml`.

## Licenses

The scripts in this repository are under the MIT license. The bindings come from [libheif-sys](https://github.com/Cykooz/libheif-sys) by Kirill Kuzminykh, also MIT (`LICENSE-libheif-sys`). The packaged libraries keep their own licenses, which each archive includes under `installed/<triplet>/share/<port>/copyright`, with the full LGPL and GPL texts in `licenses/`:

- libheif: LGPL-3.0-only AND MIT
- libde265: LGPL-3.0-only AND DOC
- dav1d: BSD-2-Clause AND ISC

Every release also publishes `libheif-static-sources.tar.gz`: the exact libheif and libde265 source archives vcpkg built, with the vcpkg ports and patches applied to them.
