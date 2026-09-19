# Hardware support

System requirements and per-platform hardware status. For which *image* fits
your hardware (HWE kernels, NVIDIA, ARM), see the
[User Guide](USER-GUIDE.md) and the tag reference in
[IMAGE-TAGS.md](IMAGE-TAGS.md).

## System requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **CPU** | x86_64, ARM64 | x86_64, ARM64 |
| **RAM** | 4 GB | 8 GB+ |
| **Storage** | 20 GB | 50 GB+ |

## Supported hardware (ARM laptops)

| Hardware | Status | Docs |
|----------|--------|------|
| Snapdragon X Elite (e.g. Lenovo ThinkPad X13s) | Supported via [Bonito](https://tunaos.org/docs/bonito) (ARM64) | [Snapdragon X Elite FAQ](https://tunaos.org/docs/faq); the dedicated [bonito-x13s](https://tunaos.org/docs/bonito-x13s) / [dakota-x13s](https://tunaos.org/docs/dakota-x13s) pages are archived |
| Apple Silicon (M1, M2) | In progress via [Asahi Linux](https://asahilinux.org/) — see note below | [bootc-installer-asahi](https://github.com/tuna-os/bootc-installer-asahi) |
| Apple Silicon (M3 and newer) | Not supported (no Asahi support for M3+ yet) | — |

> **Apple Silicon status.** [`ROADMAP.md`](../ROADMAP.md) is the one source of
> truth. It gives Apple Silicon support as 🟡 **in progress**
> ([#781](https://github.com/tuna-os/tunaOS/issues/781)). This row says the
> same, and does not say a flat "Supported".

What exists today: the `-asahi` images build, and a CI gate holds them
(Bonito & Grouper, 36/36 verified,
[#776](https://github.com/tuna-os/tunaOS/issues/776)). The installer track has
D0–D2 and D4 done.

What does not exist yet: the D3 installer app for macOS, a tagged release of
`bootc-installer-asahi`, and any test on real Apple hardware. The deepest test
in that repo is qemu plus U-Boot, which it calls "the deepest fidelity
achievable without Apple hardware". To install today, you must drive the Asahi
installer path by hand. If you have M1 or M2 hardware to test on, #781 is the
place to help.
