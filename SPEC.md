# 規格書：NOVA 全球／區域業績儀表板

## 1. 專案概述

### 1.1 發想動機
我在實習時常常要整理業績報表：每個月從系統匯出好幾份 Excel（預算、滾動預估、實際出貨、未出貨訂單、經銷商銷售、裝置數量…），再手動整理成圖表給主管看，每次都要花不少時間。
剛好碰到這次作業，就想把這件事做成一個 **Dashboard 雛形**：上傳 Excel 後自動算出達成率、畫出圖表，讓報表製作更快，也讓我之後能把 Web App 實際應用在實習工作上。

這個作業版的設計：

- 全部寫在**單一 `index.html`**，雙擊或用 GitHub Pages 網址就能開。
- 因為要公開展示，所有公司名稱、客戶、產品型號、人名、金額都是**虛構的範例資料**，產品品牌命名為「NOVA」。
- 打開網頁就自動載入假資料，訪客不用準備任何檔案就能操作每個功能。

### 1.2 目標使用者
- **業務主管**：看各區域「預算 vs 實際」的達成率與趨勢。
- **業務助理**：查經銷商（VPP Partner）名單、誰是 Active、誰買了哪些產品。
- **產品／營運人員**：看裝置數量、授權狀況、重點客戶成長。
- **一般訪客（老師、同學）**：打開網址就能點來點去，不需要準備任何資料。

### 1.3 一句話說明
> 一個左側選單切換頁面、可依區域篩選，結合甜甜圈圖、長條圖、折線圖、可排序表格與明細彈窗的業績儀表板；資料全部是瀏覽器即時產生的假資料。

---

## 2. 技術規格

| 項目 | 規格 |
|---|---|
| 檔案 | 單一 `index.html`，CSS 寫在 `<style>`、JavaScript 寫在 `<script>` |
| 外部函式庫（CDN） | Chart.js 4.4.0（圖表）、chartjs-plugin-datalabels 2.2.0（圖上數字）、SheetJS 0.18.5（產生／讀取 Excel） |
| 開啟方式 | 本機雙擊即可（`file://`），或透過 GitHub Pages 網址開啟 |
| 假資料 | 開啟頁面時，用「固定亂數種子」在瀏覽器裡產生假的 Excel 檔，再交給網頁本身的 Excel 解析程式處理（和上傳真實檔案走同一套流程）；每次打開數字都一樣 |
| 儲存 | 解析後的資料存在瀏覽器 `localStorage`（Cache Data 頁可以檢視、刪除） |
| 隱私 | 不包含任何真實公司、客戶、產品型號、人名、金額；不會讀取訪客電腦裡的檔案 |

---

## 3. 功能規格

### 3.0 共通操作
- **左側選單**：分成 Sales、Devices & SI、VU Sales、Admin 四大群組，點選後立即切換頁面（不重新載入），目前所在頁面以綠色反白標示。
- **手機版**：螢幕寬度不足時選單自動收起，點左上角的漢堡按鈕（☰）展開，點選頁面後自動收回。
- **圖表共通互動**：滑鼠移到長條或折線點上會出現提示框（月份、數值）；點圖例可以隱藏／顯示某一組資料；長條上方直接標示數字。
- **彈窗**：所有明細彈窗右上角都有同一款 × 關閉按鈕。
- **金額單位**：卡片與圖表以「千美元（K）」顯示；明細表可切換「1／K」單位。

### 3.1 Sales › Overview（全球業績總覽）

**區域篩選**
- 右上角 **Region** 下拉選單：ALL、TW、ANZ、EU、VJP、MN、JPD、VU。
- 切換後，四張 KPI 卡片、月度追蹤圖、年度比較圖全部重新計算。

**四張 KPI 甜甜圈卡片**

| 卡片 | 計算方式 | 卡片下方 | 點擊後 |
|---|---|---|---|
| YTD（9A vs. 9B） | 1–9 月實際業績 ÷ 1–9 月預算 | 預算、實際金額；ALL 時列出各區域佔比 | 跳出「區域 × 月份」明細表 |
| MTD Achievement | 最近一個月實際 ÷ 當月預算 | 當月預算、實際 | 同上，只看當月 |
| 8A+4R | 8 個月實際 + 4 個月滾動預估，對比全年預算 | 全年預算、8A+4R 金額 | 同上，實際月份與預估月份並列 |
| Open PO | 未出貨訂單總額；紅色區塊是交期未定（TBD） | Open PO、TBD 金額 | 跳出 Open PO 依月份／TBD 的明細 |

- 甜甜圈中間顯示百分比；達成率 ≥ 100% 顯示綠色。

**「區域 × 月份」明細彈窗**
- 列＝各區域、欄＝各月份，最後兩欄是期間預算與達成率，最下面一列是合計。
- 右上角可切換金額單位（1／K）。
- 點表格中的數字可以再往下看該區域、該月份的**客戶明細**，並可按 Back 回上一層。

**月度追蹤圖（Budget vs. Rolling vs. Actual + Open PO）**
- 每個月並排顯示：Budget（灰）、Rolling（淺藍）、Actual（藍），未來月份顯示 Open PO（綠）與 TBD（紅）堆疊。
- 右側軸的橘色折線是每月達成率。
- 長條上方標示金額，多個標籤會自動錯開不重疊。

**Year-over-Year Monthly Comparison（年度比較）**
- 上方三張卡片：2024、2025、2026 各年度實際業績總額。
- 圖表：三年逐月長條並排，紅色折線是今年相對去年的 YoY 成長率（右側軸）。
- **點圖表**會打開完整資料表（每年逐月數字＋YoY%），下方有 **Custom Range YoY** 計算器：選擇起訖月份後按 Calculate YoY，算出該區間今年 vs 去年的成長率。

### 3.2 Devices & SI › Device Insights（裝置數據）

- 右上角 **Region** 選單：Global 或 US、EU、JP、AU、TW、LATAM、SEA、MEA，切換後整頁數字改為該區域。
- **NOVA Regional Progress Overview**：5 個甜甜圈
  - DTC Unlicensed／BTC Unlicensed：未購買授權的裝置比例。
  - DTC Expiring／BTC Expiring：付費裝置中 30 天內到期的比例。
  - Cloud Storage：已購買雲端備份的裝置中，實際啟用的比例。
  - 每個甜甜圈下方顯示「+N（+x%）vs last week」，和上週相比的變化。
- **Device Numbers**：Total、BTC、DTC、VSS、CloudSight 五張卡片，括號內是與上週相比的增減。
- **Device Numbers by Region**：各區域裝置數與佔比表格，裝置最多的區域以綠色、最少的以紅色標示。
- **Monthly and Weekly Devices Onboarding**：Total、BTC、DTC、Paid 四條趨勢線；過去的月份每月一點，本月則每週一點；上方卡片顯示最新數字與「vs last week」。
- **Connection License Distribution**：依組織類型與授權等級（xPro、xStd、Unlicensed、免費組織）交叉統計 BTC、VSS、DTC、CloudSight 的裝置數與各種比例。

### 3.3 Devices & SI › Top SI Analysis（SI 經銷商分析）

- 三張 KPI 卡片：SI 總數、SI 管理的 NOVA 裝置數、每個 SI 平均裝置數。
- **TOP SI Detailed Classification Data** 表格：SI 名稱、Email、主要區域、裝置總數、DTC、BTC（免費／付費）、IP Speaker、其他、組織數、純 BTC／純 DTC／混合組織數。
  - **點欄位標題排序**（再點一次反向），表格可上下捲動。
  - 依右上角 Region 篩選，只看該區域的 SI。

### 3.4 Devices & SI › NOVA KA Analysis（重點客戶分析）

- **KA Comparison 折線圖**：10 家重點客戶的裝置數成長（2025 年加總 + 2026 年逐月／逐期），每家一種顏色。
  - 點圖例可隱藏／顯示某家客戶。
  - **在線上按右鍵**可切換該條線的數字標籤位置，避免數字擠在一起。
- **Key Accounts 表格**：排名、客戶名稱、區域、是否為 KA（綠色 KA 標籤）、最新總數與各期數字；沒有資料的期間顯示「-」。
  - **點任一列**，下方明細圖就切換成該客戶的單獨趨勢圖。

### 3.5 VU Sales（美國／加拿大經銷銷售）

**頁面上方篩選（五個子頁共用）**
- **Region**：兩層選單，先選大區（US-East／US-West／Canada），再選子區域（共 13 個）；交易依郵遞區號／州別自動分區。
- **State**：只看某一州。
- **月份**：切換要看哪個月份的資料。

**Overview**
- 五個甜甜圈：POS 達成率（YTD）、POS MTD 達成率、Pipeline 達成率（YTD）、Pipeline MTD 達成率、VPP Partner Active 數；每張卡片下方列出目標、實際，以及 NOVA 產品佔比。
- **Monthly POS Performance**／**Monthly Pipeline Performance**：每月目標（灰）vs 實際（綠）長條圖。
- **Top 10 VPP Partners by YTD Sales** 橫條圖、**Monthly Hit Rate Trend** 達成率趨勢折線。

**Product Analysis**
- 四張卡片：產品數、YTD 產品銷售額、最大類別、最熱銷產品。
- 產品類別（Frontend／Backend／NOVA）營收甜甜圈、數量甜甜圈。
- **Top 15 Products by Sales** 長條圖、**Monthly Product Sales Trend** 面積圖。

**NOVA SI（NOVA 產品客戶清單）**
- 三張 KPI 卡片：NOVA SI 客戶數、2026 YTD、2026 MTD。
  - 點客戶數卡片 → 「每月客戶數」圖表彈窗。
  - 點 YTD 卡片 → 「營收拆解」彈窗：2024–2026 每月營收比較圖，並可自訂區間計算 YoY。
- 客戶表格：狀態（Active／Inactive／Unregistered）、客戶名稱、每月金額、YTD、去年與前年加總、平均、YoY、交易次數。
  - **搜尋框**即時篩選客戶名稱。
  - **快速篩選按鈕**：All／Active／Inactive／Unregistered，按鈕上顯示筆數。
  - **Columns** 按鈕：勾選要顯示哪些欄位。
  - **點欄位標題排序**。
  - **點客戶名稱** → 產品分布彈窗（買了哪些型號、金額、數量、佔比）。
  - 滑鼠移到狀態標籤上，顯示這位客戶是怎麼對應到經銷商編號的。
- 未註冊客戶的判斷：客戶名稱比對時忽略大小寫、空白、標點與 Inc／LLC／Co 等公司字尾，避免同一家公司因拼法不同被算成兩家。

**VPP Partners（經銷商明細）**
- 三張 KPI 卡片：經銷商數、YTD 銷售、MTD 銷售。
- 經銷商表格：狀態、VPP ID、名稱、州、等級、今年 YTD、去年、前年、平均、YoY、交易次數。
  - 搜尋（VPP ID 或名稱）、快速篩選（All／Active／Inactive／NOVA）、Columns 選欄位、點標題排序。
  - **點 VPP ID** → 該經銷商的產品分布彈窗（型號、類別、YTD／MTD、數量、交易數、佔比長條）。
- **Active 定義**：最近三個月內有任何一筆金額 > 0 的購買紀錄；退貨、折讓不算。

**Partner Progress（經銷商成長）**
- 三張卡片：經銷商總數、Active 數、今年新加入數。
- **Partner Growth Trend**：每月 Active 經銷商數折線圖。
- **New Partners Acquired**：每月新加入經銷商數。
- **Partner Level Distribution**：ELITE／SELECT／AUTHORIZED 三個等級的家數長條。
- **Top 10 Partners by YTD Sales**：排名、名稱、等級、VPP ID、金額。

### 3.6 Admin

**Data Upload**
- 10 張上傳卡片，每張對應一種報表 Excel：OPEN PO、OPEN PO - JPD（可輸入日圓匯率）、Actual、Actual History（可多檔）、Region Mapping、NOVA POS（可多檔）、Sales Budget、Rolling、Device Snapshot、VU Regional Sales Data。
- 作業公開版：**不會開啟檔案選擇視窗、不會讀取訪客的檔案**。點任一張卡片會載入對應的**假資料 Excel 檔**，卡片變成綠框並顯示假檔名。
- **Generate Dashboard** 按鈕：依已載入的檔案重新計算並產生整個儀表板。
- **Reload all demo data** 按鈕：清掉快取、一次重新載入全部假資料。

**Cache Data**
- 分成 Actual、Device Snapshot、Actual History、NOVA POS、Region Mapping 五區，列出瀏覽器裡快取的每一筆（檔名、快照日期或年份）。
- 每筆旁邊有 × 可單獨移除；每區右上角有 **Clear All**（Region Mapping 是 **Reset to default**）。

**Reports**
- **Print report**：列印目前的儀表板。
- **Export all data**：下載目前的整理後數據。
- **Data info**／**Help**：資料說明與使用說明。

**Settings**
- 預設區域（勾選）、預設月份範圍（全年／各季／上下半年）。
- 顯示選項：熱力圖、自動更新圖表、顯示圖例。
- 數字格式：千分位符號、小數位數。
- **Preview** 預覽、**Save & Apply** 儲存（存在瀏覽器，下次打開保留）、**Restore defaults** 還原。

---

## 4. 假資料設計

| 資料 | 內容 |
|---|---|
| Sales Budget／Rolling／Actual／Open PO | 7 個區域 × 12 個月，依季節係數上下浮動；Actual 只到 9 月 |
| Actual History | 2024、2025、2026 三年，逐年成長 |
| Device Snapshot | 2026/8/7 起每週一份、共 11 份，每週裝置數成長約 2% |
| VU Regional Sales | 13 個 Sub Region、48 家虛構經銷商、22 家未註冊客戶、10 個虛構產品型號 |
| NOVA POS | 2024–2026 每年一個檔案、每月一張工作表 |
| KA | 10 家虛構重點客戶 |

公司名稱由字庫隨機組合（如 Cobalt Integration Group、Falcon Security LLC），email 全部是 `@demo-partner.example`。

---

## 5. 視覺風格

| 項目 | 規格 |
|---|---|
| 主色 | Teal `#0d9488`（Sidebar 選取、按鈕） |
| 背景 | 淺灰 `#f4f5f2`；卡片白底、圓角、淡陰影 |
| 圖表色盤 | 藍 `#2a78d6`、橙 `#eb6834`、水綠 `#1baf7a`、黃 `#eda100`、洋紅 `#e87ba4`、綠 `#2f9e44`、紫 `#8b7ae0`、紅 `#e34948` |
| 狀態色 | 達成／成長：綠；警示：黃；未達成／衰退：紅 |
| 字型 | 內文 IBM Plex Sans、標題 Bricolage Grotesque、數字 IBM Plex Mono（等寬方便對齊） |

---

## 6. 開發流程與版本紀錄

每一版的做法都一樣：**先用文字跟 AI 說清楚想要什麼 → AI 實作 → 我實際操作、截圖 → 把看到的問題或新想法回報給 AI → 再修改**，反覆進行。

| 版本 | 想加的功能／發現的問題 | 請 AI 怎麼做 | 結果 |
|---|---|---|---|
| v1 | 每個月手動整理業績報表太花時間 | 做出第一版：上傳預算、滾動預估、實際出貨、未出貨訂單的 Excel，自動算出各區域達成率，畫出 KPI 卡片與月度圖表；另外有經銷商銷售頁 | 有了能用的業績總覽 |
| v2 | 所有程式都擠在一個檔案，越改越難維護；有些頁面用不到；部分中文變亂碼 | 把 CSS、JavaScript、資料拆成獨立檔案；刪掉用不到的頁面與程式；修正亂碼；「最後更新時間」改成會記住；修正瀏覽器儲存空間不足的錯誤 | 程式結構清楚，之後好改 |
| v3 | 想追蹤裝置數量與 SI 經銷商 | 新增 Device & SI Insights 頁：先用假資料做出畫面，依我的回饋調整兩輪版面，最後改成讀取每週上傳的裝置報表 | 裝置頁正式上線 |
| v4 | 想比較今年和去年同期；整體配色太雜 | 新增 Year-over-Year 年度比較圖，可一次上傳多個年度檔案；全站換新配色；圖上加數字標籤、達成率與 YoY 折線，並可點開完整資料表 | 一眼看出成長或衰退 |
| v5 | AI 幫忙檢查程式，找到 5 個計算錯誤；想點卡片看明細 | 修好 5 個錯誤；點 YTD／MTD 卡片會跳出「區域 × 月份」明細表，有合計列、可切換「元／千元」；所有彈窗的關閉按鈕統一；表格可排序、表頭固定 | 數字可以往下追查 |
| v6 | 想看每月、每週新增多少裝置；圖上的數字互相重疊 | 新增裝置上線趨勢圖；數字標籤間距試了 30→18→6→10px，最後選 10px；排除示範用帳號；趨勢資料改成由每週報表自動計算，不用再手動輸入 | 圖表清楚、資料自動更新 |
| v7 | 想知道誰買了 NOVA 產品，包括還沒註冊的客戶 | 依產品型號清單自動判斷 NOVA 產品；經銷商頁加 NOVA 篩選；新增 NOVA SI 頁，列出已註冊和未註冊客戶，有快速篩選、欄位顯示切換、產品分布彈窗 | 客戶名單一次看完 |
| v8 | NOVA SI 的圖表太多、顏色不好分辨 | 合併成一張圖，加上 2024–2026 每月營收比較，依回饋調整兩次顏色 | 圖表更精簡 |
| v9 | 上方分頁越來越多，很難找 | 介面大改版：上方分頁列改成左側分組選單、移除頂部工具列、換背景色；裝置數拆出更多類別；新增重點客戶（KA）分析頁；快取資料可以逐筆刪除；KA 圖表標籤太擠，改成可用右鍵切換標籤位置 | 介面更好找、更現代 |
| v10 | 每次都要把報表檔寄給同事，很麻煩 | 新增「發布」功能：上傳後一鍵發布，同事打開網址就看到最新資料，並依帳號只顯示有權限的區域；載入時顯示 Loading 畫面 | 不用再寄檔案（作業公開版不含此功能） |
| v11 | 想讓字型、顏色更一致 | 依設計規範統一字型與色彩；修正彈窗被卡住、表格少了內距 | 視覺一致 |
| v12 | 客戶名稱拼法不一致對不起來；區域劃分要更細；Active 定義要更準 | 客戶名稱比對時忽略大小寫、標點與 Inc／LLC 等字尾；區域改成兩層（大區 → 子區域），依郵遞區號自動分區；Active 改成「最近三個月有購買」；KA 表格改版 | 數字更準確 |
| v13 | 要做成作業公開：只能有一個 `index.html`，而且不能有任何真實資料 | 把所有資料模組合併成一個 `index.html`；上傳區改成載入瀏覽器產生的假 Excel，打開網頁自動載入；所有公司、客戶、型號、人名、金額換成虛構資料 | 雙擊就能開、可以公開 |
