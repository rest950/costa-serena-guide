# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 專案性質

Costa Serena 郵輪樓層設施導覽的**零相依性靜態站台**。無 package.json、無 build step、無測試、無 lint、無 CI。整站就是根目錄的 `index.html`（CSS + 資料 + JS 全內嵌），部署方式為 GitHub Pages 直接服務 main 分支根目錄（`d2a28af` 已移除 Surge CNAME）。

## 開發指令

```bash
open index.html                 # 直接預覽（file:// 即可，無外部 API 依賴）
python3 -m http.server 8000     # 需模擬 http:// 時使用
```

Google Fonts 是唯一外部資源；離線環境會 fallback 到 system font，非錯誤。

## 架構

### 單一資料來源：`index.html` 的 `const D`（grep `^const D=` 取行號）

整份甲板／設施資料是**一行極長的 minified 陣列**（約 25K 字元，14 層 / 76 項設施）。用 Read/Edit 處理這行時要注意 token 消耗；大批改動請改用「Python 產生器 → 整行 replace」的方式，單點修正才用精準 string match。

內容基準為 **Costa 官方甲板圖**（`curl https://www.costacruises.eu/deckplan.deckList.se.json` → `/content/dam/deck-plan/se/SERENA-DECK-NN-edited.svg`，SVG 內 `<text class="custom-text">` 即場館名）。cruisedeckplans / cruisemapper 是改裝前配置，勿採用。

但官方 EU 站的甲板圖**不畫亞洲航線限定場館**——海上撈火鍋（Deck 9 船尾自助餐區旁，付費）實際存在但官方圖上沒有。官方圖是「基準」不是「唯一真相」，刪東西前先確認不是航線限定。

```
Deck  = { id, num, zh, it, icon, gold, desc, nature, tags[], fac[] }
Fac   = { id, zh, en, cat, loc, free, icon, desc, tips }
```

三個不可破壞的約束：

1. **陣列順序即畫面順序**（14 → 0，無 13 樓；Deck 0 為醫療層，非客艙層）。`index.html:770` 的 swipe 換層直接用 `D` 的陣列 index 前後移動，重排陣列會讓滑動方向與樓層高低脫鉤。
2. **`fac.id` 是 localStorage key**（`cs_stars`，`index.html:759`/`:896`）。命名慣例 `f<deck>_<n>`；改 id 會孤兒化既有使用者收藏。
3. **`cat` 是封閉列舉**（9 種：dining / pool / entertainment / bar / spa / kids / service / shopping / cabin）。新增分類需同步三處：`D` 內的 `cat`、搜尋 chip 的 `data-cat`（`index.html:653-662`）、`renderOverview` 內 pill 配色對映（`index.html:830`）。

### 渲染

無框架，全部 `innerHTML` 字串樣板。狀態只有 4 個全域變數（`index.html:759`：`mode` / `deckId` / `filter` / `stars`），任何狀態變更後統一呼叫 `render()`（`index.html:797`）分派到三個模式：

| mode | 函式 | 說明 |
|---|---|---|
| `overview` | `renderOverview` | 全船剖面，各層可摺疊；頂部 `qf-grid` 快捷卡的樓層字串是硬編碼，改資料時要一起改 |
| `single` | `renderSingle` | 單層詳情，支援 swipe 換層 |
| `starred` | `renderStarred` | localStorage 收藏 |

`renderFacItem` 是三種模式共用的設施卡片產生器，改卡片外觀只需動這裡。搜尋（`doSearch`）獨立走 overlay，不經過 `render()`。

改完資料的驗證方式：抽出 `<script>` 內容跑 `node --check`，再用 stub 過的 `document`/`localStorage` 呼叫三個 render 函式做 smoke test（無測試框架，手動跑即可）。

### 硬編碼、不由 `D` 衍生的內容

改設施資料時**不會**自動連動，需手動同步：

- 動線指南 modal（`id="navModal"`）
- 餐飲速查 modal（`id="dineModal"`）
- 全樓層速覽的快捷卡 `qf-grid`（在 `renderOverview` 內）
- 船舶諸元 bar（`id="specsBar"`，與 README 表格重複）
- `costa_serena_deck_guide.md` — 同一份甲板資料的人類可讀版，與 `D` 各自獨立維護

### CSS

Mobile-first，斷點以 320–428px 為 base。所有色彩與尺寸集中在 `index.html:12` 的 `:root` token；`--header-h` / `--bottom-h` / `--safe-*`（iOS safe-area）被 `body` padding 與 fixed 元件共用，改動會同時影響 header、bottom nav 與捲動區。

## 內容規範

- 全站面向旅客，一律 zh-TW；設施同時保留 `zh` 與 `en` 官方名稱。
- 授權為 CC BY-NC 4.0，非官方專案。README 已載明免責聲明：營運時間／收費以船上每日日誌為準——新增設施描述時勿寫死具體時間與價格。
