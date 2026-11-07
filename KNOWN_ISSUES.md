# Known issues

Problems we know about in the current release (**0.2.11**), what you'll see, and what to do about
them. Most of the rendering issues come from [Vello](https://github.com/linebender/vello), the
renderer Nib uses (versions in [VELLO_COMPATIBILITY.md](VELLO_COMPATIBILITY.md)); each links to its
upstream report. Last checked 2026-11-07 against vello 0.11.0 and vello_cpu 0.3.0.

**GPU ≠ CPU** marks a case where the canvas (GPU renderer) and exports or thumbnails (CPU renderer)
can look different.

## Rendering: images

| What you see | When | Workaround | Upstream |
| --- | --- | --- | --- |
| An image fill draws blank, on every frame, in a file with very many or very large images. | Image-heavy files (hundreds of MB of pictures on one page). The renderer's image atlas is full and silently leaves an image out. | Split the images across pages or frames, or downscale very large pictures before importing. | [vello#2011](https://github.com/linebender/vello/issues/2011), [#788](https://github.com/linebender/vello/issues/788) |
| Images look slightly soft or aliased when zoomed far out. | Zoom far below 100% on a large image. | Cosmetic; exports at 1× and above are unaffected. | [vello#1289](https://github.com/linebender/vello/issues/1289) |

Nib already works around Vello's "images turn transparent after a frame without images" bug
([vello#1948](https://github.com/linebender/vello/issues/1948), [#1809](https://github.com/linebender/vello/issues/1809)).
If you still see an image vanish after switching pages, please report it.

## Rendering: gradients

| What you see | When | Workaround | Upstream |
| --- | --- | --- | --- |
| **GPU ≠ CPU.** A gradient whose first stop isn't at 0% (or last stop isn't at 100%) is stretched on the canvas, but holds the end color in exports. | Gradients with stops that don't reach the ends, common in Figma and Sketch imports. | Add stops at 0% and 100% with the end colors; both renderers then agree. | [vello#2002](https://github.com/linebender/vello/issues/2002) |
| Hard color stops (two stops at the same position) shift by up to a pixel as the gradient gets longer. | Striped or hard-edged linear gradients on the canvas. | Cosmetic. | [vello#1975](https://github.com/linebender/vello/issues/1975) |
| A one-pixel white line at a hard stop in an export. | Two stops a hair apart (what some SVG and design-file importers produce), CPU renderer only. | Put both stops at exactly the same position. | [vello#1998](https://github.com/linebender/vello/issues/1998) |
| Visible banding in soft, large gradients. | Subtle gradients over large areas, both renderers. | Add a little noise texture on top. | [vello#856](https://github.com/linebender/vello/issues/856) |

## Rendering: shadows, blur and masks

| What you see | When | Workaround | Upstream |
| --- | --- | --- | --- |
| **GPU ≠ CPU.** Drop shadows on rectangles and circles look a little tighter in exports than on the canvas. | Box and circle drop shadows exported to PNG/JPG/WebP/PDF, or in thumbnails. | Increase the blur slightly if the export must match exactly. | [vello#2043](https://github.com/linebender/vello/issues/2043) |
| **GPU ≠ CPU.** A masked group inside a layer blur is misplaced or missing in exports. | Layer blur applied over a group that uses a mask, CPU renderer. | Flatten the mask (or the blurred group) before exporting. | [vello#2004](https://github.com/linebender/vello/pull/2004) |

## Rendering: edges and text

| What you see | When | Workaround | Upstream |
| --- | --- | --- | --- |
| A faint hairline seam where two shapes meet edge to edge, or at a frame's clip over its own fill. | Shapes that share an edge exactly (tiles, split buttons, clipped frames). | Overlap the shapes by a fraction of a pixel, or merge them. | [vello#49](https://github.com/linebender/vello/issues/49), [#1953](https://github.com/linebender/vello/issues/1953) |
| **GPU ≠ CPU.** Small text looks slightly lighter in exports than on the canvas. | Text at body sizes and below, CPU renderer. | Export at 2× for small text. | [vello#2036](https://github.com/linebender/vello/issues/2036) |
| A blend mode looks wrong inside a clipped frame. | A layer with a blend mode inside a frame with *Clip content*. Not confirmed in Nib yet. | Report the file if you hit it. | [vello#1198](https://github.com/linebender/vello/issues/1198) |

## Exports and very large canvases

| What you see | When | Workaround | Upstream |
| --- | --- | --- | --- |
| Export fails, or the renderer stops and the canvas falls back, on a huge export. | Any exported image wider or taller than about 65,000 px (for example a very wide frame at 4×), or a very dense file at a huge size. | Export at a lower scale, or export parts of the frame separately. | [vello#2040](https://github.com/linebender/vello/issues/2040), [#2037](https://github.com/linebender/vello/issues/2037) |
| Pieces of imported vector art draw wrong or go missing. | Paths with extremely large coordinates or near-cusp curves (some imported SVGs). | Scale the artwork down, or simplify the path. | [vello#1277](https://github.com/linebender/vello/issues/1277), [#581](https://github.com/linebender/vello/issues/581), [#1139](https://github.com/linebender/vello/issues/1139) |
| A very dense frame draws wrong for a frame or two. | Extremely complex frames (tens of thousands of paths) on the GPU. | Zoom or pan once to redraw; split the frame if it persists. | [vello#366](https://github.com/linebender/vello/issues/366) |

## Graphics hardware

| What you see | When | Workaround | Upstream |
| --- | --- | --- | --- |
| Strokes draw wrong, or the canvas is blank or flickers. | Windows or Linux on AMD, Intel or NVIDIA GPUs. These aren't tested yet. | Update your graphics driver. Nib falls back to the CPU renderer when the GPU fails; please report your GPU and driver. | [vello#2023](https://github.com/linebender/vello/issues/2023), [#690](https://github.com/linebender/vello/issues/690) |
| The canvas is slower than usual. | No WebGPU adapter (virtual machines, remote desktop, very old GPUs): Nib is drawing on the CPU. | Expected; use a machine with a supported GPU for large files. | — |

## Install and updates

| What you see | When | Workaround |
| --- | --- | --- |
| macOS blocks the first launch, or says the app "is damaged". | Every macOS install: the app isn't notarized. | Right-click ▸ Open, or `xattr -dr com.apple.quarantine /Applications/Nib.app`. See the [README](README.md#macos). |
| macOS doesn't update itself. | Every macOS release: unsigned apps can't update in place. | Nib tells you a new version is out; download the new `.dmg` and replace the app. Your files are kept. |
| Windows SmartScreen warns about the installer. | The installer isn't code-signed. | **More info ▸ Run anyway**. |
| The AppImage won't start. | Linux distributions without FUSE 2. | Install `libfuse2` (Ubuntu 22.04 and later). |
| No download for my machine. | Intel Macs, ARM Windows or Linux. | Not built yet. |

## Reporting a renderer problem

Open an [issue](https://github.com/chenzizhen237-crypto/nib-releases/issues) with:

- your Nib version (**Settings ▸ Updates**) and platform;
- your GPU model and driver version;
- whether it happens on the canvas, in exports, or both;
- a screenshot, and the file (or a small part of it) if you can share it.

When an upstream Vello fix ships, Nib upgrades (see
[VELLO_COMPATIBILITY.md § How Nib upgrades Vello](VELLO_COMPATIBILITY.md#how-nib-upgrades-vello))
and the row moves out of this page.
