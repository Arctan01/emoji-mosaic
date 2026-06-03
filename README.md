# Emoji Mosaic

*[English](#english) · [中文](#中文)*

A single-file, in-browser tool that turns any image into emoji art by **color matching** — every cell becomes the emoji whose measured average color sits closest to that patch of the image, in perceptual Lab space. It also includes a **moon-phase relief** mode that sculpts images out of 🌑🌒🌓🌔🌕🌖🌗🌘.

No build step, no dependencies, no server. Open `emoji-mosaic.html` in a browser and go.

---

## English

### Quick start

1. Open `emoji-mosaic.html` in any modern browser (Chrome, Safari, Firefox, Edge).
2. Drag an image onto the drop zone, or click to choose a file.
3. Adjust the controls. The output re-renders live.
4. Export with **↓ jpeg**, **↓ svg**, **↓ Text .txt**, or **⧉ Copy text**. Reload a saved grid with **↑ Import text**.

A small sample image renders on load, so you can see the engine working before uploading anything.

### How it works

The core idea is that the emoji palette is **measured, not hard-coded**:

1. On load, each candidate emoji is drawn to an offscreen canvas using your system's emoji font.
2. Its **alpha-weighted average color** is computed over opaque pixels only (so transparent corners don't drag the color toward the background), then converted to **CIELAB**.
3. The uploaded image is downscaled to a `cols × rows` grid so each cell becomes one area-averaged color.
4. Each cell is matched to the nearest palette entry by **Lab distance**, and the emoji is painted into an output canvas.

Because colors are measured on your device, the matching stays correct whether your system renders emoji via Apple Color Emoji, Noto, Twemoji, or Segoe. The sidebar swatches show the actual measured color of each emoji — that's the palette the matcher uses.

If on-device measurement fails for any glyph (some browsers can't read color-emoji bitmaps back from canvas), the tool falls back to a built-in approximate color so the palette is never empty.

### Controls

| Control | What it does |
|---|---|
| **Mode** | `Color match` (nearest-color mosaic) or `Moon relief` (luminance + light-direction emboss). |
| **Resolution** | Number of columns, 16–512. Rows are derived from the image aspect ratio. |
| **Palette** | Emoji set used for color matching (see below). Disabled in Moon relief mode. |
| **Backing color** | Fill behind the glyphs. Matters most with non-square emoji, whose transparent corners let it show through. |
| **↓ jpeg** | Download the rendered mosaic as a jpeg. |
| **↓ svg** | Download the rendered mosaic as a svg. |
| **↓ Text .txt** | Download the emoji grid as a UTF-8 `.txt` file. |
| **⧉ Copy text** | Copy the emoji grid to the clipboard. |
| **↑ Import text** | Load a saved `.txt` emoji grid and render it to the canvas. |

#### Palettes

- **Squares** — `🟥🟧🟨🟩🟦🟪🟫⬛⬜`. Fully fill each cell, giving the cleanest solid mosaic. Only ~9 colors, so output is intentionally posterized.
- **Round** — circle equivalents; similar gamut, leaves background visible in the corners.
- **Spectrum+** — squares plus assorted colored objects for a wider range.
- **Max** — ~60 emoji spanning the broadest gamut, including the mid-grays and off-whites the small sets miss. A dedupe pass merges any emoji that measure to near-identical colors, keeping the palette efficient.
- **Hearts** — `❤️🧡💛💚💙💜🤎🖤🤍🩷🩵` for a themed look.

#### Moon relief mode

Ignores color entirely and builds the image from the eight moon phases:

- **Brightness** picks the phase along the dark→light axis (🌑 → 🌕).
- The **horizontal light gradient** within the image decides waxing vs waning: cells that are brighter toward the right use right-lit phases (🌒🌓🌔), brighter toward the left use left-lit phases (🌘🌗🌖).

Encoding light direction this way gives edges a lit-from-the-side, sculpted feel rather than flat shading. Works best on images with strong directional lighting (portraits, side-lit scenes) on a dark backing.

### Text import / export

The emoji grid round-trips as plain text. Export with **↓ Text .txt** (or copy to clipboard), and later reload it with **↑ Import text** to view or re-export . Import uses `Intl.Segmenter` (grapheme granularity) so multi-codepoint emoji — ZWJ sequences and variation-selector glyphs like ❤️ — stay intact as single cells instead of being split apart. Importing clears the current image, so slider and color tweaks won't overwrite the imported view; changing the backing color re-renders the imported grid against it.

### Performance

Rendering the slider is **debounced**: while you drag the resolution control the column count updates live, but the full mosaic rebuild waits until you pause (~25ms). This keeps high resolutions (up to 512 columns) smooth instead of recomputing on every tick. The same debounce applies to the backing-color picker.

### Notes & limitations

- **Emoji gamut is narrow.** Even the Max palette can't reach deeply desaturated or muted tones — emoji cluster around saturated primaries, mid-grays, and a few browns. Photos with subtle palettes will flatten.
- **Text alignment** in other apps depends on their emoji width handling.
- Everything runs client-side. Images never leave your machine.

### Possible next steps

- **Dithering** (e.g. Floyd–Steinberg) to make the limited palette mix optically and fake in-between colors — the single biggest quality gain available for color mode.
- **Light-direction control** for relief mode, to rotate the "sun" to any angle (including vertical gradients) instead of just left/right.(√)


### File

- `emoji-mosaic.html` — the entire application (HTML + CSS + JS in one file).

---

## 中文

一个单文件、纯浏览器运行的工具，通过**颜色匹配**把任意图片转换成 emoji 字符画——每个格子都会替换成在感知均匀的 Lab 色彩空间中，平均颜色与该图块最接近的 emoji。此外还提供**月相浮雕**模式，用 🌑🌒🌓🌔🌕🌖🌗🌘 来"雕刻"图像。

无需构建、无依赖、无服务器。用浏览器打开 `emoji-mosaic.html` 即可使用。

### 快速开始

1. 用任意现代浏览器（Chrome、Safari、Firefox、Edge）打开 `emoji-mosaic.html`。
2. 把图片拖到拖放区，或点击选择文件。
3. 调整控件，输出会实时重绘。
4. 用 **↓ jpeg**、**↓ svg**、 **↓ Text .txt** 或 **⧉ Copy text** 导出；用 **↑ Import text** 重新载入已保存的网格。

页面加载时会先渲染一张示例图，方便在上传前就看到引擎的效果。

### 工作原理

核心思路是：emoji 调色板是**实测得到的，而非写死的**：

1. 加载时，用你系统的 emoji 字体把每个候选 emoji 绘制到离屏画布上。
2. 只在不透明像素上计算其 **按透明度加权的平均颜色**（这样透明的边角不会把颜色拉向背景色），再转换到 **CIELAB**。
3. 上传的图片被缩小到 `列 × 行` 的网格，每个格子即为一块区域的平均颜色。
4. 每个格子按 **Lab 距离** 匹配到最接近的调色板条目，并把对应 emoji 绘制到输出画布上。

由于颜色是在你的设备上实测的，无论系统用 Apple Color Emoji、Noto、Twemoji 还是 Segoe 渲染，匹配都保持准确。侧栏的色块显示每个 emoji 的实测颜色——这正是匹配器使用的调色板。

如果某个字形的实测失败（部分浏览器无法从画布读回彩色 emoji 的位图），工具会回退到内置的近似颜色，确保调色板永远不为空。

### 控件

| 控件 | 作用 |
|---|---|
| **Mode（模式）** | `Color match`（最近颜色马赛克）或 `Moon relief`（亮度 + 光照方向浮雕）。 |
| **Resolution（分辨率）** | 列数，范围 16–512。行数由图片宽高比推算。 |
| **Palette（调色板）** | 颜色匹配所用的 emoji 集合（见下）。月相浮雕模式下禁用。 |
| **Backing color（背景色）** | 字形背后的填充色。对非方形 emoji 影响最大，因为透明边角会透出该色。 |
| **↓ jpeg** | 把渲染好的马赛克下载为 jpeg。 |
| **↓ svg** | 把渲染好的马赛克下载为 svg。 |
| **↓ Text .txt** | 把 emoji 网格下载为 UTF-8 编码的 `.txt` 文件。 |
| **⧉ Copy text** | 把 emoji 网格复制到剪贴板。 |
| **↑ Import text** | 载入已保存的 `.txt` emoji 网格并渲染到画布。 |

#### 调色板

- **Squares（方块）**——`🟥🟧🟨🟩🟦🟪🟫⬛⬜`。完整填满每个格子，呈现最干净的实色马赛克。只有约 9 种颜色，因此输出有意呈现"色阶断裂"的海报感。
- **Round（圆形）**——对应的圆形 emoji；色域相近，边角会露出背景。
- **Spectrum+（光谱+）**——方块加上各种彩色物件，色域更广。
- **Max（最大）**——约 60 个 emoji，覆盖最广色域，包含小调色板缺失的中性灰和米白色。一个去重步骤会合并实测颜色几乎相同的 emoji，保持调色板精简高效。
- **Hearts（爱心）**——`❤️🧡💛💚💙💜🤎🖤🤍🩷🩵`，主题化的效果。

#### 月相浮雕模式

完全忽略颜色，用八个月相构建图像：

- **亮度** 决定在暗→亮轴上选哪个月相（🌑 → 🌕）。
- 图像内的 **水平光照梯度** 决定盈月还是亏月：右侧更亮的格子用右侧受光的月相（🌒🌓🌔），左侧更亮的用左侧受光的月相（🌘🌗🌖）。

这样把光照方向编码进去后，边缘会呈现一种侧面受光的雕塑感，而不是平淡的明暗。对光照方向明显的图片（人像、侧光场景）配深色背景效果最佳。

### 文本导入 / 导出

emoji 网格可作为纯文本往返保存。用 **↓ Text .txt**（或复制到剪贴板）导出，之后用 **↑ Import text** 重新载入以查看或重新导出为 jpeg、svg。导入使用 `Intl.Segmenter`（字素粒度），因此多码点 emoji——ZWJ 组合序列以及带变体选择符的字形（如 ❤️）——会作为单个格子保持完整，而不会被拆散。导入会清除当前图片，所以拖动滑块或调整颜色不会覆盖导入的视图；更改背景色会让导入的网格基于新背景重新渲染。

### 性能

滑块渲染采用**防抖**：拖动分辨率控件时列数会实时更新，但完整的马赛克重建会等到你停止拖动（约 25 毫秒）后才执行。这让高分辨率（最高 512 列）保持流畅，而不是每次微动都重新计算。背景色选择器同样采用此防抖。

### 说明与局限

- **emoji 色域偏窄。** 即便是 Max 调色板也无法触及高度去饱和或灰暗的色调——emoji 集中在饱和的原色、中性灰和少数棕色附近。色彩微妙的照片会被压平。
- **文本对齐** 在其它应用中取决于它们对 emoji 宽度的处理。
- 全部在客户端运行，图片绝不离开你的设备。

### 后续可能的方向

- **抖动算法**（如 Floyd–Steinberg），让有限的调色板在视觉上混合、伪造出中间色——这是颜色模式下能带来的最大画质提升。
- **光照方向控制**，让浮雕模式的"太阳"可旋转到任意角度（包括垂直梯度），而不仅是左右。(√)

### 文件

- `emoji-mosaic.html`——整个应用（HTML + CSS + JS 合为一个文件）。
