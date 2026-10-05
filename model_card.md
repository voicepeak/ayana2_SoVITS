---
language:
  - ja
library_name: gpt-sovits
tags:
  - text-to-speech
  - voice-cloning
  - japanese
  - gpt-sovits
  - v2pro
license: other
license_name: research-only
---

# ayana2_SoVITS

Fine-tuned **GPT-SoVITS v2Pro** voice model for the Japanese voice of
**Otonashi Ayana** — **second model** (different source material than
[Vociepeak/ayana_SoVITS](https://huggingface.co/Vociepeak/ayana_SoVITS)).
For personal / research use only.

## Files
| file | what |
|---|---|
| `ayana2-e14.ckpt` | GPT (prosody) weights — v2Pro |
| `ayana2_e8_s728.pth` | SoVITS (timbre) weights — v2Pro |

Place them in a GPT-SoVITS checkout as:
```
GPT_weights_v2Pro/ayana2-e14.ckpt
SoVITS_weights_v2Pro/ayana2_e8_s728.pth
```

## Usage
Code repo: <https://github.com/voicepeak/ayana2_SoVITS>

```python
from GPT_SoVITS.inference_webui import change_gpt_weights, change_sovits_weights, get_tts_wav
change_gpt_weights("GPT_weights_v2Pro/ayana2-e14.ckpt")
change_sovits_weights("SoVITS_weights_v2Pro/ayana2_e8_s728.pth")
gen = list(get_tts_wav(
    ref_wav_path="refs/ref_ayana2.wav",
    prompt_text="違うとすればそれは、言葉と音の違いほど。",
    prompt_language="日文",
    text="こんにちは、ユキト君。", text_language="日文",
    top_k=20, top_p=0.6, temperature=0.6))
sr, audio = gen[-1]
```

## Device
GPU auto (RTF ≈ 0.12 on RTX 4090) or CPU (RTF ≈ 0.7–1.0). Force CPU with `AYANA_DEVICE=cpu`.

## Recommended settings
- `temperature=0.6, top_p=0.6, top_k=20` (higher temperature → premature EOS → dropped endings)
- Split long text into ≤24-char chunks and concatenate (prevents `max_sec` truncation)
- Optional 15 kHz low-pass to reduce vocoder hiss
- A 3–10 s reference clip + its exact transcript is required; it sets the tone

## Training
- ~84 min single-speaker Japanese (`0ayana純享.wav`)
- Word-level ASR (large-v3): 1075 segments / 12712 words → 1017 clean clips (English segments dropped) → 538 curated (36.4 min)
- SoVITS 8 epochs, GPT 15 epochs, single RTX 4090

## Language
Japanese only.

## Disclaimer
The character and its voice belong to the original rights holders. Do not use
for commercial purposes, impersonation, or redistribution of original assets.
Research / personal use only.
