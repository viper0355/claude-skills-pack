# Claude Code Skills Pack

幫 Claude Code 加裝的實用 skills，已通過原始碼安全審查並做繁中在地化調整。

## 📦 收錄的 Skills

| Skill | 功能 | 依賴 | 原始出處 |
|---|---|---|---|
| 📺 [youtube-watcher](youtube-watcher/) | 抓 YouTube 影片字幕，做摘要/問答/研究 | `yt-dlp` | [ClawHub](https://clawhub.ai/michaelgathara/youtube-watcher) |
| ✍️ [humanizer](humanizer/) | 去除 AI 寫作痕跡，讓文字更自然（24 種模式） | 無 | [ClawHub](https://clawhub.ai/biostartechnology/humanizer) |
| 🍌 [nano-banana-pro](nano-banana-pro/) | 用 Gemini 3 Pro Image 生成/編輯圖片（最高 4K） | `uv`、Gemini API key | [ClawHub](https://clawhub.ai/steipete/nano-banana-pro) |
| 📈 [self-improving-agent](self-improving-agent/) | 把踩坑與教訓自動記錄成 `.learnings/` 日誌，跨 session/跨 AI 共讀，越用越聰明 | 無 | [ClawHub](https://clawhub.ai/pskoett/self-improving-agent) |

本版調整：
- `youtube-watcher`：字幕語言改為繁中優先（`zh-Hant,zh-TW,zh,en`），路徑改為 Claude Code 格式
- `nano-banana-pro`：路徑由 `~/.codex` 改為 `~/.claude`
- `self-improving-agent`：重寫為 Claude Code 優化版——繁中化、移除 OpenClaude 專屬內容（AGENTS/SOUL/TOOLS.md、hooks），升級目標改為 CLAUDE.md 與 Claude 記憶

## ⚡ 安裝（最簡單的方式）

打開 Claude 對話，貼上本 repo 網址 https://github.com/viper0355/claude-skills-pack

然後說：> 幫我審查這個 SKILL，並注意是否有危險代碼，如果沒有的話幫我安裝並啟用這個 SKILL

Claude 會自動處理 clone、依賴安裝（yt-dlp、uv）和啟用，重啟 Claude Code 後生效。

<details>
<summary>手動安裝（進階使用者）</summary>

```bash
git clone https://github.com/viper0355/claude-skills-pack
cp -r claude-skills-pack/{youtube-watcher,humanizer,nano-banana-pro,self-improving-agent} ~/.claude/skills/
brew install yt-dlp uv
```
</details>

## 🔍 推薦搭配：Tavily 官方 Skills（網頁搜尋/擷取/深度研究）

官方 repo：**https://github.com/tavily-ai/skills**

包含 `tavily-search`（LLM 優化搜尋）、`tavily-extract`（網頁轉 markdown）、`tavily-research`（帶引用的深度研究報告）等，免費方案每月 1,000 credits。

### 🔑 Tavily API Key 申請教學（1 分鐘）

1. 到 [tavily.com](https://www.tavily.com/) 點 **Sign Up**，用 Google 帳號登入即可
2. 登入後進入 Dashboard（[app.tavily.com](https://app.tavily.com/)），在 **API Keys** 區塊會看到一組預設的 `tvly-dev-...` key，點眼睛圖示顯示後複製
3. 回到 Claude 對話，貼上官方 repo 的網址（https://github.com/tavily-ai/skills），告訴 Claude：

   > 幫我審查這個 SKILL，並注意是否有危險代碼，如果沒有的話幫我安裝並啟用這個 SKILL

4. 安裝完成後，把你的 API key 貼給 Claude，請它幫你設定登入即可

> 免費方案 1,000 credits/月：basic 搜尋 1 credit、advanced 搜尋 2 credits、深度研究消耗較多，省著用。

## 🤝 推薦搭配：Axton ai-pair（多 AI 互相調用協作）

官方 repo：**https://github.com/axtonliu/ai-pair**

**摘要**：Axton Liu 開發的多 AI 協作 skill。核心概念是「一個創作、兩個審查」——由 Claude 擔任產出者（開發者或作者），再調用 Codex（GPT）和 Gemini 兩個不同家族的模型當審查者。因為不同模型的審查視角和盲點不同，交叉審查能找出單一 AI 看不到的問題。內建開發團隊（dev-team）與內容團隊（content-team）兩種模式，寫程式、文章、影片腳本都適用，一句指令就能讓三個 AI 開始協作。

安裝方式、依賴與完整用法，詳情請到他的專案查看：https://github.com/axtonliu/ai-pair

## 🧠 推薦搭配：Harry Lee second-brain（第二大腦技能集）

官方 repo：**https://github.com/harryleemedia/second-brain**

**摘要**：Harry Lee（harryleemedia）開發的「第二大腦」技能集，把 Claude Code 從寫程式工具擴展成知識工作系統。收錄品牌與語調產生器、PPTX 品牌簡報／LinkedIn 輪播圖產生器、SOP／Runbook 文件建立器、MCP 客戶端（按需載入工具定義、不撐爆上下文）、技能建立器，以及 Remotion 程式化影片製作。核心是「漸進式載入」——Claude 只在需要時才載入詳細指令，省上下文又保專業深度。對做簡報、品牌一致性、影片內容的創作者特別實用。

安裝方式、依賴與完整用法，詳情請到他的專案查看：https://github.com/harryleemedia/second-brain

## 🔑 Gemini API Key（nano-banana-pro 用）

> [!WARNING]
> ### ⚠️ 此功能需要另外付費
> **Gemini 3 Pro Image（Nano Banana Pro）的圖片生成是計次收費，不在免費額度內。請先到 [Google AI Studio](https://aistudio.google.com/) 確認你的方案與費率，再啟用此 skill。**

到 [aistudio.google.com/apikey](https://aistudio.google.com/apikey) 建立 key，然後：

```bash
echo 'export GEMINI_API_KEY="你的key"' >> ~/.zshrc
```

## 📄 授權

收錄的 skills 原始授權皆為 MIT-0。本 repo 的調整部分同樣以 MIT-0 釋出。
