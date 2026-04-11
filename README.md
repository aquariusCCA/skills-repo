# Personal Codex Skills

這個倉庫用來存放我自己的 Codex skills。每個 skill 都放在 `skills/` 底下的一個獨立資料夾中，並以 `SKILL.md` 作為主要入口。

## 目錄結構

```text
skills-repo/
└── skills/
    ├── repair-study-notes/
    │   ├── SKILL.md
    │   ├── agents/
    │   └── references/
    ├── source-code-reading/
    │   ├── SKILL.md
    │   ├── agents/
    │   └── references/
    ├── source-code-refactoring/
    │   ├── SKILL.md
    │   ├── agents/
    │   ├── prompts/
    │   └── references/
    ├── spec-to-code/
    │   ├── SKILL.md
    │   └── agents/
    └── study-notes-markdown/
        ├── SKILL.md
        └── references/
```

## 現有 Skills

- `study-notes-markdown`: 將學習筆記整理成 Markdown 結構。
- `repair-study-notes`: 修復、整理或重建學習筆記內容。
- `source-code-reading`: 閱讀、追蹤與整理專案原始碼理解筆記。
- `source-code-refactoring`: 安全重構既有程式碼並保護行為。
- `spec-to-code`: 將需求描述、規格或 ticket 落地成可驗證的程式碼變更。

- 目前已經完善的 Skills: `study-notes-markdown`、`source-code-reading`、`repair-study-notes`

## 新增 Skill

新增 skill 時，建議使用以下結構：

```text
skills/
└── my-skill-name/
    ├── SKILL.md
    ├── references/
    ├── scripts/
    └── assets/
```

其中只有 `SKILL.md` 是必要檔案，其他資料夾依需求建立。

`SKILL.md` 建議包含 frontmatter：

```markdown
---
name: my-skill-name
description: 說明這個 skill 何時應該被使用。
---

# My Skill Name

在這裡描述使用流程、規則、範例與注意事項。
```

## 維護原則

- 一個資料夾只放一個 skill。
- skill 名稱使用小寫英文與連字號，例如 `study-notes-markdown`。
- 大型範例、模板或背景資料放在 `references/`。
- 可重複執行的工具或輔助程式放在 `scripts/`。
- 圖片、範本或其他靜態檔案放在 `assets/`。
- 讓 `SKILL.md` 保持精簡，只引用必要的補充檔案。

## 使用方式

這個倉庫可作為個人 skills 的來源目錄。需要啟用某個 skill 時，將對應資料夾放到 Codex 可讀取的 skills 目錄中，或依目前環境的設定引用這個倉庫中的 `skills/<skill-name>`。
