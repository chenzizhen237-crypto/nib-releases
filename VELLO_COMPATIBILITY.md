# Vello compatibility

Nib draws the canvas with [Vello](https://github.com/linebender/vello), Linebender's GPU vector
renderer, compiled to WebAssembly and running on WebGPU inside Nib's Electron window. This page
says which Vello each Nib release ships, which backend draws your file, and what that means for
your hardware. Problems that come from Vello itself are listed in [KNOWN_ISSUES.md](KNOWN_ISSUES.md).

## Current release

| Component | Version | What it does in Nib |
| --- | --- | --- |
| `vello` | **0.11.0** (with `vello_encoding` / `vello_shaders` 0.11.0) | The GPU renderer: everything you see on the canvas when a GPU is available. |
| `vello_cpu` | **0.3.0** (with `vello_common` 0.3.0) | The CPU renderer: the same drawing without a GPU, used as a fallback. |
| `wgpu` / `naga` | **30.0.1** | The WebGPU layer Vello runs on; naga also translates shader fills to WGSL. |
| `peniko` / `kurbo` | 0.6.1 / 0.13.1 | Colors, brushes and geometry shared by both renderers. |
| Electron | 45 (Chromium WebGPU) | The browser engine that provides WebGPU on every platform. |

Every release's notes on the [releases page](https://github.com/chenzizhen237-crypto/nib-releases/releases)
start with a **Renderer:** line naming these versions, read from the build's lockfile.

## Vello by Nib release

| Nib | vello | vello_cpu | wgpu | Notes |
| --- | --- | --- | --- | --- |
| 0.2.5 – 0.2.11 | 0.11.0 | 0.3.0 | 30.0.1 | First releases on Vello. The GPU renderer is on by default; CPU fallback since 0.2.5. |
| 0.1.0 – 0.2.3 | — | — | — | Canvas 2D renderer; no Vello. |

There was no 0.2.4 release. A row is added whenever a release changes any of these versions.

## Which renderer draws your file

Nib picks a backend when the canvas opens and falls back on its own if the GPU fails:

1. **GPU (Vello on WebGPU).** The normal path. Blur, background blur, liquid glass and shader fills
   are Nib's own GPU passes layered on top of Vello.
2. **A new GPU device.** If the GPU device is lost (a driver reset, a sleeping eGPU), Nib asks for a
   new one once.
3. **CPU (vello_cpu).** If that fails too, or no WebGPU adapter exists, the CPU renderer draws the
   same scene. It is slower on large files. **Exports and thumbnails always use the CPU renderer.**
4. **Canvas 2D.** Only while the CPU renderer is loading, or if it fails as well.

Because the editor (GPU) and exports (CPU) use two different Vello renderers, a few effects can look
slightly different between the canvas and an exported PNG. Those cases are marked
**GPU ≠ CPU** in [KNOWN_ISSUES.md](KNOWN_ISSUES.md).

## Hardware and platforms

| Platform | Graphics API under WebGPU | Status |
| --- | --- | --- |
| macOS, Apple Silicon | Metal | **Tested** on every release. |
| Windows 10/11, x64 | Direct3D 12 | Builds and starts; not tested on AMD, Intel or NVIDIA GPUs. |
| Linux, x86_64 | Vulkan | Builds and starts; tested only with a software GPU (SwiftShader). |
| Software GPU (SwiftShader, VMs, remote desktop) | — | Works; usually ends up on the CPU renderer. |
| Intel Macs, ARM Windows/Linux, mobile | — | Not built. |

If the canvas is blank, flickers or draws wrong on Windows or Linux, please
[open an issue](https://github.com/chenzizhen237-crypto/nib-releases/issues) with your GPU model and
driver version (see [KNOWN_ISSUES.md § Reporting a renderer problem](KNOWN_ISSUES.md#reporting-a-renderer-problem)).

## Your files and Vello upgrades

`.nib` files store the design, not anything Vello-specific: a Vello upgrade never changes or
migrates your files. It can change how a few pixels look (gradient ends, anti-aliased edges, blur
radii). Before an upgrade ships, Nib's test corpus renders a set of real files on the old and new
version and the release waits until no file scores worse.

## How Nib upgrades Vello

Vello is pinned to an exact version (`=0.11.0`), so a Vello release never reaches Nib by accident.
A weekly check looks for new `vello`, `vello_cpu` and `wgpu` releases and opens a tracking issue;
the upgrade then goes through the GPU and CPU test suites, the render corpus and a manual check on
real hardware before it lands. The release that ships it adds a row to the table above and updates
[KNOWN_ISSUES.md](KNOWN_ISSUES.md).
