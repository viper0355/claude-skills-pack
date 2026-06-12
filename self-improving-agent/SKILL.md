---
name: self-improving-agent
description: "Captures learnings, errors, and corrections into .learnings/ markdown logs for continuous improvement. Use when: (1) a command or operation fails unexpectedly, (2) user corrects the agent ('不對'、'其實是…'、'No, that's wrong'), (3) user requests a missing capability, (4) an external API/tool fails or behaves unexpectedly, (5) knowledge turns out to be outdated, (6) a better approach is discovered for a recurring task. Also review .learnings/ before starting major tasks."
metadata:
  version: 1.0.0-claude
  origin: pskoett/self-improving-agent（OpenClaude 版改寫）
---

# Self-Improving Agent（Claude Code 優化版）

把工作過程中的教訓記錄成 repo 內的 markdown 日誌，讓所有 AI（Claude/Codex/Gemini）
與未來的 session 都能參考，避免重複踩坑。

## 初始化（首次使用時）

在專案根目錄建立 `.learnings/`（已存在則跳過，絕不覆蓋）：

```bash
mkdir -p .learnings
[ -f .learnings/LEARNINGS.md ] || printf "# Learnings\n\n修正、洞察與最佳實踐。分類：correction | insight | knowledge_gap | best_practice\n\n---\n" > .learnings/LEARNINGS.md
[ -f .learnings/ERRORS.md ] || printf "# Errors\n\n指令失敗與整合錯誤。\n\n---\n" > .learnings/ERRORS.md
[ -f .learnings/FEATURE_REQUESTS.md ] || printf "# Feature Requests\n\n使用者提出但尚未實現的能力。\n\n---\n" > .learnings/FEATURE_REQUESTS.md
```

## 何時記錄什麼

| 情境 | 寫入 | 分類 |
|---|---|---|
| 指令/操作意外失敗 | `ERRORS.md` | — |
| 使用者糾正你 | `LEARNINGS.md` | `correction` |
| 外部 API/工具故障或行為怪異 | `ERRORS.md` | 含整合細節 |
| 發現自己知識過時 | `LEARNINGS.md` | `knowledge_gap` |
| 找到更好的做法 | `LEARNINGS.md` | `best_practice` |
| 使用者想要的功能還不存在 | `FEATURE_REQUESTS.md` | — |

## 記錄格式

```markdown
## [YYYY-MM-DD] 一句話標題
- **分類**：correction / insight / knowledge_gap / best_practice
- **情境**：當時在做什麼
- **教訓**：學到什麼（一兩句，重點是「下次怎麼做」）
- **See Also**：相關條目（可選）
```

保持精簡：摘要勝過完整輸出。與既有條目相似時用 **See Also** 連結而非重複新增。

## 升級機制（重要條目往上提）

廣泛適用的教訓不要留在日誌裡腐爛，提升到常駐位置：

| 教訓類型 | 提升到 |
|---|---|
| 專案規則、工作流程 | 專案 `CLAUDE.md` |
| 使用者偏好、跨專案習慣 | Claude 長期記憶（memory） |
| 可程式化的修復 | 直接改腳本/設定，並在條目標註「已修復」 |

## 安全紅線

- **絕不記錄**：API key、token、密碼、環境變數值、完整設定檔
- 記錄錯誤時用摘要或遮蔽後的片段，不貼完整原始輸出
- `.learnings/` 預設進版控（這是它的價值）；若專案含敏感資訊才加進 `.gitignore`

## 開工前回顧

執行重大任務前，先掃一眼 `.learnings/` 的相關條目（grep 關鍵字即可），
特別是 `ERRORS.md` 裡同一工具/API 的舊坑。
