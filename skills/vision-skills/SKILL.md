---
name: vision-skills
description: >-
  本地视觉命令行工具：glance（描述/提问/OCR 图像）、ground（定位目标，像素框）、detect（元素清单）、trace（图像转 SVG 几何图形）、crop（将像素框裁剪为文件），以及 scripts/html_shot.py（HTML 文件转图像）。适用于任何涉及图像的任务——提问、文本提取、分割和转录长截图或聊天记录、定位元素、比较、重建为 HTML/SVG、数字化草图或图表、读取图表数值、通过截图操作 GUI——以及在给定描述缺少细节时自行重新检查图像。
---

# vision-skills

五个本地命令行工具，为纯文本代理赋予视觉能力。它们共享同一份视觉配置（`VISION_API_KEY` / `VISION_BASE_URL` / `VISION_MODEL` / `LANG`），以及可选的 Python 客户端设置 `VISION_API_PROTOCOL`、`VISION_REASONING_EFFORT` 和 `VISION_USER_AGENT`——无需额外凭证。

根据你要回答的问题选择合适的工具：

| 问题 | 工具 |
|---|---|
| “这张图片显示/说了什么？” | `glance` |
| “X 在哪里？”——你能命名的某个事物 | `ground` |
| “所有的 X 都在哪里？”——某一类别的每个实例 | `detect` |
| “它的精确形状、尺寸、偏移是多少？” | `trace` |
| “把这个框裁剪成独立的图像文件” | `crop` |
| “OCR 这段长截图 / 滚动页面 / 聊天记录” | `scripts/long_screenshot_ocr.py` |
| “提取图标/标志前景为透明 PNG——手动区域或自动（裁剪+缩放截图）” | `scripts/extract_fg.py` |
| “把这个 HTML 文件变成视口或整页截图” | `scripts/html_shot.py` |
| “哪个颜色在某个区域占主导，哪个调色板值最匹配？” | `scripts/dominant_colors.py` |
| 以上工具都无法返回的关系——两个已定位事物之间的间隙、距离 | 基于像素的代码（Pillow） |

`glance` 回答“是什么”；`ground` 和 `detect` 回答“在哪里”。你向 `ground` 提供对某个特定事物的描述；你向 `detect` 提供某个类别，它会枚举所有实例。

两者都返回真实坐标，但并非像素级精确：框以 0-1000 的网格返回，并缩放到你的图像，因此最后几个像素不可靠。但这足以用于裁剪、点击和位置比较。当数值必须精确时，`trace` 会从实际像素中推导——偏移、尺寸、形状。

## 优先使用提供的工具，而非手写像素操作

本工具包为某个功能提供了工具，就调用该工具——不要在任务中途用 Pillow 重写。这些命令行工具的存在是为了避免每次都以不同方式手写相同的像素操作：

- 从图像中裁剪框 → `crop`，而不是 `Image.open(...).crop(...)`
- 采样区域调色板 → `scripts/dominant_colors.py`
- 比较两张图像 → `scripts/pixel_diff.py`
- 矢量化到 SVG → `trace`
- 定位 / 清点元素 → `ground` / `detect`
- 描述 / OCR 图像 → `glance`
- 安全分割、OCR 和合并长截图 → `scripts/long_screenshot_ocr.py`
- HTML 文件转视口或整页截图 → `scripts/html_shot.py`

手写 Pillow 仅用于上述工具无法返回的内容：两个已定位事物之间的关系（间隙、距离）、调整大小或叠加、绘图。如果你发现自己正在编写 `.crop()`、`.convert()` 或直方图代码，而上述某个工具恰好适用，请用工具调用替换——相同的坐标、相同的框格式，输出可直接输入下一个工具。

## glance — 询问图像

```bash
glance <image>                                 # 详细描述
glance <image> -q "<问题>"                 # 针对性问题（仅限定性）
glance <image> --ocr                           # 逐字 OCR
glance <image> --region X1,Y1,X2,Y2 -q "..."   # 放大到裁剪区域
glance <img1> <img2> -q "..."                  # 在单次调用中比较
```

当你确实使用 `glance` 进行比较时，将所有路径传递给一次调用——单独的调用无法同时看到两张图像，因此事后比较两个描述是两次幻觉表面，而非真正的比较。`--region` 仅上传裁剪区域，因此小文本和图标变得可读。

但“这两者之间发生了什么变化？”不是 glance 能回答的问题。一个单词的徽章或微小的偏移对视觉模型来说是舍入误差，但对 `scripts/pixel_diff.py` 来说却是精确的。先进行差异比较以获取框，然后使用 `glance --region` 查看该框以读取变化的实际内容。

对于高耸的滚动截图，不要将整个图像通过一次 OCR 调用发送并接受模型的降采样损失。请运行长截图工作流，它会找到低内容剪切带，对每个块调用 `glance`，对聊天记录使用结构化提取，仅合并重复的重叠部分，并写入边界审计：

```bash
python3 scripts/long_screenshot_ocr.py work/page.png -o work/page.ocr.md
python3 scripts/long_screenshot_ocr.py work/chat.png --mode chat --resume -o work/chat.ocr.md
```

使用前请阅读 `references/long-screenshot-ocr.md`。它定义了不安全剪切和聊天消息边界的验证流程。

## ground — 定位命名目标

```bash
ground <image> "<目标描述>"
ground <image> "<目标>" --region X1,Y1,X2,Y2
```

输出：`x1: .., y1: .., x2: .., y2: ..`，单位为原始图像像素——使用 `--region` 时同样如此（裁剪命中会映射回原图）。

提供商原生的 0-1000 框并非都使用相同的数组顺序：Gemini 使用 `[y0, x0, y1, x1]`，而 Qwen3-VL、Qwen3.5 和 Qwen3.6 使用 `[x0, y0, x1, y1]`。定位代码必须在缩放到像素之前按模型系列（或显式覆盖）选择顺序；切勿将每个提供商都解析为 Gemini 风格的 `yxyx`。

如果返回了多个带编号的框，说明你的描述匹配了多个元素，而非选中了单个事物。用能区分你指的那个元素的特征——其文本、位置、所在的块——来缩小范围，然后再次询问。

该框是一个句柄，而不仅仅是一个答案——它可输入下一次调用：

```bash
$ ground screenshot.png "发送按钮"
x1: 1067, y1: 841, x2: 1108, y2: 881
$ glance screenshot.png --region 1067,841,1108,881 -q "它是启用状态还是灰色禁用状态？"
```

这种两步操作是检查任何太小而无法在整图传递中存活的元素的方式。

## detect — 查找某个类别的每个实例

```bash
detect <image>                        # 每个 UI 元素
detect <image> "按钮"              # 仅一种类别
detect <image> --region X1,Y1,X2,Y2   # 仅在一个框内
```

你为 `ground` 命名一个特定事物；你为 `detect` 命名一个类别，它会枚举实例。输出是带编号的列表，包含每个项目的可见文本和框。全屏传递是快速的初稿——在密集屏幕上，计数因运行而异。为了完整性，先检测布局块，然后对每个块执行 `detect --region`。

## trace — 精确形状几何（本地，无需视觉 API）

```bash
trace <image>                                  # 黑白样条 SVG 输出到标准输出
trace <image> --polygon                        # 盒状图表/线框图
trace <image> --region X1,Y1,X2,Y2 -o out.svg  # 先裁剪
```

坐标来自实际像素，而非模型的估计。仅适用于平坦、高对比度的图形；文本会变成曲线（当文本重要时，与 `--ocr` 结合使用）。小图像在追踪前会自动放大，因此 30px 的图标与截图一样易于追踪——尺寸不是跳过该工具的理由。在发布或重用追踪的 SVG 之前，请阅读 `references/restore-graphic.md`——其中包含重用陷阱以及发布与手写之间的决策。

## crop — 从图像中裁剪像素框（本地，无需视觉 API）

```bash
crop <image> --region X1,Y1,X2,Y2             # 在输入旁边写入 <图像名>.crop.png
crop <image> --region X1,Y1,X2,Y2 -o out.png
crop <image> --region X1,Y1,X2,Y2 --scale 4   # 先将裁剪区域放大 4 倍（LANCZOS）
```

与 `ground`/`detect` 打印的 X1,Y1,X2,Y2 像素框相同，并限制在图像边界内。一旦某个框值得保留——同一裁剪区域即将依次输入 `pixel_diff`、`dominant_colors` 和 `trace`——将其裁剪为文件一次并重用，而不是在每次调用时在内存中重新裁剪。`--scale N` 在写入前放大裁剪区域（默认输出名称变为 `<图像名>.crop@Nx.png`）：对于 `ground`/`trace` 无法清晰看到的小图标，使用 `--scale 4` 裁剪，然后在放大后的文件上运行 `ground`/`trace`——返回的坐标位于放大网格中，除以 `N` 可映射回原始图像。需要可选的 `pillow`。

## extract_fg — 图标前景为透明 PNG：手动区域或自动（本地，无需视觉 API）

```bash
# 手动：你知道区域（以及可选的背景颜色）
python3 scripts/extract_fg.py shot.png --region X1,Y1,X2,Y2 -o icon.png
python3 scripts/extract_fg.py shot.png --region X1,Y1,X2,Y2 --mode dark          # 灰色/黑色线条标志
python3 scripts/extract_fg.py shot.png --region X1,Y1,X2,Y2 --exclude-color '#E6E6E6'
# 自动：`crop --scale` 裁剪区域，图标居中——无需区域
crop shot.png --region X1,Y1,X2,Y2 --scale 4 -o d/icon1.png
python3 scripts/extract_fg.py d/icon1.png d/icon2.png       # 在每个输入旁边写入 <名称>.clean.png
python3 scripts/extract_fg.py d/icon1.png --disc-radius 60
python3 scripts/extract_fg.py d/icon1.png --boxes "101,84,184,171"
```

手动模式保留区域中每个足够大的连通分量（独立的标志子形状保持在一起；斑点被丢弃）。自动模式接受 `crop --scale` 裁剪区域，图标居中（圆盘 + 字形）：圆盘中心是图像中心，圆盘半径默认为 `min(w,h)/2 * 0.6`，圆盘颜色从中心周围的环中采样；排除该颜色，字形被选为三个最大彩色分量中饱和度最高的一个（白色环、波纹和文本被去除），输出为 1:1 透明 PNG。当自动推断失败时，使用 `--disc-radius` 覆盖半径，或将 `ground` 框（在放大网格中）作为 `--boxes` 传递以重新居中并按重叠重新过滤。可一次传递多个图像（自动模式）。需要可选的 `pillow`（自动模式还需要 `numpy`）。

## html_shot — 将 HTML 文件渲染为图像（本地，需要 Chrome 系列浏览器）

```bash
python3 scripts/html_shot.py page.html                      # 写入 page.png，1280x800
python3 scripts/html_shot.py page.html --width 1440 --height 900 -o page.png
python3 scripts/html_shot.py page.html --scale 2            # 2 倍像素：小文本保持可读
python3 scripts/html_shot.py page.html --full-page           # 完整滚动高度，相同布局视口
python3 scripts/html_shot.py page.html --full-page --max-pixels 40000000
```

视觉对齐循环：编写 HTML，在参考视口下截图，然后与设计进行比较。使用 `pixel_diff` 定位实质性差异，而非追求零差异分数。渲染在无头 Chrome/Chromium/Edge 中进行——无 Python 依赖。默认仅捕获视口。使用 `--full-page` 获取完整文档，同时保持 `--width` 和 `--height` 作为布局视口，以便 `vh`/`svh` 和响应式断点不改变。当页面高度不可信时，添加 `--max-pixels N`。`--wait-ms N` 在捕获前暂停以等待字体、图像或动画。路径相对于本技能自身目录。

## pixel_diff — 两张图像的差异位置（本地，无需视觉 API）

```bash
python3 scripts/pixel_diff.py <a> <b>      # 路径相对于本技能目录
```

打印整体差异百分比以及最差区域，格式为 `x1: ..` 框，可直接输入 `glance --region`。在视觉模型会舍入的地方精确。

## dominant_colors — 区域调色板，以及候选项中的精确值（本地，无需视觉 API）

```bash
python3 scripts/dominant_colors.py <image> --region X1,Y1,X2,Y2          # 主要颜色簇 + 占比
python3 scripts/dominant_colors.py <image> --region X1,Y1,X2,Y2 \
  --candidates '#F9FAFA,#F5F5F5,#F3F3F3,#EDEDED'                        # 选择最佳候选项
```

视觉模型能命名颜色（“浅灰色”）但不能给出其值。第一种模式降采样、量化和合并近似重复项，以列出区域中的显著颜色及其各自占比——直方图显示哪种颜色是背景，哪种是强调色。给定标签所暗示的候选调色板，第二种模式根据区域像素与每个候选的接近程度进行评分，并打印获胜者。从此处获取值，切勿从 `glance` 的叙述中获取。路径相对于本技能自身目录。

## 从副本工作，而非临时路径

如果图像位于临时目录中，在对其执行第一次工具调用之前，将其复制到持久位置，并针对副本运行所有操作——这样才能确保图像稍后仍可访问：

```bash
cp "<临时路径>" work/shot.png
glance work/shot.png -q "..."
```

例外情况：用户要求图像保留在临时文件夹中。

## 当你只有描述而没有图像时

如果图像仅以文本形式到达——由人、工具或其他模型编写的描述——且图像的文件路径在对话中可见，不要在没有细节的情况下进行推理。请自行再次查看：

1. `glance <path> -q "<具体细节>"` — 一次定性跟进。
2. `ground <path> "<目标>"` 然后 `glance <path> --region <该框> -q "..."` — 定位，然后放大。这是仔细检查单个元素的可靠方式。

如果文件不再存在，请如实说明，而非猜测。

## 从粗到细——上述每个任务背后的方法

对于关于图像的单个问题，`glance` 就是完整答案。对于任何多步骤任务，请由外而内工作：

1. 一次全图传递（`glance`，或你已有的描述）以了解布局和内容清单。
2. 对于任何重要的元素，使用 `ground` 定位，然后使用 `glance --region <框> -q "..."` 放大。全图传递通常会遗漏小文本和图标；裁剪将全部像素集中在一个细节上，因此模型能以有效更高的分辨率看到它。当同一框需要多次检查时，先用 `crop` 将其裁剪为文件。
3. 切勿将*叙述性*答案视为像素级事实——精确颜色、微小偏移、尺寸。视觉模型会自信地报告不存在的样式：单色代码块中的彩色语法高亮、不存在的边框。从 `trace`、`ground` 框或 `pixel_diff` 获取数值；仅对它们无法返回的内容自行采样像素。

## 使用案例

以下每个文件都是一项完整任务：适用时的调用序列，以及如何判断你已正确完成。

| 任务 | 阅读 |
|---|---|
| OCR 长截图、滚动页面或聊天记录，且不在块边界丢失文本 | `references/long-screenshot-ocr.md` |
| 将页面或组件重建为 HTML/CSS，包括约三分钟的快速近似模式，或将现有 UI 与其参考图像对齐 | `references/restore-ui.md` |
| 提取或重建图标、标志、插图或其他独立图形为透明 PNG/SVG | `references/restore-graphic.md` |
| 将草图、图表或白板转换为 Mermaid、Graphviz 或其他结构化表示 | `references/restore-structure.md` |
| 通过截图操作 GUI——定位、操作、验证每一步 | `references/gui.md` |

## 备注

- 仅支持 PNG / JPEG / GIF / WebP 图像。
- 如果命令未找到，说明可选工具未安装——请向用户报告，而非临时拼凑替代方案。
- 如果视觉 API 失败，请如实转达错误；切勿虚构图像内容。

源仓库：https://github.com/Anionex/agent-vision-toolkit

安装指南：https://github.com/Anionex/agent-vision-toolkit/blob/main/AGENT_INSTALL.md