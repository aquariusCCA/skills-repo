---
name: spec-to-code
description: 當使用者提供一份或多份規格文件，要求整理需求、合併規則、標記衝突與缺漏，並生成新功能、模組、API、資料模型、測試或程式骨架時使用。若目標是接到既有系統中，先結合 source-code-reading 建立必要上下文；若主要目標是修改既有實作，改用 source-code-refactoring。
---

# 規格到程式碼

使用這個 skill 來把規格文件轉成可實作的工程輸出。目標是先從一份或多份規格中萃取已確認需求、找出衝突與缺漏、建立最小可實作藍圖，再生成程式碼、測試、說明文件或候選 artifact。

這是生成型 skill。不要只停在摘要規格；若使用者要求產出程式碼、模組、API、DTO、SQL、頁面或測試，應在建立足夠上下文後直接產出可用結果，並清楚標示哪些內容來自明確規格、哪些來自合理假設。

## 何時使用

以下請求適合使用這個 skill：

- 上傳一份或多份規格文件，要求生成新功能或模組。
- 根據需求文件、流程文件、資料字典、介面文件、API 規格、欄位說明或範例資料產生程式碼。
- 把多份規格整合成 API、資料模型、DTO、驗證規則、頁面流程或測試案例。
- 根據文件生成 CRUD、查詢、報表、批次任務、前端頁面骨架或前後端對接程式。
- 先產出實作藍圖，再依藍圖生成分階段程式碼。

以下情況不要只用這個 skill 回答：

- 主要目標是理解既有陌生程式碼流程：先用 `source-code-reading`。
- 主要目標是整理、抽取、拆分或安全修改既有程式：改用 `source-code-refactoring`。
- 需求仍停留在產品策略、模糊構想或大範圍技術選型：先做需求澄清或設計，不要直接進入生成。

## 建議輸入

能提供越多越好，但不必每次都完整提供。

- `goal`：這次生成要達成什麼，例如產出 controller/service/mapper、Vue 頁面骨架、SQL schema、測試案例。
- `spec_inputs`：規格來源清單，例如需求文件、流程圖、資料字典、PDF、Markdown、圖片、表格。
- `entry`：可選。指定從哪一份文件、哪個章節、哪個功能或哪個端點開始。
- `target_stack`：技術棧，例如 Java + Spring Boot + MyBatis、Vue 3 + TypeScript + Vite、Node.js + Express。
- `codebase_mode`：`greenfield` 或 `existing-codebase`。若是接到既有專案，需先找出整合邊界。
- `generation_scope`：可選。`skeleton`、`mvp`、`full-module`、`tests-only`、`docs-first`。
- `output_mode`：可選。`apply-in-place`、`artifact-dir`、`chat-only`。預設依風險與使用者要求決定。
- `assumption_policy`：可選。`conservative`、`balanced`、`aggressive`。預設 `conservative`。
- `spec_priority`：可選。當多份文件衝突時的優先順序，例如 PRD > API spec > 畫面稿 > 舊文件。
- `constraints`：可選。限制修改範圍、命名規則、不要碰的目錄、相容性需求、程式風格、驗證方式。
- `expected_output`：可選。直接產碼、先產生實作藍圖、先列衝突與問題、輸出到特定目錄等。

## 核心規則

- 先整合規格，再生成程式。不要從單一文件片段直接推定完整需求。
- 區分需求狀態：`已確認`、`衝突`、`缺漏`、`合理假設`。
- 除非使用者明確允許，對重要業務規則採保守假設；不清楚的地方要標示，而不是假裝規格已明確。
- 多份文件有衝突時，先依 `spec_priority` 處理；若未提供，優先採較新、較具約束力、較接近系統契約的文件，但仍要把衝突寫進回報。
- 先產出最小可實作藍圖：功能清單、模組邊界、資料流、API、資料模型、驗證規則、錯誤處理、測試點。
- 若目標是接入既有專案，不要直接依規格空降新架構；先以最小整合方式對齊現有目錄、命名、框架與依賴方向。
- 生成的程式碼應區分「規格要求」與「框架慣例補足」。
- 不要因為文件缺漏就自行發明關鍵業務規則、權限邏輯、交易邊界、欄位意義或外部系統契約。
- 驗證應與風險相稱。至少做型別檢查、build、lint、測試或結構一致性檢查；無法執行時要說明原因。
- 若輸出較大，優先產出 artifact，而不是把大量檔案直接貼在對話中。

## 多規格整合規則

在生成前，先把規格整理成以下四類：

### 已確認
可由文件明確指出，且不同來源間沒有衝突的需求，例如：
- API 路徑、欄位名稱、必填條件
- 狀態枚舉、流程步驟
- 資料表欄位、型別、唯一鍵
- 頁面欄位、互動規則、按鈕行為

### 衝突
不同文件對同一件事給出不一致描述，例如：
- 欄位是否必填不一致
- API path、method 或 response schema 不一致
- 流程圖與文字說明步驟順序不同
- 舊規格與新規格對同一規則定義不同

### 缺漏
要完成實作必須知道，但文件沒有說清楚的內容，例如：
- 主鍵來源與 ID 生成規則
- 權限控制規則
- 錯誤碼與錯誤訊息格式
- 分頁、排序、去重、時區、null 行為
- 交易邊界、重試策略、外部系統失敗處理

### 合理假設
為了讓程式可生成而基於框架慣例補上的部分，例如：
- Spring Boot 的 controller/service/repository 分層
- Vue 頁面使用 composable 或 store 管理狀態
- REST API 的常見 response wrapper
- 測試命名與檔案放置慣例

合理假設只能補工程細節；不能替代未定義的核心業務規則。

## 生成流程

1. 用一句話確認這次要從哪些規格生成什麼。
2. 整理已知輸入：`goal`、`spec_inputs`、`target_stack`、`codebase_mode`、`generation_scope`、`constraints`。
3. 閱讀規格，建立需求清單，並標記 `已確認 / 衝突 / 缺漏 / 合理假設`。
4. 若是 `existing-codebase`：
   - 先用 `source-code-reading` 的方法找出整合入口、模組邊界、命名慣例與現有依賴。
   - 確認哪些檔案應延伸、哪些應新增，不要憑空重造平行結構。
5. 先產出最小可實作藍圖，通常包含：
   - 功能與範圍
   - 模組拆分
   - API / DTO / entity / schema
   - 驗證規則
   - 錯誤處理
   - 狀態流或資料流
   - 測試案例
6. 選擇生成策略：
   - `skeleton`：先產骨架與介面
   - `mvp`：先產最小可跑功能
   - `full-module`：生成完整模組與測試
   - `docs-first`：先產藍圖、清單與待決策項
7. 生成程式碼、測試與必要文件。
8. 執行相稱的驗證：build、lint、typecheck、單元測試、整合測試或手動檢查。
9. 回報輸出內容、衝突處理方式、使用的假設、驗證結果與殘餘風險。

## 停止條件

滿足以下任一條件即可停止擴張範圍：

- 已完成使用者指定範圍的可交付輸出。
- 已產出最小可實作藍圖，且剩餘問題明確落在規格衝突或缺漏，需要使用者決策。
- 若繼續生成將會大量依賴未驗證假設。
- 關鍵資訊落在未提供的執行時環境、外部系統契約、資料庫現況或公司內部規則。

## 預設忽略

除非與目前生成目標直接相關，否則不要優先展開：

- `node_modules`、`dist`、`build`、`.next`、`coverage`
- lock file、編譯輸出與大型生成檔
- 與目標功能無關的測試檔
- 與目前功能無關的第三方函式庫原始碼
- 舊版或備份文件，除非它們正是衝突來源之一

## 生成策略

根據目標選擇策略。必要時可分階段執行，而不是一次生成全部。

- `api-first`：先生成端點、request/response schema、DTO、controller 與測試。
- `schema-first`：先生成資料表、entity、mapper/repository、migration 與欄位驗證。
- `ui-first`：先生成頁面骨架、路由、表單、欄位驗證與 mock API。
- `flow-first`：先實作狀態流、工作流、條件規則與服務編排。
- `test-first`：先把規格轉成測試案例，再回推最小實作。
- `docs-first`：先產出實作藍圖、衝突清單與待確認項，再進入產碼。

## 與既有程式碼整合

若 `codebase_mode = existing-codebase`，額外遵守：

- 先找現有目錄、命名、分層與共用元件，不要另起平行架構。
- 優先沿用既有 response 格式、錯誤處理、驗證方式與測試風格。
- 對公開 API、資料格式、資料表、訊息契約與批次行為保持保守。
- 若規格要求與現有系統衝突，先標示差異，再決定是相容延伸還是新版本實作。
- 若需跨多模組修改，先建立最小整合藍圖，再分批生成。

## 輸出模式

預設依任務風險與使用者需求選擇：

### `apply-in-place`
直接在既有專案中新增或修改目標檔案。適合：
- 已明確指定目錄與模組
- 規格清楚
- 風險可控
- 使用者希望直接落地

### `artifact-dir`
將輸出寫入候選目錄，例如：
- `generated/<task-slug>/`
- `spec-output/<task-slug>/`

適合：
- 多文件整合後的首次產碼
- 使用者想先審核
- 生成範圍大
- 需要保留候選版本與說明文件

使用 `artifact-dir` 時，應補一份 `README.md`，說明：
- 來源規格
- 目標範圍
- 衝突清單
- 假設清單
- 檔案對應
- 套用方式
- 建議驗證命令

### `chat-only`
只在對話中輸出藍圖、衝突清單、檔案草案或局部程式片段。適合先討論方向，但不適合作為大型輸出的預設模式。

## 輸出指引

回報應精簡但保留工程上必要資訊：

- 這次根據哪些規格生成了哪些內容
- 哪些需求是已確認的
- 哪些地方存在衝突或缺漏
- 使用了哪些合理假設
- 產出了哪些檔案或 artifact 位置
- 執行了哪些驗證與結果
- 哪些驗證沒有執行，以及原因
- 剩餘風險與需要使用者決策的點

若任務較廣，優先產出以下結構化內容：

- 範圍與目標
- 規格來源清單
- 已確認需求
- 衝突清單
- 缺漏清單
- 假設清單
- 實作藍圖
- 生成檔案清單
- 驗證結果
- 後續建議

## 技術線索

優先依實際技術棧與規格內容決定生成方式。以下只是提醒，不是固定限制。

- Java / Spring Boot：controller、service、repository/mapper、DTO、entity、validation、transaction、exception handler、test。
- MyBatis / MyBatis-Plus：mapper interface、XML、resultMap、query object、entity mapping、SQL 驗證。
- Vue / React：route、page、component、store/composable/hook、API client、form 驗證、狀態更新。
- SQL-heavy 專案：schema、migration、index、constraint、view、stored procedure、query test。
- 批次或工作流：trigger、job、service orchestration、side effect、retry、idempotency、logging。

## 品質底線

- 生成出的程式碼必須可被解釋，不要只追求「看起來很多」。
- 不要把規格摘要偽裝成已完成實作。
- 不要把重要未定義規則偷偷藏在程式裡。
- 不要一次混入與目標無關的大量格式化或架構重寫。
- 能先小步交付就先小步交付，讓後續驗證與迭代成本可控。
