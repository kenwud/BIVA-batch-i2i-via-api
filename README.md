# BIVA-batch-i2i-via-api
sends 1 or more preselected images to auto111 api sequentially with many parameters

# BIVA — Batch Img2Img Via API

> A single-file browser UI for batch processing images through [AUTOMATIC1111 Stable Diffusion WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) via its API. No installs, no Python environment, no downloads — just open the HTML file and go.

![HTML](https://img.shields.io/badge/single--file-HTML-orange) ![License](https://img.shields.io/badge/license-MIT-green) ![SD](https://img.shields.io/badge/requires-A1111%20WebUI-blue)

---

## What it does

Queue up a folder of images, dial in your settings, hit Generate. BIVA sends each image to A1111's img2img API sequentially with full control over ControlNet, Tiled Diffusion, Tiled VAE, ADetailer, upscaling, and more. A1111 saves the results directly to disk — no forced browser downloads.

Built for Blender-to-SD pipelines (beauty pass + depth pass), but works on any batch of images.

---

## Requirements

- [AUTOMATIC1111 Stable Diffusion WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) running with `--api` flag
- Extensions (optional but recommended):
  - [sd-webui-controlnet](https://github.com/Mikubill/sd-webui-controlnet)
  - [multidiffusion-upscaler-for-automatic1111](https://github.com/pkuliyi2015/multidiffusion-upscaler-for-automatic1111) (Tiled Diffusion + Tiled VAE)
  - [adetailer](https://github.com/Bing-su/adetailer)

Launch A1111 with:
```
webui-user.bat --api --xformers
```

---

## Usage

1. Download `sd_batch_ui_v8.html`
2. Serve it locally (required for file access):
   ```
   python -m http.server 8080
   ```
   Then open `http://localhost:8080/sd_batch_ui_v8.html`
3. Enter your A1111 URL (default `http://127.0.0.1:7860`) and click **Connect + Fetch Models**
4. Configure your settings, queue images, and click **🚀 Generate**

> You can also just open the HTML file directly in Firefox (Chrome blocks local file access).

---

## Features

### Core
- **Batch img2img** — queue any number of images, process sequentially
- **Aspect ratio preservation** — output dimensions auto-calculated from input
- **A1111 server-side saving** — files go straight to disk, no browser downloads
- **Inline previews** — thumbnails appear in the log as each image completes, click to download manually
- **Per-file queue status** — each file tag shows ⏳ running / ✓ done / ✗ error live
- **ETA** — estimated time remaining after first image completes, avg seconds/image

### Generation Controls
- Denoising strength, CFG scale, steps, sampler
- **Seed** — fixed, random (-1), or locked across batch. Per-image offset when unlocked
- **CLIP Skip** — slider 1–12, wired to `CLIP_stop_at_last_layers`
- **img2img Color Correction** — reduces color drift at high denoising strengths

### Resolution
- **Resize To** — fixed max long edge (512–2048px), short edge auto-calculated
- **Resize By** — scale factor multiplier (0.5×–4.0×), great for iterative upscaling
- Pre-scale input cap — optionally cap input size before sending to SD
- Post-generation ESRGAN upscale — optional second pass via `/sdapi/v1/extra-single-image`

### ControlNet
- Model + preprocessor dropdowns (fetched from A1111)
- Weight, guidance start/end sliders
- Supports separate depth map folder for Blender beauty+depth workflows
- **Interrogate CLIP** — sends first queued image to `/sdapi/v1/interrogate`, dumps result to positive prompt

### Tiled Diffusion
- Enable/disable, method (MultiDiffusion / Mixture of Diffusers)
- Tile width/height, overlap, batch size
- Move ControlNet tensor to CPU (saves VRAM during tiling)
- Noise Inversion support

### Tiled VAE
- Encoder/decoder tile size
- Fast Encoder Color Fix toggle

### ADetailer
- Optional face/hand/person detection and inpainting pass
- Detection model dropdown, denoising strength control

### Prompts
- Positive + negative prompt fields
- **Save/load/delete named prompt presets** (localStorage)
- LoRA insertion — dropdown fetched from A1111, weight input, inserts `<lora:name:weight>` into prompt
- Embedding/TI insertion — dropdown fetched from A1111, inserts token into prompt

### Settings Profiles
- Save/load/delete complete UI state by name (localStorage)
- Captures all sliders, toggles, dropdowns, resize mode
- Switch entire workflows in one click (e.g. `alien_high_denoise`, `portrait_subtle`)

### Script Export
- Generates a standalone Python batch processor script with current settings baked in

---

## Blender Workflow

BIVA was built around a Blender → SD pipeline:

1. Render **Beauty** pass and **Depth (Z)** pass as separate PNG sequences
2. In Compositor: Z pass → **Normalize node** → File Output (critical — converts Z depth to 0–1 greyscale)
3. Load beauty folder + depth folder into BIVA
4. Use `control_v11f1p_sd15_depth` ControlNet model with preprocessor set to `none`

Recommended ControlNet models (install to `extensions/sd-webui-controlnet/models`):
- `control_v11f1p_sd15_depth [cfd03158]` — primary for Blender depth
- `control_v11f1e_sd15_tile` — for tile-based upscaling passes
- `control_v11p_sd15_softedge` — softer edge guidance

---

## Instruct Pix2Pix (IP2P)

For text-directed edits ("make it look like lava", "turn it into a painting"):
- Set CN preprocessor to `ip2p`
- Write your edit instruction as the positive prompt
- Requires `control_v11e_sd15_ip2p` model

---

## VRAM Notes (tested on GTX 1060 6GB)

| Setting | Recommended |
|---|---|
| Tiled Diffusion tile size | 144×144 |
| Tiled Diffusion overlap | 72 |
| Tiled Diffusion batch | 1 |
| Tiled VAE encoder tile | 1552 |
| Tiled VAE decoder tile | 144 |
| Move CN tensor to CPU | ✓ enabled |

---

## Changelog

### v8
- Seed control with 🎲 random and 🔒 lock
- CLIP Skip slider (1–12)
- img2img Color Correction toggle
- Interrogate CLIP button (fills prompt from image)
- IP2P usage note in prompt panel
- Per-file queue status badges (live update)
- ETA + elapsed time tracking
- Settings Profiles (save/load full UI state)

### v7 and earlier
- A1111 server-side saving (no forced downloads)
- Resize To / Resize By modes
- ADetailer integration
- LoRA + Embedding dropdowns
- Tiled Diffusion + Tiled VAE panels
- Prompt save/load presets
- Python script export generator
- Inline thumbnail previews in log

---

## License

MIT — do whatever you want with it.
