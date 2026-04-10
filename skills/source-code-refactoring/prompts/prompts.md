# 原始碼重構提問模板

這份檔案放「怎麼問」。

目標是讓使用者能快速提供一個清楚的重構任務，並和 `SKILL.md` 中的規則保持一致：先建立足夠上下文、保留既有行為、控制變更範圍、必要時使用 `source-code-reading` 產出的 Markdown 筆記，最後用測試、build、typecheck、lint 或手動檢查驗證。

以下模板都可直接複製使用；不需要的欄位可以刪掉。

---

## 通用萬用模板

```text
Use $source-code-refactoring

goal: [這次重構要達成什麼，例如抽取函式、拆分模組、整理命名、降低重複、降低耦合]
entry: [入口檔案 / 類別 / 方法 / 模組 / 功能 / 路由 / 端點 / UI 動作]
refactor_type: [rename / extract / split-module / deduplicate / dependency-cleanup / dead-code-removal / api-preserving-redesign]
behavior_change_allowed: false
reading_note: [可選。source-code-reading 產出的 Markdown 筆記路徑]
output_mode: apply-in-place
artifact_dir: [可選。只有 output_mode: artifact-dir 時填，例如 refactor/order-service]
test_scope: [要跑的測試 / build / typecheck / lint / 手動驗證]
constraints: [限制修改範圍 / 不要碰的檔案 / 相容性要求 / 輸出格式]
```

---

## 1. 直接修改原專案檔案

適合：已確定要重構原檔，讓 Codex 直接修改 `.vue`、`.ts`、`.js`、測試或相關 `.md` 文件。

```text
Use $source-code-refactoring

goal: 直接重構 [目標]，改善 [結構 / 命名 / 重複 / 耦合 / 模組邊界]
entry: [入口檔案 / 模組 / 功能]
refactor_type: [重構類型]
behavior_change_allowed: false
reading_note: [可選。source-code-reading 產出的 .md 路徑]
output_mode: apply-in-place
test_scope: [相關測試或驗證命令]
constraints: 直接修改原檔；控制台只回報摘要、檔案清單、驗證結果與風險，不要貼出完整大型檔案內容
```

### 例子

```text
Use $source-code-refactoring

goal: 直接重構 PDF viewer 元件，抽出頁面縮放計算邏輯
entry: src/components/PdfViewer.vue
refactor_type: extract
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: npm run typecheck
constraints: 不改 props/events，不改畫面輸出行為；控制台不要貼完整 .vue 檔
```

---

## 2. 輸出候選重構到 refactor 目錄

適合：想先審核草案、不要直接改原檔，或重構會產生多個 `.vue`、`.ts`、`.js`、`.md` 候選檔案。

```text
Use $source-code-refactoring

goal: 產出 [目標] 的候選重構版本，先不要修改原檔
entry: [入口檔案 / 模組 / 功能]
refactor_type: [重構類型]
behavior_change_allowed: false
reading_note: [可選。source-code-reading 產出的 .md 路徑]
output_mode: artifact-dir
artifact_dir: refactor/[task-slug]
test_scope: [建議驗證命令；若只是草案可寫請列出建議命令]
constraints: 保留原始相對路徑；在 artifact_dir 補 README.md，說明檔案對應、如何套用、驗證命令與風險；控制台只回報 artifact 位置與重點摘要
```

### 例子

```text
Use $source-code-refactoring

goal: 產出訂單查詢流程的候選重構版本，先不要修改原檔
entry: src/main/java/com/example/order/OrderService.java
reading_note: notes/order-query-reading.md
refactor_type: deduplicate
behavior_change_allowed: false
output_mode: artifact-dir
artifact_dir: refactor/order-query-service
test_scope: ./gradlew test --tests "*OrderServiceTest"
constraints: 保留 public method signature、response shape、錯誤處理與排序規則；產出 README.md 說明如何套用
```

---

## 3. 使用 source-code-reading 筆記重構

適合：已經先產出閱讀筆記，想把它作為重構交接輸入。

```text
Use $source-code-refactoring

goal: 根據閱讀筆記重構 [目標]，改善 [結構 / 命名 / 重複 / 耦合 / 模組邊界]
entry: [主要入口檔案或模組]
reading_note: [source-code-reading 產出的 .md 路徑]
refactor_type: [重構類型]
behavior_change_allowed: false
output_mode: [apply-in-place / artifact-dir]
artifact_dir: [若 output_mode: artifact-dir，填 refactor/[task-slug]]
test_scope: [相關測試或驗證命令]
constraints: 先讀 reading_note，但修改前仍要重新開啟相關檔案確認現況；只把已確認行為當作保留行為依據；不要順手處理待驗證項目，除非會阻擋重構
```

---

## 4. 沒有閱讀筆記的保守重構

適合：還沒有 `reading_note`，但重構範圍很小，或希望 Codex 先做最小閉環閱讀再改。

```text
Use $source-code-refactoring

goal: 重構 [目標]，但保留既有行為
entry: [檔案 / 方法 / 類別 / 模組]
refactor_type: [重構類型]
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: [相關測試或驗證命令]
constraints: 如果上下文不足，先用 source-code-reading 的最小閉環方法理解相關流程，再進行最小範圍修改
```

---

## 5. 抽取函式

適合：方法太長、條件分支複雜、局部邏輯需要命名。

```text
Use $source-code-refactoring

goal: 將 [檔案 / 方法] 中的 [邏輯片段] 抽取成清楚命名的函式
entry: [檔案路徑與方法名稱]
refactor_type: extract
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: [相關測試或驗證命令]
constraints: 保留錯誤處理、early return、logging、metrics、transaction、副作用與輸出結果；若抽取後參數過多，先回報邊界問題
```

---

## 6. 拆分模組或大型檔案

適合：單一 service、component、module 過大，責任混在一起。

```text
Use $source-code-refactoring

goal: 拆分 [模組 / 檔案]，讓 [責任 A] 和 [責任 B] 的邊界更清楚
entry: [模組目錄或檔案]
refactor_type: split-module
behavior_change_allowed: false
reading_note: [可選。重構前置閱讀筆記]
output_mode: [apply-in-place / artifact-dir]
artifact_dir: [若先產出草案，填 refactor/[task-slug]]
test_scope: [相關測試、build 或 typecheck]
constraints: 保留 public entrypoint 或 export surface；先拆檔與調整 import，不要同時引入新架構風格
```

---

## 7. 降低重複

適合：多個方法、service、component 或 mapper 有重複邏輯。

```text
Use $source-code-refactoring

goal: 合併 [重複邏輯]，降低重複但保留各呼叫點行為
entry: [檔案 A]、[檔案 B]、[共同呼叫方或模組]
refactor_type: deduplicate
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: [覆蓋兩個以上原呼叫點的測試或驗證命令]
constraints: 先確認重複邏輯語意相同；比對空值、排序、預設值、錯誤處理、權限、timezone、transaction 與副作用
```

---

## 8. 重新命名

適合：變數、函式、類別、檔案或模組命名不清楚。

```text
Use $source-code-refactoring

goal: 將 [舊名稱] 重新命名為 [新名稱]
entry: [符號所在檔案 / 類別 / 方法 / 模組]
refactor_type: rename
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: [typecheck / build / 相關測試]
constraints: 先搜尋所有引用點；檢查字串引用、反射、模板、設定、路由、序列化欄位與外部 API 欄位；公開契約不要直接破壞
```

---

## 9. 依賴整理

適合：import 混亂、循環依賴、模組方向錯誤、helper 放錯層。

```text
Use $source-code-refactoring

goal: 整理 [模組 / 檔案] 的依賴方向，降低耦合
entry: [模組目錄或入口檔案]
refactor_type: dependency-cleanup
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: [typecheck / build / 相關測試]
constraints: 先確認直接依賴方向；注意 runtime import、type-only import、初始化順序、side-effect import、DI registration 與測試 mock
```

---

## 10. 移除死碼

適合：看似未使用的函式、類別、檔案、設定、feature flag 或分支。

```text
Use $source-code-refactoring

goal: 移除 [看似未使用的程式碼]
entry: [符號 / 檔案 / 模組 / 設定鍵]
refactor_type: dead-code-removal
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: [build / typecheck / 相關測試]
constraints: 先搜尋符號、路徑、字串名稱與設定鍵；檢查反射、template、router、CLI command、cron job、feature flag、DI registration、ORM mapping、SQL mapper、serialization、external webhook
```

---

## 11. 保留 API 的內部重組

適合：想改善內部結構，但不能破壞外部呼叫方。

```text
Use $source-code-refactoring

goal: 重組 [模組 / service / API 實作] 的內部結構，但保留公開 API
entry: [公開入口與主要實作檔案]
refactor_type: api-preserving-redesign
behavior_change_allowed: false
reading_note: [可選。重構前置閱讀筆記]
output_mode: [apply-in-place / artifact-dir]
artifact_dir: [若先產出草案，填 refactor/[task-slug]]
test_scope: [覆蓋公開入口的測試或驗證命令]
constraints: 保留函式簽名、路由、request/response shape、錯誤格式、狀態碼、事件、side effect 與資料庫寫入；不要同時改資料模型
```

---

## 12. 允許行為變更的重構

適合：重構目標明確包含行為調整，例如移除舊相容層、改錯誤格式、改 API response。

```text
Use $source-code-refactoring

goal: 重構 [目標]，並允許以下行為變更：[列出允許改變的行為]
entry: [入口檔案 / 模組 / 功能]
refactor_type: [重構類型]
behavior_change_allowed: true
output_mode: [apply-in-place / artifact-dir]
artifact_dir: [若先產出草案，填 refactor/[task-slug]]
test_scope: [相關測試或驗證命令]
constraints: 只允許改變明確列出的行為；其他公開 API、資料格式、錯誤處理與副作用仍需保留
```

---

## 13. 先擬定重構步驟再執行

適合：範圍稍大，想先看到小步驟，但仍希望 Codex 繼續實作。

```text
Use $source-code-refactoring

goal: 重構 [目標]
entry: [入口檔案 / 模組 / 功能]
refactor_type: [重構類型]
behavior_change_allowed: false
reading_note: [可選。重構前置閱讀筆記]
output_mode: apply-in-place
test_scope: [相關驗證命令]
constraints: 先用簡短 checklist 說明預計分幾步修改，再直接執行；不要停在計畫
```

---

## 14. 只要評估是否適合重構

適合：還不確定是否要改，想先判斷風險與切分方式。

```text
Use $source-code-refactoring

goal: 評估 [目標] 是否適合現在重構，並指出最小安全重構範圍
entry: [入口檔案 / 模組 / 功能]
reading_note: [可選。source-code-reading 產出的 .md 路徑]
behavior_change_allowed: false
output_mode: no-edit
expected_output: 只輸出重構可行性、風險、建議切分步驟與必要驗證；先不要修改檔案
constraints: 若需要更多上下文，列出最值得先閱讀的檔案
```

---

## 使用提醒

- 若重構範圍跨檔或跨模組，優先先用 `source-code-reading` 產出 Markdown 筆記，再把路徑填到 `reading_note`。
- 若你要保守重構，明確寫 `behavior_change_allowed: false`。
- 若你想直接改專案檔案，使用 `output_mode: apply-in-place`。
- 若你想先審核草案，使用 `output_mode: artifact-dir` 並填 `artifact_dir: refactor/[task-slug]`。
- 若只是想評估，不想修改，寫 `output_mode: no-edit`，並在 `expected_output` 說明「先不要修改檔案」。
- 若使用 `artifact-dir`，要求補 `README.md` 會讓候選檔案更容易套用與審查。
- `test_scope` 越具體，驗證越精準，例如指定單一測試類別、單一 package、typecheck 或 build。
- 如果你希望 Codex 直接做完，不要只寫「幫我看看」；請寫「重構」、「修改」、「完成後驗證」。

---

## 最小可用提問

趕時間時，至少給這六個欄位：

```text
Use $source-code-refactoring

goal: [要重構什麼，為什麼]
entry: [從哪裡開始]
refactor_type: [重構類型]
behavior_change_allowed: false
output_mode: apply-in-place
test_scope: [要跑的驗證，若不知道就寫請自動判斷]
```
