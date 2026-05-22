---
title: 'AI 圖像生成 & 影片製作完整指南 — @art 的工作流分享'
description: '從 OAB Bot 自動生圖、手動文生圖/圖生圖 PROMPT 技巧，到 AI 影片 MV 製作 pipeline 的完整教學。'
pubDate: 'May 22 2026'
heroImage: '../../assets/banner.jpg'
---

# AI 圖像生成 & 影片製作指南

> 由 @art (art_huang) 分享於 OpenAB Discord — 2026-05-21  
> 整理自 `#help` 頻道 → [cron + codex gen image](https://discord.com/channels/1491295327620169908/1500132173959921914/1507061728725438555) 討論串

---

## 目錄

1. [透過 OAB Bot 自動生圖流程](#1-透過-oab-bot-自動生圖流程)
2. [手動網站生圖（文生圖 & 圖生圖）](#2-手動網站生圖文生圖--圖生圖)
3. [AI 影片 / MV 製作流程](#3-ai-影片--mv-製作流程)
4. [工具與資源連結](#4-工具與資源連結)

---

## 1. 透過 OAB Bot 自動生圖流程

art 在 OpenAB 上設定 Bot 技能（Skills），讓 Bot 自動化整個圖像生成流程：

### 流程步驟

1. **準備參考圖** — 多張，依情境分類
2. **丟參考圖到 Discord** — 讓 Bot 接收素材
3. **Bot 產生 PROMPT** — 根據參考圖自動生成描述
4. **PROMPT 經過潤飾技能** — 另一道技能去除「擺拍感」，讓畫面更自然
5. **參考圖 + PROMPT 一起送到內建工具** — 執行圖像生成

### 目前挑戰

> 效果沒有很好，還需要很長時間調教。往往會因為動態描述太多景物，造成權重偏向景物，人物臉型就跑掉。

**可能的改進方向：**
- 先產人物，再產景色，兩張融合
- 採用固定 PROMPT 模板減少變異

### 範例：Bot 自動生圖 vs 手動生圖的差異

![Bot 自動生圖效果對比 1](https://cdn.discordapp.com/attachments/1500132173959921914/1507066098921639936/image.png?ex=6a1134ed&is=6a0fe36d&hm=3391db8d179fb0782c74b979a1f81991143d8340b51be38580071e9fa53c4bc9&)

![Bot 自動生圖效果對比 2](https://cdn.discordapp.com/attachments/1500132173959921914/1507066099252859064/image.png?ex=6a1134ed&is=6a0fe36d&hm=2be7c0e032b1958e05c3bf019a3c29ec33026b588e07f3af90bd6170004b7b33&)

> 就算正確用對方法產圖，出來的圖都不像（人物臉型偏移問題）

---

## 2. 手動網站生圖（文生圖 & 圖生圖）

在網站上手動操作更簡單，效果也更好（因為可以用 Think 模式，GPT-IMAGE 效果更佳）。

### 基本做法

1. **上傳一張喜歡的角色圖卡**（人物參考）
2. **複製 Bot 產的 PROMPT，或網路蒐集的 PROMPT**
3. 送出生成

### 文生圖 PROMPT 範例

直接用以下 PROMPT 做「文生圖」：

```
一位20多歲的精緻東亞美女，深棕色長髮優雅高盤成凌亂髮髻，鬆散碎髮垂落臉側與頸部、
第一人稱視角，一名女子在鏡頭前用紅色口紅在玻璃上書寫，鏡頭上有紅色口紅的筆觸跟唇印，
半透明顏料，墨跡飛濺，污漬，一隻手伸向鏡頭，廣角鏡頭畸變，面部特寫，胡茬，凌亂的頭髮，
隨意的表情，戶外場景，背景是膠片和日式和風建築，日光，自然光，筆觸不規則，字跡潦草，
隨意飛濺。鏡頭畸變，景深，前景模糊。透明表面反射，玻璃上的光線反射。
有三排文字，第一排上面簽名寫著中文書法草體字「ｘｘｘ」，
第二排用西洋書法寫「ｘｘｘ」,第三排寫著電話號碼「ｘｘｘ」
```

### 圖生圖 PROMPT 範例

改寫成「圖生圖」版本 — 開頭加上「人物參考照片」引導模型參考上傳的角色卡：

```
人物參考照片、第一人稱視角，女子在鏡頭前用紅色口紅在玻璃上書寫，
鏡頭上有紅色口紅的筆觸跟唇印，半透明顏料，墨跡飛濺，污漬，一隻手伸向鏡頭，
廣角鏡頭畸變，面部特寫，凌亂的頭髮，隨意的表情，戶外場景，
背景是膠片和日式和風建築，日光，自然光，筆觸不規則，字跡潦草，隨意飛濺。
鏡頭畸變，景深，前景模糊。透明表面反射，玻璃上的光線反射。
有三排文字，第一排上面簽名寫著中文書法草體字「ｘｘｘ」，
第二排用西洋書法寫「ｘｘｘ」,第三排寫著電話號碼「ｘｘｘ」
```

### 進階 PROMPT 範例：賽車女郎雜誌風

Bot 產出的完整 PROMPT（用於角色卡 + 圖生圖）：

<details>
<summary>展開完整 PROMPT</summary>

```
使用已上傳的參考圖女生作為唯一人物身份參考，保留她的臉部辨識度、短黑髮、自然寫實氣質、
乾淨日系感與成熟神情。生成一張 16:9 橫幅、高級寫實、時尚機車雜誌主視覺。

主題方向：
MELTY BLOOD / ARCUEID inspired racing queen editorial。
注意：人物不是原作角色本人，而是參考圖女生穿著受 ARCUEID 氣質啟發的高級賽車女郎造型。
不要生成動漫角色介紹頁，不要放人物設定文字。

人物：
參考圖女生全身站在高科技賽車機車旁，姿態自信、冷靜、時尚。她手持大型賽車傘，
傘面可以有月亮、銀白弧線、紅黑圖形、抽象吉祥物圖案與少量賽車隊標誌感設計，
但不要寫人物介紹。服裝是精緻賽車女郎造型，白色、黑色、紅色為主，
加入銀色金屬材質、透明科技材質、細緻縫線、護甲感肩線與高級時裝剪裁。
整體性感但不低俗，不過度裸露，像高級時尚雜誌 editorial，不是廉價 cosplay。

機車：
一台鋼彈風格的未來機車賽車，重科技感，使用白、紅、黑、金屬灰作為主色，
車體有機械裝甲、發光能量線、空力翼片、低趴賽道姿態、未來懸吊、輪圈光環、
碳纖維細節與精密機械關節。車體外觀像高階概念賽車與機動裝甲融合，
但仍然是一台可騎乘的機車，不要變成人形機器人。

背景與構圖：
夜間未來賽道維修區或發車格，濕潤地面反射霓虹燈，遠方有賽道燈、維修艙、
能源充電塔與模糊觀眾席。電影級光影、超現實寫實攝影、照片寫實主義、
高級時尚雜誌編輯版面美學。人物與機車是主視圖，畫面要有全身人物與完整機車，
16:9 橫幅構圖，像雜誌跨頁封面。

雜誌排版：
畫面可以有少量個人生活照片風格的小圖拼貼，但主畫面仍以人物與機車為核心。
所有繁體中文字都只描述機車與車體規格，不要介紹動漫人物，不要寫角色背景，不要人物檔案欄。

請在畫面中加入少量清楚、設計感強的繁體中文機車文字，例如：
「月蝕賽道特仕」「白紅裝甲塗裝」「雙核心電驅系統」「可變空力翼片」
「低重心競技車架」「夜戰賽道版」「碳纖維整流罩」「高輸出輪轂馬達」

文字要像高級機車雜誌或概念車型錄排版，乾淨、少量、不要塞滿畫面。
可以有車體標註線、規格小框、主標題，但不要任何人物介紹文字。

風格關鍵詞：
high-end realistic fashion magazine editorial, cinematic lighting, hyper-realistic photography,
Japanese futuristic racing queen, luxury motorsport catalog, mecha motorcycle concept,
Gundam-inspired armored superbike, wet neon pit lane, polished editorial layout,
detailed fabric, detailed mechanical design, realistic skin, full body, 16:9.

避免：
不要動漫風、不要 Q 版、不要低解析、不要廉價 cosplay、不要過度裸露、
不要人物介紹文字、不要角色設定欄、不要把機車變成人形鋼彈、不要多個主角、
不要臉不像參考圖、不要錯亂手指、不要過多文字、不要英文大段落、不要把版面做成玩具包裝。
```

</details>

### 生成結果展示

![網站手動生圖結果 1](https://cdn.discordapp.com/attachments/1500132173959921914/1507067120553562133/image.png?ex=6a1135e1&is=6a0fe461&hm=b89ad575bba7a235fd6f0ea3cd5dc49b38eca1d48612adea65ae05f6e418cebb&)

![網站手動生圖結果 2](https://cdn.discordapp.com/attachments/1500132173959921914/1507067120863678684/image.png?ex=6a1135e1&is=6a0fe461&hm=6d5c7c2d2bac3ac1ae96ada2c01a4c0cbd1aa8c499fe5cb1f0250a436f741965&)

![網站手動生圖結果 3](https://cdn.discordapp.com/attachments/1500132173959921914/1507067121279045643/98282f70-e1a8-4b1b-b066-31cf5163cbf4.png?ex=6a1135e1&is=6a0fe461&hm=cc2fa8aba30cb336d1f5e56f5989aad32d59fa6739efc83b35b36aa11d77d7f6&)

![網站手動生圖結果 4](https://cdn.discordapp.com/attachments/1500132173959921914/1507067121618649289/image.png?ex=6a1135e1&is=6a0fe461&hm=c4ca1fba08b3ec473095ea22e23e7b3c92b399cb95c93e9a1cfcdd730adb18b6&)

![網站手動生圖結果 5](https://cdn.discordapp.com/attachments/1500132173959921914/1507067122008854730/image.png?ex=6a1135e1&is=6a0fe461&hm=1d25c45804774746bb7d3257f03162f54766ed143225b350e8d6c663c0c4d106&)

### 角色參考圖

![角色參考圖 hana](https://cdn.discordapp.com/attachments/1500132173959921914/1507339838435758108/hana-reference-image-2026-05-22-001.png?ex=6a118b1e&is=6a10399e&hm=cb9fa1d17f5566dc47b3fa0479ab140bb4586e8eeea0979b6e07bdf94272c35a&)

---

## 3. AI 影片 / MV 製作流程

art 分享了一套完整的 AI 影片製作 pipeline，使用分鏡表 + oiioii 平台。

### 整體流程

```
故事大綱 → 鳥巢分鏡圖 → GPT 建立分鏡 → 分鏡分析 → oiioii 建立角色/場景 → 影片產出
```

### Step 1：建立故事大綱（鳥巢 AI 智能剪切分鏡圖）

使用 Google AI Studio 的「鳥巢故事版」App 建立故事大綱：

🔗 [鳥巢AI智能剪切分鏡圖](https://aistudio.google.com/apps/0fca8094-1c0a-40a1-b4ea-797564be952e?fullscreenApplet=true&showPreview=true&showAssistant=true)

- 輸入你的故事概念
- 它會產出結構化的故事大綱
- **下載 JSON 檔案**

### Step 2：用 GPT 建立分鏡圖

將 JSON 丟到 ChatGPT，讓它幫你建立視覺化分鏡圖：

🔗 [GPT 分鏡圖對話範例](https://chatgpt.com/share/6a0f3db4-dc54-83a9-993b-e190b1a3a9d8)

![GPT 分鏡圖建立過程](https://cdn.discordapp.com/attachments/1500132173959921914/1507069307329188160/image.png?ex=6a1137ea&is=6a0fe66a&hm=cf52c8e69717ff162a8aa995c61813165b7ea6e9cb311b6fc11effc8842feeb8&)

產出結果會是一個 ZIP 包，包含所有分鏡資源。

### Step 3：分鏡圖分析

拿分鏡圖去做進一步分析（確認場景、角色、動作等細節）：

![分鏡分析工具](https://cdn.discordapp.com/attachments/1500132173959921914/1507070245695918191/image.png?ex=6a1138ca&is=6a0fe74a&hm=90d5d311c74c2dd8c8690644ed98bc6eaf519a174465df3da121e83c3cb32180&)

### Step 4：產出 Storyboard Bundle

完成分析後會自動下載一個 ZIP 包：

![Storyboard Bundle 下載](https://cdn.discordapp.com/attachments/1500132173959921914/1507070950900895764/image.png?ex=6a113972&is=6a0fe7f2&hm=1689aa54238b607e091ce6f234b78e34f634066b10f405b5c2151d53fbdb3727&)

### Step 5：進入 oiioii 平台

拿著 Storyboard Bundle 的資料，到 [oiioii](https://oiioii.com) 網站：
- 建立角色
- 建立場景
- 組合成影片

### 參考簡報

art 分享了一份完整的 AI 影音製作教學簡報（Keynote）：

🔗 [感謝祭 0509 — AI 影音製作](https://www.icloud.com/keynote/0b8E9euNgMuLsNTrtNIGH20CA#%E6%84%9F%E8%AC%9D%E7%A5%AD0509_AI%E5%BD%B1%E9%9F%B3%E8%A3%BD%E4%BD%9C)

> 裡面的 APP 作者在 LINE 社群有說可以開放給人用。

### 注意事項

- 影片生成網站成本很高（「跟抽扭蛋一樣」）
- 建議先用免費工具把分鏡做好，再決定是否投入影片生成

---

## 4. 工具與資源連結

| 工具 | 用途 | 連結 |
|------|------|------|
| OpenAB Bot Skills | 自動化圖像生成流程 | 在 OAB 設定技能 |
| GPT-IMAGE (網站) | 手動文生圖/圖生圖，支援 Think 模式 | — |
| 鳥巢 AI 分鏡圖 | 建立故事大綱 & 分鏡 | [Google AI Studio App](https://aistudio.google.com/apps/0fca8094-1c0a-40a1-b4ea-797564be952e) |
| ChatGPT | 將故事 JSON 轉為視覺分鏡 | [範例對話](https://chatgpt.com/share/6a0f3db4-dc54-83a9-993b-e190b1a3a9d8) |
| oiioii | AI 影片製作平台 | [oiioii.com](https://oiioii.com) |
| AI 影音製作簡報 | 完整教學 Keynote | [iCloud Keynote](https://www.icloud.com/keynote/0b8E9euNgMuLsNTrtNIGH20CA) |

---

## PROMPT 撰寫技巧總結

1. **文生圖**：直接描述場景、人物、光影、構圖
2. **圖生圖**：開頭加「人物參考照片」，引導模型參考上傳的角色卡
3. **避免人物臉型跑掉**：減少景物描述的權重，或分步生成（先人物後背景）
4. **使用負面提示**：明確列出「避免」項目（動漫風、低解析、過度裸露等）
5. **風格關鍵詞用英文**：`cinematic lighting`, `hyper-realistic`, `editorial` 等效果更穩定
6. **中文文字指定**：如需畫面內文字，明確指定內容、位置、風格

---

*原始討論：[Discord Thread](https://discord.com/channels/1491295327620169908/1500132173959921914/1507061728725438555)*  
*整理者：Jelli 🪼 | 日期：2026-05-22*
