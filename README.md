# Claude Code Skills Pack

幫 Claude Code 加裝的實用 skills，已通過原始碼安全審查並做繁中在地化調整。

## 收錄的 Skills

| Skill | 功能 | 依賴 | 原始出處 |
|---|---|---|---|
| [youtube-watcher](youtube-watcher/) | 抓 YouTube 影片字幕，做摘要/問答/研究 | `yt-dlp` | [ClawHub](https://clawhub.ai/michaelgathara/youtube-watcher) |
| [humanizer](humanizer/) | 去除 AI 寫作痕跡，讓文字更自然（24 種模式） | 無 | [ClawHub](https://clawhub.ai/biostartechnology/humanizer) |
| [nano-banana-pro](nano-banana-pro/) | 用 Gemini 3 Pro Image 生成/編輯圖片（最高 4K） | `uv`、Gemini API key | [ClawHub](https://clawhub.ai/steipete/nano-banana-pro) |

本版調整：
- `youtube-watcher`：字幕語言改為繁中優先（`zh-Hant,zh-TW,zh,en`），路徑改為 Claude Code 格式
- `nano-banana-pro`：路徑由 `~/.codex` 改為 `~/.claude`

## 安裝（最簡單的方式）

打開 Claude 對話，貼上本 repo 網址（https://github.com/viper0355/claude-skills-pack），然後說：

> 幫我審查這個 SKILL，並注意是否有危險代碼，如果沒有的話幫我安裝並啟用這個 SKILL

Claude 會自動處理 clone、依賴安裝（yt-dlp、uv）和啟用，重啟 Claude Code 後生效。

<details>
<summary>手動安裝（進階使用者）</summary>

```bash
git clone https://github.com/viper0355/claude-skills-pack
cp -r claude-skills-pack/{youtube-watcher,humanizer,nano-banana-pro} ~/.claude/skills/
brew install yt-dlp uv
```
</details>

## 推薦搭配：Tavily 官方 Skills（網頁搜尋/擷取/深度研究）

官方 repo：**https://github.com/tavily-ai/skills**

包含 `tavily-search`（LLM 優化搜尋）、`tavily-extract`（網頁轉 markdown）、`tavily-research`（帶引用的深度研究報告）等，免費方案每月 1,000 credits。

### Tavily API Key 申請教學（1 分鐘）

1. 到 [tavily.com](https://www.tavily.com/) 點 **Sign Up**，用 Google 帳號登入即可
2. 登入後進入 Dashboard（[app.tavily.com](https://app.tavily.com/)），在 **API Keys** 區塊會看到一組預設的 `tvly-dev-...` key，點眼睛圖示顯示後複製
3. 回到 Claude 對話，貼上官方 repo 的網址（https://github.com/tavily-ai/skills），告訴 Claude：

   > 幫我審查這個 SKILL，並注意是否有危險代碼，如果沒有的話幫我安裝並啟用這個 SKILL

4. 安裝完成後，把你的 API key 貼給 Claude，請它幫你設定登入即可

> 免費方案 1,000 credits/月：basic 搜尋 1 credit、advanced 搜尋 2 credits、深度研究消耗較多，省著用。

## 推薦搭配：Axton ai-pair（多 AI 互相調用協作）

官方 repo：**https://github.com/axtonliu/ai-pair**

讓 Claude 指揮一個異質 AI 團隊：一個負責產出（Claude），兩個負責審查（Codex + Gemini）。不同模型家族的審查視角不同，覆蓋面最大化。寫程式、文章、影片腳本都適用。

```
/ai-pair dev-team [專案]       # 開發團隊：developer + codex-reviewer + gemini-reviewer
/ai-pair content-team [主題]   # 內容團隊：author + codex-reviewer + gemini-reviewer
/ai-pair team-stop             # 收工，清理資源
```

### 安裝

跟 Claude 說：

> 幫我審查這個 SKILL（https://github.com/axtonliu/ai-pair），並注意是否有危險代碼，如果沒有的話幫我安裝並啟用這個 SKILL，依賴的 Codex CLI 和 Gemini CLI 也一起裝

裝好後分別跑一次 `codex` 和 `gemini` 登入帳號即可，不需額外 API key。

## Gemini API Key（nano-banana-pro 用）

到 [aistudio.google.com/apikey](https://aistudio.google.com/apikey) 建立 key，然後：

```bash
echo 'export GEMINI_API_KEY="你的key"' >> ~/.zshrc
```

## 授權

收錄的 skills 原始授權皆為 MIT-0。本 repo 的調整部分同樣以 MIT-0 釋出。
