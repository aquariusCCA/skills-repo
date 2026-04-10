# 原始碼閱讀提問模板

這份檔案放「怎麼問」。

目標是讓使用者能快速提供一個清楚的閱讀任務，並和 `SKILL.md` 中的規則保持一致：
從具體入口開始、只展開直接相關的依賴、追到足以回答問題的最小閉環為止，並在回答中區分已確認、合理推論、待驗證。

以下模板都可直接複製使用；不需要的欄位可以刪掉。

---

## 通用萬用模板

```text
Use $source-code-reading

goal: [這次閱讀是為了什麼，例如理解、除錯前置、重構前置、功能追蹤]
entry: [入口檔案 / 類別 / 方法 / 路由 / 端點 / 模組 / UI 動作 / 錯誤位置]
mode: [file / feature / module]
expected_output: [直接回答 / 結構化筆記 / 呼叫鏈 / 資料流 / 下一步閱讀清單]
constraints: [只看前端 / 只看後端 / 只看 SQL / 限制輸出長度 / 限制範圍]
max_depth: [可選。若不填，請依最小閉環原則決定展開範圍]
```

---

## 1. 單檔閱讀

適合：理解單一 component、service、class、mapper、util、設定檔。

```text
Use $source-code-reading

goal: 修改前先理解這個檔案的角色與關鍵邏輯
mode: file
entry: [檔案路徑]
expected_output: 結構化筆記，包含檔案角色、主要結構、已確認行為、依賴互動、待驗證、下一步閱讀
constraints: 只看與這個檔案直接相關的依賴
```

### 例子

```text
Use $source-code-reading

goal: 理解這個 Vue 元件的角色與資料流
mode: file
entry: src/components/PdfViewer.vue
expected_output: 先用 3 到 5 句話說明檔案角色，再列出已確認行為、依賴互動、待驗證、下一步閱讀
constraints: 不要做整個專案總覽
```

---

## 2. 功能流程追蹤

適合：按鈕、路由、頁面提交、端點、批次工作、跨檔流程。

```text
Use $source-code-reading

goal: 追蹤 [功能名稱] 的實際流程
mode: feature
entry: [功能入口，例如按鈕、路由、API、controller、job]
expected_output: 呼叫鏈 + 資料流 + 已確認行為 + 待驗證 + 下一步閱讀
constraints: 只展開與此功能直接相關的檔案
```

### 例子

```text
Use $source-code-reading

goal: 追蹤送出訂單功能從前端到後端再到資料庫的流程
mode: feature
entry: 前端 OrderSubmitButton / API POST /orders / 後端 OrderController.createOrder
expected_output: 先給流程摘要，再列呼叫鏈、資料流、已確認行為、待驗證、下一步閱讀
constraints: 不要擴張到與訂單提交無關的模組
```

---

## 3. 模組總覽

適合：對某個模組建立可重用筆記。

```text
Use $source-code-reading

goal: 理解 [模組名稱] 的責任、主要檔案與典型流程
mode: module
entry: [模組目錄或入口檔案]
expected_output: 模組總覽筆記，包含主要檔案與角色、典型流程、依賴、耦合風險、已確認 / 合理推論 / 待驗證
constraints: 先做保守掃描，不要擴張到不相關模組
```

### 例子

```text
Use $source-code-reading

goal: 理解 auth 模組的責任與登入相關流程
mode: module
entry: src/modules/auth
expected_output: 模組總覽筆記
constraints: 只整理實際讀過的檔案能支撐的內容
```

---

## 4. 不知道入口時的找入口模板

適合：你只知道功能名稱，不知道從哪個檔案開始。

```text
Use $source-code-reading

goal: 找到 [功能名稱] 的最可能入口，並建立最小閉環理解
entry: [功能名稱 / 關鍵字 / 畫面名稱 / 錯誤訊息]
mode: feature
expected_output: 先列出最可能的入口與判斷依據，再從最保守的入口開始追流程，最後給我下一步閱讀建議
constraints: 只做輕量掃描；先看 manifest、路由、設定與符號搜尋結果
```

### 例子

```text
Use $source-code-reading

goal: 找出登入功能的入口並追蹤實際流程
entry: login
mode: feature
expected_output: 先列最可能入口，再給流程摘要、呼叫鏈、待驗證與下一步閱讀
constraints: 若沒有明確入口，先用輕量掃描找候選檔案
```

---

## 5. 除錯前置閱讀

適合：你還不想立刻修，而是先搞懂問題在哪個局部流程。

```text
Use $source-code-reading

goal: 在修 bug 前先理解 [問題現象] 相關的實際程式流程
entry: [錯誤位置 / 報錯訊息 / 異常畫面 / stack trace / 涉及檔案]
mode: feature
expected_output: 先聚焦問題相關流程，列出已確認行為、可疑區域、待驗證、下一步最值得檢查的檔案
constraints: 不要直接提出重構方案；先建立有證據的理解
```

### 例子

```text
Use $source-code-reading

goal: 在修 PDF 頁面渲染錯位前，先理解畫面初始化與縮放流程
entry: 錯位發生在 PdfContent.vue，切頁後 canvas 尺寸異常
mode: feature
expected_output: 聚焦初始化、切頁、縮放相關呼叫鏈，指出最可疑區域與待驗證點
constraints: 只看直接相關檔案
```

---

## 6. 重構前置閱讀

適合：你知道之後要改，但現在先建立上下文。

```text
Use $source-code-reading

goal: 在重構 [目標] 前先理解目前實作的責任分布、耦合與風險
entry: [模組 / 檔案 / 功能入口]
mode: module
expected_output: 說明目前結構、主要檔案角色、跨檔耦合、已確認風險、待驗證，以及最值得優先拆分或繼續閱讀的點
constraints: 只根據實際讀過的檔案下結論
```

### 例子

```text
Use $source-code-reading

goal: 在重構訂單模組前先理解 controller、service、mapper 之間的責任分布
entry: src/main/java/com/example/order
mode: module
expected_output: 模組總覽 + 耦合風險 + 下一步閱讀
constraints: 不要直接設計新架構
```

---

## 7. Java / Spring / Spring Boot 端點追蹤

適合：controller -> service -> repository/mapper -> SQL/外部 API。

```text
Use $source-code-reading

goal: 追蹤 [API / endpoint] 的實際後端流程
entry: [controller 類別 / method / request path]
mode: feature
expected_output: 流程摘要、呼叫鏈、DTO / entity 流動、驗證、交易邊界、查詢或更新點、待驗證、下一步閱讀
constraints: 追到足以解釋 request 到 response 的最小閉環
```

### 例子

```text
Use $source-code-reading

goal: 追蹤 GET /orders/{id} 端點從 controller 到 mapper 的流程
entry: OrderController.getOrderDetail
mode: feature
expected_output: 說明 request 進來後經過哪些方法、使用哪些 DTO/entity、在哪裡查資料、怎麼組 response
constraints: 若有 mapper XML 或 SQL，請一起納入最小閉環
```

---

## 8. MyBatis / SQL 流程追蹤

適合：想知道資料到底怎麼查、怎麼映射。

```text
Use $source-code-reading

goal: 理解 [查詢 / 更新] 的 SQL 與資料映射流程
entry: [mapper interface / mapper XML / service method]
mode: feature
expected_output: 先找出誰呼叫這段 mapper，再列出 SQL、參數來源、result mapping、回傳物件流向、待驗證
constraints: 聚焦 mapper、XML、DTO/entity 與直接呼叫方
```

### 例子

```text
Use $source-code-reading

goal: 理解這段查詢是如何從 service 傳參到 mapper XML，再映射成 DTO
entry: OrderMapper.selectOrderSummary
mode: feature
expected_output: 呼叫鏈 + SQL 參數來源 + resultMap / DTO 流向 + 待驗證
constraints: 不要延伸到整個 order 模組
```

---

## 9. Vue / React 前端互動追蹤

適合：路由、畫面初始化、按鈕、狀態管理、API 呼叫、條件渲染。

```text
Use $source-code-reading

goal: 追蹤 [畫面 / 互動] 的前端流程
entry: [route / page component / button / composable / store]
mode: feature
expected_output: 流程摘要、元件邊界、props/events/store/API、狀態轉換、渲染條件、待驗證、下一步閱讀
constraints: 追到能解釋互動如何觸發資料更新與畫面變化的最小閉環
```

### 例子

```text
Use $source-code-reading

goal: 追蹤使用者切換頁碼後 PDF 畫面如何更新
entry: PdfContent.vue 的分頁按鈕 click event
mode: feature
expected_output: 說明事件從觸發到 state 更新再到 canvas 重繪的流程
constraints: 聚焦元件、store、API 與渲染邏輯
```

---

## 10. 舊式前端 / jQuery 流程追蹤

適合：事件綁定、AJAX、DOM 操作、全域狀態。

```text
Use $source-code-reading

goal: 理解 [舊頁面 / jQuery 功能] 的初始化與互動流程
entry: [HTML 頁面 / 初始化函式 / DOM selector / 事件名稱]
mode: feature
expected_output: 初始化順序、事件綁定、全域狀態、AJAX、DOM 更新、待驗證、下一步閱讀
constraints: 只看和此頁面直接相關的 script 與工具函式
```

---

## 11. 批次工作 / 排程任務

適合：scheduler、job、worker、batch。

```text
Use $source-code-reading

goal: 追蹤 [批次任務名稱] 的觸發條件、主要流程與副作用
entry: [scheduler / job class / command / queue consumer]
mode: feature
expected_output: trigger -> job -> service -> persistence / side effect 的流程摘要，並標示待驗證點與下一步閱讀
constraints: 聚焦實際觸發鏈與資料更新點
```

---

## 12. 比對兩個檔案的責任分工

適合：兩個 service 很像、兩個 component 看起來重疊。

```text
Use $source-code-reading

goal: 比較兩個檔案的責任、重疊與差異
entry: [檔案 A]、[檔案 B]
mode: file
expected_output: 分別說明兩者角色、關鍵方法、依賴、共同點、差異、可能重疊責任、待驗證
constraints: 不要延伸到整個模組，除非差異只能靠上游呼叫方解釋
```

---

## 13. 只要下一步閱讀清單

適合：你現在不需要完整分析，只想知道先讀哪裡最划算。

```text
Use $source-code-reading

goal: 為 [功能 / 模組 / bug] 找出最值得優先閱讀的檔案順序
entry: [已知入口或功能名稱]
mode: [file / feature / module]
expected_output: 不用完整分析；只列 3 到 7 個最值得讀的檔案，並說明每個檔案的閱讀理由與預期回答的問題
constraints: 先做保守掃描
```

---

## 14. 只要已確認事實，不要推測太多

適合：你要非常保守的閱讀結果。

```text
Use $source-code-reading

goal: 保守地理解 [目標]
entry: [入口]
mode: [file / feature / module]
expected_output: 只列已確認行為、引用的檔案、待驗證，不要擴大推論；若需推論請單獨列在合理推論區塊
constraints: 結論必須能對應到已開啟檔案或搜尋證據
```

---

## 15. 先短答，再決定要不要深入

適合：你還不確定要不要展開完整筆記。

```text
Use $source-code-reading

goal: 先快速理解 [目標]，再決定是否深入閱讀
entry: [入口]
mode: [file / feature / module]
expected_output: 先用簡短回答說明目前看起來做了什麼、還缺什麼證據、下一個最值得讀的檔案；不要一開始就輸出長篇筆記
constraints: 保持精簡
```

---

## 使用提醒

- 入口越具體，結果通常越好。
- 若你只知道功能名稱，不知道入口，就用「找入口模板」。
- 若你想建立可重複使用的筆記，優先指定 `expected_output: 結構化筆記`。
- 若你只是在修 bug 前想先掌握局部流程，優先指定 `constraints: 只看直接相關檔案`。
- 若你怕它讀太多，可指定 `max_depth`；不指定時，就讓它依最小閉環原則停止。
- 若你只要結論，不要長文，直接在 `expected_output` 寫「直接回答」或「先短答」。

---

## 最小可用提問

趕時間時，至少給這四個欄位：

```text
Use $source-code-reading

goal: [為什麼要讀]
entry: [從哪裡開始]
mode: [file / feature / module]
expected_output: [你要它怎麼回]
```
