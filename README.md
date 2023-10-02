# GitHub Action Demo for Cross-Compiling for Raspberry Pi (ARM64)

## Introduction

This repo acts as a demo for how a [GitHub
Action](https://github.com/dp0/action-raspberry-pi-cross-compile/actions/workflows/cross-compile.yml)
can be used to cross-compile for an ARM64 Raspberry Pi running Raspberry Pi OS
(as of 08 August 2023).

## The Problem

Cross-compiling on some systems may result in the libc version used at
link-time not being compatible with the run-time version, and
you'll get an error similar to:

```
# Try and run a binary cross built elsewhere...
pi@raspberrypi:~ $ ./hello
./hello: /lib/aarch64-linux-gnu/libc.so.6: version `GLIBC_2.34' not found (required by ./hello)
```

And you can see why this is a problem:

```
# View which GLIBC versions it was linked against...
pi@raspberrypi:~ $ objdump -T hello | grep -Po 'GLIBC_[0-9.]+' | sort | uniq
GLIBC_2.17
GLIBC_2.34

# View which GLIBC versions are available locally...
pi@raspberrypi:~ $ objdump -T /lib/aarch64-linux-gnu/libc.so.6 | grep -Po 'GLIBC_[0-9.]+' | sort | uniq
GLIBC_2.17
GLIBC_2.18
GLIBC_2.22
GLIBC_2.23
GLIBC_2.24
GLIBC_2.25
GLIBC_2.26
GLIBC_2.27
GLIBC_2.28
GLIBC_2.29
GLIBC_2.30
```

## A Solution

Raspberry Pi OS is based on Debian 11, so a solution is to cross compile in a
Debian environment, such as a `debian:11` container running on a GitHub Action.
No need to download mystery binaries/toolchains!

You can find the GitHub Action configuraton for this repo
[here in `cross-compile.yml`](https://github.com/dp0/action-raspberry-pi-cross-compile/blob/main/.github/workflows/cross-compile.yml).

### Usage

Simply copy the `cross-compile.yml` file into your own repo under
`.github/workflows` and adjust the compile steps/`cmake` config for your own
project.

### Caveats

This repo will age, and I'm sure it'll become outdated! If you're from the
future, using `debian:11` may be too old. Check which distribution your target
version of Raspberry Pi OS is based on and adjust accordingly.
