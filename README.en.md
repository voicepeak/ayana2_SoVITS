# ayana2_SoVITS

Japanese voice clone of **Otonashi Ayana** fine-tuned with
[GPT-SoVITS](https://github.com/RVC-Boss/GPT-SoVITS) **v2Pro** — **second model**,
runs on **GPU (recommended) or CPU**.

> Weights: <https://huggingface.co/Vociepeak/ayana2_SoVITS>

**Japanese only.** English / Chinese are cross-lingual and unreliable.

> Separate from the first model (<https://github.com/voicepeak/ayana_SoVITS>) —
> different source material, similar timbre, slightly different prosody.

## Features
- Fine-tuned on ~84 min of single-speaker Japanese
- Long text is split into short chunks and concatenated → no truncated endings
- Optional 15 kHz low-pass to remove vocoder high-frequency hiss
- Auto device: GPU (RTF ≈ 0.12 on a 4090) or CPU (RTF ≈ 0.7–1.0)

## Install
1. Clone GPT-SoVITS and install its deps + `pretrained_models`.
2. Put the weights in place:
   `GPT_weights_v2Pro/ayana2-e14.ckpt`, `SoVITS_weights_v2Pro/ayana2_e8_s728.pth`
3. Install torch (pick one), then the rest:
   ```bash
   # GPU (CUDA 12.4)
   pip install torch==2.5.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu124
   # or CPU
   # pip install torch==2.5.1 torchaudio==2.5.1
   pip install -r requirements.txt
   ```
4. Set `AYANA_GSV_ROOT` to the GPT-SoVITS folder (or place this repo next to it).

## Usage
```bash
python src/tts.py --text "こんにちは、ユキト君。" --out hello.wav
python src/tts.py --text-file script.txt --out out.wav --speed 1.05
```
Key settings: `temperature=0.6, top_p=0.6, top_k=20`, `lowpass 15kHz`, chunked synthesis.

Device auto-detected; force CPU with `AYANA_DEVICE=cpu`. Benchmark: `python src/benchmark.py`.

## Disclaimer
Personal / research use only. The character and its voice belong to the original
rights holders. Do not use commercially, for impersonation, or redistribute the
original game assets.

## License
Code: MIT. Weights: research use only.
