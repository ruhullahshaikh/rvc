<div align="center">

# Retrieval-based-Voice-Conversion-WebUI
A simple and easy-to-use voice conversion framework based on VITS<br><br>

[![madewithlove](https://img.shields.io/badge/made_with-%E2%9D%A4-red?style=for-the-badge&labelColor=orange
)](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI)

<img src="https://counter.seku.su/cmoe?name=rvc&theme=r34" /><br>

[![Open In Colab](https://img.shields.io/badge/Colab-F9AB00?style=for-the-badge&logo=googlecolab&color=525252)](https://colab.research.google.com/github/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/blob/main/Retrieval_based_Voice_Conversion_WebUI.ipynb)
[![Licence](https://img.shields.io/badge/LICENSE-MIT-green.svg?style=for-the-badge)](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/blob/main/LICENSE)
[![Huggingface](https://img.shields.io/badge/🤗%20-Spaces-yellow.svg?style=for-the-badge)](https://huggingface.co/lj1995/VoiceConversionWebUI/tree/main/)

[![Discord](https://img.shields.io/badge/RVC%20Developers-Discord-7289DA?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/HcsmBBGyVk)

[**Changelog**](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/blob/main/docs/Changelog_CN.md) | [**FAQ**](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%E8%A7%A3%E7%AD%94) | [**AutoDL Tutorial**](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/wiki/Autodl%E8%AE%AD%E7%BB%83RVC%C2%B7AI%E6%AD%8C%E6%89%8B%E6%95%99%E7%A8%8B) | [**Experimental Records**](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/wiki/%E5%AF%B9%E7%85%A7%E5%AE%9E%E9%AA%8C%C2%B7%E5%AE%9E%E9%AA%8C%E8%AE%B0%E5%BD%95) | [**Online Demo**](https://modelscope.cn/studios/FlowerCry/RVCv2demo)

[**English**](./docs/en/README.en.md) | [**简体中文**](./README.md) | [**日本語**](./docs/jp/README.ja.md) | [**한국어**](./docs/kr/README.ko.md) ([**韓國語**](./docs/kr/README.ko.han.md)) | [**Français**](./docs/fr/README.fr.md) | [**Türkçe**](./docs/tr/README.tr.md) | [**Português**](./docs/pt/README.pt.md)

</div>

> The base model is trained using nearly 50 hours of open-source high-quality VCTK training set, with no copyright concerns. Feel free to use it.

> Look forward to RVCv3's base model: larger parameters, more data, better results, comparable inference speed, and requires less training data.

<table>
   <tr>
      <td align="center">Training & Inference Interface</td>
      <td align="center">Real-time Voice Conversion Interface</td>
   </tr>
   <tr>
      <td align="center"><img src="https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/assets/129054828/092e5c12-0d49-4168-a590-0b0ef6a4f630"></td>
      <td align="center"><img src="https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/assets/129054828/730b4114-8805-44a1-ab1a-04668f3c30a6"></td>
   </tr>
   <tr>
      <td align="center">go-web.bat</td>
      <td align="center">go-realtime-gui.bat</td>
   </tr>
   <tr>
      <td align="center">You can freely choose the operations you want to perform.</td>
      <td align="center">We have achieved end-to-end 170ms latency. Using ASIO input/output devices, 90ms end-to-end latency is possible, but heavily depends on hardware driver support.</td>
   </tr>
</table>

## Introduction
This repository has the following features:
+ Uses top1 retrieval to replace input source features with training set features to eliminate timbre leakage
+ Fast training even on relatively modest GPUs
+ Good results with minimal training data (recommended to collect at least 10 minutes of low-noise voice data)
+ Can modify timbre through model fusion (using ckpt-merge in the checkpoint processing tab)
+ Simple and easy-to-use web interface
+ Can call UVR5 model for quick separation of vocals and accompaniment
+ Uses state-of-the-art [vocal pitch extraction algorithm InterSpeech2023-RMVPE](#reference-projects) to eliminate muted sound issues. Best results (significantly) while being faster and less resource-intensive than crepe_full
+ Support for AMD and Intel GPU acceleration

Check out our [demo video](https://www.bilibili.com/video/BV1pm4y1z7Gm/)!

## Environment Setup
The following commands need to be executed in a Python environment version 3.8 or higher.

### Universal Method for Windows/Linux/MacOS Platforms
Choose one of the following methods.
#### 1. Install dependencies via pip
1. Install PyTorch and its core dependencies (skip if already installed). Reference: https://pytorch.org/get-started/locally/
```bash
pip install torch torchvision torchaudio
```
2. For Windows systems + Nvidia Ampere architecture (RTX30xx), according to #21 experience, need to specify pytorch's corresponding cuda version
```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu117
```
3. Install dependencies according to your GPU
- Nvidia GPU
```bash
pip install -r requirements.txt
```
- AMD/Intel GPU
```bash
pip install -r requirements-dml.txt
```
- AMD ROCM (Linux)
```bash
pip install -r requirements-amd.txt
```
- Intel IPEX (Linux)
```bash
pip install -r requirements-ipex.txt
```

#### 2. Install dependencies via poetry
Install Poetry dependency management tool (skip if already installed). Reference: https://python-poetry.org/docs/#installation
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

When installing dependencies through Poetry, Python version 3.7-3.10 is recommended, as other versions will conflict when installing llvmlite==0.39.0
```bash
poetry init -n
poetry env use "path to your python.exe"
poetry run pip install -r requirements.txt
```

### MacOS
You can install dependencies through `run.sh`
```bash
sh ./run.sh
```

## Other Pre-model Preparation
RVC needs other pre-models for inference and training.

You can download these models from our [Hugging Face space](https://huggingface.co/lj1995/VoiceConversionWebUI/tree/main/).

### 1. Download assets
Here's a checklist of all pre-models and other files RVC needs. You can find scripts to download them in the `tools` folder.

- ./assets/hubert/hubert_base.pt
- ./assets/pretrained 
- ./assets/uvr5_weights

For v2 version models, you need to additionally download:
- ./assets/pretrained_v2

### 2. Install ffmpeg
Skip if ffmpeg and ffprobe are already installed.

#### Ubuntu/Debian users
```bash
sudo apt install ffmpeg
```
#### MacOS users
```bash
brew install ffmpeg
```
#### Windows users
Download and place in root directory.
- Download [ffmpeg.exe](https://huggingface.co/lj1995/VoiceConversionWebUI/blob/main/ffmpeg.exe)
- Download [ffprobe.exe](https://huggingface.co/lj1995/VoiceConversionWebUI/blob/main/ffprobe.exe)

### 3. Download files required for rmvpe vocal pitch extraction algorithm

If you want to use the latest RMVPE vocal pitch extraction algorithm, you need to download the pitch extraction model parameters and place them in the RVC root directory.

- Download [rmvpe.pt](https://huggingface.co/lj1995/VoiceConversionWebUI/blob/main/rmvpe.pt)

#### Download rmvpe dml environment (Optional, for AMD/Intel GPU users)
- Download [rmvpe.onnx](https://huggingface.co/lj1995/VoiceConversionWebUI/blob/main/rmvpe.onnx)

### 4. AMD GPU Rocm (Optional, Linux only)

If you want to run RVC on Linux using AMD's Rocm technology, please first install the required drivers [here](https://rocm.docs.amd.com/en/latest/deploy/linux/os-native/install.html).

For Arch Linux users, you can install the required drivers using pacman:
```bash
pacman -S rocm-hip-sdk rocm-opencl-sdk
```
For some GPU models, you might need to configure additional environment variables (e.g., RX6700XT):
```bash
export ROCM_PATH=/opt/rocm
export HSA_OVERRIDE_GFX_VERSION=10.3.0
```
Also ensure your current user is in the `render` and `video` groups:
```bash
sudo usermod -aG render $USERNAME
sudo usermod -aG video $USERNAME
```

## Getting Started
### Direct Launch
Use the following command to start WebUI
```bash
python infer-web.py
```

If you previously installed dependencies with Poetry, you can start WebUI with:
```bash
poetry run python infer-web.py
```

### Using the Integration Package
Download and extract `RVC-beta.7z`
#### Windows users
Double-click `go-web.bat`
#### MacOS users
```bash
sh ./run.sh
```
### For Intel GPU users needing IPEX technology (Linux only)
```bash
source /opt/intel/oneapi/setvars.sh
```

## Reference Projects
+ [ContentVec](https://github.com/auspicious3000/contentvec/)
+ [VITS](https://github.com/jaywalnut310/vits)
+ [HIFIGAN](https://github.com/jik876/hifi-gan)
+ [Gradio](https://github.com/gradio-app/gradio)
+ [FFmpeg](https://github.com/FFmpeg/FFmpeg)
+ [Ultimate Vocal Remover](https://github.com/Anjok07/ultimatevocalremovergui)
+ [audio-slicer](https://github.com/openvpi/audio-slicer)
+ [Vocal pitch extraction:RMVPE](https://github.com/Dream-High/RMVPE)
  + The pretrained model is trained and tested by [yxlllc](https://github.com/yxlllc/RMVPE) and [RVC-Boss](https://github.com/RVC-Boss).

## Thanks to All Contributors
<a href="https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/graphs/contributors" target="_blank">
  <img src="https://contrib.rocks/image?repo=RVC-Project/Retrieval-based-Voice-Conversion-WebUI" />
</a>
