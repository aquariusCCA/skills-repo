# Deep Tracing

這份 reference 提供主 `SKILL.md` 沒保留的進階閱讀內容。只有在以下情況再讀：

- 入口不明，無法直接選起點
- 需要更細的核對清單或符號級閱讀模板
- 擔心誤讀，需要更完整的反誤讀規則
- 需要對動態行為做最小驗證，但主檔的邊界還不夠細

## 一、找不到入口時的退避策略

若最小盤點後仍找不到明確入口，不可直接亂追。必須依序採用以下退避策略：

### 第一層退避：從最接近使用者訊號的地方開始

優先順序：

1. stack trace / 錯誤訊息
2. API path / route path / button text / event name
3. 關鍵業務詞彙 / 方法名 / component 名
4. 測試名稱 / log 關鍵字
5. 契約名稱 / DTO / SQL id

### 第二層退避：從系統註冊點開始

常見註冊點：

- 路由表
- controller 掃描
- dependency injection 設定
- event listener / scheduler / consumer 註冊
- router config / menu config / page registry
- ORM / mapper / XML / repository 掃描

### 第三層退避：從邊界檔開始

若核心邏輯未知，先讀最可能的邊界檔：

- 入口頁 / 容器頁
- controller / handler
- service facade
- job / consumer
- API client
- module index / setup / bootstrap

### 第四層退避：宣布暫時無法定位

若以上仍找不到入口，必須明確輸出：

- 入口未定位
- 原因類型
- 建議下一個最值得補的資訊

## 二、必做閱讀核對清單

除非使用者明確要求超短答，否則每次閱讀至少應回答以下問題；答不出也要交代缺口。

### 通用核對

- 入口是什麼
- 輸入是什麼
- 輸出是什麼
- 狀態在哪裡改變
- 副作用在哪裡發生
- 錯誤路徑是什麼
- 同步 / 非同步邊界在哪裡
- 設定是否影響行為
- 目前確認到的是定義、註冊，還是實際生效
- 是否還有主要替代路徑未排除

### 後端流程額外核對

- request 先經過哪些 controller / filter / interceptor / middleware
- 核心業務邏輯在哪個 service
- persistence 實際落在哪個 repository / mapper / SQL
- transaction、權限、例外處理是否介入
- 是否有 cache、retry、排程、非同步或外部系統互動
- profile / env / conditional bean 是否影響實作選擇

### 前端流程額外核對

- 事件如何綁定
- 狀態由誰持有與更新
- API 從哪裡發出
- 回應如何映射回 UI
- route guard、dynamic import、lazy loading 是否改變流程
- 呈現差異是否由 class、條件渲染、動畫狀態或權限控制影響
- 是否有 store、query cache、keep-alive、suspense、plugin 介入

### SQL / 資料存取額外核對

- SQL 定義在哪裡
- SQL 由誰組裝或呼叫
- 參數如何綁定
- 結果如何映射
- 是否存在動態 SQL、stored procedure、view、trigger、resultMap、generated query
- 目前確認的是靜態 SQL，還是實際命中的 SQL

### 非同步 / 事件驅動額外核對

- 事件來源是什麼
- 消費者如何註冊
- payload 結構在哪裡定義
- side effect 落在哪裡
- retry / idempotency / dead-letter / 補償邏輯是否存在
- 是否能確認當前問題真的走過該隊列 / worker

## 三、符號級最小閱讀模板

### 方法 / 函式

至少確認：

- 誰呼叫它
- 它呼叫誰
- 參數從哪裡來
- 回傳值去哪裡
- 是否存在 early return / 例外攔截 / 隱藏副作用
- 是否讀寫共享狀態、cache、DB、檔案、外部服務

### 元件 / 頁面

至少確認：

- props / route / store / composable 輸入
- 使用者操作如何觸發事件
- 狀態在哪裡改變
- 什麼條件影響渲染
- API / 子元件 / 外部 hooks 的邊界在哪裡
- 是否有懶載入、插槽、動態元件、權限控制影響實際流程

### 端點 / controller / handler

至少確認：

- 路由如何匹配到它
- request 驗證、授權、轉換在哪一層完成
- 核心邏輯交給哪個 service
- response 由誰組裝
- 失敗路徑如何處理
- AOP / middleware / filter / interceptor 是否介入

### repository / mapper / DAO

至少確認：

- 查詢真正來源是 XML、註解、query builder 還是 generated code
- 參數如何綁定到 SQL
- 結果如何映射到 entity / DTO
- 是否存在動態 SQL、條件拼接、resultMap、join 擴充
- 是否還有其他同名 statement / mapper / override

### 模組 / package / 目錄

至少確認：

- 模組責任是什麼
- 對外入口有哪些
- 典型流程如何流經主要檔案
- 哪些檔案是核心、哪些只是支援
- 邊界、耦合點、跨界點在哪裡
- 與其他模組互動透過什麼契約

## 四、硬性反誤讀規則

以下行為預設禁止，除非已有直接證據：

- 只憑檔名、目錄名、類別名、方法名判定真實責任
- 只看到 interface / abstract class 就宣稱某實作一定被執行
- 只看到 DTO / entity / 欄位名就推定完整業務語意
- 只看到按鈕文字、template 標記、路由名稱就宣稱已追到事件鏈
- 只看到 mapper 介面或 SQL 片段就宣稱已確認實際命中的 SQL
- 只看單一測試就把它當全部行為證據
- 只看 barrel / re-export / index file 就宣稱已理解核心邏輯
- 把搜尋命中當成已確認
- 把框架慣例當成專案事實
- 把過往專案經驗包裝成目前專案結論
- 因為一條路徑說得通，就忽略其他尚未排除的候選路徑

遇到以下情況時，必須額外保守：

### 1. 同名符號

若有多個同名 class / method / component / service / mapper：

- 不得只看第一個搜尋命中
- 必須交代為什麼排除其他候選
- 若尚未排除，結論只能列為待驗證或合理推論

### 2. 多實作 / 插件機制 / DI

若看到 interface、plugin contract、abstract class、strategy registry、bean 注入：

- 先列可能實作
- 再找註冊點、注入點、匹配條件
- 沒有注入 / 匹配證據，不得宣稱哪個實作一定生效

### 3. 動態映射 / 動態 SQL / 動態路由

若流程依賴 runtime config、profile、annotation、AOP、反射、dynamic import、feature flag、MyBatis 動態 SQL、ORM 映射：

- 先說明靜態可見部分
- 再說明哪段仍受 runtime 影響
- 必要時才做最小驗證
- 無法驗證時，必須明確降級為待驗證

### 4. generated code / codegen / build 產物

若核心邏輯來自 generated file、annotation processor、ORM codegen、SDK codegen：

- 不得假裝完整理解 source 即等於理解最終行為
- 必須指出生成來源或缺口
- 若未看到生成規則，最多只能到合理推論或待驗證

### 5. 環境 / profile / tenant 差異

若行為可能因 env、profile、tenant、region、version 差異而不同：

- 必須明確指出環境依賴
- 未確認當前環境時，不得宣稱唯一結論

## 五、動態行為的最小驗證策略

以下情況常無法只靠靜態閱讀完全確認；應優先做**最小、唯讀、低副作用**驗證，而不是直接假設。

### 1. Spring / DI / Annotation / AOP

優先檢查：

- annotation / interface / aspect 定義
- bean 註冊或條件裝配位置
- 實際注入點
- 攔截 / 匹配條件
- profile / conditional bean / order
- 本流程中的實際被呼叫點

### 2. MyBatis / ORM / SQL-heavy

優先檢查：

- mapper / repository 介面
- XML / 註解 / query builder / generated SQL
- service 如何組參數
- resultMap / entity / DTO 映射
- include / choose / if / trim / foreach / dynamic fragment
- 是否有 stored procedure、view、trigger

### 3. 前端動態流程

優先檢查：

- route 定義與實際頁面綁定
- dynamic import / lazy loading
- event 綁定點
- store / composable / hook 狀態更新
- API client 與回應映射
- route guard、permission、feature flag、AB test

### 4. Queue / Scheduler / Worker

優先檢查：

- trigger 來源
- job / consumer 註冊
- payload 內容
- side effect
- retry / fallback / dead-letter / idempotency
- 是否有多個 consumer / versioned payload

### 5. Feature flag / Runtime config / Environment

優先檢查：

- 定義在哪裡
- 哪裡讀取
- 哪裡套用
- 預設值是什麼
- 目前是否有證據顯示在本流程中實際生效

若以上仍無法確認，應在輸出中明確寫出缺口，而不是假裝完整理解。
