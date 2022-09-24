# M1xxx

[![Build](https://github.com/fwcd/m1xxx/actions/workflows/build.yml/badge.svg)](https://github.com/fwcd/m1xxx/actions/workflows/build.yml)
<!-- [![Automerge](https://github.com/fwcd/m1xxx/actions/workflows/automerge.yml/badge.svg)](https://github.com/fwcd/m1xxx/actions/workflows/automerge.yml) -->

Unofficial Apple Silicon (arm64 macOS) builds of the free and open source DJ software [Mixxx](https://mixxx.org/).

![Screenshot](images/screenshot.png)

## Background

Mixxx currently only provides builds for x86-64 macOS. While these run fine under Rosetta 2, native Mixxx builds for arm64 run at roughly half the CPU load and feel noticeably more snappy than the x86-64 version.

Mixxx requires Qt 5.12 due to a breaking change in the OpenGL views that underpin waveform rendering, the first version of Qt that supports arm64 macOS, however, is 5.15. Therefore the linked `vcpkg` fork [backports the required patches](https://github.com/fwcd/vcpkg/tree/arm64-osx-mixxx/overlay/osx/qt5-base/patches) for supporting Qt 5.12 on arm64 macOS.

In short, this repo builds Mixxx's dependencies, including a patched version of Qt, and Mixxx itself by cross-compiling to arm64 macOS in GitHub Actions.

## Upstreaming process

While the `main` branch of Mixxx can already be compiled for arm64 macOS using the right flags ([see the CI workflow](https://github.com/fwcd/m1xxx/blob/3595ff0f1da65a9ad7504a70f2d693d55dcf2b19/.github/workflows/build.yml#L94-L108)), [the patches to the dependencies in `vcpkg`](https://github.com/fwcd/vcpkg/compare/fwcd:f9cdab3...fwcd:cbaa173) have not been (completely) upstreamed yet.

## Building natively on Apple Silicon (arm64 macOS) hosts

While the CI workflows in this repo mainly deal with the case of cross-compiling Mixxx from x86_64 macOS runners to arm64, it is also possible to compile directly from an arm64 macOS (Apple Silicon) host.

First, make sure to have the Homebrew installed and the submodules in this repo checked out (using `git submodule update`). To install the system dependencies, run:

```sh
scripts/install-brew-deps
```

To compile and install Mixxx's dependencies into `vcpkg` run:

```sh
scripts/bootstrap-vcpkg
scripts/install-vcpkg-deps
```

To build Mixxx, run:

```sh
export VCPKG_ROOT="$(pwd)/vcpkg"
export VCPKG_DEFAULT_TRIPLET=arm64-osx

cd mixxx
cmake -B build -G Ninja
cmake --build build
```

## Credits

Thanks to [`daschuer`](https://github.com/daschuer) for the work involved in backporting the relevant Qt patches, parts of the CI workflows and assistance in building the dependencies!
