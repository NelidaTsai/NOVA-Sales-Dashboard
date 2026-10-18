# NOVA 業績儀表板（作業示範版）

一個單頁、可互動的業績儀表板 Web App：左側選單切換頁面，可依區域篩選，包含 KPI 甜甜圈卡片、月度趨勢圖、年度比較圖、可搜尋／排序的經銷商表格，以及點擊後跳出的明細彈窗。

- **整個網站只有一個 `index.html`**，CSS 與 JavaScript 都寫在裡面，雙擊就能開啟。
- **所有資料都是虛構的**：打開頁面時，在瀏覽器裡用固定亂數種子產生假 Excel 檔，再交給儀表板的解析程式處理。不會讀取你電腦裡的任何檔案。
- 規格書：[`SPEC.md`](SPEC.md)

## 使用的函式庫（CDN）
- [Chart.js](https://www.chartjs.org/) 4.4.0、chartjs-plugin-datalabels 2.2.0
- [SheetJS](https://sheetjs.com/) 0.18.5
