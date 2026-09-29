# Stable Diffusion WebUI Forge Neo on Google Colab

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Horlicks-p/Stable-Diffusion-WebUI/blob/main/Forge_Neo_Cloudflare_Colab.ipynb)

這份 Google Colab notebook 會安裝 [Stable Diffusion WebUI Forge - Neo](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo)，並透過 Cloudflare Tunnel 提供遠端存取。

## 主要功能

- 使用 Forge Classic 的 `neo` 分支，不再安裝 AUTOMATIC1111。
- 依照 Neo 的建議建立 Python 3.13 虛擬環境。
- WebUI 程式與虛擬環境放在 Colab 暫存空間，避免 Google Drive 小檔案 I/O 拖慢啟動。
- 模型、設定、擴充套件與輸出保存在 Google Drive。
- 可直接引用舊 AUTOMATIC1111 的模型目錄，不必搬移或重複下載模型。
- 預設安裝 Civitai Helper RED UPDATE，可管理模型、預覽圖與 Civitai 資料。
- 支援 Cloudflare Quick Tunnel 與具名 Tunnel Token。
- 支援 Gradio 帳號密碼及 WebUI API；API 預設關閉。
- 可從 Hugging Face、Civitai、Google Drive 或一般 HTTPS 網址下載模型。

## 使用方式

1. 點上方 **Open in Colab**。
2. 將執行階段切換為 GPU。
3. 依序執行 notebook 的儲存空間、安裝、模型及啟動區塊。
4. 啟動區塊會顯示 Cloudflare 網址；開啟網址後即可使用 Forge Neo。

第一次啟動需要下載 Python、PyTorch 與 Neo 的相依套件，之後模型、設定和輸出仍會保留在 Google Drive。Colab 每次建立新執行階段時，程式與虛擬環境會重新建立。

## Google Drive 目錄

預設資料放在：

```text
MyDrive/sd/forge-neo/
├── models/
│   ├── Stable-diffusion/
│   ├── Lora/
│   ├── VAE/
│   ├── ControlNet/
│   └── text_encoder/
├── extensions/
├── output/
├── config.json
└── ui-config.json
```

若你原本的模型位於 `MyDrive/sd/stable-diffusion-webui/`，在第一個設定區塊保留 `USE_LEGACY_A1111_MODELS = True`。Notebook 會加上 Neo 官方的 `--forge-ref-a1111-home` 參數，直接讀取舊目錄。

## Cloudflare Tunnel

`CF_TUNNEL_TOKEN` 留空時會建立臨時的 `trycloudflare.com` 網址。填入 Cloudflare Zero Trust Tunnel Token 時會啟動你的具名 Tunnel；請先在 Cloudflare 將該 Tunnel 的 Public Hostname 指向 `http://localhost:7860`。

建議同時設定 `WEBUI_USER` 和 `WEBUI_PASSWORD`。臨時網址是公開網址，任何取得網址的人都能連線。啟用 API 時，同一組帳密也會套用到 API；若沒有設定帳密，notebook 會顯示警告。

## 模型與擴充套件

Forge Neo 已內建 ControlNet，不需要安裝 `Mikubill/sd-webui-controlnet`。ControlNet 模型放在 `models/ControlNet`。

Notebook 預設安裝 [Civitai Helper RED UPDATE](https://github.com/Replactionap/Stable-Diffusion-Webui-Civitai-Helper-RED-UPDATE)，可用 `INSTALL_CIVITAI_HELPER` 關閉。Civitai API Key 可在 WebUI 的 Civitai Helper 設定中填入，設定會隨 `forge-neo` 資料目錄保存在 Google Drive。

第三方 AUTOMATIC1111 擴充套件不一定相容 Neo。Notebook 只會另外安裝你在 `EXTENSION_URLS` 明確列出的 Git repository；若擴充套件造成啟動錯誤，可從 Google Drive 的 `forge-neo/extensions` 移除該目錄。

## 與舊版 notebook 的差異

舊版會安裝 TheLastBen 的 A1111 patch、固定舊版 Gradio/Pydantic，並覆寫 `modules/extras.py`、`modules/sd_models.py` 等 A1111 內部檔案。這些操作與 Forge Neo 不相容，已全部移除。Neo 現在自行管理相依套件與模型路徑。

## 授權與來源

- 本專案：[GNU AGPL-3.0](LICENSE)
- [Stable Diffusion WebUI Forge - Neo](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo)：AGPL-3.0
- 原始 notebook 工作流程源自 [TheLastBen/fast-stable-diffusion](https://github.com/TheLastBen/fast-stable-diffusion)
