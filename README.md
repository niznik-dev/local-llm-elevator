# How deep can your machine go? 🛗

A one-page field guide to running open-weight LLMs locally, on a Mac, a shared-memory mini PC, or a
gaming GPU.

**Live page:** https://niznik-dev.github.io/local-llm-elevator/

Models are drawn as floors in an elevator shaft: small models sit near the surface and the giants
are far below. Every machine's elevator only goes so deep, and its **stop** marks where it runs out of
memory. The shaft's built-in stops are reference machines (Macs and mini PCs in Unified mode, graphics cards in VRAM mode); add your own in two steps:

1. **Pick a memory type:** Unified or GPU VRAM (the **?** explains the difference).
2. **Pick a preset or type your own amount:** 🍎 Macs or a mini PC (Strix Halo, DGX Spark) for unified, 🎮 gaming cards for VRAM. Press
   Enter or ↓ to ride down to your stop; **Clear** removes it.

| Mode | Enter | Usable estimate |
|---|---|---|
| **Unified** | Total RAM on Apple Silicon or another shared-memory machine (AMD Strix Halo, NVIDIA DGX Spark) | ~67% (≤36GB) or ~75%, leaving room for the OS and a few apps |
| **GPU VRAM** | A dedicated GPU's VRAM, summed across cards (presets: RX 9060 XT 8GB, RTX 5070 12GB, RX 9070 XT 16GB, RTX 3090 24GB, RTX 5090 32GB, with a 1× / 2× / 4× card count; counts a card isn't usually combined at are greyed out) | ~90%, after driver and display overhead |

VRAM mode assumes the whole model fits on the GPU. Partial offload to system RAM (llama.cpp layer
splitting, MoE experts on CPU) is out of scope.

Once you've set a stop, the elevator parks there and everything deeper dims, so you can see at a
glance what's out of reach. The rider's shirt and your stop take the vendor's color when you pick a
GPU preset (NVIDIA green, AMD red) and stay orange otherwise.

A money-green **hardware value** box floats above the floor tracker: the US price of the preset you
picked (times the card count), or, for a typed amount, an estimate from the cheapest preset's $/GB.
Hover it for where the number comes from.

## What's on each floor

| Field | Meaning |
|---|---|
| **Q4 / Q8 memory** | Approximate weight footprint at 4-bit and 8-bit quantization |
| **Models** | Notable open-weight releases from the past year at that size, linked to Hugging Face |
| **Fit pills** | The smallest reference machine that holds the Q4 / Q8 weights. Unified mode names Macs and mini PCs; VRAM mode names card setups with their total VRAM. Setups with equal room take turns |
| **Cheapest option** | The least expensive setup that holds the Q4 weights at default limits, within the current memory mode (a card isn't compared to a whole computer) |
| **🏢 Data center** | How many 80GB H100s or ~180GB B200s hold the 8-bit (FP8) weights, at 90% usable per card (minimums, not deployment-shaped counts) |
| **Comparable Claude model** | The Claude tier you'd reach for on the same kind of task (not a claim of equal performance) |
| **Comparable ChatGPT model** | The same idea for OpenAI's API tiers (GPT-6 Luna, GPT-6.1 Terra, GPT-6.1 Sol, GPT-6 Astra) |
| **Popularity** | A rough three-step signal from OpenRouter token usage and Hugging Face downloads |
| **Gem** | Decoration. It's a mine shaft, so gems get roughly pricier the deeper you go (quartz to red diamond, then antimatter). They say nothing about the model |

Reference stops are shown for 32GB and 64GB MacBook Pros (💻) and 128 / 256 / 512GB Mac Studios (🤍 🩶 🖤), plus 128GB Strix Halo (❤️) and DGX Spark (💚) mini PCs, which take turns with the 128GB Studio in the fit pills. In VRAM mode those stops swap out for one per GPU preset, plus a stop for every multi-card count the selector allows (2× RX 9070 XT; 2× and 4× RTX 3090 and 5090), and one 8× RTX 5090 🔥 stop that's deliberately left out of the picker. A
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
| `"levels"` | Each floor: size, Q4/Q8 memory, models, comparable Claude and ChatGPT models, gem |
| `"devices"` | Reference Mac stops: usable / raised GPU memory and `price` |
| `"minis"` | Unified mini PCs (Strix Halo, DGX Spark): usable memory, `icon`, `price` |
| `"gpus"` | VRAM presets: `vendor` (`nvidia` or `amd`) for the dot and shirt color, `price` per card, `max` cards (with a `why` tooltip for greyed-out counts), `rigStops`, and an optional `showoff` count |
| `"datacenter"` | Cards for the 🏢 line, with a `url` for each name's link |

To add a model, append it to the matching level's `models` array; to change the memory budget
assumptions, edit `appsReserveGB` and `contextReserveGB`. Prices are US prices as of November 2026
(street prices for GPUs); add `"priceEst": true` to mark one as a rough estimate. Bump `"updated"`
when you do.

## Caveats

Memory figures are approximate and vary by quant format and runtime. Check each model card before you
download 400GB of weights.

## License

[MIT](LICENSE) © 2026 Mattie Niznik
