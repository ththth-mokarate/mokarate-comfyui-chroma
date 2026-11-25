# mokarate-comfyui-chroma

Baked ComfyUI image for SaladCloud GPU generation: the Salad ComfyUI API
**runtime** base image plus the Chroma1-HD model set, so a cold Salad node
never has to download models after start.

Why the runtime base (not flux1dev): the flux1dev image's manifest makes
the wrapper download an 11 GB FLUX checkpoint before startup and run a
FLUX warmup prompt. The runtime image has no manifest and no warmup, so
it reaches /ready as soon as ComfyUI launches.

Built by GitHub Actions (`.github/workflows/build.yml`): downloads the
models from Hugging Face (SHA256-verified), packs them as layers at
`/opt/ComfyUI/models/{diffusion_models,text_encoders,vae}`, and appends
them to the base image with `crane append`, then pushes to GHCR.

GHCR allows max 10 GB per layer, so the 17.8 GB Chroma file is split
into 8 GiB parts (one layer each). At container start the parts are
reassembled by `assemble.sh` — atomically (into a `.tmp` file, size +
SHA256 verified, then renamed; the parts are kept so a crashed assembly
can retry) — run it via the Salad group's command override:
`["/bin/sh", "/opt/ComfyUI/assemble.sh"]`. Salad's max container image
size is 35 GB compressed; the build asserts the pushed image stays
under it.

Result: `ghcr.io/ththth-mokarate/mokarate-comfyui-chroma:chroma1-hd`

Models (all public Hugging Face weights):
- `diffusion_models/Chroma1-HD.safetensors` (split into `chroma.part-00/01/02`) — lodestones/Chroma1-HD
- `text_encoders/flan-t5-xxl_float8_e4m3fn_scaled_stochastic.safetensors` — silveroxides/flan-t5-xxl-encoder-only
- `vae/ae.safetensors` — Comfy-Org/Lumina_Image_2.0_Repackaged
