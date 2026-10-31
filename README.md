# libheif-static

Prebuilt static libraries of [libheif](https://github.com/strukturag/libheif), [libde265](https://github.com/strukturag/libde265) and [dav1d](https://code.videolan.org/videolan/dav1d), for linking into [kmrs](https://github.com/kmworks/kmrs) release binaries.

They are built with [vcpkg](https://github.com/microsoft/vcpkg) at the commit in `vcpkg.commit`, with no encoders: x265, which is GPL, is deliberately left out.

| Target | vcpkg triplet |
|---|---|
| `x86_64-unknown-linux-gnu` | `x64-linux` |
| `aarch64-unknown-linux-gnu` | `arm64-linux` |
| `aarch64-apple-darwin` | `arm64-osx` |
| `x86_64-apple-darwin` | `x64-osx` |
| `x86_64-pc-windows-msvc` | `x64-windows-static-md` |

Linux archives are built on Ubuntu 24.04, so their glibc matches the kmrs release builds. macOS archives target macOS 11.0.

## Using an archive

Each archive keeps vcpkg's layout: `.vcpkg-root` at the top and the libraries under `installed/<triplet>`.

On Linux and macOS, point `pkg-config` at it. The `.pc` files are relocatable:

```sh
export PKG_CONFIG_PATH="$PWD/libheif-static-<target>/installed/<triplet>/lib/pkgconfig"
export SYSTEM_DEPS_LIBHEIF_LINK=static
```

On Windows, set `VCPKG_ROOT` to the extracted directory and `VCPKGRS_TRIPLET=x64-windows-static-md`.

On `x86_64-apple-darwin`, also link `libclang_rt.osx.a`: libheif's CPU feature checks need compiler-rt, which rustc does not link.

## Releasing

Update `vcpkg.commit`, then push a `v<libheif version>-<revision>` tag. The workflow builds every target and publishes the archives with `SHA256SUMS`.

## Licenses

The scripts in this repository are under the MIT license. The packaged libraries keep their own licenses, which each archive includes under `installed/<triplet>/share/<port>/copyright`:

- libheif: LGPL-3.0-only AND MIT
- libde265: LGPL-3.0-only AND DOC
- dav1d: BSD-2-Clause AND ISC
