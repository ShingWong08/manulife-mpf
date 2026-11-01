# 香港宏利強積金 (Manulife MPF) 自動定時數據抓取與 GitHub Release 發佈

本專案使用 **GitHub Actions** 每天自動定時從香港宏利（Manulife HK）官方 API 抓取「**宏利環球精選(強積金)計劃**」全 29 檔成份基金的最新價格、資產類別、投資檔案及歷史淨值走勢，並**自動發佈至 GitHub Releases（共 32 個檔案）供直接下載**。

---

## 📦 GitHub Release 32 個檔案清單

每次 Action 自動執行完畢後，會在 GitHub Release（Tag 格式如 `v2026.11.01`）中發佈剛好 **32 個檔案**：

### 1. 全計劃當日總匯總表 (1 個)
* **`manulife_mpf_funds_summary.csv`**：全計劃 29 檔基金當日總匯總表
  * **欄位**：基金代號、成份基金名稱、計價幣別、資產類別、單位資產淨值、截至日期、每日變動價格、每日變動百分比、每月公佈利率、風險級別、風險級別截至日期、1年回報率、3年回報率、5年回報率、成立至今回報率。

### 2. 全計劃投資檔案總表 (1 個)
* **`manulife_mpf_funds_details.csv`**：29 檔基金投資檔案總表
  * **欄位**：基金代號、成份基金名稱、計價幣別、資產類別、發行日期、發行價、風險級別、投資目標說明、管理費。

### 3. 一鍵打包全部檔案 ZIP (1 個)
* **`manulife_mpf_all_files.zip`**：包含上述 2 個總表 CSV 以及全數 29 檔基金歷史走勢 CSV 的完整壓縮包。

### 4. 29 檔個別基金歷史價格走勢 CSV (29 個)
每個基金皆有獨立的歷史走勢 CSV，可隨時單獨點擊下載（欄位：日期、基金代號、單位資產淨值、狀態說明）：
* **保證類 (2檔)**：`DHK121.csv`, `SHK122.csv`
* **混合資產類 (13檔)**：`SHK123.csv`, `SHK124.csv`, `SHK134.csv`, `SHK135.csv`, `SHK138.csv`, `SHK140.csv`, `SHK141.csv`, `SHK142.csv`, `SHK143.csv`, `SHK144.csv`, `SHK148.csv`, `SHK149.csv`, `SHK150.csv`
* **貨幣市場類 (1檔)**：`SHK125.csv`
* **股票類 (9檔)**：`SHK126.csv`, `SHK127.csv`, `SHK128.csv`, `SHK129.csv`, `SHK130.csv`, `SHK131.csv`, `SHK136.csv`, `SHK137.csv`, `SHK145.csv`
* **債券類 (4檔)**：`SHK132.csv`, `SHK133.csv`, `SHK146.csv`, `SHK147.csv`

> **1 (summary) + 1 (details) + 1 (zip) + 29 (單一基金 CSV) = 剛好 32 個檔案**

---

## 🚀 部署至 GitHub

本地 Git 倉庫及 Commit 已為你建立完畢，只需在終端執行：

```bash
# 1. 關聯你在 GitHub 建立的倉庫（請替換為你的倉庫地址）
git remote add origin https://github.com/<你的GitHub帳號>/manulife-mpf.git

# 2. 推送至 GitHub
git push -u origin main
```

### 關鍵權限設定（必做）
1. 進入你的 GitHub 倉庫頁面，點選上方 **Settings**。
2. 左側欄位點選 **Actions** -> **General**。
3. 滾動到 **Workflow permissions**，選擇 **Read and write permissions**。
4. 點擊 **Save** 儲存。

---

## ⏰ 執行時間

* **每日定時執行**：每週一至週五 **UTC 14:00（香港時間 HKT 22:00）** 自動排程執行。
* **隨時手動執行**：前往 GitHub 倉庫的 **Actions** -> **Daily Fetch Manulife MPF Funds Data** -> 點擊 **Run workflow** 即可隨時更新。
