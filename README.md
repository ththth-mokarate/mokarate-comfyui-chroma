# mokarate-comfyui-chroma

Baked ComfyUI image for SaladCloud GPU generation: the Salad ComfyUI API
FLUX base image plus the Chroma1-HD model set, so a cold Salad node never
has to download ~24 GB of models after start.

Built by GitHub Actions (`.github/workflows/build.yml`): downloads the
models from Hugging Face, packs one layer per model directory at
`/opt/ComfyUI/models/{diffusion_models,text_encoders,vae}`, and appends
them to the base image with `crane append`, then pushes to GHCR.

Result: `ghcr.io/ththth-mokarate/mokarate-comfyui-chroma:chroma1-hd`

Models (all public Hugging Face weights):
- `diffusion_models/Chroma1-HD.safetensors` — lodestones/Chroma1-HD
- `text_encoders/flan-t5-xxl_float8_e4m3fn_scaled_stochastic.safetensors` — silveroxides/flan-t5-xxl-encoder-only
- `vae/ae.safetensors` — Comfy-Org/Lumina_Image_2.0_Repackaged
