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

內容基準為 **Costa 官方甲板圖**（`curl https://www.costacruises.eu/deckplan.deckList.se.json` → `/content/dam/deck-plan/se/SERENA-DECK-NN-edited.svg`）。

解析 SVG 要抓 **`data-*` 屬性，不要抓 `<text>`**：帶 `data-location` 的元素（`path`/`rect`/`polygon`…）上有 `data-en` / `data-tw` / `data-category`，是完整場館清單；`<text class="custom-text">` 只印出部分場館，曾因此漏掉 Cupido Piano Bar。`data-tw` 是官方繁中但品質差（混簡體字），僅供對照勿照抄。

costacrociere.it 與 costacruises.eu 的 deckList 與 14 張 SVG **位元組相同**，不必重複爬。兩站頁面內嵌 JSON 另有場館文案與 FAQ（`termsTitle`/`termsDescription`），是免費／付費與規格的唯一線索。

cruisedeckplans / cruisemapper 是改裝前配置，勿採用。

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
| `overview` | `renderOverview` | 全船剖面，各層可摺疊；頂部 `qf-grid` 快捷卡由 `QF` 產生 |
| `single` | `renderSingle` | 單層詳情，支援 swipe 換 Deck |

**Deck bar 有兩個易踩的坑**：① 全域 touch swipe 掛在 `document` 上，必須排除水平捲動區與覆蓋層（`NO_SWIPE` 選擇器），否則在 chip 列上快滑會誤觸換 Deck；② `renderDeckBar` 不可每次 `innerHTML` 重建（會把 `scrollLeft` 歸零），只在 chip 數量不符時才重建，並靠 `centeredDeck` 守衛避免每次 render 都`scrollIntoView` 把使用者捲動位置拉回去。
| `starred` | `renderStarred` | localStorage 收藏 |

`renderFacItem(f, d, scope)` 是三種模式共用的設施卡片產生器，改卡片外觀只需動這裡。搜尋（`doSearch`）獨立走 overlay，不經過 `render()`。

**搜尋 overlay 與 `#mainContent` 同時存在於 DOM**，所以卡片 DOM id 必須帶 `scope` 前綴（搜尋用 `s_`），否則 `getElementById` 會命中被遮住的那張。展開改用 `toggleFac(this)` + `closest()`，不要用 id 查找；收藏按鈕要把 `this` 傳進 `toggleStar`，因為 `render()` 不會重繪 overlay 內的卡片。

改完資料的驗證方式：抽出 `<script>` 內容跑 `node --check`，再用 stub 過的 `document`/`localStorage` 呼叫三個 render 函式做 smoke test（無測試框架，手動跑即可）。

### 硬編碼、不由 `D` 衍生的內容

改設施資料時**不會**自動連動，需手動同步：

- 動線指南 modal（`id="navModal"`）
- 餐飲速查 modal（`id="dineModal"`）
- 船舶諸元 bar（`id="specsBar"`，與 README 表格重複）
- 關於／免責 modal（`id="aboutModal"`，與 README 免責聲明段重複）
- `costa_serena_deck_guide.md` — 同一份甲板資料的人類可讀版，與 `D` 各自獨立維護

### CSS

**高度受限的 flex column 捲動區**（`.search-results`、`.modal-sheet-body`、`.desktop-sidebar-list`）的直接子元素必須 `flex-shrink: 0`。
少了它，內容超出容器時子元素會被壓扁而非觸發捲動，再配上 `.fac-item { overflow: hidden }` 就會把卡片內容截斷。
新增這類容器時記得一併加進那條共用規則。


Mobile-first，斷點以 320–428px 為 base。所有色彩與尺寸集中在 `index.html:12` 的 `:root` token；`--header-h` / `--bottom-h` / `--safe-*`（iOS safe-area）被 `body` padding 與 fixed 元件共用，改動會同時影響 header、bottom nav 與捲動區。

## 快捷主題 `QF`

速覽頁頂部的「依需求直達」卡片與搜尋的主題篩選共用 `QF` 這一份定義（`{key, icon, label, hint, test}`）。卡片上的 Deck 清單由 `qfDecks()` 從 `D` 即時推導，**不要寫死**——先前寫死的版本 8 張有 5 張是錯的。

`filter` 有三種值：`all`、單一 `cat`（來自 chip）、`qf:<key>`（來自快捷卡）。判定一律走 `matchFilter()`，不要直接比 `f.cat===filter`。套用 `qf:` 時 `updateSearchChips()` 會動態插入一顆可清除的主題 chip。

## 版本號

`APP_VERSION` / `DATA_DATE` 定義在 `<script>` 開頭，由 `DOMContentLoaded` 注入 footer 的 `#verLabel` 與關於 modal 的 `#verDetail`。**不要把版本字串寫死在 HTML**——改一處即可。資料校正後記得同步 `DATA_DATE`。

## 內容規範

- 全站面向旅客，一律 zh-TW；設施同時保留 `zh` 與 `en` 官方名稱。
- 授權為 CC BY-NC 4.0，非官方專案。README 已載明免責聲明：營運時間／收費以船上每日日誌為準——新增設施描述時勿寫死具體時間與價格。
