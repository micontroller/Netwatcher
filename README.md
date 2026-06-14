<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>net_watcher 使用時間監看 — 隱私權政策 / Privacy Policy</title>
<style>
  :root{--ink:#1a2027;--muted:#55606b;--line:#e2e6ea;--accent:#1f6feb;--bg:#ffffff;--card:#f6f8fa;}
  *{box-sizing:border-box;}
  body{margin:0;background:var(--bg);color:var(--ink);font-family:"Segoe UI","PingFang TC","Microsoft JhengHei",system-ui,sans-serif;line-height:1.7;font-size:17px;}
  .wrap{max-width:820px;margin:0 auto;padding:40px 24px 80px;}
  h1{font-size:28px;margin:0 0 4px;}
  .meta{color:var(--muted);font-size:16px;margin-bottom:28px;}
  h2{font-size:21px;margin:34px 0 10px;padding-bottom:6px;border-bottom:2px solid var(--line);}
  h3{font-size:18px;margin:22px 0 8px;color:var(--accent);}
  p,li{font-size:17px;}
  ul{padding-left:22px;} li{margin:6px 0;}
  table{width:100%;border-collapse:collapse;margin:10px 0;}
  th,td{text-align:left;padding:10px 12px;border:1px solid var(--line);font-size:16px;vertical-align:top;}
  th{background:var(--card);font-weight:600;}
  .card{background:var(--card);border:1px solid var(--line);border-radius:10px;padding:16px 20px;margin:14px 0;}
  .en{color:var(--muted);}
  b{color:#000;}
  code{background:var(--card);border:1px solid var(--line);border-radius:4px;padding:1px 6px;font-family:Consolas,monospace;font-size:16px;}
  a{color:var(--accent);}
  .fill{background:#fff7e6;border:1px solid #f0c36d;border-radius:4px;padding:1px 6px;}
</style>
</head>
<body>
<div class="wrap">
  <h1>net_watcher 使用時間監看 — 隱私權政策</h1>
  <div class="meta">最後更新 / Last updated：2026-06-14</div>

  <div class="card">
    本擴充（net_watcher 使用時間監看）是一款<b>家長對自己未成年子女、在自家電腦上透明安裝</b>的螢幕使用時間管理工具。它偵測 YouTube、Netflix、Instagram、TikTok 的實際使用時間，回報給家長在自家網域自行架設的後端，由家長設定每日額度並在超額時封鎖。本擴充非隱藏式監控、不對外散布、不販售。
  </div>

  <h2>1. 由誰蒐集、為何蒐集</h2>
  <ul>
    <li><b>蒐集者</b>：安裝本工具的家長（個人 / 家庭用途），並非任何商業組織。</li>
    <li><b>目的</b>：管理未成年子女每日在受監控影音 / 社群平台的使用時間，超額時封鎖，僅此單一用途。</li>
  </ul>

  <h2>2. 蒐集哪些資料</h2>
  <table>
    <tr><th>資料</th><th>用途</th></tr>
    <tr><td>受監控分頁的網址、分頁標題、播放狀態（是否正在播放 / 暫停）</td><td>判斷該服務是否「實際在使用」以正確計時</td></tr>
    <tr><td>各服務每日累計使用秒數</td><td>對照家長設定的每日額度，決定是否封鎖</td></tr>
    <tr><td>裝置名稱（電腦 hostname）</td><td>讓家長在後台辨識是哪一台裝置</td></tr>
  </table>
  <p>本工具<b>不</b>蒐集：帳號密碼、輸入內容、剪貼簿、非受監控網站的瀏覽內容、影音畫面或聲音、任何金融或健康資料。</p>

  <h2>3. 資料如何使用、流向何處</h2>
  <ul>
    <li>資料由本機常駐程式經本機通道（Native Messaging）接收後，傳送到<b>家長自行架設的後端</b>（<code>autoassistance.cnj.tw</code>），作為「今天用了多少、還剩多少」的唯一真實來源。</li>
    <li>資料<b>僅用於</b>上述使用時間管理之單一用途。</li>
    <li><b>不販售、不分享給任何第三方</b>，<b>不用於廣告或任何與核心功能無關之用途</b>，<b>不用於建立任何使用者畫像或訓練模型</b>。</li>
    <li>資料只在家長本機與家長自架的後端之間流動，不經過開發者或任何外部服務商。</li>
  </ul>

  <div class="card">
    <b>符合 Chrome Web Store「有限使用 (Limited Use)」要求</b>：本擴充對使用者資料的蒐集與使用，完全遵守
    <a href="https://developer.chrome.com/docs/webstore/program-policies/limited-use">Chrome Web Store User Data Policy</a>
    （含 Limited Use 條款）。資料僅用於對使用者揭露的單一用途，不轉售、不用於廣告、不做與功能無關之使用。
  </div>

  <h2>4. 資料保存與刪除</h2>
  <ul>
    <li>使用時間與瀏覽紀錄保存在家長本機及家長自架後端；瀏覽紀錄於後端保留約 90 天後自動清除。</li>
    <li><b>停用或移除本擴充、或解除安裝本工具，即停止一切資料蒐集</b>；家長可自行刪除後端已儲存的資料。</li>
  </ul>

  <h2>5. 權限說明</h2>
  <table>
    <tr><th>權限</th><th>為何需要</th></tr>
    <tr><td><code>nativeMessaging</code></td><td>把使用心跳安全傳給本機常駐程式（不開任何網路埠，避免被偽造）</td></tr>
    <tr><td><code>alarms</code></td><td>維持背景程序存活，確保心跳不中斷（否則會被誤判為規避而封鎖）</td></tr>
    <tr><td>對 YouTube / Netflix / Instagram / TikTok 的存取</td><td>偵測這四個平台分頁的實際播放狀態以計時</td></tr>
    <tr><td>對所有網站的最小存取（keepalive）</td><td>僅用於維持背景程序存活，<b>不</b>讀取或蒐集這些網站的任何內容</td></tr>
  </table>

  <h2>6. 兒童資料</h2>
  <p>本工具設計用途即為家長管理自己未成年子女的裝置使用，由家長在自家裝置上知情安裝並掌控所有資料。資料不離開家庭範圍（家長本機 + 家長自架後端），不提供給開發者或任何第三方。</p>

  <h2>7. 聯絡方式</h2>
  <p>如對本隱私權政策有任何疑問，請聯絡：<span class="fill">[請填入家長聯絡 email]</span></p>

  <hr style="margin:40px 0;border:none;border-top:1px solid var(--line);">

  <h1 class="en">Privacy Policy (English)</h1>
  <div class="meta">Last updated: 2026-06-14</div>

  <div class="card en">
    net_watcher is a screen-time management tool a <b>parent installs transparently on their own minor child's home computer</b>. It detects actual usage time on YouTube, Netflix, Instagram, and TikTok, reports it to a backend the parent self-hosts, and blocks each service once the parent-set daily limit is exceeded. It is not hidden surveillance, is not distributed publicly, and is not sold.
  </div>

  <h3>Who collects & why</h3>
  <p class="en">Collected by the installing parent (personal/family use only) for the single purpose of managing a minor child's daily usage time on the monitored video/social platforms and blocking when over the limit.</p>

  <h3>What is collected</h3>
  <ul class="en">
    <li>URL, tab title, and playback state of monitored tabs — to determine whether the service is actually in use for accurate timing.</li>
    <li>Daily accumulated seconds per service — to compare against the parent-set limit.</li>
    <li>Device name (hostname) — so the parent can identify which device it is.</li>
  </ul>
  <p class="en">It does <b>not</b> collect passwords, keystrokes, clipboard, browsing of non-monitored sites, audio/video media, or any financial/health data.</p>

  <h3>How data is used & where it goes</h3>
  <ul class="en">
    <li>Received locally via Native Messaging by a local agent, then sent to a <b>backend self-hosted by the parent</b> (<code>autoassistance.cnj.tw</code>) as the source of truth for remaining time.</li>
    <li>Used <b>solely</b> for the stated single purpose. <b>Never sold, never shared with third parties, never used for advertising, profiling, or model training.</b></li>
    <li>Data flows only between the parent's machine and the parent's self-hosted backend — not through the developer or any external provider.</li>
  </ul>

  <div class="card en">
    <b>Limited Use compliance</b>: This extension's use of user data complies with the
    <a href="https://developer.chrome.com/docs/webstore/program-policies/limited-use">Chrome Web Store User Data Policy</a>, including the Limited Use requirements.
  </div>

  <h3>Retention & deletion</h3>
  <p class="en">Usage and browsing records are stored on the parent's machine and self-hosted backend; browsing records auto-expire after ~90 days. Disabling/removing the extension or uninstalling the tool stops all collection; the parent can delete stored backend data at any time.</p>

  <h3>Contact</h3>
  <p class="en">Questions about this policy: <span class="fill">[parent contact email]</span></p>
</div>
</body>
</html>
