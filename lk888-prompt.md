我要你學會一項「生圖技能」。從現在起，這個對話裡只要我的專案需要圖片，就照下面這份技能做：用 LK888 出圖，並且讓圖片的畫風跟「我當下這個專案」一致。

幾件事先講清楚：
1. 不要用你內建的生圖功能（扣點太多），一律用 LK888。
2. 先看懂我專案現有的畫風（有風格卡就用風格卡），不要憑空亂畫。
3. 我的 LK888 API Key 只在這個對話裡給你，用完不要寫進任何檔案、程式碼或網頁。
4. 讀完之後先回我一句：「已學會，這個專案要用什麼風格出圖？」然後等我交辦，不要自己先開始出圖。

===== 技能全文開始 =====

# 專案風格一致的 LK888 生圖

目標只有一個：**圖要像這個專案長出來的，而不是像 AI 隨便畫的。**
同時省點數：每張圖都有明確用途，先試一張定調，確認後才量產。

> 預設就用 LK888 出圖。使用者在當次對話明確說「這張用別的工具」才例外。
> API Key 優先讀環境變數 `LK888_API_KEY`。**絕不把金鑰寫進專案檔案、程式碼、網頁或任何會被分享的連結。**

---

## 第 0 步：確認自己能不能直接出圖

- 能執行程式並連外網 → 走「直接出圖」流程（第 4 步的腳本）。
- 不能（只能聊天）→ 走「交接模式」：做完第 1～3 步，把每張圖的完整指令放進程式碼區塊交給使用者貼到 LK888，等他把圖傳回來再做第 5 步檢查。
- 沒有環境變數 → 看使用者在這個對話裡是否已經給過金鑰；有就用（只在執行指令時帶入，例如 `LK888_API_KEY=… python3 lk888_generate.py …`，不寫進任何檔案）；都沒有就只問一次：「請給我 LK888 的 API Key」。同一個對話不要重複問。

## 第 1 步：讀懂專案，做出「風格卡」

**先找現成的，不要重新發明：**

1. 專案根目錄有 `.image-style.md` → 直接用它，跳到第 2 步。這是上次定下的風格，是最高優先。
2. 使用者這次的明確指示（例如「這次要寫實照片」）→ 覆蓋風格卡中衝突的項目，並更新風格卡。
3. 沒有風格卡就依序偵察，**由強到弱**：
   - **現有圖片**：圖片資料夾（`images/`、`assets/`、`public/`、`static/`）裡挑 2～3 張最有代表性的，實際打開來看。能看圖就一定要看，這比任何文字推測都準。
   - **設計語彙**：CSS 變數、Tailwind 設定、主題檔裡的主色／輔色／背景色／字體／圓角。
   - **內容調性**：首頁文案、README、品牌說明——是專業穩重、親切溫暖、科技感、還是活潑？目標讀者是誰？
   - **產業與用途**：餐飲、教育、B2B、活動報名，各自慣用的視覺語言不同。
4. 以上都沒有（全新專案）→ 只問使用者一句：「這個專案的圖，您想要偏向 ①寫實照片 ②扁平插畫 ③手繪溫暖 ④其他？」使用者說隨便，就依產業與讀者自己選一個，並在第 3 步用試圖確認。

**風格卡格式**（存成專案根目錄 `.image-style.md`，之後每次都讀它，專案畫風變了才更新）：

```
# 圖片風格卡
- 媒材：（寫實攝影／扁平向量插畫／水彩手繪／3D 渲染／等距插畫…）
- 色彩：主色 #xxxxxx、輔色 #xxxxxx、背景 #xxxxxx；整體色溫（暖／冷／中性）、飽和度（高／中／低）
- 光線與質感：（柔光／自然光／無陰影平塗／細膩顆粒…）
- 線條與造型：（無描邊／細線描邊／圓潤／銳利…）
- 人物規則：（不出現人／只出現背影／亞洲臉孔／插畫風簡化人物…）
- 構圖習慣：（留白多寡、主體置中或偏一側、是否需預留放文字的區域）
- 禁止項：（例如不要文字、不要商標、不要霓虹、不要卡通風…）
- 風格咒語（英文，一段，每張圖原封不動貼上）：
  "…"
- 參考檔：（2～3 張現有代表圖的路徑）
```

「風格咒語」是一致性的關鍵：**它寫一次，之後每張圖的指令都逐字重複使用，只換主體。** 把它寫得具體——媒材、色碼、光線、質感、構圖，缺一項就會漂移。

## 第 2 步：列出圖片清單（先算清楚再花錢）

用表格列出每一張：編號｜放在哪裡（首頁主圖、第二區塊插圖…）｜主體內容｜尺寸。

- 沒有明確用途的圖不出。能用 CSS 漸層、純色或既有圖解決的就不出圖。
- 尺寸對應（用途 → size）：橫幅／主視覺用 `1536x1024`，直式用 `1024x1536`，正方形用 `1024x1024`。**目前實測可靠的是 `1024x1024`**；其他尺寸若 API 回錯，退回 `1024x1024` 後用裁切處理，不要重試多次。
- 圖片內需要文字（標題、標語）→ 不要讓 AI 畫字，改在網頁或簡報上用文字疊上去；否則中文常出現亂碼。
- 張數超過 5 張，先告訴使用者「共 N 張，約 N 點」，等他說可以才量產。

## 第 3 步：先試一張定調

挑清單裡最能代表專案畫風的一張，**只出這一張**，給使用者確認方向。確認後才量產其餘的。使用者說「方向不對」，改風格卡再試，不要在原指令上小修小補一直重抽。

**每一張圖的指令結構（英文，內容完全自足）：**

```
[主體] 這張圖要畫什麼：誰／什麼東西、在做什麼、在什麼場景，具體到能想像畫面。
[構圖] 視角、主體位置、留白區域（例如 "leave the left 40% empty for a headline"）。
[風格] 風格卡的「風格咒語」，逐字貼上。
[限制] 風格卡的「禁止項」。結尾固定加：
       No text, no letters, no numbers, no labels, no watermark, no logo.
```

寫指令的原則：
- 要數量或比例就用數字（"three people", "the building is about ten times the height of a person"），不要寫「很多」「更大」。
- 色彩一律用色碼（"dominant color #1F6FEB"），不要只寫「藍色」。
- 使用者已確認的部分，寫成 `LOCKED — DO NOT CHANGE`，重畫時只改其他項目。
- 參考圖：目前只驗證過文字生圖端點。**不要假設 LK888 吃參考圖**；參考圖的作用是讓「你」看了之後把風格寫進咒語，而不是上傳給生圖。

## 第 4 步：出圖（直接出圖模式）

先設好金鑰（使用者做一次即可）：`export LK888_API_KEY=sk-...`

把下面存成 `lk888_generate.py`（放在專案之外或 `tools/`，不要放進公開網站目錄）：

```python
#!/usr/bin/env python3
"""LK888 單張出圖。已存在的檔案預設跳過，避免重複扣點。
用法: python3 lk888_generate.py --prompt-file p.txt --out images/hero.png [--size 1024x1024] [--dry-run] [--force]
"""
import argparse, base64, json, os, sys, urllib.request, urllib.error
from pathlib import Path

API = "https://api.lk888.ai/v1/images/generations"

# 大樹 Mac 工作區有收費閘門（重複請求免費回傳、限速、換備援金鑰），有就走它
_open = urllib.request.urlopen
_scripts = Path.home() / ".openclaw" / "workspace" / "scripts"
if _scripts.exists():
    sys.path.insert(0, str(_scripts))
    try:
        import lk888_guard
        _open = lk888_guard.urlopen
    except ImportError:
        pass

def main():
    ap = argparse.ArgumentParser()
    ap.add_argument("--prompt-file", required=True)
    ap.add_argument("--out", required=True)
    ap.add_argument("--size", default="1024x1024")
    ap.add_argument("--dry-run", action="store_true")
    ap.add_argument("--force", action="store_true")
    a = ap.parse_args()

    out = Path(a.out)
    prompt = Path(a.prompt_file).read_text(encoding="utf-8").strip()
    if out.exists() and not a.force:
        print(f"SKIP 已存在（不扣點）: {out}"); return
    if a.dry_run:
        print(f"DRY-RUN 不會呼叫 API\n size={a.size} out={out}\n prompt({len(prompt)}字):\n{prompt}"); return

    key = os.environ.get("LK888_API_KEY")
    if not key:
        sys.exit("缺少環境變數 LK888_API_KEY")
    body = json.dumps({"model": "gpt-image-2", "prompt": prompt, "size": a.size}).encode()
    req = urllib.request.Request(API, data=body, headers={
        "Authorization": f"Bearer {key}", "Content-Type": "application/json"})
    try:
        with _open(req, timeout=180) as r:
            data = json.loads(r.read())
    except urllib.error.HTTPError as e:
        sys.exit(f"LK888 回錯 {e.code}: {e.read().decode(errors='replace')[:300]}")
    item = data["data"][0]
    if item.get("b64_json"):
        img = base64.b64decode(item["b64_json"])
    else:
        img = urllib.request.urlopen(item["url"], timeout=60).read()
    out.parent.mkdir(parents=True, exist_ok=True)
    out.write_bytes(img)
    print(f"OK {out} ({len(img)//1024} KB)")

if __name__ == "__main__":
    main()
```

**執行紀律（省點的核心）：**
- 一張圖**只呼叫一次**。失敗（逾時、回錯）先看錯誤原因，不要原指令連打。
- 被問「再來一張」或「重畫」才重畫，並加 `--force`；同一張最多重畫 2 次，第 3 次起先停下來問使用者要不要改風格卡。
- 不要自己寫迴圈對同一個指令連續出圖。要量產就一張一張明確列出、各自一個 `--out` 檔名。
- 出圖前永遠先用 `--dry-run` 確認指令與檔名無誤。

## 第 5 步：收到圖後親眼檢查

打開每一張圖，對照風格卡逐項檢查：

1. **風格一致**：與專案現有圖片並排比，媒材、色調、光線、線條像不像同一套。
2. **內容正確**：主體、數量、構圖、留白位置符合指令。
3. **瑕疵**：手指、臉、物件變形；畫面裡不該有的文字或亂碼；邊緣被裁掉。
4. **可用性**：預留放文字的區域夠不夠、文字疊上去看不看得清楚。

回報分兩段：**做到的**、**沒做到的**（每點寫具體差異，例如「左側本應留白，但被一棵樹佔滿」）。有重要項目沒做到就先不用，附上修正後的指令；使用者說某部分已定案，下一版就把它寫成 LOCKED。

## 第 6 步：放進專案

- 用新檔名存（例如 `hero-v1.png`），不覆蓋已交付的檔案。
- 網頁用：轉成 WebP 或壓成 JPG，目標單張 300 KB 以下；寫 `alt` 文字；設 `width`／`height` 避免版面跳動。
- 放進頁面後，用手機寬度與桌機寬度各看一次，確認裁切與文字可讀。
- 簡單問題（裁切、補邊）直接處理並說明做了什麼，不要為了小修再花一點出圖。
- 把這次新增的圖與使用的指令摘要補進 `.image-style.md` 的「參考檔」，下次風格就接得上。

## 一句話總結

先看專案長什麼樣 → 寫成風格卡 → 試一張 → 確認後逐張量產 → 每張圖只付一次錢。

===== 技能全文結束 =====
