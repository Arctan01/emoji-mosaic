# Emoji Mosaic · 彩色匹配图片引擎

[English](#english) | [中文](#中文)

---

## English

### Overview

**Emoji Mosaic** is a browser-based tool that transforms any image into a mosaic composed of emoji characters. Each cell in the output grid is the emoji whose **measured average color** (perceptually in Lab color space) best matches the corresponding region of the source image.

Unlike traditional emoji art tools that rely on hard-coded color mappings, this engine **measures each emoji’s actual rendered color on your device** (macOS, Windows, Linux, mobile, etc.) and builds a dynamic palette. The result is always accurate, whether your system uses Apple Color Emoji, Noto Color Emoji, or Twemoji.

### Features

- **Drag & drop** or click to upload any image (JPEG, PNG, WebP, etc.)
- **Adjustable resolution** – control the number of columns (16–512)
- **Multiple emoji palettes**:
  - Squares, rounds, hearts, extended spectrum, and a "max" palette with dozens of emoji
  - Each palette is **measured live** – you see the actual swatch colors
- **Two rendering modes**:
  - *Color match* – nearest emoji based on perceptual color distance (Lab ΔE)
  - *Moon relief* – maps brightness to moon phase emoji (🌑→🌕) with directional lighting
- **Custom background color** – choose any hex color for the canvas behind emoji cells
- **Export** – download as PNG or copy the mosaic as plain text (emoji grid)
- **Performance stats** – grid size, cell count, palette size, and render time

### How It Works

1. For the selected palette, each emoji is rendered onto a small hidden canvas and its average RGB color is measured.
2. Colors are converted to **Lab color space** (perceptually uniform), which gives better matching than RGB.
3. The uploaded image is downsampled to the target grid size (`cols` × `rows`).
4. For each cell, the source color’s Lab value is compared against all measured emoji colors – the closest match wins.
5. The emoji are drawn onto an output canvas, respecting the chosen background color.

The “moon relief” mode ignores the color palette and instead converts brightness (luminance) into moon phase symbols, with horizontal gradient detection to decide waxing vs. waning phases.

### Technical Notes

- **No external dependencies** – vanilla HTML/CSS/JavaScript.
- **Emoji measurement** happens entirely on your device (privacy-friendly, no uploads to any server).
- Fallback colors are provided for emoji that fail to render (rare, but ensures palette never breaks).
- The output canvas size is adaptive: `cell size = clamp(7, 24, 2600/cols)`, so large grids remain viewable without overflowing.

### Usage

1. Open `index.html` in any modern browser (Chrome, Firefox, Safari, Edge).
2. Drag an image onto the drop zone or click to select a file.
3. Adjust **Resolution** to change the grid density.
4. Switch **Color match** or **Moon relief** modes.
5. Experiment with different **Palettes** – the swatch row shows live measured colors.
6. Click **↓ PNG** to download the result, or **⧉ Copy text** to copy the raw emoji grid.
7. Change **Backing color** to suit your preference (visible in gaps or transparent emoji).

### Why On-Device Measurement?

Emoji fonts vary widely across platforms. Apple’s red square (🟥) is not the same shade as Google’s or Microsoft’s. By measuring the actual rendered color on *your* browser, the mosaic stays true to what you see – no assumptions, no hard-coded RGB values.

---

## 中文

### 概述

**Emoji Mosaic** 是一款在浏览器中运行的图像转换工具，能将任意图片变成由 Emoji 字符组成的马赛克图案。输出网格中的每一个单元格，都是与图像对应区域在 **感知均匀的 Lab 色彩空间** 中平均颜色最接近的那个 Emoji。

与传统 Emoji 艺术工具不同，本引擎会 **实时测量每个 Emoji 在你当前设备上实际渲染出来的颜色**（无论 macOS、Windows、Linux 还是手机），并建立动态调色板。因此，无论你的系统使用 Apple Color Emoji、Noto Color Emoji 还是 Twemoji，匹配结果始终准确。

### 功能特性

- **拖拽或点击**上传图像（支持 JPEG、PNG、WebP 等）
- **可调分辨率** – 控制列数（16～512 列）
- **多种 Emoji 调色板**：
  - 方块、圆形、爱心、扩展色板、“Max” 大调色板（包含数十个 Emoji）
  - 每个调色板 **实时测量** – 你看到的色块就是设备上的真实颜色
- **两种渲染模式**：
  - *颜色匹配* – 基于 Lab 色差选取最接近的 Emoji
  - *月相浮雕* – 将亮度映射为月相符号（🌑→🌕），并检测水平渐变方向决定盈/亏
- **自定义背景色** – 可任意选择十六进制颜色作为 Emoji 背后的底色
- **导出** – 下载为 PNG 图片，或复制纯文本格式的 Emoji 网格
- **性能统计** – 显示网格尺寸、单元格数量、调色板大小和渲染耗时

### 工作原理

1. 根据所选调色板，将每个 Emoji 渲染到一个隐藏的小画布上，测量其平均 RGB 颜色。
2. 将颜色转换到 **Lab 色彩空间**（感知均匀），匹配效果优于 RGB。
3. 将上传的图片缩放到目标网格尺寸（列数 × 行数）。
4. 对于每个单元格，将源颜色的 Lab 值与所有已测量的 Emoji 颜色进行比较 – 选择最接近的那个。
5. 在输出画布上绘制 Emoji，并应用所选背景色。

“月相浮雕”模式不使用颜色调色板，而是将亮度（明度）转换为月相符号，并通过水平方向的亮度梯度判断盈月或亏月。

### 技术说明

- **无外部依赖** – 纯原生 HTML/CSS/JavaScript。
- **Emoji 测量** 完全在本地进行（保护隐私，不上传任何图像到服务器）。
- 为极少数无法渲染的 Emoji 提供了备用颜色（避免调色板断裂）。
- 输出画布尺寸自适应：`单元格大小 = clamp(7, 24, 2600/列数)`，保证大网格仍能在页面上正常显示。

### 使用方法

1. 用任意现代浏览器（Chrome、Firefox、Safari、Edge）打开 `index.html`。
2. 将图片拖放到上传区，或点击选择文件。
3. 调整 **分辨率** 滑块改变马赛克密度。
4. 切换 **颜色匹配** 或 **月相浮雕** 模式。
5. 尝试不同的 **调色板** – 顶部的色块显示实时测量颜色。
6. 点击 **↓ PNG** 下载结果，或点击 **⧉ 复制文本** 复制纯文本 Emoji 网格。
7. 调整 **背景色** 适应个人喜好（在间隙或半透明 Emoji 处可见）。

### 为什么要实时测量 Emoji？

不同平台上的 Emoji 字体差异很大。苹果的红色方块（🟥）与谷歌或微软的红色方块并非同一色度。通过测量当前浏览器中实际渲染的颜色，马赛克能够保持所见即所得 – 不依赖任何固定 RGB 数值。

---