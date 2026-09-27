# 更表轉行事曆

網址：https://ninpj.github.io/shift-calendar/

點日子切換 日更 → 夜更 → 休息，下載 `.ics` 匯入手機行事曆。純前端，沒有伺服器、沒有建置步驟。
班次資料只存在你自己手機的瀏覽器裡（localStorage），不會上傳到任何地方。

## 檔案

| 檔案 | 用途 |
|---|---|
| `index.html` | 整個網頁（HTML + CSS + JS 都在裡面） |
| `manifest.webmanifest` | 「加到主畫面」用的 app 名稱、顏色、圖示 |
| `sw.js` | 離線快取（先上網拿新版，沒網路才用快取），一般不用改 |
| `icons/` | App 圖示（PNG） |

## 常見修改

**改班次時間／標題（只改自己手機）**
網頁最下面「更次時間設定」直接改，會記在那部手機上。

**改預設班次時間／標題（所有人、所有新裝置）**
`index.html` 裡搜尋 `var DEF=`：

```js
var DEF={dS:"08:00",dE:"18:00",nS:"18:00",nE:"08:00",dT:"日更",nT:"夜更"};
```

`dS`/`dE` = 日更開始／結束，`nS`/`nE` = 夜更開始／結束，`dT`/`nT` = 行事曆上顯示的標題。
結束時間早於（或等於）開始時間，會自動當成翌日結束。
注意：已經在手機上改過設定的，手機上的設定優先；要套用新預設，在設定裡手動改回，或清除該網站資料。

**改文字、顏色**
- 畫面文字：`index.html` 的 `<main>` 區塊。
- 顏色：`index.html` 最上面 `:root{ ... }`（`--day` 日更色、`--night` 夜更色）。
- App 名稱：`manifest.webmanifest` 的 `name` / `short_name`，以及 `index.html` 的 `apple-mobile-web-app-title`。

## 怎樣更新網站

改完檔案後，在這個 repo 資料夾：

```bash
git add -A && git commit -m "說明改了什麼" && git push
```

約 1 分鐘後 GitHub Pages 自動更新。手機上完全關掉 app 再打開就會拿到新版。
也可以直接在 github.com 的 repo 頁面點檔案 → 鉛筆圖示線上編輯、Commit，不需要電腦上有 git。

## .ics 的規則（改程式時別弄壞）

- 時間用 floating time（`DTSTART:20261031T180000`，不加 `Z`、不加 `TZID`），手機照當地時間顯示。
- 夜更結束日用 `Date.setDate(+1)` 算，自動處理月底→下月 1 號、12/31→翌年 1/1、閏年 2/29。
- UID 只按日期（`shift-YYYY-MM-DD@roster-tool`），同一天改班次後重新匯入，支援的行事曆會更新該筆而非重複。
- iPhone 上「下載」按鈕改用 `data:text/calendar` 開啟（blob 下載在 iOS 不會叫出行事曆）；另有「分享／存到檔案」作後備。
