# 香港宏利強積金 (Manulife MPF) 自動定時數據抓取與 GitHub Release 發佈

本專案使用 **GitHub Actions** 每天自動定時從香港宏利（Manulife HK）官方 API 抓取「**宏利環球精選(強積金)計劃**」下所有成份基金的最新價格、投資檔案及歷史淨值走勢，輸出結構化 **CSV 檔案**，並**自動發佈至 GitHub Releases 供直接下載**。

---

## 📦 GitHub Releases 發佈內容

每次執行後，GitHub Actions 會自動建立或更新當天的 Release（標籤格式如 `v2026.11.01`），並在 Release Assets 中提供直接下載連結：

1. **`manulife_mpf_funds_summary.csv`**：全計劃所有成份基金當日最新 NAV、每日變動、各期回報率、風險級別總表。
2. **`manulife_mpf_funds_details.csv`**：全計劃基金詳細檔案總表（投資目標、管理費、發行價等）。
3. **`manulife_mpf_history_all.zip`**：所有個別基金歷史走勢 CSV 的打包壓縮檔。
4. **`{基金編號}.csv`**（例如 `SHK136.csv` 等）：個別基金的完整歷史走勢 CSV，可直接點選下載。

同時，資料亦會同步提交儲存在倉庫內的 `data/` 目錄中，方便 Git 歷史追蹤。

---

## 📊 CSV 欄位說明

### 1. `manulife_mpf_funds_summary.csv`（每日全基金總表）
* `FundCode`：基金編號（如 `SHK136`）
* `FundName`：成份基金名稱（如 `宏利MPF中華威力基金`）
* `Currency`：計價貨幣（如 `HKD`）
* `NAV`：最新每單位資產淨值
* `AsOfDate`：數據截至日期（YYYY-MM-DD）
* `DailyChangePrice`：每日價格變動
* `DailyChangePercent`：每日變動百分比
* `RiskRating`：風險級別（1-7）
* `RiskRatingAsOfDate`：風險級別評定日期
* `Return1Y` / `Return3Y` / `Return5Y`：1年/3年/5年累積回報率
* `ReturnSinceLaunch`：成立至今累積回報率

### 2. `history/{FundCode}.csv`（單檔基金歷史價格走勢）
* `Date`：日期（YYYY-MM-DD）
* `FundCode`：基金編號（如 `SHK136`）
* `NAV`：單位資產淨值
* `Status`：交易狀態（正常或暫停 "S"）

---

## 🚀 快速部署至 GitHub（三步驟）

### 第一步：初始化本地倉庫並提交

```bash
git init
git add .
git commit -m "feat: setup daily MPF fetch and GitHub Release workflow"
git branch -M main
```

### 第二步：關聯遠端 GitHub 倉庫並推送

在 GitHub 建立一個新倉庫（例如 `manulife-mpf`），然後執行：

```bash
git remote add origin https://github.com/<你的GitHub帳號>/manulife-mpf.git
git push -u origin main
```

### 第三步：開啟 GitHub Actions 寫入權限（最關鍵！）

GitHub Action 需要寫入權限才能建立 **Release** 並上傳資產：
1. 進入你的 GitHub 倉庫，點擊上方 **Settings**。
2. 左側選單點選 **Actions** -> **General**。
3. 滾動到 **Workflow permissions**。
4. 選擇 **Read and write permissions**。
5. 點擊 **Save** 儲存。

---

## ⏰ 執行機制說明

1. **定時自動執行**：
   工作流程定義在 `.github/workflows/daily-fetch.yml`。
   預設於香港時間 **每週一至週五 22:00 (UTC 14:00)** 自動執行，此時間點剛好是香港金融市場結算完成、強積金公佈當日最新淨值之時。

2. **手動隨時觸發**：
   可隨時前往 GitHub 倉庫的 **Actions** 標籤頁 -> 點選 **Daily Fetch Manulife MPF Funds Data** -> 點擊右側 **Run workflow** 進行手動立即執行。
   執行完成後，即可直接在右側 **Releases** 頁面下載最新生成的 CSV 檔案！

---

## ⚙️ 設定檔 `config.json` 說明

你可以依需求修改 `config.json`：

```json
{
  "productLine": "mpf",
  "overrideLocale": "zh_Hant_HK",
  "fetch_summary": true,
  "fetch_history": true,
  "fetch_details": true,
  "fetch_all_funds_history": true,
  "target_funds": [
    "SHK136"
  ],
  "request_delay": 1.0,
  "output_dir": "data"
}
```

* `fetch_all_funds_history`: 設為 `true` 會自動抓取計劃下**所有成份基金**的歷史走勢 CSV；若改為 `false`，則只會抓取 `target_funds` 陣列中指定的基金代碼（如 `SHK136` 等）。
* `request_delay`: 每次向 API 請求之間的間隔秒數（預設 1 秒），避免對官方伺服器造成負擔。
