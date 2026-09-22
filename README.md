# Bonsai 2 27B — 14 t/s on an AMD Radeon 890M iGPU

**Stock llama.cpp Vulkan: 1.85 t/s → this recipe: 11.5–12 t/s over HTTP, 14.1 t/s peak (7.5×).**

AMD Ryzen AI 9 HX 470 · Radeon 890M · 28 GB LPDDR5X · llama.cpp Vulkan · MTP speculative decoding (acceptance 0.84) · 5/5 exact-answer quality gate.

## What's in here

- **[The complete recipe](vulkan-890m-bonsai2-recipe.md)** — build config, launch flags, environment variables, the full measured optimization ladder, and the pitfalls that cost us days (raw-gates on Vulkan, spec-depth, quants, threads).

## TL;DR launch

```bash
LLAMA_SSM_BF16_STATE=1 ./llama-server   -m Bonsai-2-27B-Q2_0-fork-MTP.gguf   --spec-type draft-mtp --spec-draft-n-max 1   -ngl 99 -ngld 99 -fa on -t 24 -td 8 -c 16384   --jinja --host 0.0.0.0 --port 8080
```

(Requires the fork commits from [PR #187 on PrismML-Eng/llama.cpp](https://github.com/PrismML-Eng/llama.cpp/pull/187) — bf16 SSM state pools, fused GDN rows-mode, PTQ1_0/Q2fork matvec kernels.)

## Results

| Config | t/s |
|---|---|
| Stock Vulkan | 1.85 |
| + bf16 state pools, fused GDN, custom matvecs | 8–10 |
| + MTP speculation n=1 | **11.5–12 (14.1 peak)** |
| Batch np8 (multi-user) | 14.3 aggregate |

*Measured Sept 2026. Speeds scale with your iGPU's memory bandwidth.*
