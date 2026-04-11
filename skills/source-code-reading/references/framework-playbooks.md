# Framework Playbooks

這份 reference 提供特定技術棧的預設閱讀順序。只有在主 `SKILL.md` 已確認要做原始碼閱讀，且你需要依框架決定起手式時再讀。

以下不是絕對流程，而是預設優先順序。

## A. Vue / 前端互動型專案

建議順序：

1. route / page entry
2. template 中的事件綁定
3. script 中的 handler / composable / store
4. API client / service
5. response → state → UI 映射
6. dynamic import / keep-alive / guard / permission
7. 樣式或條件渲染是否影響可見行為

## B. React / Hook 型專案

建議順序：

1. route / page component
2. props / state / hook
3. event handler
4. query / mutation / API client
5. global state / context / store
6. effect / memo / callback 是否改變觸發條件
7. feature flag / permission / lazy loading

## C. Spring Boot / Java 後端

建議順序：

1. controller / request mapping
2. validation / filter / interceptor / AOP
3. service / use case
4. repository / mapper / DAO
5. transaction / exception / permission
6. cache / MQ / scheduler / external API
7. profile / config / conditional bean

## D. MyBatis / SQL-heavy

建議順序：

1. mapper interface
2. XML statement / annotation SQL
3. service 組參數
4. 動態 SQL 片段與條件
5. resultMap / DTO / entity 映射
6. stored procedure / view / trigger / generated SQL
7. 實際命中 SQL 的執行時證據

## E. Node / Express / Nest / Koa

建議順序：

1. route / controller
2. middleware / guard / pipe / interceptor
3. service
4. repository / DB access
5. config / provider / module registration
6. queue / event / scheduler
7. env 變數與 bootstrapping

## F. 排程 / Queue / Worker

建議順序：

1. trigger / producer
2. job / consumer registration
3. payload schema
4. handler
5. side effect
6. retry / dead-letter / idempotency
7. 失敗補償與觀測點
