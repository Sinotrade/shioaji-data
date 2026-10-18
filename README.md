# shioaji-data

給 Shioaji、Shioaji Pro 與 AI Agent 使用的公開資料。第一個資料集是台股上市櫃公司資料：業務簡介、業務組成、客戶與供應商、競爭與合作、產業分類、產業鏈與題材，以及每日更新的收盤價、市值與月營收。

> 本資料彙整自政府開放資料與公開文件，部分內容由 AI 整理並經多個模型交叉核對，可能過時或有誤，僅供參考，不構成投資建議或買賣推薦。

## 怎麼取用

這個 repo 就是資料本身，兩種方式讀的是同一份內容：

- HTTPS：讀 `v1/manifest.json`，依其中的路徑下載需要的檔案；sha256 沒變就不用重抓，下載時加 `?v=<sha256>` 可避開 CDN 快取。
- 本機：`git clone https://github.com/Sinotrade/shioaji-data.git`，之後 `git pull` 更新，讀本機的 `v1/manifest.json`。

```text
index.json                           有哪些版本
v1/manifest.json                     檔案清單、sha256、各資料集 schema 版本（唯一需要記的網址）
v1/global/entities/                  實體字典：tw.2330、us.NVDA、jp.8035、theme.cowos …
v1/global/sources/                   來源、授權與標示文字
v1/global/taxonomy/                  產業分類（類別 > 產業群 > 產業）
v1/tw/companies/cards.json           每家公司的小卡
v1/tw/companies/json/<code>.json     單一公司完整資料（雙向關係、出處）
v1/tw/relations/edges.json         關係邊表
v1/tw/themes/themes.json           產業鏈與題材，含成員、階段、上中下游
v1/tw/market/daily.json            收盤價與市值（每個交易日更新）
v1/tw/revenue/latest.json          最新月營收（全部公司一份，方便跨公司篩選）
v1/tw/financials/json/<code>.json  單一公司財務狀況：monthly_revenue 為歷史月營收（千元，由舊到新，2023-10 起逐月累積不刪減）；statements 為季度財報（2023Q1 起，見下方說明）
v1/tw/financials/<code>.md         同一份資料的 Markdown 頁面（月營收表由新到舊；最近 3 個年度與最近 4 季的重點財報）
v1/tw/companies/<code>.yaml          每家公司一個 YAML（固定檔名，方便閱讀與看歷史變更）
v1/tw/companies/<code>.md            同一份資料的 Markdown 頁面；README.md 是全部公司索引
v1/tw/themes/<id>.yaml               每個產業鏈與題材一個 YAML
（.yaml 與 .md 給人看，App 讀 manifest 列出的 JSON）
```

`statements` 採欄式儲存：`periods` 為季別（由舊到新，如 `2026Q2`），其餘每個科目是與 `periods` 等長的數列，沒有資料為 `null`；科目名稱即公開資訊觀測站彙總表的欄名，金融業（銀行、證券、金控、保險）科目與一般業不同，`format` 標示各季的報表格式。單位為千元新台幣，每股盈餘與每股參考淨值為元。

- `income_ytd`、`cash_flow_ytd`：公告數，為年初至該季的累計數；第 4 季即全年
- `income_quarter`、`cash_flow_quarter`：單季數，由累計數減上一季累計數推算（第 1 季即公告數；上一季缺漏時為 `null`）；每股盈餘的單季數也是相減推算
- `balance`：季底數字

路徑依「版本 > 區域 > 資料類型」排列。同一版本內的所有區域與共用資料保證相容；有破壞性變更時才升 `v2`，`v1` 會保留一段時間。

## 資料怎麼來

| 資料 | 來源 | 更新 |
| --- | --- | --- |
| 公司基本資料、產業別 | 證交所、櫃買中心開放資料（政府資料開放授權條款第 1 版） | 每個交易日 |
| 收盤價、市值 | 證交所、櫃買中心開放資料；市值＝收盤價 × 已發行普通股數 | 每個交易日 |
| 月營收 | 證交所、櫃買中心開放資料；2023-10 起的歷史由公開資訊觀測站每月營業收入彙總表回補，之後每天以開放資料更新當月（有更正時覆蓋該月） | 每月 |
| 季度財報（綜合損益表、資產負債表、現金流量表） | 公開資訊觀測站採 IFRSs 後彙總報表，合併報表（無子公司者為個體報表）；2023 年第 1 季起回補，之後每天抓最近兩季（有更正時覆蓋該季） | 每季財報公告期間 |
| 業務、關係、分類、題材 | AI 依公開文件研究（`rules/research.md`），每筆附出處與逐字引文；再由另一個模型逐條核對、程式驗證引文確實在來源中，意見不同時由第三個模型判定 | 每季與重大事件 |

研究線索參考自 [My-TW-Coverage](https://github.com/Timeverse/My-TW-Coverage)（MIT License），所有內容都經重新查證，不直接沿用其文字；其財務資料（來自 Yahoo Finance）完全不使用。

## 授權

- 官方開放資料：依政府資料開放授權條款第 1 版，使用時請標示來源（見 `v1/global/sources`）
- 研究資料：待定

這個 repo 由自動流程每個交易日更新，請勿直接提交修改；資料問題請開 issue。
