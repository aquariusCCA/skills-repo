# 原始碼閱讀提問模板

這份檔案放「怎麼問」。

目標是讓使用者能快速提供清楚的閱讀任務，並和 `SKILL.md` 的規則一致：從具體入口開始、確認必要的專案邊界、只展開直接相關依賴、追到足以回答問題的最小閉環，並在輸出中區分已確認、合理推論與待驗證。

較長、結構化、可重用或跨檔案的閱讀筆記，建議寫入 `.md` 檔案；短答案、一次性判斷或只要下一步清單時，才要求直接在對話中回覆。若未指定輸出路徑，skill 會依序優先使用專案既有的 `docs/code-reading/`、`notes/code-reading/`、`docs/` 或 `notes/` 下合理子路徑；若都沒有，使用 `docs/code-reading/`。

以下模板都可直接複製使用；不需要的欄位可以刪掉。

---

## 選擇模式

- `mode: file`：理解單一檔案。
- `mode: feature`：追蹤一個功能、端點、按鈕、路由、錯誤流程、批次觸發鏈或跨檔流程。
- `mode: module`：理解一個有明確邊界的目錄、package、domain、功能群、workspace、service 或子系統。

如果只提供檔案路徑，通常用 `file`。
如果提供功能名稱加入口，通常用 `feature`。
如果提供目錄、package、workspace、service 或模組並要求總覽，通常用 `module`。

---

## 基本模板

### 通用萬用模板

```text
Use $source-code-reading

goal: [這次閱讀是為了什麼，例如理解、除錯前置、重構前置、功能追蹤]
entry: [入口檔案 / 類別 / 方法 / 路由 / 端點 / 模組 / UI 動作 / 錯誤位置]
mode: [file / feature / module]
project_boundary: [可選。monorepo / 多服務時指定 app / service / package / workspace / 子系統]
expected_output: 將 Markdown 閱讀筆記寫入 [清楚的 .md 輸出路徑]，至少包含範圍與入口、實際閱讀或搜尋過的檔案、已確認行為、待驗證項目、下一步閱讀或下一步行動
constraints: [只看前端 / 只看後端 / 只看 SQL / 限制輸出長度 / 限制範圍 / 不跨 workspace]
max_depth: [可選。若不填，請依最小閉環原則決定展開範圍]
```

### 最小可用提問

趕時間時，至少給這幾個欄位：

```text
Use $source-code-reading

goal: [為什麼要讀]
entry: [從哪裡開始]
mode: [file / feature / module]
expected_output: [寫入 .md 檔案 / 直接在對話中短答]
```

### 不知道入口時

適合：只知道功能名稱、畫面名稱或錯誤訊息，不知道從哪個檔案開始。

```text
Use $source-code-reading

goal: 找到 [功能名稱] 的最可能入口，並建立最小閉環理解
entry: [功能名稱 / 關鍵字 / 畫面名稱 / 錯誤訊息]
mode: feature
expected_output: 將閱讀筆記寫入 [docs/code-reading/[feature-name].md]。先列出 2 到 4 個最可能入口與判斷依據，再從最保守的入口追流程，最後列下一步閱讀建議
constraints: 只做輕量掃描；先看 manifest、workspace、路由、設定與符號搜尋結果
```

### 多候選入口時

適合：同一功能可能有多個頁面、端點、service 或實作。

```text
Use $source-code-reading

goal: 判定 [功能 / 流程] 應從哪個入口開始讀，並追到最小閉環
entry: [多個可能入口或關鍵字]
mode: feature
project_boundary: [可選。app / service / package / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[topic]-entry-analysis.md]。先列候選入口與選擇理由，再只追蹤最保守、最接近問題的入口；其餘入口列為備選路徑
constraints: 不要同步展開所有候選入口
```

### 專案很大時

適合：monorepo、多服務、多工作區，不想一開始就讀太廣。

```text
Use $source-code-reading

goal: 在大型專案中保守地理解 [功能 / 模組 / 問題]
entry: [已知入口 / 功能名稱 / 錯誤訊息]
mode: [file / feature / module]
project_boundary: [apps/web / services/order / packages/shared-ui / src/main/java/com/example/order]
expected_output: 將閱讀筆記寫入 [docs/code-reading/[topic].md]，只整理此邊界內實際讀過的檔案能支撐的結論
constraints: 不要跨 app、跨 service 或跨 workspace，除非問題本身就是跨界流程
```

---

## 常見任務

### 單檔閱讀

適合：理解單一 component、service、class、mapper、util、設定檔。

```text
Use $source-code-reading

goal: 修改前先理解這個檔案的角色與關鍵邏輯
entry: [檔案路徑]
mode: file
expected_output: 將 Markdown 筆記寫入 [docs/code-reading/[file-name].md]，包含檔案角色、主要結構、已確認行為、依賴互動、待驗證、下一步閱讀
constraints: 只看與這個檔案直接相關的依賴；不要做整個專案總覽
```

例子：

```text
Use $source-code-reading

goal: 理解這個 Vue 元件的角色與資料流
entry: src/components/PdfViewer.vue
mode: file
expected_output: 將筆記寫入 docs/code-reading/pdf-viewer.md。先用 3 到 5 句話說明檔案角色，再列出已確認行為、依賴互動、待驗證、下一步閱讀
constraints: 不要做整個專案總覽
```

### 功能流程追蹤

適合：按鈕、路由、頁面提交、端點、批次工作、跨檔流程。

```text
Use $source-code-reading

goal: 追蹤 [功能名稱] 的實際流程
entry: [功能入口，例如按鈕、路由、API、controller、job]
mode: feature
project_boundary: [可選。app / service / package / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[feature-name]-flow.md]，包含流程摘要、呼叫鏈、資料流、已確認行為、待驗證、下一步閱讀
constraints: 只展開與此功能直接相關的檔案；追到能解釋問題的最小閉環
```

例子：

```text
Use $source-code-reading

goal: 追蹤送出訂單功能從前端到後端再到資料庫的流程
entry: 前端 OrderSubmitButton / API POST /orders / 後端 OrderController.createOrder
mode: feature
project_boundary: apps/web + services/order
expected_output: 將筆記寫入 docs/code-reading/order-submit-flow.md。標出前端 -> API -> 後端 -> DB 的跨界點，再分段追蹤每一段最小閉環
constraints: 不要擴張到與訂單提交無關的模組
```

### 模組總覽

適合：對有明確邊界的目錄、package、workspace、service 或功能群建立可重用筆記。

```text
Use $source-code-reading

goal: 理解 [模組名稱] 的責任、主要檔案與典型流程
entry: [模組目錄 / package / workspace / service / 入口檔案]
mode: module
project_boundary: [可選。若 entry 已經是明確邊界可省略]
expected_output: 將模組總覽筆記寫入 [docs/code-reading/[module-name]-overview.md]，包含主要檔案與角色、典型流程、依賴、耦合風險、已確認 / 合理推論 / 待驗證
constraints: 先做保守掃描；只整理實際讀過的檔案能支撐的內容；不要把目錄下每個檔案逐一摘要
```

例子：

```text
Use $source-code-reading

goal: 理解 auth 模組的責任與登入相關流程
entry: src/modules/auth
mode: module
expected_output: 將模組總覽筆記寫入 docs/code-reading/auth-overview.md
constraints: 只整理代表性檔案與典型流程，不要擴張到整個專案
```

### 除錯前置閱讀

適合：還不想立刻修，而是先搞懂問題在哪個局部流程。

```text
Use $source-code-reading

goal: 在修 bug 前先理解 [問題現象] 相關的實際程式流程
entry: [錯誤位置 / 報錯訊息 / 異常畫面 / stack trace / 涉及檔案]
mode: feature
project_boundary: [可選。app / service / package / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[bug-name]-debug-context.md]，聚焦問題相關流程，列出已確認行為、可疑區域、待驗證、下一步最值得檢查的檔案
constraints: 不要直接提出重構方案；先建立有證據的理解
```

例子：

```text
Use $source-code-reading

goal: 在修 PDF 頁面渲染錯位前，先理解畫面初始化與縮放流程
entry: 錯位發生在 PdfContent.vue，切頁後 canvas 尺寸異常
mode: feature
project_boundary: apps/web
expected_output: 將筆記寫入 docs/code-reading/pdf-render-debug-context.md，聚焦初始化、切頁、縮放相關呼叫鏈，指出最可疑區域與待驗證點
constraints: 只看直接相關檔案
```

### 重構 / 遷移 / 重寫前置閱讀

適合：知道之後要改，但現在先建立可驗證上下文。

```text
Use $source-code-reading

goal: 在 [重構 / 遷移 / 重寫] [目標] 前先理解目前實作的責任分布、耦合與風險
entry: [模組 / 檔案 / 功能入口 / package / service]
mode: [module / feature]
project_boundary: [可選。app / service / package / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[target]-change-context.md]，說明目前結構、主要檔案角色、跨檔耦合、已確認風險、待驗證，以及最值得優先拆分或繼續閱讀的點
constraints: 只根據實際讀過的檔案下結論；不要直接設計新架構
```

例子：

```text
Use $source-code-reading

goal: 在重構訂單模組前先理解 controller、service、mapper 之間的責任分布
entry: src/main/java/com/example/order
mode: module
expected_output: 將模組總覽與耦合風險筆記寫入 docs/code-reading/order-refactor-context.md
constraints: 不要直接設計新架構
```

---

## 邊界與跨界流程

### Monorepo / 多工作區模組閱讀

適合：倉庫包含多個 app、package、workspace 或 service。

```text
Use $source-code-reading

goal: 理解 monorepo 中 [workspace / app / package] 的局部責任與代表性流程
entry: [apps/web / packages/ui / services/payment / workspace 名稱]
mode: module
project_boundary: [明確 workspace / app / package / service]
expected_output: 將筆記寫入 [docs/code-reading/[workspace-name]-overview.md]，包含專案邊界、主要入口、代表性流程、shared package 依賴、跨界點、待驗證、下一步閱讀
constraints: 除非流程必要，不要跨 workspace、跨 service 或跨 app 擴張
```

例子：

```text
Use $source-code-reading

goal: 理解 web app 裡 profile 功能群的責任與主要流程
entry: apps/web/src/features/profile
mode: module
project_boundary: apps/web
expected_output: 將筆記寫入 docs/code-reading/web-profile-overview.md
constraints: 若引用 shared package，只列出交界點；不要展開整個 packages 目錄
```

### 跨前後端 / 跨服務流程

適合：流程本身穿過前端、API、gateway、service、queue 或 DB。

```text
Use $source-code-reading

goal: 追蹤 [跨界流程] 的實際路徑
entry: [前端動作 / API / gateway route / service method / queue topic]
mode: feature
project_boundary: [涉及的 app / service / package，例如 apps/web + services/payment]
expected_output: 將筆記寫入 [docs/code-reading/[flow-name]-cross-boundary-flow.md]。先標出跨界點，再分段追蹤每一段最小閉環，列出已確認、合理推論、待驗證
constraints: 不要把整個系統攤平；只追此流程直接經過的跨界點
```

例子：

```text
Use $source-code-reading

goal: 追蹤付款送出後從 web app 到 payment service 再到 queue 的流程
entry: CheckoutPayButton / POST /payments / services/payment
mode: feature
project_boundary: apps/web + services/payment
expected_output: 將筆記寫入 docs/code-reading/payment-submit-cross-boundary-flow.md，標出前端 -> API -> payment service -> queue 的跨界點與待驗證項目
constraints: 不要延伸到不相關的 billing 或 notification service
```

---

## 技術情境

### Java / Spring / Spring Boot 端點追蹤

適合：controller -> service -> repository / mapper -> SQL / 外部 API。

```text
Use $source-code-reading

goal: 追蹤 [API / endpoint] 的實際後端流程
entry: [controller 類別 / method / request path]
mode: feature
project_boundary: [可選。service / package]
expected_output: 將筆記寫入 [docs/code-reading/[endpoint-name]-backend-flow.md]，包含流程摘要、呼叫鏈、DTO / entity 流動、驗證、交易邊界、查詢或更新點、待驗證、下一步閱讀
constraints: 追到足以解釋 request 到 response 的最小閉環
```

例子：

```text
Use $source-code-reading

goal: 追蹤 GET /orders/{id} 端點從 controller 到 mapper 的流程
entry: OrderController.getOrderDetail
mode: feature
project_boundary: services/order
expected_output: 將筆記寫入 docs/code-reading/order-detail-backend-flow.md，說明 request 進來後經過哪些方法、使用哪些 DTO/entity、在哪裡查資料、怎麼組 response
constraints: 若有 mapper XML 或 SQL，請一起納入最小閉環
```

### MyBatis / SQL 流程追蹤

適合：想知道資料到底怎麼查、怎麼映射。

```text
Use $source-code-reading

goal: 理解 [查詢 / 更新] 的 SQL 與資料映射流程
entry: [mapper interface / mapper XML / service method]
mode: feature
expected_output: 將筆記寫入 [docs/code-reading/[query-name]-sql-flow.md]。先找出誰呼叫這段 mapper，再列出 SQL、參數來源、result mapping、回傳物件流向、待驗證
constraints: 聚焦 mapper、XML、DTO/entity 與直接呼叫方
```

例子：

```text
Use $source-code-reading

goal: 理解這段查詢是如何從 service 傳參到 mapper XML，再映射成 DTO
entry: OrderMapper.selectOrderSummary
mode: feature
expected_output: 將筆記寫入 docs/code-reading/order-summary-sql-flow.md，包含呼叫鏈、SQL 參數來源、resultMap / DTO 流向、待驗證
constraints: 不要延伸到整個 order 模組
```

### Vue / React 前端互動追蹤

適合：路由、畫面初始化、按鈕、狀態管理、API 呼叫、條件渲染。

```text
Use $source-code-reading

goal: 追蹤 [畫面 / 互動] 的前端流程
entry: [route / page component / button / composable / store]
mode: feature
project_boundary: [可選。app / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[interaction-name]-frontend-flow.md]，包含流程摘要、元件邊界、props/events/store/API、狀態轉換、渲染條件、待驗證、下一步閱讀
constraints: 追到能解釋互動如何觸發資料更新與畫面變化的最小閉環
```

例子：

```text
Use $source-code-reading

goal: 追蹤使用者切換頁碼後 PDF 畫面如何更新
entry: PdfContent.vue 的分頁按鈕 click event
mode: feature
project_boundary: apps/web
expected_output: 將筆記寫入 docs/code-reading/pdf-page-switch-frontend-flow.md，說明事件從觸發到 state 更新再到 canvas 重繪的流程
constraints: 聚焦元件、store、API 與渲染邏輯
```

### 舊式前端 / jQuery 流程追蹤

適合：事件綁定、AJAX、DOM 操作、全域狀態、動態 HTML。

```text
Use $source-code-reading

goal: 理解 [舊頁面 / jQuery 功能] 的初始化與互動流程
entry: [HTML 頁面 / 初始化函式 / DOM selector / 事件名稱]
mode: feature
expected_output: 將筆記寫入 [docs/code-reading/[page-name]-jquery-flow.md]，包含初始化順序、事件綁定、全域狀態、AJAX、DOM 更新、待驗證、下一步閱讀
constraints: 只看和此頁面直接相關的 script 與工具函式
```

### 批次工作 / 排程任務 / Worker

適合：scheduler、job、queue consumer、worker、batch。

```text
Use $source-code-reading

goal: 追蹤 [批次任務 / worker 名稱] 的觸發條件、主要流程與副作用
entry: [scheduler / job class / command / queue consumer / worker]
mode: feature
project_boundary: [可選。service / package / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[job-name]-batch-flow.md]，包含 trigger -> job / consumer -> service -> persistence / side effect 的流程摘要，並標示重試邏輯、資料更新點、待驗證與下一步閱讀
constraints: 聚焦實際觸發鏈與資料更新點
```

### Shared package / 共用模組

適合：想理解共用工具、UI library、form library、schema package 的責任與使用方式。

```text
Use $source-code-reading

goal: 理解 [shared package / 共用模組] 提供什麼能力、主要入口與代表性呼叫方
entry: [packages/ui / src/shared/form / packages/schema]
mode: module
project_boundary: [shared package / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[shared-module]-overview.md]，包含公開入口、主要檔案角色、代表性呼叫方、依賴、待驗證、下一步閱讀
constraints: 不要展開所有呼叫方；只選最能代表使用方式的呼叫方
```

---

## 精簡控制

### 比對兩個檔案的責任分工

適合：兩個 service 很像、兩個 component 看起來重疊。

```text
Use $source-code-reading

goal: 比較兩個檔案的責任、重疊與差異
entry: [檔案 A]、[檔案 B]
mode: file
expected_output: 將比對筆記寫入 [docs/code-reading/[topic]-file-comparison.md]，分別說明兩者角色、關鍵方法、依賴、共同點、差異、可能重疊責任、待驗證
constraints: 不要延伸到整個模組，除非差異只能靠上游呼叫方解釋
```

### 只要下一步閱讀清單

適合：現在不需要完整分析，只想知道先讀哪裡最划算。

```text
Use $source-code-reading

goal: 為 [功能 / 模組 / bug] 找出最值得優先閱讀的檔案順序
entry: [已知入口或功能名稱]
mode: [file / feature / module]
project_boundary: [可選。app / service / package / workspace]
expected_output: 直接在對話中回答；不用完整分析，只列 3 到 7 個最值得讀的檔案，並說明每個檔案的閱讀理由與預期回答的問題
constraints: 先做保守掃描
```

### 只要已確認事實，不要推測太多

適合：想要非常保守的閱讀結果。

```text
Use $source-code-reading

goal: 保守地理解 [目標]
entry: [入口]
mode: [file / feature / module]
project_boundary: [可選。app / service / package / workspace]
expected_output: 將筆記寫入 [docs/code-reading/[target]-confirmed-only.md]，只列已確認行為、引用的檔案、待驗證；若需推論請單獨列在合理推論區塊
constraints: 結論必須能對應到已開啟檔案或搜尋證據
```

### 先短答，再決定要不要深入

適合：還不確定要不要展開完整筆記。

```text
Use $source-code-reading

goal: 先快速理解 [目標]，再決定是否深入閱讀
entry: [入口]
mode: [file / feature / module]
expected_output: 直接在對話中短答，說明目前看起來做了什麼、還缺什麼證據、下一個最值得讀的檔案；不要一開始就輸出長篇筆記
constraints: 保持精簡
```

### 自訂輸出格式

適合：想控制筆記長度、欄位或模板使用方式。

```text
Use $source-code-reading

goal: 理解 [目標]
entry: [入口]
mode: [file / feature / module]
expected_output: 將筆記寫入 [docs/code-reading/[target].md]。使用結構化 Markdown，但模板欄位可依內容調整、合併或省略；不要為了套模板破壞閱讀流暢度
constraints: [長度 / 範圍 / 只看哪些檔案 / 不看哪些檔案]
```

---

## 使用提醒

- 入口越具體，結果通常越好。
- 若只知道功能名稱，不知道入口，就用「不知道入口時」模板。
- 若是 monorepo、多服務或多工作區，優先補上 `project_boundary`。
- 若有多個候選入口，要求先列候選與判斷依據，再選最保守的入口開始。
- 若要建立可重複使用的筆記，在 `expected_output` 明確指定 `.md` 輸出路徑；不指定時，skill 會依專案慣例選路徑。
- 若只是修 bug 前想先掌握局部流程，優先指定 `constraints: 只看直接相關檔案`。
- 若怕它讀太多，可指定 `max_depth`；不指定時，就讓它依最小閉環原則停止。
- 若只要結論，不要長文，直接在 `expected_output` 寫「直接在對話中回答」或「先短答」。
