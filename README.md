# 香港宏利強積金 (Manulife MPF) 自動定時數據抓取與 CSV 匯出

本專案使用 **GitHub Actions** 每天自動定時從香港宏利（Manulife HK）官方 API 抓取「**宏利環球精選(強積金)計劃**」下所有成份基金的最新價格、投資檔案及歷史淨值走勢，並自動輸出為結構化的 **CSV 檔案** 提交保存至 GitHub 倉庫中。

---

## 輸出檔案結構

執行後會自動在 `data/` 目錄下生成下列 CSV 與數據檔：

```text
data/
├── manulife_mpf_funds_summary.csv   # 【每日總表】全計劃所有基金當日最新 NAV、每日變動、各期回報率、風險級別
├── manulife_mpf_funds_details.csv   # 【詳細檔案總表】各基金投資目標、成立日期、發行價、管理費等
├── history/                         # 【歷史淨值走勢目錄】
│   ├── SHK136.csv                   # 宏利 MPF 中華威力基金歷史走勢 (Date, FundCode, NAV, Status)
│   ├── SHK101.csv                   # 其他各基金歷史淨值 CSV
│   └── ...                          
└── details_json/                    # 各基金詳細資料的原始 JSON 備份
```

### 1. `manulife_mpf_funds_summary.csv` 欄位說明
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

### 2. `history/{FundCode}.csv` 歷史價格走勢欄位說明
* `Date`：日期（YYYY-MM-DD）
* `FundCode`：基金編號（如 `SHK136`）
* `NAV`：單位資產淨值
* `Status`：交易狀態（正常或暫停 "S"）

---

## 快速部署至 GitHub

### 第一步：初始化本地倉庫並提交

在專案目錄下執行以下指令：

```bash
git init
git add .
git commit -m "feat: initial commit for Manulife MPF auto fetcher"
git branch -M main
```

### 第二步：關聯遠端 GitHub 倉庫並推送

在 GitHub 建立一個新倉庫（例如 `manulife-mpf`），然後執行：

```bash
git remote add origin https://github.com/<你的GitHub帳號>/manulife-mpf.git
git push -u origin main
```

### 第三步：開啟 GitHub Actions 寫入權限（重要！）

為了讓 GitHub Action 能夠將產生的 CSV 自動 `git push` 回本倉庫，必須開啟權限：
1. 進入 GitHub 倉庫頁面，點擊上方 **Settings**。
2. 在左側選單點擊 **Actions** -> **General**。
3. 向下捲動至 **Workflow permissions** 區塊。
4. 選擇 **Read and write permissions**。
5. 勾選 **Allow GitHub Actions to create and approve pull requests**（選填）。
6. 點擊 **Save** 儲存。

---

## 執行機制說明

1. **定時自動執行**：
   工作流程定義在 `.github/workflows/daily-fetch.yml`。
   預設於香港時間 **每週一至週五 22:00 (UTC 14:00)** 自動執行，此時間點剛好是香港金融市場結算完成、強積金公佈當日最新淨值之時。

2. **手動隨時觸發**：
   可隨時前往 GitHub 倉庫的 **Actions** 標籤頁 -> 點選 **Daily Fetch Manulife MPF Funds Data** -> 點擊右側 **Run workflow** 進行手動立即執行。

---

## 設定檔 `config.json` 說明

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

* `fetch_all_funds_history`: 設為 `true` 會自動抓取計劃下**所有基金**的歷史走勢 CSV；若只希望抓取特定幾檔，可改為 `false`，並在 `target_funds` 陣列中指定基金代號（例如 `["SHK136", "SHK101"]`）。
* `request_delay`: 每次向 API 請求之間的間隔秒數（預設 1 秒），避免對官方伺服器造成負擔。
