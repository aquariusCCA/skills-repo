# 重構 Playbooks

只有在需要具體檢查清單、任務較複雜，或 `SKILL.md` 的高層流程不足時，才讀取這份 reference。

## 通用檢查清單

1. 確認目標：改善結構、命名、重複、耦合、模組邊界或死碼。
2. 確認是否允許行為變更；預設不允許。
3. 建立保護點：現有測試、可新增的窄測試、typecheck、build、lint 或手動檢查。
4. 盤點外部契約：public API、路由、事件名稱、資料格式、錯誤碼、權限、交易邊界、排序、預設值、空值處理與副作用。
5. 分小步修改，避免把多種重構混在一起。
6. 修改後搜尋舊名稱、舊路徑或重複邏輯殘留。
7. 跑目標驗證，並把未驗證風險寫入回報。

## Rename

適用於重新命名變數、方法、類別、檔案、元件、模組或路由附近的內部符號。

- 先搜尋所有引用點。
- 檢查是否有字串引用、反射、模板引用、設定檔引用、路由名稱、序列化欄位或外部 API 欄位。
- 內部符號可直接改；公開契約要保留 alias、相容層或先取得使用者同意。
- 改檔名時同步檢查 import、測試、文件範例與 barrel export。
- 修改後搜尋舊名稱，確認只留下合理的歷史文字或相容層。

## Extract Function

適用於降低單一方法或元件內的複雜度。

- 先界定抽取片段的輸入、輸出、可變狀態與副作用。
- 優先抽取純邏輯；有副作用的抽取要讓函式名稱明確表達行為。
- 保持錯誤處理、early return、logging、metrics 與 transaction 行為不變。
- 避免抽出只被呼叫一次且反而增加理解成本的微小函式。
- 若抽取後參數過多，重新檢查邊界是否錯了，而不是直接建立巨大參數物件。

## Extract Class Or Module

適用於檔案過大、責任混雜或需要建立更清楚邊界。

- 先列出現有責任，再選一個穩定責任抽出。
- 保留原入口 API，除非使用者允許呼叫方同步改動。
- 優先搬移低耦合、可測、語意完整的邏輯。
- 檢查初始化順序、依賴注入、循環 import、單例狀態與共享 mutable state。
- 抽出後跑相關測試，並搜尋舊路徑與舊類別名稱。

## Split Module

適用於拆分大型模組、feature folder 或 service。

- 先找自然邊界：domain、I/O adapter、UI component、state management、API client、persistence、validation。
- 保持 public entrypoint 或 export surface 穩定，讓呼叫方不必一次全部改。
- 不要在同一步引入新框架或新架構風格。
- 先拆檔與調整 import，再做命名與邏輯整理。
- 注意測試 fixture、mock path、barrel export 與 package boundary。

## Deduplicate

適用於合併重複函式、驗證邏輯、查詢組裝、UI 狀態轉換或錯誤處理。

- 先確認重複程式碼的語意真的相同。
- 比對邊界條件：空值、排序、預設值、錯誤處理、權限、locale、timezone、transaction 與副作用。
- 若兩段程式碼只是目前剛好相似，但屬於不同業務概念，避免過早抽象。
- 抽出的共用函式名稱要反映業務語意，而不是只描述技術步驟。
- 修改後用既有測試或新增窄測試覆蓋兩個以上原呼叫點。

## Dependency Cleanup

適用於整理 import、降低耦合、反轉依賴方向或移除循環依賴。

- 先畫出直接依賴方向，確認哪個方向違反模組邊界。
- 優先搬移型別、介面、小 helper 或 adapter，不要一開始搬整個 service。
- 檢查 runtime import 與 type-only import 的差異。
- 注意初始化順序、side-effect import、DI container registration 與測試 mock。
- 修改後跑 typecheck 或 build，因為依賴清理常在靜態檢查才露出問題。

## Dead Code Removal

適用於移除未使用函式、類別、檔案、設定或分支。

- 先搜尋符號、路徑、字串名稱與設定鍵。
- 檢查動態引用：反射、template、router、CLI command、cron job、feature flag、DI registration、ORM mapping、SQL mapper、serialization、external webhook。
- 若是 public API、資料庫欄位、事件名稱或 migration，不要只因程式碼搜尋不到引用就刪除。
- 優先移除明確私有且搜尋無引用的程式碼。
- 刪除後跑 build/typecheck/test，並搜尋檔名與符號殘留。

## API-Preserving Redesign

適用於內部重組但保留外部行為。

- 先列出必須保留的 API：函式簽名、路由、request/response shape、錯誤格式、狀態碼、事件、side effect、資料庫寫入。
- 先建立相容層或 facade，再移動內部實作。
- 逐步把呼叫方導向新內部結構，但維持公開入口不變。
- 不要同時改資料模型與 API 行為。
- 驗證要覆蓋公開入口，而不是只測新內部 helper。
