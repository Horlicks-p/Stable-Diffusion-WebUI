# Stable Diffusion WebUI Forge Neo on Google Colab

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Horlicks-p/Stable-Diffusion-WebUI/blob/main/Forge_Neo_Cloudflare_Colab.ipynb)

這份 Google Colab notebook 會安裝 [Stable Diffusion WebUI Forge - Neo](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo)，並透過 Cloudflare Tunnel 提供遠端存取。

## 主要功能

- 使用 Forge Classic 的 `neo` 分支，不再安裝 AUTOMATIC1111。
- 依照 Neo 的建議建立 Python 3.13 虛擬環境。
- WebUI 程式與虛擬環境放在 Colab 暫存空間，避免 Google Drive 小檔案 I/O 拖慢啟動。
- 模型、設定、擴充套件與輸出保存在 Google Drive。
- 可直接引用舊 AUTOMATIC1111 的模型目錄，不必搬移或重複下載模型。
- 可自動偵測或指定多個 ComfyUI 模型目錄，讓 Neo 共用 checkpoint、LoRA、VAE、ControlNet 與文字編碼器。
- 預設安裝 Civitai Helper RED UPDATE，可管理模型、預覽圖與 Civitai 資料。
- 支援 Cloudflare Quick Tunnel 與具名 Tunnel Token。
- 支援 Gradio 帳號密碼及 WebUI API；API 預設關閉。
- 可從 Hugging Face、Civitai、Google Drive 或一般 HTTPS 網址下載模型。

## 使用方式

1. 點上方 **Open in Colab**。
2. 在 **執行階段 → 變更執行階段類型** 選擇 GPU（例如 T4）。
3. 從第 1 格開始依序執行。每次重新啟動執行階段後都要從第 1 格重跑，後面的區塊會用到第 1 格建立的路徑與變數。
4. 啟動區塊出現 `Running on local URL:  http://0.0.0.0:7860` 後，開啟 Cloudflare 網址即可使用。
5. 要停止 WebUI 時，中斷第 5 格的執行即可，Cloudflare Tunnel 會一併關閉。

第一次啟動需要下載 Python 3.13、PyTorch 與 Neo 的相依套件，可能需要數分鐘。之後模型、設定和輸出仍會保留在 Google Drive；Colab 每次建立新執行階段時，程式與虛擬環境會重新建立。

### 各區塊說明

| 區塊 | 用途 | 主要參數 |
| --- | --- | --- |
| 1. 儲存空間設定 | 掛載 Google Drive、建立資料目錄、偵測舊 A1111 與 ComfyUI 模型 | `DRIVE_FOLDER`、`SHARED_DRIVE`、`USE_LEGACY_A1111_MODELS`、`COMFYUI_PATHS` |
| 2. 安裝或更新 Forge Neo | 下載或更新 `neo` 分支，建立 Python 3.13 venv | — |
| 3. 下載模型（選用） | 下載單一模型到對應的 `models` 子目錄 | `MODEL_URL`、`MODEL_TYPE`、`HF_TOKEN`、`CIVITAI_TOKEN` |
| 4. 擴充套件（選用） | 安裝或更新 Civitai Helper 及自訂擴充套件 | `INSTALL_CIVITAI_HELPER`、`EXTENSION_URLS` |
| 5. 啟動 | 啟動 Cloudflare Tunnel 與 Forge Neo | `WEBUI_USER`、`WEBUI_PASSWORD`、`CF_TUNNEL_TOKEN`、`ENABLE_API`、`INSTALL_XFORMERS`、`EXTRA_ARGS` |

`EXTRA_ARGS` 會原樣附加到 `launch.py`，可加入 Neo 支援的其他啟動參數。

### 啟動記錄

Forge Neo 的輸出會即時顯示在第 5 格，並同時寫入 `/content/forge-neo-launch.log`。啟動失敗時，notebook 會在最後重印結束前的輸出，方便找出錯誤原因。輸出包含啟動參數，若有設定帳密，分享記錄前請先遮掉 `--gradio-auth` 後面的內容。

Notebook 啟動 Forge Neo 時會調整幾個環境變數，避免 Colab 的預設值干擾：

- 設定 `VIRTUAL_ENV`、`UV_PYTHON` 並忽略 Colab 的其他 `UV_*` 設定，確保 PyTorch 等套件裝進 venv，而不是 Colab 系統 Python。
- 將 `MPLBACKEND` 設為 `Agg`，避免 Colab 的 notebook 繪圖設定讓內建 ControlNet 載入失敗。

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

## 共用 ComfyUI 模型

`AUTO_DETECT_COMFYUI = True` 會檢查常見位置，包括 `/content/ComfyUI`、`/content/comfyui`、MyDrive 根目錄及 `MyDrive/sd/ComfyUI`。

其他位置可填入 `COMFYUI_PATHS`。它接受 ComfyUI 根目錄或直接指向 `models` 目錄，多個位置以逗號、分號或換行分隔，例如：

```text
/content/ComfyUI, /content/gdrive/MyDrive/AI/ComfyUI
```

Notebook 會將找到的路徑合併成暫時的 `extra_model_paths.yaml`，再用 Neo 的 `--forge-ref-comfy-yaml` 載入。模型檔案維持在原處，不會複製到 `forge-neo/models`。目前會共用 ComfyUI 的 `checkpoints`、`diffusion_models`、`unet`、`clip`、`text_encoders`、`loras`、`vae` 和 `controlnet` 目錄。

## Cloudflare Tunnel

`CF_TUNNEL_TOKEN` 留空時會建立臨時的 `trycloudflare.com` 網址。填入 Cloudflare Zero Trust Tunnel Token 時會啟動你的具名 Tunnel；請先在 Cloudflare 將該 Tunnel 的 Public Hostname 指向 `http://localhost:7860`。

建議同時設定 `WEBUI_USER` 和 `WEBUI_PASSWORD`。臨時網址是公開網址，任何取得網址的人都能連線。啟用 API 時，同一組帳密也會套用到 API；若沒有設定帳密，notebook 會顯示警告。

## 模型與擴充套件

Forge Neo 已內建 ControlNet，不需要安裝 `Mikubill/sd-webui-controlnet`。ControlNet 模型放在 `models/ControlNet`。

Notebook 預設安裝 [Civitai Helper RED UPDATE](https://github.com/Replactionap/Stable-Diffusion-Webui-Civitai-Helper-RED-UPDATE)，可用 `INSTALL_CIVITAI_HELPER` 關閉。Civitai API Key 可在 WebUI 的 Civitai Helper 設定中填入，設定會隨 `forge-neo` 資料目錄保存在 Google Drive。

第三方 AUTOMATIC1111 擴充套件不一定相容 Neo。Notebook 只會另外安裝你在 `EXTENSION_URLS` 明確列出的 Git repository；若擴充套件造成啟動錯誤，可從 Google Drive 的 `forge-neo/extensions` 移除該目錄。

## 常見問題

- **`You do not have any model...`**：資料目錄中還沒有 checkpoint。這只是提示，WebUI 仍會啟動；將模型放進 `forge-neo/models/Stable-diffusion` 或用第 3 格下載後，在 WebUI 重新整理模型清單即可。
- **`NameError: name 'Path' is not defined` 或找不到 `DATA_DIR`**：執行階段重新啟動後跳過了第 1 格，請從第 1 格依序重跑。
- **`PyTorch is not able to access any compute device (GPU)`**：先確認執行階段是 GPU；若記錄中出現 `environment at: /usr`，代表套件裝到了系統 Python，請重新啟動執行階段並使用最新版 notebook。
- **Cloudflare 網址打不開**：等第 5 格出現 `Running on local URL` 再連線；Quick Tunnel 網址會比 WebUI 早準備好。具名 Tunnel 請確認 Public Hostname 指向 `http://localhost:7860`。
- **第 5 格結束後出現 `[cloudflared] ERR Connection terminated`**：這是 WebUI 停止後 notebook 關閉 Tunnel 的正常訊息。
- **介面版面跑掉、按 Generate 沒反應**：記錄中若有 `failed to accept QUIC stream`，是 Tunnel 的 QUIC 連線在 Colab 上不穩定，notebook 已改用 `--protocol http2`；若有 `[Errno 107] Transport endpoint is not connected`，是 Google Drive 掛載中斷，請停止第 5 格再重跑（會自動重新掛載），仍不行就重新啟動執行階段。
- **擴充套件導致錯誤**：刪除 Google Drive 中 `forge-neo/extensions` 內對應的目錄後重新啟動。

## 與舊版 notebook 的差異

舊版會安裝 TheLastBen 的 A1111 patch、固定舊版 Gradio/Pydantic，並覆寫 `modules/extras.py`、`modules/sd_models.py` 等 A1111 內部檔案。這些操作與 Forge Neo 不相容，已全部移除。Neo 現在自行管理相依套件與模型路徑。

## 授權與來源

- 本專案：[GNU AGPL-3.0](LICENSE)
- [Stable Diffusion WebUI Forge - Neo](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo)：AGPL-3.0
- 原始 notebook 工作流程源自 [TheLastBen/fast-stable-diffusion](https://github.com/TheLastBen/fast-stable-diffusion)
