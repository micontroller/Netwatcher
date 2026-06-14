# net_watcher 擴充 — 權限正當性與上架合規審視

> 產出日期：2026-06
> 對象：Chrome Web Store 審核（unlisted）／家長存檔
> 來源：`extension/manifest.json` + `extension/src/` 全檔只讀分析（未改動任何檔）

---

## 1. 目前宣告的權限逐項說明（審核要每項有正當理由）

### 1.1 `permissions`

| 權限 | 用途 | 正當性說明（給審核） |
|------|------|----------------------|
| `nativeMessaging` | 經 `chrome.runtime.connectNative("com.netwatcher.host")` 開一條持久 stdio port，把「當下正在使用哪個影音服務」的 1 秒心跳推給本機常駐 agent（家長端）。 | 這是本擴充的**核心傳輸通道**。改用 Native Messaging 而非 localhost HTTP 的理由是安全：HTTP 端點任何本機程序都能 `curl` 自設 Origin 偽造心跳把計時歸零；Native Messaging 由 Chrome 經 stdio 生出 host 子程序、host manifest 以 `allowed_origins` 綁定本擴充 ID，無開放 TCP port，無法被偽造。**權限與單一用途直接相符**，審核應可接受。 |
| `alarms` | `chrome.alarms` 設標稱 30 秒週期的 backstop 心跳，喚醒已被 MV3 回收的 service worker。 | MV3 service worker 約 30 秒 idle 即被回收，連帶殺掉心跳定時器。`alarms` 是官方建議的 MV3 週期喚醒手段，用來確保「瀏覽器開著就一定有心跳」（避免 agent 端誤判「擴充被停用」而 fail-closed 封鎖全部）。屬低敏感權限，審核幾乎不會質疑。 |

> 註：`README.md` 與舊註解提到「`permissions` 僅 `nativeMessaging`」，但**現行 `manifest.json` 實際同時宣告 `nativeMessaging` 與 `alarms`**（`service_worker.js` 確實有用 `chrome.alarms`）。文件與實際碼有此小落差，以實際 manifest 為準。`alarms` 有實際使用、不是殘留，無須移除。

### 1.2 `host_permissions`

**目前 manifest 完全沒有宣告 `host_permissions` 區塊。** content script 的網域是寫在 `content_scripts[].matches`（見下），不需要 `host_permissions`。這是好事：沒有 broad host_permissions（例如 `<all_urls>` 放進 host_permissions）能讓審核大幅降低敏感度。**無需任何調整。**

### 1.3 `content_scripts[].matches`

擴充宣告**兩支** content script：

**(A) `src/content/detect_active_use.js`** — 注入 4 個服務 / 6 個網域，`all_frames: true`：

| match pattern | 用途與正當性 |
|---------------|--------------|
| `https://*.youtube.com/*`, `https://youtube.com/*` | 偵測 YouTube 影片實際播放時間。 |
| `https://*.youtube-nocookie.com/*`, `https://youtube-nocookie.com/*` | `/embed` 可放任意 YouTube 影片，計時併入 youtube，防止用 nocookie embed 繞過。 |
| `https://*.youtubekids.com/*`, `https://youtubekids.com/*` | YouTube Kids 內容同樣計入 youtube。 |
| `https://*.netflix.com/*`, `https://netflix.com/*` | 偵測 Netflix 影片實際播放時間。 |
| `https://*.instagram.com/*`, `https://instagram.com/*` | 偵測 Instagram（含 Reels）觀看／純滑動時間。 |
| `https://*.tiktok.com/*`, `https://tiktok.com/*` | 偵測 TikTok 觀看／純滑動時間。 |

正當性：**這 4 個服務正是擴充宣稱的單一用途（家長使用時間監看）的標的**，matches 精確列舉、沒有過寬。`all_frames: true` 的理由是 YouTube 嵌入播放器／廣告框各自一個 frame，需逐 frame 偵測避免廣告框 inactive 覆蓋主框 playing 的漏算（碼中 `track_tab_states.js` 以 `tabId:frameId` 為鍵正是為此）。這是**合理且可解釋**的。

**(B) `src/content/keepalive.js`** — `matches: ["<all_urls>"]`，`all_frames: false`：

| match pattern | 用途與正當性 |
|---------------|--------------|
| `<all_urls>` | 在每個頁面主框開一條空 ping 長連線 port，維持 MV3 service worker 存活，確保「瀏覽器開著就一定有心跳」。 |

正當性（**這是審核唯一可能多看一眼的點**）：`<all_urls>` 看起來很寬，但 `keepalive.js` 經查證**完全不讀頁面內容、不蒐集任何資料、不上報網址／標題／DOM**——port 訊息只是 `{ type: "keepalive-ping" }` 空 ping（見 `keepalive.js` 第 47 行）。它存在的唯一理由是修正一個 P0 誤封 bug：女兒只開 Chrome 寫作業（無受監控分頁）時 SW 被回收 → 無心跳 → agent 誤判「擴充被停用」而封鎖全部。**審核時須在權限說明與隱私揭露中明確聲明「`<all_urls>` 僅用於 keep-alive、不蒐集資料」**（草稿已在 `privacy_disclosure_draft.md` 寫好）。

---

## 2. 會不會被 Web Store 審核打回？逐點明確結論

| 檢查項 | 結論 | 行動 |
|--------|------|------|
| **manifest 的 `key` 欄位** | **必須移除**。上架全新項目時 Web Store 會回 `"key" field not allowed in manifest`，ID 由商店自動指派。本機 `key` 不能留在上傳的 zip 裡。 | `pack_for_webstore.ps1` 已實作「打包前產生去 key 的暫時 manifest，不動原檔」。詳見 `id_binding_inventory.md` 的 key↔ID 關係說明。 |
| **remote code（遠端載入程式碼）** | **無風險**。全擴充只有靜態 `.js`，無 `eval`、無 `new Function`、無遠端 `<script>` 注入、無 CDN 抓 JS。MV3 本就禁 remote code，本擴充天生合規。 | 無須改。 |
| **過寬權限** | **基本安全，一個需特別說明點**。`nativeMessaging` / `alarms` / 4 服務 matches 都精準對應單一用途。唯一需主動說明的是 keepalive 的 `<all_urls>`。 | 在審核的「權限說明」欄與隱私揭露明確寫出 `<all_urls>` 只做 keep-alive、零資料蒐集（草稿已備）。 |
| **單一用途（single purpose）** | **明確、單一**：偵測 4 個影音平台的使用時間並回報本機 agent 供家長設額度。description 已如實說明、非隱藏監控。 | 隱私揭露的「單一用途說明」直接用草稿。 |
| **資料蒐集揭露** | **需如實填**。擴充確實會把網址／標題／播放證據（evidence）送到本機 agent（再由 agent 轉家長自架後端 autoassistance.cnj.tw）。屬「網站內容 / 使用者活動」類資料。 | 在 Privacy practices tab 勾選對應資料類型、填 limited use 認證、附隱私政策 URL。草稿已備繁中＋英文。 |
| **隱私政策 URL** | **2026 強制**：凡請求資料相關權限者，Developer Dashboard 必須填隱私政策 URL，否則 30 天警告後停用。 | 家長須準備一個可公開存取的隱私政策頁（可放在 autoassistance.cnj.tw 下，或用免費隱私政策產生器）。草稿文字已備，貼上去即可。 |
| **Native Messaging host 的揭露** | 審核可能想知道資料送去哪。 | 隱私揭露已說明：資料只送本機 agent，再到家長自架後端，不送任何第三方／不販售。 |
| **監控類擴充政策風險** | **中等需留意**。Google 對「監控／spyware」類有政策；但本擴充定位為**家長對未成年子女的透明監控**（description 已聲明非隱藏、安裝在自家機器、資料只進家長自己的後端）。這類「家長控制」用途是被允許的，但**務必在 listing 與隱私政策中強調透明性與家長／受監護人關係**，避免被誤判為偷窺類 spyware。 | listing 描述與隱私政策都強調「家長透明安裝、監控自己未成年子女、資料僅家長自用」。草稿已寫入此定位。 |

### 總結論

**可以上架，預期能過審**，但出貨前必做三件事：
1. **移除 manifest 的 `key`**（腳本已處理）。
2. **填妥 Privacy practices tab**：單一用途、各權限用途（特別解釋 keepalive 的 `<all_urls>`）、資料蒐集類型、limited use 認證、隱私政策 URL。
3. **listing／隱私政策強調「家長透明監控未成年子女、資料僅家長自用、不售不分享」**，降低被歸類為 spyware 的風險。

---

## 來源（2026 查證）

- [Chrome Web Store review process — Chrome for Developers](https://developer.chrome.com/docs/webstore/review-process)
- [Are "Unlisted" Chrome extensions reviewed the same as "Public" ones? — chromium-extensions group](https://groups.google.com/a/chromium.org/g/chromium-extensions/c/bMs4Rqsm4mQ)
- [Manifest - key — Chrome for Developers](https://developer.chrome.com/docs/extensions/reference/manifest/key)
- ["Keeping a consistent extension ID" without "key" in manifest v3 — chromium-extensions group](https://groups.google.com/a/chromium.org/g/chromium-extensions/c/Su50pbNzRms)
- [Updated Privacy Policy & Secure Handling Requirements (User Data FAQ) — Chrome for Developers](https://developer.chrome.com/docs/webstore/program-policies/user-data-faq)
- [Chrome Web Store Program Policies — Chrome for Developers](https://developer.chrome.com/docs/webstore/program-policies/policies)
