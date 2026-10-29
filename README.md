# How deep can your machine go? 🛗

A one-page field guide to running open-weight LLMs locally, on a Mac, a shared-memory mini PC, or a
gaming GPU.

Models are drawn as floors in an elevator shaft: small models sit near the surface and the giants
are far below. Every machine's elevator only goes so deep, and its **stop** marks where it runs out of
memory. The shaft's built-in stops are reference Macs; add your own in two steps:

1. **Pick a memory type:** Unified or GPU VRAM.
2. **Enter how much you have**, or pick a preset (🍎 Macs for unified, 🎮 popular gaming cards for VRAM).

| Mode | Enter | Usable estimate |
|---|---|---|
| **Unified** | Total RAM on Apple Silicon or another shared-memory machine (AMD Strix Halo, NVIDIA DGX Spark) | ~67% (≤36GB) or ~75%, leaving room for the OS and a few apps |
| **GPU VRAM** | A dedicated GPU's VRAM, summed across cards (presets: RTX 5060 through RTX 5090, RX 9070 XT, RX 7900 XTX) | ~90%, after driver and display overhead |

VRAM mode assumes the whole model fits on the GPU. Partial offload to system RAM (llama.cpp layer
splitting, MoE experts on CPU) is out of scope.

## What's on each floor

| Field | Meaning |
|---|---|
| **Q4 / Q8 memory** | Approximate weight footprint at 4-bit and 8-bit quantization |
| **Models** | Notable open-weight releases from the past year at that size, linked to Hugging Face |
| **Comparable Claude model** | The Claude tier you'd reach for on the same kind of task (not a claim of equal performance) |
| **Popularity** | A rough three-step signal from OpenRouter token usage and Hugging Face downloads |

Reference stops are shown for 32GB and 64GB MacBook Pros and 128 / 256 / 512GB Mac Studios. A **solid** box is
the last floor that fits; a **dashed** box is the recommended stop if you want to keep other apps open.

## Rules of thumb

- **Weights:** about 0.6GB per billion parameters at 4-bit, about 1.1GB at 8-bit. Leave room for
  context and the OS on top of that.
- **Mixture-of-experts:** an MoE model needs memory for *all* its parameters but runs at the speed of
  the few active per token.
- **The macOS GPU cap:** by default the GPU can use roughly two-thirds to three-quarters of unified
  memory. You can raise it with `sudo sysctl iogpu.wired_limit_mb=<MB>` (resets on reboot).

## Usage

No build step and no dependencies. Open `index.html` in a browser:

```sh
open index.html
```

The page loads fonts from Google Fonts and otherwise works offline.

## Updating the data

All model and device data lives in a JSON block inside `index.html` (look for `"levels"`,
`"devices"`, and `"gpus"`). To add a model, append it to the matching size level's `models` array; to change the
memory budget assumptions, edit `appsReserveGB` and `contextReserveGB`. Bump `"updated"` when you do.

## Caveats

Memory figures are approximate and vary by quant format and runtime. Check each model card before you
download 400GB of weights.

## License

[MIT](LICENSE) © 2026 Mattie Niznik
