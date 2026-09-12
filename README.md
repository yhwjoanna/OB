# 組織行為 HRM912　台北在職專班／學分班　課程主頁

國立中山大學人力資源管理研究所，2026 Fall。這是給修課學生的教材入口，純靜態網頁，沒有任何外部框架與字型，離線也能開。

---

## 一、資料夾內容

```
index.html      主頁，所有內容與邏輯都在這一個檔案裡
robots.txt      拒絕搜尋引擎與 AI 爬蟲收錄
.nojekyll       關閉 GitHub Pages 的 Jekyll 處理，避免底線開頭的檔名被忽略
lectures/       十一份講義（含情緒互動實驗室）
lectures/images/video/   OB-01 內嵌的攜程影片與封面圖，講義以相對路徑 images/video/ 引用，不可搬移
sims/           兩份學生端模擬遊戲
docs/           課程大綱 PDF，檔名為純英數 2026Fall_OB_Taipei_syllabus.pdf
```

教師手冊（OB-06_sim_pay_manual.html、OB-07_sim_teammate_manual.html）不在這個資料夾，也不要放進來。

指定閱讀的 PDF 也不要放進來。那十八篇期刊全文有版權，其中五篇 Annual Reviews 的檔案內頁印著下載者的 IP 位址，放到公開網址上等於把追查線索一起公開。全文一律放 Google Classroom，主頁的作業區只列出處與取得管道。原始檔在 `05_參考資料/作業文獻/`。

---

## 二、第一次上線

在這個資料夾裡執行：

```bash
git init
git add .
git commit -m "課程主頁與教材"
git branch -M main
git remote add origin https://github.com/<帳號>/<儲存庫名稱>.git
git push -u origin main
```

接著到 GitHub 的 Settings → Pages，Source 選 `Deploy from a branch`，分支選 `main`、資料夾選 `/ (root)`，存檔後約一分鐘網址就會出現，格式為 `https://<帳號>.github.io/<儲存庫名稱>/`。

把這個網址貼到 Google Classroom 的課程說明或置頂公告，學生從那裡進來。

---

## 三、學期中最常做的三件事

### 1. 解鎖或延後某一次的教材

`index.html` 裡的 `SESSIONS` 陣列是唯一的資料來源。教材預設在上課日前七天自動開放，要提前或延後就加一行 `open`：

```js
{ n: 4, date: '2026-11-01', open: '2026-10-20', title: '知覺與個人決策、工作動機與激勵', items: [ ... ] }
```

### 2. 補上原本標示為尚未開放的講義

把該筆 `items` 的 `href: null` 換成檔案路徑即可：

```js
{ unit: 'CH 11', label: '溝通', href: 'lectures/OB-11_communication.html' }
```

`href` 是 `null` 時，按鈕會渲染成灰色虛線、標示尚未開放且不可點選，不會產生死連結。未到開放日的教材也是同一種樣式與同一句標示，學生看不出哪些是還沒做、哪些是還沒到時間。

### 3. 更新某一份講義

把新版檔案覆蓋到 `lectures/` 底下的同名檔案，然後：

```bash
git add lectures/OB-07_teams.html
git commit -m "OB-07 更新"
git push
```

推上去之後約一分鐘生效。學生若看到舊版，請他們重新整理一次。

---

## 四、日期邏輯

頁面依**瀏覽裝置的當地日期**判斷狀態，沒有後端也不需要手動切換：

| 狀態 | 判定 |
| --- | --- |
| 已結束 | 上課日已過 |
| 今天上課 | 上課日就是今天 |
| 本週上課 | 上課日落在今天所在的那一週（週一起算） |
| 下一次上課 | 最近的未來場次，但不在本週 |
| 尚未開始 | 其餘未來場次 |

同一時間只會有一張卡片被標成紅色，最上方的焦點區永遠對應那一張。整學期結束後，焦點區改為課程已結束，所有教材維持可讀取。

---

## 五、防呆與保護措施

- 未開放的週次與尚未製作的講義都渲染為 `is-pending` 的灰色虛線元素，是 `span` 而非 `a`，並帶 `aria-disabled="true"`，點不下去也不會產生 404。
- `robots.txt` 全站 `Disallow: /`，並個別列出 GPTBot、ClaudeBot、Google-Extended、CCBot、PerplexityBot、Bytespider 等抓取器。
- `<head>` 內為 `noindex, nofollow, noarchive, nosnippet, noimageindex`，另設 `referrer: no-referrer`。
- 這兩項只能擋住守規矩的爬蟲，擋不住拿到網址的人。講義裡若要放教科書圖表或有版權限制的案例全文，請改放 Google Classroom。

---

## 六、視覺與互動

視覺沿用課程講義的識別：紙、墨、單一紅、等寬字標籤。材質與動態依 Apple 的做法，版面的品質門檻依 deck-web-delivery 的特徵量測。

- **深色舞台**：頁首與本次焦點合成一整塊深色區，是全頁最重的材質，也是學生進站第一眼要看的東西。其餘區塊維持紙色，層級一眼分得出來。
- **字級**：14 階，正文 15px、最大 46px，跨距 3.07 倍；大字用負字距與緊行高，小標籤用正字距。間距一律 rem，使用者把系統字級調大時版面會跟著放大。
- **字重**：五種。大數字用 300，正文 400，標籤 600，標題 700。
- **時間軸**：連續的軸線加節點，不是八張等寬白卡。已結束的場次退成無框，本次抬起並帶紅色節點，未開始的用虛線框。
- **材質**：懸浮膠囊為 `backdrop-filter: blur(24px) saturate(180%)`，內容從底下捲過去；捲動時陰影加重、頂端浮出漸層遮罩，取代硬分隔線。膠囊在深色舞台上方會換成深色材質。
- **動態**：折疊用臨界阻尼彈簧（response 0.38 秒，不回彈），從畫面上的當前高度出發，動畫播到一半再點一次會立刻反向。按鈕的回饋在按下的當下，不等放開。
- **系統偏好**：`prefers-reduced-motion` 取消位移與縮放、`prefers-reduced-transparency` 讓膠囊改為實色、`prefers-contrast: more` 加深線條與加粗框線。深色模式跟隨系統。

## 七、技術細節

- 純 HTML5、CSS3 與原生 JavaScript，零依賴、零建置步驟。`index.html` 約 42 KB，首次載入只有這一個請求。
- 使用系統字型堆疊（蘋果裝置為 PingFang TC、Windows 為 Microsoft JhengHei），不連外部字型伺服器。
- 懸浮導覽的 `backdrop-filter` 若瀏覽器不支援，會退回半透明底色，功能不受影響。
- 課程總覽的折疊狀態存在 `localStorage` 的 `hrm912_overview_collapsed`，存取包在 `try / catch` 內，無痕視窗或封鎖儲存時仍可正常瀏覽。
- 版面在 608px 以下切換為單欄，時間軸的軸線收起，日期改排在標題上方。
