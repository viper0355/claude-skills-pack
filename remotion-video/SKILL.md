---
name: remotion-video
description: "把逐字稿＋配音＋實錄素材做成成品影片（Remotion）。含時間軸鐵則、抽幀自審、播放相容轉檔。"
version: 1.0.0
author: 皮可（Piko）／皮可的 AI 新手教室
license: MIT
platforms: [macos]
metadata:
  hermes:
    tags: [remotion, video, youtube, rendering, react]
---

# Remotion 影片產線

把「定稿逐字稿 ＋ 配音 ＋ 實錄素材」做成可上架的成品影片。

這份 skill 的每一條規則都是**踩過才寫的**。它們不是建議，是**驗收條件**。
違反其中任何一條，產出會被退回重做——已經發生過整支影片重來的案例。

---

## 0. 動手前先確認四件事

```bash
ls <配音>.mp3 <配音>.srt        # 兩個都要在
ffprobe -v error -show_entries format=duration -of csv=p=0 <配音>.mp3
node --version && npx remotion --version
```

| 確認 | 為什麼 |
|---|---|
| 配音 mp3 與 **詞級對齊的 SRT** 都存在 | SRT 是時間軸唯一真實來源 |
| 配音實長用 `ffprobe` 讀出來 | **不准手打秒數** |
| 素材清單與「可用區間」文件 | 實錄常有不能用的段落（載入中、靜止等待）|
| 去識別化清單 | 私人資訊入鏡＝重做 |

---

## 1. 🔴 時間軸鐵則：秒數一律由 SRT 推導

**`.tsx` 檔案裡不准出現任何手打的秒數。**

做法：先用一支 `prep` 腳本把 SRT 解析成 `cues.json`，元件只用 `at(n)` 取值。

```js
// prep.mjs — 從 SRT 產生 cues.json，順便驗證素材
const cues = parseSrt(SRT);              // [{n, start, end, text}]
const fps = 30;
const durationInFrames = Math.round(audioDurationSec * fps);  // ffprobe 讀來的
writeJson('src/data/epN-cues.json', cues.map(c => ({...c, f: Math.round(c.start*fps)})));
```

```tsx
// 元件裡
const at = (n) => cues[n-1].f;           // 取第 n 句的起始格
<Sequence from={at(69)} durationInFrames={at(72)-at(69)}> ... </Sequence>
```

**為什麼**：曾經有一版手打秒數，改了旁白之後全片畫面與旁白錯開，整支重做。

### 改旁白之後必做的事

**如果 SRT 的 cue 數量變了，插入點之後的所有 cue 編號會位移。**

```bash
# 比對新舊 SRT，找出第一個不同的 cue
diff <(grep -A2 '^[0-9]*$' 舊.srt) <(grep -A2 '^[0-9]*$' 新.srt) | head
```

⇒ tsx 裡寫死的 cue 編號要照位移量調整。**逐一核對，漏一個就整段錯位。**
這件事發生過：cue 從 202 變 207，插入點之後全部 +5，差點整段畫面對錯旁白。

---

## 2. 結構規則

| 規則 | 為什麼 |
|---|---|
| **每組鏡頭包在 `<Sequence>` 裡**，`OffthreadVideo` 的 `startFrom` 用組內相對格數 | 不包的話每支素材都要手動扣絕對偏移，極易出錯 |
| **同源連續幕要合併成一個 Sequence** | 各自掛 `OffthreadVideo` 會重載跳閃，`premountFor` 擋不住 |
| **同一個輸出檔只准單一渲染行程** | 雙行程同寫會讓檔案損毀 |
| 長渲用 `nohup` 獨立跑 | 關掉終端機不會中斷 |

---

## 3. 🔴 畫面品質三條（會被退回的常見原因）

### 3-1　單一靜止畫面 ≤ 10 秒

超過就要換鏡、推近、或讓元素逐句遞進。

**曾經有一支影片 65% 的時間是靜止畫，等於投影片配旁白，補拍六支才救回來。**

字卡幕特別容易犯：一張卡放 14 秒不動＝失敗。做法是**逐句遞進**（一句亮一個元素）＋
**緩慢推鏡**（scale 1.0 → 1.026 這種幅度就夠）。

### 3-2　字高 ≥ 26px（以 1920×1080 輸出計）

小於 26px 在手機上讀不到。**渲完要實際量**，不要目測。

### 3-3　直式素材不准拉伸

手機錄影常見 552×1080、484×1080。放進 16:9 的做法：

```
原比例置中 + 背景放同一畫面放大虛化 + 深色手機外框
```

**絕不要** `width:100%` 硬撐——人臉會變形，觀眾一眼看得出來。

---

## 4. 🔴 去識別化用不透明色塊

**不准用模糊（blur）**——模糊可以被還原，而且錄影壓縮後常常糊得不夠。

```tsx
<div style={{position:'absolute', left:X, top:Y, width:W, height:H,
             background:'#1e1e1e'}} />   // 用抽幀量到的實際背景色
```

色塊顏色**要用抽幀量到的實際背景色**，隨便填黑色會像補丁。

要遮什麼由專案的清單決定，常見：真實姓名、帳號代號、完整檔案路徑、
側邊欄裡的私人項目名稱。**寧可多遮，不要漏。**

---

## 5. 內嵌字幕

約 9 成觀眾不開 CC，**字幕要燒進畫面**。

- 吃**頁級 SRT**（每頁一到兩行），不是句級
- 句級 SRT 留給 YouTube CC 上傳用
- 字幕帶區域要淨空，**不要讓角色或元素壓在字上**

---

## 6. 渲染與交付

### 6-1　改動後先渲一張靜幀再渲全片

```bash
npx remotion still <CompId> out/check.png --frame=<某格>
```

看過再渲全片。**曾經因為跳過這步，整支渲完才發現版面破圖。**

### 6-2　播放相容轉檔（必做）

Remotion 母帶預設是 `yuvj420p`（full range），**多數播放器放不出來**。

```bash
ffmpeg -i out/母帶.mp4 \
  -vf "scale=in_range=full:out_range=tv,format=yuv420p" \
  -color_range tv \
  -c:v libx264 -crf 18 -preset medium \
  -x264-params colorprim=bt709:transfer=bt709:colormatrix=bt709 \
  -c:a aac -b:a 192k -movflags +faststart \
  -y 播放版.mp4
```

⚠️ **只給 `-pix_fmt yuv420p` 沒有用**，出來仍是 `yuvj420p`。
必須用 `scale=in_range=full:out_range=tv` ＋ `-color_range tv`，
而且 bt709 三件套要靠 `-x264-params` 才真的寫進 SPS VUI。

驗證：

```bash
ffprobe -v error -select_streams v:0 \
  -show_entries stream=pix_fmt,color_range,color_primaries,color_space \
  -of default=nw=1 播放版.mp4
# 要看到 yuv420p / tv / bt709 / bt709
```

---

## 7. 🔴 交付前的自審（不准跳過）

**渲完要等它真的完成，然後抽幀，用眼睛看過。**

```bash
# 全片每 15–30 秒抽一幀
for t in $(seq 0 30 $DURATION); do
  ffmpeg -v error -ss $t -i 播放版.mp4 -frames:v 1 -y frames/f$t.png
done
```

**然後真的打開圖來看**，逐張過這六類：

| # | 檢查 |
|---|---|
| 1 | 版面破圖／文字溢出／元素互相打架 |
| 2 | 字高 < 26px |
| 3 | 靜止畫面超過 10 秒 |
| 4 | 去識別化有沒有漏 |
| 5 | 直式素材有沒有被拉伸 |
| 6 | 字幕與旁白對不對得上 |

⚠️ **只跑 `ffprobe` 不算自審。** 規格對不代表畫面對。
⚠️ **不要把錯誤整包丟給人類去發現。** 自己看過、自己修完再回報。

### 回報要附的實證

1. `ffprobe` 的**實際輸出**（不是預期值）
2. 鏡頭組表：組別／cue 範圍／秒數／素材／動態設計
3. 六類自審逐項結果 ＋ **你實際看過的抽幀時間點**
4. 已知瑕疵誠實列出（有就列，沒有就說沒有）

---

## 8. 長任務怎麼不被中斷

渲染動輒數分鐘到數十分鐘，**指令逾時被砍是常見失敗原因**。

```bash
# 用 nohup 脫離，然後輪詢等待
nohup npx remotion render <CompId> out/影片.mp4 --log=error > /tmp/render.log 2>&1 &

until ! pgrep -f "remotion render <CompId>" >/dev/null; do sleep 20; done
tail -5 /tmp/render.log
```

**每完成一個里程碑就寫進施工日誌並 commit**，這樣中斷了可以續接，
不用從頭重來。

---

## 9. 常見失敗模式對照表

| 症狀 | 真正原因 | 解法 |
|---|---|---|
| 影片放不出來／顏色怪 | 母帶是 `yuvj420p` | §6-2 轉檔 |
| 畫面與旁白對不上 | 手打秒數，或改稿後 cue 編號沒位移 | §1 |
| 素材切換時閃一下 | 同源素材各自掛 `OffthreadVideo` | 合併 Sequence |
| 看起來像投影片 | 靜止畫超過 10 秒 | §3-1 逐句遞進＋推鏡 |
| 人臉變形 | 直式素材被拉伸 | §3-3 |
| 輸出檔損毀 | 兩個渲染行程同寫一個檔 | 單一行程 |
| 渲到一半沒了 | 指令逾時被砍 | §8 `nohup` ＋ 輪詢 |

---

## 10. 這份 skill 的底線

- **秒數從 SRT 來，不從腦袋來。**
- **交付前自己看過畫面。** 看不到畫面就不要宣稱做完了。
- **不確定就說不確定**，不要用「應該沒問題」交差。
