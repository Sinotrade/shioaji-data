# shioaji-data

給 Shioaji、Shioaji Pro 與 AI Agent 使用的公開資料。第一個資料集是台股上市櫃公司資料：業務簡介、業務組成、客戶與供應商、競爭與合作、產業分類、產業鏈與題材，以及每日更新的收盤價、市值與月營收。

> 本資料彙整自政府開放資料與公開文件，部分內容由 AI 整理並經多個模型交叉核對，可能過時或有誤，僅供參考，不構成投資建議或買賣推薦。

## 怎麼取用

這個 repo 就是資料本身，兩種方式讀的是同一份內容：

- HTTPS：讀 `v1/manifest.json`，依其中的路徑與 sha256 下載需要的檔案（檔名帶 content hash，內容永不改變）。
- 本機：`git clone https://github.com/Sinotrade/shioaji-data.git`，之後 `git pull` 更新，讀本機的 `v1/manifest.json`。

```text
index.json                           有哪些版本
v1/manifest.json                     檔案清單、sha256、各資料集 schema 版本（唯一需要記的網址）
v1/global/entities/                  實體字典：tw.2330、us.NVDA、jp.8035、theme.cowos …
v1/global/sources/                   來源、授權與標示文字
v1/global/taxonomy/                  產業分類（類別 > 產業群 > 產業）
v1/tw/companies/cards.*.json         每家公司的小卡
v1/tw/companies/<code>.*.json        單一公司完整資料（雙向關係、出處）
v1/tw/relations/edges.*.json         關係邊表
v1/tw/themes/themes.*.json           產業鏈與題材，含成員、階段、上中下游
v1/tw/market/daily.*.json            收盤價與市值（每個交易日更新）
v1/tw/revenue/latest.*.json          最新月營收
pages/                               由同一份資料產生的 Markdown 頁面
```

路徑依「版本 > 區域 > 資料類型」排列。同一版本內的所有區域與共用資料保證相容；有破壞性變更時才升 `v2`，`v1` 會保留一段時間。

## 資料怎麼來

| 資料 | 來源 | 更新 |
| --- | --- | --- |
| 公司基本資料、產業別 | 證交所、櫃買中心開放資料（政府資料開放授權條款第 1 版） | 每個交易日 |
| 收盤價、市值 | 證交所、櫃買中心開放資料；市值＝收盤價 × 已發行普通股數 | 每個交易日 |
| 月營收 | 證交所、櫃買中心開放資料 | 每月 |
| 業務、關係、分類、題材 | AI 依公開文件研究（`rules/research.md`），每筆附出處與逐字引文；再由另一個模型逐條核對、程式驗證引文確實在來源中，意見不同時由第三個模型判定 | 每季與重大事件 |

研究線索參考自 [My-TW-Coverage](https://github.com/Timeverse/My-TW-Coverage)（MIT License），所有內容都經重新查證，不直接沿用其文字；其財務資料（來自 Yahoo Finance）完全不使用。

## 授權

- 官方開放資料：依政府資料開放授權條款第 1 版，使用時請標示來源（見 `v1/global/sources`）
- 研究資料：待定

這個 repo 由自動流程每個交易日更新，請勿直接提交修改；資料問題請開 issue。
