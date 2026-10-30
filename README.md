<div align="center">
  <img src="icon.png" width="96" alt="AndroidSubSystem4Linux icon">
  <h1>AndroidSubSystem4Linux</h1>
  <p><strong>Run a compact GNU/Linux userspace on rooted Android with Rurima.</strong></p>
  <p>Termux-backed runtime · KAM build system · arm64 Android</p>

  [![License](https://img.shields.io/github/license/LIghtJUNction/asl-ksu?style=flat-square)](LICENSE)
  [![Last commit](https://img.shields.io/github/last-commit/LIghtJUNction/asl-ksu?style=flat-square)](https://github.com/LIghtJUNction/asl-ksu/commits/main)
  [![Stars](https://img.shields.io/github/stars/LIghtJUNction/asl-ksu?style=flat-square)](https://github.com/LIghtJUNction/asl-ksu/stargazers)
  [![Releases](https://img.shields.io/github/v/release/KernelSU-Modules-Repo/asl?label=release&style=flat-square)](https://github.com/KernelSU-Modules-Repo/asl/releases)
</div>

<p align="center">
  <a href="#install">Install</a> ·
  <a href="#first-linux-environment">First environment</a> ·
  <a href="#build-from-source">Build</a> ·
  <a href="#project-layout">Internals</a>
</p>

<p align="center">
  <img src="Screenshot_2025-12-08-01-18-25-97_84d3000e3f4017145260f7618db1d683.jpg" width="760" alt="AndroidSubSystem4Linux running on Android">
</p>

## What this is

AndroidSubSystem4Linux is a small root module that makes a Rurima-based Linux userspace practical on Android. The current line deliberately keeps the module small by relying on Termux instead of bundling a shell framework and a large runtime.

In plain terms, the module supplies the Android-side integration; Termux supplies the userland tools; Rurima creates and enters the Linux filesystem.

Current releases:

- include Rurima/Ruri integration;
- require Termux;
- no longer bundle Zsh or the Zim framework;
- keep older, larger builds available in the release history.

This repository contains the maintained source. Signed installable packages are published through the canonical [`KernelSU-Modules-Repo/asl`](https://github.com/KernelSU-Modules-Repo/asl) release channel.

## Compatibility

| Requirement | Current contract |
| --- | --- |
| Architecture | `arm64-v8a` |
| Android API | 21–35 |
| Root environment | A root-capable Android setup able to install the module |
| Runtime dependency | Termux |
| Build/install tool | KAM 0.5.17 or newer |

## Install

### Install a published build

Download the latest package from the [canonical releases page](https://github.com/KernelSU-Modules-Repo/asl/releases), or install it with KAM:

```bash
kam install KernelSU-Modules-Repo/asl
```

Install Termux before using Rurima. The module intentionally does not duplicate the tools already supplied by Termux.

### First Linux environment

The following example creates an Alpine Edge filesystem under `/data/asl`:

```bash
su
rurima dep
mkdir -p /data/asl
cd /data/asl
rurima pull alpine:edge ./alpine
rurima r ./alpine
```

Image names are available from [Linux Containers images](https://images.linuxcontainers.org/).

If the environment starts but has no network access, recreate or edit its `/etc/resolv.conf` and add a reachable DNS resolver. This is a filesystem configuration issue inside the pulled environment, not a reason to reinstall the Android module.

## Build from source

```bash
git clone https://github.com/LIghtJUNction/asl-ksu.git
cd asl-ksu
kam build
```

The package is written to the configured `dist/` output directory. To build and install on the connected device in one flow:

```bash
kam install
```

Do not commit signing keys. CI signing expects the private key through the `KAM_PRIVATE_KEY` repository secret.

## Project layout

```text
kam.toml       Module metadata and KAM build contract
hooks/         Pre-build and post-build automation
crates/        Native/Rust components
kam.sh         Project-specific build entry
module.prop    Android module metadata
```

KAM hooks are ordinary scripts executed at defined build stages. They are used here for repeatable packaging, metadata updates, dependency synchronization and release signing rather than hiding those steps inside an opaque build service.

## Removal note

A pulled Rurima environment may mark `.rurienv` immutable. Remove that attribute before deleting the filesystem:

```bash
chattr -i /data/asl/alpine/.rurienv
rm -rf /data/asl/alpine
```

Adjust the path to match the environment you created.

## Origin and license

This project originated from [`RuriOSS/asl`](https://github.com/RuriOSS/asl) and now uses a substantially smaller, Termux-dependent design. Preserve upstream attribution when redistributing modified builds.

Released under the [MIT License](LICENSE).
