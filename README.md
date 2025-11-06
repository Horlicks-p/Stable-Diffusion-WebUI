# Stable Diffusion AUTOMATIC1111 for Google Colab with Cloudflare Tunnel

基於 [TheLastBen/fast-stable-diffusion](https://github.com/TheLastBen/fast-stable-diffusion) 專案修改的 Google Colab notebook,支援透過 Cloudflare Tunnel 安全存取 AUTOMATIC1111 WebUI。

## ✨ 功能特色

- 🚀 快速部署 AUTOMATIC1111 Stable Diffusion WebUI 到 Google Colab
- 💾 支援 Google Drive 整合,模型與生成圖片持久化儲存
- 🌐 內建 Cloudflare Tunnel 支援,無需公開 IP 即可遠端存取
- 🔐 支援帳號密碼驗證,保護你的 WebUI 介面
- 📦 自動安裝所有必要的依賴套件
- 🎨 支援自訂模型路徑與擴充套件安裝
- ⚡ 使用 xFormers 優化記憶體使用

## 📋 系統需求

- Google Colab 帳號(建議使用 Colab Pro 以獲得更好的 GPU)
- Google Drive 空間(至少 10GB 以上)
- Cloudflare 帳號(用於設定 Tunnel)

## 🚀 使用方式

### 1. 連接 Google Drive

執行第一個程式碼區塊來掛載你的 Google Drive:

```python
# 可選:如果使用共享雲端硬碟,請填入名稱
Shared_Drive = ""  # 留空則使用個人 MyDrive
```

### 2. 安裝/更新 AUTOMATIC1111

執行第二個程式碼區塊來安裝或更新 Stable Diffusion WebUI:

- 首次執行會自動下載並安裝完整的 AUTOMATIC1111 repository
- 後續執行會更新到最新版本
- 所有檔案都會儲存在 `gdrive/MyDrive/sd/` 目錄下

### 3. 安裝需求套件

執行第三個程式碼區塊來安裝所有必要的依賴:

- Python 套件
- 系統依賴
- 預訓練模型(如果尚未下載)

### 4. 下載模型

在第四個程式碼區塊中可以選擇:

- 從 Civitai 下載模型
- 使用 Hugging Face 模型
- 或使用已經下載好的本地模型

支援的檔案格式:
- Safetensors (推薦)
- Checkpoint (.ckpt)

### 5. 啟動 WebUI

最後一個程式碼區塊用於啟動 Stable Diffusion WebUI:

**可設定的參數:**

- **Authentication** (驗證)
  - `User`: 使用者名稱
  - `Password`: 密碼
  - 留空表示不啟用驗證

- **Cloudflare Tunnel** (隧道設定)
  - `Use_Cloudflare_Tunnel`: 是否使用 Cloudflare Tunnel
  - `Tunnel_to_Region`: 選擇伺服器區域(預設:`asia`)

- **Model Settings** (模型設定)
  - `Model`: 使用的模型檔案名稱
  - `Model_Path`: 自訂模型路徑(選填)
  - `Vae`: VAE 模型檔案名稱(選填)
  - `Vae_Path`: 自訂 VAE 路徑(選填)

- **Extensions** (擴充功能)
  - `ControlNet`: 是否安裝 ControlNet 擴充
  - `Civitai_Browser`: 是否安裝 Civitai Browser+
  - `Additional_Extensions`: 額外的擴充套件 Git URL(每行一個)

## 📁 目錄結構

```
gdrive/MyDrive/sd/
├── stable-diffusion-webui/        # AUTOMATIC1111 主程式
│   ├── models/
│   │   ├── Stable-diffusion/      # Checkpoint 模型
│   │   ├── VAE/                   # VAE 模型
│   │   ├── Lora/                  # LoRA 模型
│   │   └── ControlNet/            # ControlNet 模型
│   ├── extensions/                # 擴充套件
│   ├── outputs/                   # 生成的圖片
│   └── cache/                     # 快取檔案
└── stablediffusion/               # 預訓練權重
```

## 🌐 Cloudflare Tunnel 設定

使用 Cloudflare Tunnel 的優點:
- 不需要 ngrok 或其他第三方服務
- 更穩定的連線
- 支援自訂網域
- 更好的安全性

啟動後會顯示一個 `trycloudflare.com` 的臨時網址,可直接透過瀏覽器存取。

## ⚙️ 進階設定

### 自訂模型路徑

```python
Model_Path = "/content/gdrive/MyDrive/my-custom-models"
```

### 安裝額外擴充

在 `Additional_Extensions` 欄位中輸入 Git repository URL:

```
https://github.com/username/extension1
https://github.com/username/extension2
```

### 啟用 API 模式

WebUI 預設會啟用 API 端點,可以透過 HTTP 請求來生成圖片。

## ⚠️ 注意事項

1. **Google Colab 限制**
   - 免費版有使用時間限制
   - 長時間無操作可能會被中斷連線
   - 建議使用 Colab Pro 以獲得更好的體驗

2. **儲存空間**
   - 確保 Google Drive 有足夠空間
   - 模型檔案通常需要 2-7GB
   - 生成的圖片也會佔用空間

3. **安全性**
   - 建議設定帳號密碼驗證
   - 不要分享你的 Cloudflare Tunnel URL
   - 定期更改密碼

4. **效能**
   - GPU 類型會影響生成速度
   - xFormers 可以降低記憶體使用
   - 大尺寸圖片需要更多 VRAM

## 🐛 常見問題

**Q: 顯示記憶體不足錯誤?**
A: 嘗試減少圖片尺寸或 batch size,或升級到 Colab Pro。

**Q: Cloudflare Tunnel 無法連線?**
A: 重新執行啟動區塊,或檢查網路連線。

**Q: 模型無法載入?**
A: 確認模型檔案路徑正確,且檔案格式為 safetensors 或 ckpt。

**Q: 擴充套件安裝失敗?**
A: 檢查 Git URL 是否正確,或手動到 extensions 目錄安裝。

## 📝 授權

本專案基於 [TheLastBen/fast-stable-diffusion](https://github.com/TheLastBen/fast-stable-diffusion) 修改。

AUTOMATIC1111 WebUI: [AGPL-3.0 License](https://github.com/AUTOMATIC1111/stable-diffusion-webui/blob/master/LICENSE.txt)

## 🙏 致謝

- [TheLastBen](https://github.com/TheLastBen) - 原始 fast-stable-diffusion 專案
- [AUTOMATIC1111](https://github.com/AUTOMATIC1111) - Stable Diffusion WebUI
- Stability AI - Stable Diffusion 模型

## 🔗 相關連結

- [AUTOMATIC1111 WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
- [Stable Diffusion](https://github.com/Stability-AI/stablediffusion)
- [Civitai](https://civitai.com/) - 模型分享社群
- [Hugging Face](https://huggingface.co/) - AI 模型平台
