# ayana2_SoVITS

用 [GPT-SoVITS](https://github.com/RVC-Boss/GPT-SoVITS) **v2Pro** 微调的「音無彩名（おとなし彩名 / Otonashi Ayana）」日语语音克隆 —— **第二版**，支持 **本机 GPU（推荐）或 CPU** 推理。

> 微调权重托管在 Hugging Face：<https://huggingface.co/Vociepeak/ayana2_SoVITS>

**语言范围：仅日语可靠。** 中文 / 英文属跨语言合成，模型未见过，不推荐。

> 与第一版（<https://github.com/voicepeak/ayana_SoVITS>）使用**不同素材**，音色相近、韵律有别。本仓库为独立项目。

---

## 特性
- 约 84 分钟单人日语素材微调（比第一版更多）
- 推理脚本针对**长文本分块合成再拼接**，避免 GPT-SoVITS 的 `max_sec` / 提前 EOS 导致**结尾吞字**
- 内置 **15kHz 低通**后处理，压掉声码器引入的高频"电音"
- 自动检测设备：有 N 卡用 GPU（RTF ≈ 0.12），否则 CPU（RTF ≈ 0.7–1.0）

## 目录
```
ayana2_SoVITS/
├─ src/
│  ├─ ayana_tts.py      # 可复用模块：synth() / synth_long()
│  ├─ tts.py            # 命令行：文本 -> wav（长文自动分块）
│  └─ benchmark.py      # 设备 + RTF 基准
├─ refs/
│  ├─ ref_ayana2.wav    # 推理参考音频（决定语气）
│  └─ ref_ayana2.txt    # 对应文本（脚本自动读取）
├─ requirements.txt
└─ README.md
```

## 安装
1. **准备 GPT-SoVITS 与底模**（本仓库不含引擎）
   ```bash
   git clone https://github.com/RVC-Boss/GPT-SoVITS.git
   cd GPT-SoVITS   # 按其 README 装依赖 + 下载 pretrained_models（v2Pro 等）
   ```
2. **下载微调权重**放到 GPT-SoVITS 根目录
   ```
   GPT-SoVITS/GPT_weights_v2Pro/ayana2-e14.ckpt
   GPT-SoVITS/SoVITS_weights_v2Pro/ayana2_e8_s728.pth
   ```
3. **装 torch（二选一）+ 依赖**
   ```bash
   # GPU（CUDA 12.4）
   pip install torch==2.5.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu124
   # 或 CPU
   # pip install torch==2.5.1 torchaudio==2.5.1
   pip install -r requirements.txt
   ```
   > Windows 上直接 `pip install torch`（PyPI）是 **CPU 版**；要用显卡须按上面的 CUDA index 装。
4. **告诉脚本 GPT-SoVITS 在哪**：设 `AYANA_GSV_ROOT`，或把本仓库放到 GPT-SoVITS 同级目录。

## 用法
```bash
python src/tts.py --text "こんにちは、ユキト君。今日もいい天気だね。" --out hello.wav
python src/tts.py --text-file script.txt --out out.wav --speed 1.05
```
参数：`--language 日文`、`--speed`、`--temperature 0.6`、`--max-chars 24`、`--no-lowpass`。

作为库调用：
```python
import ayana_tts
sr, audio = ayana_tts.synth_long("長い文章……", language="日文")
```

## 从 Hugging Face 下载权重
```bash
pip install -U huggingface_hub
huggingface-cli download Vociepeak/ayana2_SoVITS --local-dir hf_ayana2
# 把 .ckpt / .pth 放进 GPT_weights_v2Pro/ 与 SoVITS_weights_v2Pro/
```

## 关键设置
- **参考音频** `refs/ref_ayana2.wav`：语气、音色锚点由它决定，换它即换语气。
- `temperature=0.6, top_p=0.6, top_k=20`（GPT-SoVITS 默认；调高会随机提前结束、吞掉结尾字）。
- `lowpass 15kHz`：去高频电音；嫌闷可 `--no-lowpass`。
- 长文本请用 `tts.py` / `synth_long()`：分块合成再拼接，防截断。

## 设备与性能
| 设备 | RTF | 5s 语音 |
|---|---|---|
| **RTX 4090** | **≈ 0.12** | ~0.6s |
| CPU（Ultra 7 265K） | ≈ 0.7–1.0 | ~3.5–5s |

- 查看当前设备：`python src/benchmark.py`
- 强制 CPU：设 `AYANA_DEVICE=cpu`

## 训练数据（简述）
- 源素材：`0ayana纯享.wav`，约 **83.8 分钟** 单人日语
- 处理：Whisper large-v3 **词级时间戳**（1075 段 / 12712 词）→ 按词/句边界切片、拼接句内静音、**丢弃英文段** → **1017 条**干净切片 → 精选 **538 条**（36.4 分钟）
- 切片首尾留余量（0.12s/0.16s），避免切掉尾音
- 微调：v2Pro，SoVITS 8 epoch + GPT 20 epoch（RTX 4090）

## 致谢
- [GPT-SoVITS](https://github.com/RVC-Boss/GPT-SoVITS)（RVC-Boss）

## ⚠️ 免责声明
本项目仅用于**个人学习与研究**。角色「音無彩名（おとなし彩名）」及其声优的音色、原游戏素材均**版权归原作者/发行方所有**。请勿用于**商业用途、冒充他人或任何违法用途**；请勿公开传播原始游戏素材。若权利人提出异议，将立即删除相关内容。

## License
代码以 MIT 许可发布；模型权重仅供研究。
