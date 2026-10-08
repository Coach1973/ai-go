我要你學會一項「簡報套底圖技能」。從現在起，只要我給你一份簡報（例如 NotebookLM 匯出的 PDF，或一堆圖片）和一張底圖，就照下面這份技能做：把簡報每一頁套進底圖，輸出 .pptx。底圖可以是任何品牌或版型（BNI、公司版型、有邊框、有頂部橫幅、有側邊欄…），不用我手動量座標，程式會自己判斷。

幾件事先講清楚：
1. 全部在本機用程式合成，不要用 AI 生圖，不要花任何點數。
2. 底圖上的 LOGO、邊框、色塊一定要完整保留，內容不能被遮、不能變形。
3. 交付前一定要自己打開總覽圖看過，沒看過不准說完成。
4. 讀完之後先回我一句：「已學會，請給我簡報檔和底圖。」然後等我交辦，不要自己先開始做。

===== 技能全文開始 =====

# 簡報套底圖（任何底圖、任何 AI 都能做）

目標：拿到一份只有內容插圖的簡報（例如 NotebookLM 產出的 PDF），套進使用者指定的底圖，
做成可以直接簡報的 `.pptx`——**品牌 LOGO、邊框、色塊都完整保留，內容不被遮、不變形。**

> 本技能不需要任何 AI 生圖，全部在本機用程式合成，不扣任何點數。
> 底圖不一定是 BNI：換任何一張底圖，流程都一樣，程式自己判斷怎麼擺。

## 先講清楚一個限制
成品每一頁是「一張完整的圖」（內容＋底圖合成），**不是可編輯的文字方塊**。
NotebookLM 的 PDF 本來每頁就是一張圖，所以這是正常的。
如果使用者要在 PowerPoint 裡改字，要先告訴他：這個做法改不了字，要改字得回到來源重新產生。

## 第 0 步：環境（第一次才需要）

```bash
pip install pillow python-pptx pymupdf
```
`pymupdf` 用來讀 PDF。裝不了的話改用 poppler（Mac：`brew install poppler`；Windows／Linux 用套件管理員裝 poppler-utils）。兩者有一個就行。

把下面「腳本全文」存成 `brand_pptx.py`（存在專案之外，例如 `~/tools/`）。

## 第 1 步：確認三件事（已經講清楚的不要重問）

1. **來源**：PDF 路徑，或放圖片的資料夾（依檔名自然排序，page2 在 page10 前面）。
2. **底圖**：一張 jpg／png。要確認它是「空白的底圖」（只有 LOGO、邊框、裝飾，沒有文字內容）。
   - 底圖是 `.pptx`／`.potx` 範本 → 先在 PowerPoint／Keynote 把空白頁匯出成一張圖片當底圖。（此步驟未實測）
3. **特殊條件**（沒有就不問，用預設）：
   - 某個位置絕對不能被蓋住 → `--protect-box`
   - 封面或封底要用不同底圖 → `--bg-first`／`--bg-last`
   - 內容要留邊距、或要填滿裁切 → `--margin`／`--fill cover`

## 第 2 步：先分析底圖（不產生檔案，幾秒鐘）

```bash
python3 brand_pptx.py --src 簡報.pdf --bg 底圖.jpg --dry-run
```

它會回報選了哪種擺法，以及品牌元素約佔畫面多少：

| 模式 | 什麼時候自動選它 | 效果 |
|---|---|---|
| `mask` | 品牌元素小，只在角落或邊緣（例如左下 LOGO、右下裝飾） | 內容**滿版鋪滿**，只避開品牌元素，邊緣柔化 |
| `safe` | 有整條橫幅、側邊欄、邊框，或品牌元素超過三成 | 內容**縮放放進最大的乾淨區域**，不被橫幅／邊框蓋住 |
| `overlay` | 底圖是帶透明的 PNG | 底圖直接蓋在內容上面 |

不滿意自動選的，用 `--mode mask|safe|overlay` 強制指定。

🔴 **BNI 底圖（BNI ACCELERATE）一律滿版**：必須加 `--mode mask`，不准用預設的 auto，**禁止 `safe`、禁止 `--fill contain`**。`safe` 會把內容縮成中間一塊、四周留白，不是 BNI 要的滿版。（教練 2026-10-08 糾正：聯想照舊指令跑成縮小。）

## 第 3 步：跑

一般底圖：

```bash
python3 brand_pptx.py --src 簡報.pdf --bg 底圖.jpg --out 成品.pptx
```

BNI 底圖（必須用這一行）：

```bash
python3 brand_pptx.py --src 簡報.pdf --bg BNI_ACCELERATE_底圖.jpg --out 簡報_BNI版.pptx --mode mask
```

會在成品旁邊建立 `成品_work/` 資料夾，裡面有每頁合成圖和 `contact.png`（所有頁縮圖總覽）。

## 第 4 步：親眼檢查（沒看過不准交付）

打開 `成品_work/contact.png`，逐項確認：
1. 底圖的 LOGO、邊框、色塊完整，沒被內容蓋到、沒出現破碎的邊緣。
2. 內容沒有被切掉重要的字（`mask` 滿版模式會裁掉與底圖比例不同的邊緣）。
3. 內容與底圖交界處自然，沒有難看的硬邊或白框。
4. 每一頁都一致；封面、封底若換了底圖，確認對了。
5. 再用 PowerPoint／Keynote 實際打開 `.pptx`，抽查一兩頁。

## 第 5 步：不滿意時，照症狀調整

| 症狀 | 怎麼調 |
|---|---|
| 內容蓋到 LOGO 邊緣、有殘影 | `--grow 0.02`（往外多保護一圈）或 `--feather 40` |
| 底圖上的淡色浮水印／陰影被誤當成品牌元素，內容被擠小 | `--tol 70`（提高門檻，淡色不算） |
| 淡色的 LOGO 沒被偵測到、被蓋掉 | `--tol 20`，或用 `--protect-box x0,y0,x1,y1` 直接圈起來（比例 0~1） |
| `safe` 模式內容放得太小（例如乾淨區被一個小 LOGO 切到） | 用 `--safe-box x0,y0,x1,y1` 手動指定內容區，或改 `--mode mask` |
| `mask` 模式重要內容被裁掉 | 一般底圖可改 `--mode safe`，或 `--fill cover` 搭配 `--safe-box`。**BNI 底圖不准改 safe**，接受邊緣裁切，回報教練由他決定 |
| 文字不夠清晰 | `--dpi 200` |
| 底圖與簡報比例差很多 | 不用處理：投影片尺寸會自動跟底圖比例一致 |

調整後只要重跑一次第 3 步。每次改完都要重看 `contact.png`。

## 第 6 步：交付

- 成品 `.pptx` 的完整路徑。
- 一句話說明用了哪種模式、有沒有特殊處理。
- 中間檔 `成品_work/` 保留，使用者確認沒問題後可自行刪除（**不要代替使用者刪**）。

## 範例：BNI ACCELERATE 底圖

BNI 底圖左下是 LOGO、右下是紅色弧形裝飾，屬於「小面積品牌元素在角落」，
**必須用 `--mode mask`**：內容滿版鋪滿整頁，只避開左下 LOGO 與右下紅色弧形。

```bash
python3 brand_pptx.py --src 簡報.pdf --bg BNI_ACCELERATE_底圖.jpg --out 簡報_BNI版.pptx --mode mask
```

驗收：每頁插圖貼齊四邊、沒有白邊或縮小；左下 BNI ACCELERATE LOGO 與右下紅色弧形都還在。看到白邊代表模式錯了，重跑加 `--mode mask`，不要手動調 `--safe-box`。

---

## 腳本全文：brand_pptx.py

```python
#!/usr/bin/env python3
"""把「每頁一張圖」的簡報（NotebookLM 匯出的 PDF、或一資料夾圖片）套進任意底圖，輸出 .pptx。

底圖不限品牌：程式會自己看底圖，判斷哪些地方是 LOGO／邊框／色塊（不能蓋），
再選一種擺法：
  mask    內容滿版鋪滿，只避開底圖上的品牌元素（適合：角落有 LOGO／裝飾）
  safe    內容縮放放進底圖「最大的乾淨區域」（適合：有邊框、頂部橫幅、側邊欄）
  overlay 底圖是帶透明的 PNG，直接蓋在內容上面
  auto    （預設）依底圖自動挑上面三種之一

用法：python3 brand_pptx.py --src 簡報.pdf --bg 底圖.jpg --out 成品.pptx
需要：pip install pillow python-pptx，另外 PDF 要有 PyMuPDF（pip install pymupdf）或 poppler（pdftoppm）其一。
"""
import argparse
import re
import shutil
import subprocess
import sys
from pathlib import Path

from PIL import Image, ImageChops, ImageDraw, ImageFilter
from pptx import Presentation
from pptx.util import Emu

IMG_EXT = {".png", ".jpg", ".jpeg", ".webp", ".bmp"}
LOW = 160  # 偵測用縮圖寬度（越小越快，160 對 LOGO／邊框夠用）
EMU_IN = 914400


def natural(p):
    return [int(t) if t.isdigit() else t.lower() for t in re.split(r"(\d+)", p.name)]


def load_pages(src, work, dpi):
    src = Path(src)
    if src.is_dir():
        return sorted((p for p in src.iterdir() if p.suffix.lower() in IMG_EXT), key=natural)
    if src.suffix.lower() in IMG_EXT:
        return [src]
    if src.suffix.lower() != ".pdf":
        sys.exit(f"不支援的來源：{src}（只收 PDF、圖片、或放圖片的資料夾）")
    out = work / "pages"
    out.mkdir(parents=True, exist_ok=True)
    try:
        import pymupdf as fitz  # PyMuPDF（新名稱）
    except ImportError:
        try:
            import fitz  # PyMuPDF（舊名稱）
        except ImportError:
            fitz = None
    if fitz:
        for i, page in enumerate(fitz.open(str(src)), 1):
            page.get_pixmap(dpi=dpi).save(str(out / f"page-{i:03d}.png"))
    elif shutil.which("pdftoppm"):
        subprocess.run(["pdftoppm", "-png", "-r", str(dpi), str(src), str(out / "page")], check=True)
    else:
        sys.exit("讀 PDF 需要 PyMuPDF（pip install pymupdf）或 poppler（Mac: brew install poppler）")
    return sorted(out.glob("page-*.png"), key=natural)


# ---------- 看懂底圖 ----------
def lowres(img, w=LOW):
    return img.resize((w, max(1, round(img.height * w / img.width))), Image.BILINEAR)


def base_color(small):
    q = small.convert("RGB").quantize(colors=8)
    n, idx = max(q.getcolors())
    return tuple(q.getpalette()[idx * 3: idx * 3 + 3])


def brand_low(bg, tol, grow, boxes):
    """回傳低解析度的「品牌元素」遮罩（255＝不能蓋）與涵蓋比例。"""
    small = lowres(bg.convert("RGB"))
    diff = ImageChops.difference(small, Image.new("RGB", small.size, base_color(small)))
    r, g, b = diff.split()
    m = ImageChops.lighter(ImageChops.lighter(r, g), b).point(lambda v: 255 if v > tol else 0)
    m = m.filter(ImageFilter.MedianFilter(3))  # 去掉雜點
    d = ImageDraw.Draw(m)
    for x0, y0, x1, y1 in boxes:  # 使用者額外指定的保護區
        d.rectangle([x0 * m.width, y0 * m.height, x1 * m.width, y1 * m.height], fill=255)
    size = 2 * max(1, round(LOW * grow)) + 1
    m = m.filter(ImageFilter.MaxFilter(size))  # 往外多保護一圈
    cover = m.histogram()[255] / (m.width * m.height)
    return m, cover


def has_band(m):
    """有整條橫幅／側邊欄／邊框（某一整列或整行幾乎都是品牌元素）→ 內容不能滿版鋪，要縮進乾淨區。"""
    w, h = m.size
    px = m.load()
    rows = any(sum(1 for x in range(w) if px[x, y]) / w > 0.8 for y in range(h))
    cols = any(sum(1 for y in range(h) if px[x, y]) / h > 0.8 for x in range(w))
    return rows or cols


def largest_clear_rect(m):
    """低解析度遮罩上，找最大的「全是 0」矩形，回傳 (x0,y0,x1,y1) 比例。"""
    w, h = m.size
    px = m.load()
    heights = [0] * w
    best = (0, 0, 0, w, h)
    for y in range(h):
        for x in range(w):
            heights[x] = 0 if px[x, y] else heights[x] + 1
        stack = []
        for x in range(w + 1):
            cur = heights[x] if x < w else 0
            start = x
            while stack and stack[-1][1] >= cur:
                s, hh = stack.pop()
                area = hh * (x - s)
                if area > best[0]:
                    best = (area, s, y - hh + 1, x, y + 1)
                start = s
            stack.append((start, cur))
    _, x0, y0, x1, y1 = best
    return x0 / w, y0 / h, x1 / w, y1 / h


def analyse(bg_path, args):
    bg = Image.open(bg_path)
    has_alpha = bg.mode in ("RGBA", "LA") and bg.getchannel("A").getextrema()[0] < 250
    info = {"bg": bg.convert("RGBA") if has_alpha else bg.convert("RGB")}
    if has_alpha and args.mode in ("auto", "overlay"):
        info.update(mode="overlay", note="底圖有透明區，蓋在內容上面")
        return info
    boxes = [tuple(float(v) for v in b.split(",")) for b in args.protect_box]
    m, cover = brand_low(bg.convert("RGB"), args.tol, args.grow, boxes)
    mode = args.mode
    if mode == "auto":
        mode = "safe" if (has_band(m) or cover > 0.35) else "mask"
    info.update(mode=mode, low=m, cover=cover)
    if mode == "safe":
        rect = tuple(float(v) for v in args.safe_box.split(",")) if args.safe_box else largest_clear_rect(m)
        info["rect"] = rect
    info["note"] = f"品牌元素約佔 {cover:.0%}" + (f"，內容放進 {tuple(round(v, 3) for v in info['rect'])}" if mode == "safe" else "")
    return info


# ---------- 合成 ----------
def cover_fit(img, W, H):
    s = max(W / img.width, H / img.height)
    im = img.resize((round(img.width * s), round(img.height * s)), Image.LANCZOS)
    l, t = (im.width - W) // 2, (im.height - H) // 2
    return im.crop((l, t, l + W, t + H))


def compose(page, info, args):
    bg = info["bg"]
    W, H = bg.size
    ill = Image.open(page).convert("RGB")
    mode = info["mode"]
    if mode == "overlay":
        base = cover_fit(ill, W, H).convert("RGBA")
        base.alpha_composite(bg)
        return base.convert("RGB")
    if mode == "mask":
        feather = args.feather if args.feather is not None else W * 0.012
        keep = info["low"].resize((W, H), Image.BILINEAR).filter(ImageFilter.GaussianBlur(feather))
        return Image.composite(bg.convert("RGB"), cover_fit(ill, W, H), keep)
    # safe：縮放放進最大乾淨區
    x0, y0, x1, y1 = info["rect"]
    mx, my = args.margin * W, args.margin * H
    box = (round(x0 * W + mx), round(y0 * H + my), round(x1 * W - mx), round(y1 * H - my))
    bw, bh = box[2] - box[0], box[3] - box[1]
    out = bg.convert("RGB").copy()
    if args.fill == "cover":
        out.paste(cover_fit(ill, bw, bh), box[:2])
    else:
        s = min(bw / ill.width, bh / ill.height)
        im = ill.resize((round(ill.width * s), round(ill.height * s)), Image.LANCZOS)
        out.paste(im, (box[0] + (bw - im.width) // 2, box[1] + (bh - im.height) // 2))
    return out


def contact_sheet(paths, out, cols=4, tw=480):
    th = round(tw * Image.open(paths[0]).height / Image.open(paths[0]).width)
    rows = (len(paths) + cols - 1) // cols
    sheet = Image.new("RGB", (cols * (tw + 8) + 8, rows * (th + 8) + 8), (90, 90, 90))
    for i, p in enumerate(paths):
        sheet.paste(Image.open(p).convert("RGB").resize((tw, th), Image.LANCZOS), (8 + (i % cols) * (tw + 8), 8 + (i // cols) * (th + 8)))
    sheet.save(out)


def main():
    ap = argparse.ArgumentParser(description="簡報圖片套用任意底圖 → pptx")
    ap.add_argument("--src", required=True, help="PDF／單張圖／放圖片的資料夾")
    ap.add_argument("--bg", required=True, help="底圖（jpg/png；帶透明的 png 會蓋在內容上面）")
    ap.add_argument("--out", help="輸出 .pptx（--dry-run 時可省略）")
    ap.add_argument("--mode", default="auto", choices=["auto", "mask", "safe", "overlay"])
    ap.add_argument("--bg-first", help="第 1 頁（封面）改用這張底圖")
    ap.add_argument("--bg-last", help="最後一頁（封底）改用這張底圖")
    ap.add_argument("--protect-box", action="append", default=[], help="額外不能蓋的區域 x0,y0,x1,y1（比例0~1），可重複")
    ap.add_argument("--safe-box", help="safe 模式手動指定內容區 x0,y0,x1,y1（比例0~1）")
    ap.add_argument("--margin", type=float, default=0.01, help="safe 模式內縮邊距（比例）")
    ap.add_argument("--fill", default="contain", choices=["contain", "cover"], help="safe 模式：完整放入／填滿裁切")
    ap.add_argument("--tol", type=int, default=40, help="與底色差多少算品牌元素（0~255）")
    ap.add_argument("--grow", type=float, default=0.012, help="品牌元素外多保護一圈（占寬度比例）")
    ap.add_argument("--feather", type=float, help="mask 邊緣羽化像素，預設為寬度的百分之一點二")
    ap.add_argument("--dpi", type=int, default=150)
    ap.add_argument("--dry-run", action="store_true", help="只分析底圖、不產生檔案")
    a = ap.parse_args()

    if not a.out and not a.dry_run:
        sys.exit("缺少 --out")
    out = Path(a.out) if a.out else Path("unused.pptx")
    work = out.parent / f"{out.stem}_work"
    infos = {}

    def info_for(path):
        if path not in infos:
            infos[path] = analyse(path, a)
        return infos[path]

    main_info = info_for(a.bg)
    print(f"[底圖] 模式={main_info['mode']}；{main_info['note']}；尺寸 {main_info['bg'].size}")
    if a.dry_run:
        return
    pages = load_pages(a.src, work, a.dpi)
    if not pages:
        sys.exit("來源裡沒有找到任何頁面")
    print(f"[來源] 共 {len(pages)} 頁")
    (work / "slides").mkdir(parents=True, exist_ok=True)
    slides = []
    for i, p in enumerate(pages, 1):
        path = a.bg_first if (i == 1 and a.bg_first) else a.bg_last if (i == len(pages) and a.bg_last) else a.bg
        img = compose(p, info_for(path), a)
        f = work / "slides" / f"slide-{i:02d}.jpg"
        img.save(f, quality=95, subsampling=0)
        slides.append(f)
    W, H = main_info["bg"].size
    prs = Presentation()
    prs.slide_height = Emu(int(7.5 * EMU_IN))
    prs.slide_width = Emu(int(7.5 * EMU_IN * W / H))
    for f in slides:
        s = prs.slides.add_slide(prs.slide_layouts[6])
        s.shapes.add_picture(str(f), 0, 0, width=prs.slide_width, height=prs.slide_height)
    prs.save(str(out))
    contact_sheet(slides, work / "contact.png")
    print(f"[完成] {out}（{len(slides)} 張）\n[總覽圖] {work / 'contact.png'}  ← 交付前一定要打開看過")


if __name__ == "__main__":
    main()
```

===== 技能全文結束 =====
