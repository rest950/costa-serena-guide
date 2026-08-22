# 🔱 歌詩達郵輪 莎倫娜號 (Costa Serena) 樓層設施互動導覽

> **海上古羅馬．眾神之船 — 全船 13 層客用甲板設施、餐廳、泳池、娛樂與防迷路指南**

[![Open Source Love](https://badges.frapsoft.com/os/v1/open-source.svg?v=103)](https://github.com/)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
[![Mobile Friendly](https://img.shields.io/badge/Mobile-First-blue.svg)](https://github.com/)

---

## 📖 專案簡介 (About)

本專案為 **歌詩達郵輪 莎倫娜號 (Costa Serena)** 的**輕量化、Mobile-First 互動式靜態導覽網頁**與 **Markdown 樓層手冊**。

針對智慧型手機瀏覽體驗深度優化，提供即時設施搜尋、全樓層剖面速覽、手勢滑動切換甲板、個人專屬設施收藏、防迷路動線指南與全船餐飲速查等功能，方便旅客在郵輪旅程中隨時快速查閱。

---

## ✨ 主要特色 (Features)

* 📱 **Mobile-First 極致響應式設計**：針對 iPhone / Android 螢幕比例調校，不卡頓、不溢出、支援 PWA/加入主畫面全螢幕瀏覽。
* 🏛️ **13 層客用甲板全收錄**：Deck 1 至 Deck 14（無 13 樓）中英文名稱、設施位置、開放性質（免費/自費）完整收錄。
* 👆 **手風琴摺疊卡片 (Accordion UI)**：預設精簡列表，點擊即時展開詳細說明與貼心提醒，閱讀流暢省時。
* 🚢 **全樓層速覽矩陣 (Overview)**：垂直船身剖面結構，支援「依需求直達」（想游泳、吃大餐、看秀、做 Spa、帶小孩等）。
* 🔍 **全船即時搜尋 Overlay**：支援關鍵字與多分類標籤（美食、泳池、娛樂、酒吧、水療、親子、醫療服務等）即時過濾。
* ⭐ **我的收藏 (Favorites)**：離線 LocalStorage 儲存，快速標記心儀設施與常去餐廳。
* 🧭 **防迷路動線指南**：詳細說明第 5 層與第 9 層黃金貫通甲板、3/4 樓廚房阻隔走法與三大電梯群分佈。
* 🍽️ **全船餐飲速查**：免費包套餐廳與付費特色餐廳（海上撈火鍋、現烤披薩、日式壽司、星廚晚宴等）對照表。
* ⚡ **零相依性純靜態網頁**：無需後端伺服器，單一 HTML 檔案隨開隨用，亦可離線快取。

---

## 📂 檔案結構 (Project Structure)

```text
├── index.html                    # 互動導覽單頁應用程式 (Single Page App)
├── costa_serena_deck_guide.md    # 完整 13 層樓層設施與餐廳 Markdown 手冊
└── README.md                     # 專案說明與授權條款
```

---

## 🚢 莎倫娜號 基本諸元 (Ship Specifications)

| 項目 | 規格參數 |
| :--- | :--- |
| **總噸位 (Gross Tonnage)** | 114,500 GT |
| **船身長度 (Length)** | 290 公尺 |
| **船身寬度 (Beam)** | 35.5 公尺 |
| **最大載客量 (Passengers)** | 3,780 人 |
| **總客房數 (Staterooms)** | 1,504 間（含套房與水療艙房） |
| **客用甲板數 (Decks)** | 13 層（Deck 1 至 Deck 14，無 Deck 13） |
| **航行最高時速 (Max Speed)** | 21.5 節 (Knots) |

---

## 📄 開源授權與非商業用途宣言 (Open Source License & Disclaimer)

### ⚖️ 授權條款 (License)
本專案採用 **[創用 CC 姓名標示-非商業性 4.0 國際 (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/deed.zh_TW)** 授權條款釋出。

```text
您可以自由：
  • 分享 — 以任何媒介或格式重製及散布本素材
  • 修改 — 重混、轉換本素材，以及依本素材建立新內容

惟須遵守下列條件：
  • 姓名標示 (Attribution) — 您必須給予適當表彰，提供指向本授權條款的連結。
  • 非商業性 (Non-Commercial) — 您不得將本素材（包括程式碼與整理之內容）用於商業目的、營利販售或任何形式的有償服務。
```

---

### ⚠️ 免責聲明 (Disclaimer)

1. **非官方獨立整理**：本專案為旅客個人社群愛好與旅行實用目的所整理製作之開放原始碼專案，**並非歌詩達郵輪官方 (Costa Crociere S.p.A.) 之官方網站或受其直接贊助之專案**。
2. **資訊時效性**：各項設施營運時間、各航次特色餐廳菜單、收費標準、派對演出時間與安全規範，請一律以登船當日由船方發行之 **每日船上日誌 (Costa Diaria / 今日郵輪)** 及船上官方廣播公告為準。
3. **商標權宣告**：「Costa Cruises」、「Costa Serena」、「歌詩達郵輪」及各餐廳與設施之名稱、商標與品牌權益均歸其各自之法定註冊權利人所有。

---

*Enjoy your voyage aboard the Costa Serena! 🌊🚢*
