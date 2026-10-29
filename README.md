# How deep can your machine go? 🛗

A one-page field guide to running open-weight LLMs locally, on a Mac, a shared-memory mini PC, or a
gaming GPU.

**Live page:** https://niznik-dev.github.io/local-llm-elevator/

Models are drawn as floors in an elevator shaft: small models sit near the surface and the giants
are far below. Every machine's elevator only goes so deep, and its **stop** marks where it runs out of
memory. The shaft's built-in stops are reference Macs; add your own in two steps:

1. **Pick a memory type:** Unified or GPU VRAM (the **?** explains the difference).
2. **Pick a preset or type your own amount:** 🍎 Macs for unified, 🎮 gaming cards for VRAM. Press
   Enter or ↓ to ride down to your stop; **Clear** removes it.

| Mode | Enter | Usable estimate |
|---|---|---|
| **Unified** | Total RAM on Apple Silicon or another shared-memory machine (AMD Strix Halo, NVIDIA DGX Spark) | ~67% (≤36GB) or ~75%, leaving room for the OS and a few apps |
| **GPU VRAM** | A dedicated GPU's VRAM, summed across cards (presets, one per size: RTX 5060 8GB, RTX 5070 12GB, RX 9070 XT 16GB, RX 7900 XTX 24GB, RTX 5090 32GB) | ~90%, after driver and display overhead |

VRAM mode assumes the whole model fits on the GPU. Partial offload to system RAM (llama.cpp layer
splitting, MoE experts on CPU) is out of scope.

Once you've set a stop, the elevator parks there and everything deeper dims, so you can see at a
glance what's out of reach. The rider's shirt and your stop take the vendor's color when you pick a
GPU preset (NVIDIA green, AMD red) and stay orange otherwise.

## What's on each floor

| Field | Meaning |
|---|---|
| **Q4 / Q8 memory** | Approximate weight footprint at 4-bit and 8-bit quantization |
| **Models** | Notable open-weight releases from the past year at that size, linked to Hugging Face |
| **🏢 Data center** | How many 80GB H100s or ~180GB B200s hold the 8-bit (FP8) weights, at 90% usable per card (minimums, not deployment-shaped counts) |
| **Comparable Claude model** | The Claude tier you'd reach for on the same kind of task (not a claim of equal performance) |
| **Popularity** | A rough three-step signal from OpenRouter token usage and Hugging Face downloads |
| **Gem** | Decoration. It's a mine shaft, so gems get roughly pricier the deeper you go (quartz to red diamond, then antimatter). They say nothing about the model |

Reference stops are shown for 32GB and 64GB MacBook Pros (💻) and 128 / 256 / 512GB Mac Studios (🗄️). A
**solid** box is the last floor that fits; a **dashed** box is the recommended stop if you want to keep
other apps open.

## Rules of thumb

- **Weights:** about 0.6GB per billion parameters at 4-bit, about 1.1GB at 8-bit. Leave room for
  context and the OS on top of that.
- **Mixture-of-experts:** an MoE model needs memory for *all* its parameters but runs at the speed of
  the few active per token.
- **Shared-memory caps:** on a Mac, the GPU can use roughly two-thirds to three-quarters of unified
  memory by default; raise it with `sudo sysctl iogpu.wired_limit_mb=<MB>` (resets on reboot). Strix
  Halo sets the GPU's share in the BIOS or AMD's Variable Graphics Memory; DGX Spark has no fixed split.
- **The VRAM wall:** a discrete card has no cap to raise. What's on the card is what you get, minus a
  little for the driver and display.

## Usage

No build step and no dependencies. Open `index.html` in a browser:

```sh
open index.html
```

The page loads fonts from Google Fonts and otherwise works offline.

## Updating the data

All model and device data lives in a JSON block inside `index.html`:

| Key | Holds |
|---|---|
| `"levels"` | Each floor: size, Q4/Q8 memory, models, comparable Claude model, gem |
| `"devices"` | Reference Mac stops and their usable / raised GPU memory |
| `"gpus"` | VRAM presets, with `vendor` (`nvidia` or `amd`) for the dot and shirt color |
| `"datacenter"` | Cards for the 🏢 line, with a `url` for each name's link |

To add a model, append it to the matching level's `models` array; to change the memory budget
assumptions, edit `appsReserveGB` and `contextReserveGB`. Bump `"updated"` when you do.

## Caveats

Memory figures are approximate and vary by quant format and runtime. Check each model card before you
download 400GB of weights.

## License

[MIT](LICENSE) © 2026 Mattie Niznik
