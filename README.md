

> **[中文化] agent-vision-toolkit**
>
> 此项目是 [Anionex/agent-vision-toolkit](https://github.com/Anionex/agent-vision-toolkit) 的中文翻译版本。
> - 原项目 Stars: 1180
> - 主语言: Python
> - 许可证: MIT
> - 翻译日期: 2026-09-07
> - 原始 README: [README_en.md](README_en.md)
>
> 如有翻译不准确之处，欢迎提 Issue 或 PR。

---


---

<p align="center">
  <img src="assets/hero.png" alt="agent-vision-toolkit — 为纯文本 LLM 智能体赋予视觉能力。" width="100%">
</p>

<div align="center">

# agent-vision-toolkit
<a href="https://trendshift.io/repositories/99395?utm_source=trendshift-badge&amp;utm_medium=badge&amp;utm_campaign=badge-trendshift-99395" target="_blank" rel="noopener noreferrer"><img src="https://trendshift.io/api/badge/trendshift/repositories/99395/daily?language=Python" alt="Anionex%2Fagent-vision-toolkit | Trendshift" width="250" height="55"/></a>


[![X (Twitter)](https://img.shields.io/badge/-@anion__ex-000000?style=flat-square&logo=x&logoColor=white)](https://x.com/anion_ex)
[![GitHub stars](https://img.shields.io/github/stars/Anionex/agent-vision-toolkit?style=flat-square&logo=github)](https://github.com/Anionex/agent-vision-toolkit/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/Anionex/agent-vision-toolkit?style=flat-square&logo=github)](https://github.com/Anionex/agent-vision-toolkit/forks)
[![License: MIT](https://img.shields.io/github/license/Anionex/agent-vision-toolkit?style=flat-square&color=4EAA25)](https://github.com/Anionex/agent-vision-toolkit/blob/main/LICENSE)

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Standard-green?style=flat-square)](https://agentskills.io)
[![Extensions](https://img.shields.io/badge/-Extensions-3178C6?style=flat-square)](https://github.com/Anionex/agent-vision-toolkit/tree/main/extensions)
[![Shell](https://img.shields.io/badge/-Shell-4EAA25?style=flat-square&logo=gnubash&logoColor=white)](https://github.com/Anionex/agent-vision-toolkit/tree/main/bin)

**它所见即所想——为任何纯文本编码智能体赋予视觉能力：图像问答、长截图 OCR、前端 UI 还原和 GUI 自动化，作为一个视觉工具包加技能，并可为 Codex、Claude Code、Pi、Oh My Pi 和 OpenCode 提供可选的即插即用集成。**

🎯 智能体的视觉能力不必局限于模型本身——它可以存在于工具链中。

🌐 [**中文**](README_CN.md) ｜ **English**

</div>

如果你的智能体已经运行在诸如 DeepSeek 这样的纯文本模型上，却因缺乏多模态能力而受限——无法查看图像，每次尝试使用图像工具都被系统阻止——本仓库提供了工具、技能和代理集成，让纯文本模型能够以同等甚至更优的水平处理视觉任务。目标是让使用文本模型智能体的体验与使用多模态智能体一样无缝，并最终让配备工具和本工具包方法的文本模型智能体超越未使用本工具包的原生多模态智能体。

本仓库提供两类组件：
1. **视觉工具 CLI** —— 多个 CLI，外加一个技能，教导智能体何时使用哪一个。任何能调用 Shell 的智能体都可以使用它们。
2. **无缝集成** *(可选升级)* —— 一个透明的本地代理和单文件原生插件，使得**我们粘贴的图片和智能体内置的图像工具都能无缝工作**，无需额外安装工具或额外提示。

所有代码均已在真实的 Codex + DeepSeek 会话中验证，同一流程也已在 Claude Code、Pi、Oh My Pi 和 OpenCode 中完成端到端的实况验证。

> 如果这个项目对你有帮助或给你带来一些灵感，欢迎 star🌟 & fork。

## ❤️ 赞助商

> 想赞助这个项目？请参阅 [FUNDING.md](FUNDING.md) 或发送邮件至 davidyang042@gmail.com。

<details open>
<summary>点击折叠</summary>

<table>
<tr>
<td width="220" align="center" valign="middle"><a href="https://aihubmix.com/?aff=sinZ"><img src="assets/logo_aihubmix.png" alt="AIHubMix" height="48"></a></td>
<td valign="middle">感谢 <a href="https://aihubmix.com/?aff=sinZ">AIHubMix</a> 对本项目的赞助！AIHubMix 是一个稳定、高并发的 AI 模型 API 网关，通过单个 API 密钥即可连接 Claude、GPT、Gemini、DeepSeek 等主流模型，兼容多种协议，并提供<b>免费模型选项</b>。中国用户可通过 <a href="https://inferera.com/?aff=sinZ">中国入口</a> 使用，海外用户可通过 <a href="https://aihubmix.com/?aff=sinZ">全球入口</a> 使用。</td>
</tr>
<tr>
<td width="220" align="center" valign="middle"><a href="https://api.ewo.so/register?aff=U6PT7J"><img src="assets/logo_eapi_dark.png" alt="E-API" height="48"></a></td>
<td valign="middle">感谢 <a href="https://api.ewo.so/register?aff=U6PT7J">E-API</a> 对本项目的赞助！E-API 在兼容 OpenAI、Anthropic 和 Codex 的 API 背后聚合了主流 AI 模型，部分精选 Claude 模型价格比官方<b>低 98%</b>，DeepSeek V4 模型价格比官方<b>低约 25%</b>。</td>
</tr>
</table>

</details>

## 最新更新

**2026-08-18 — 技能更名：** 附带的智能体技能现更名为 `vision-skills`（原为 `vision-tools`），使名称更能描述其能力而非底层工具。

**2026-08-13 — 现已支持原生 DeepSeek Harness。** 新的 [`dsh-vision-toolkit`](https://github.com/Anionex/dsh-vision-toolkit) 关联包将此工具包作为原生 Profile Bundle 引入 DSH Web 和 Headless 配置。它提供了 10 个结构化的视觉工具，用于意图感知的图像问答、Grounding、检测、追踪、裁剪、像素差异、长截图 OCR、前景提取、主色分析和 HTML 截图，同时增加了 DSH 凭据、托管的隔离运行时、可预览的 Artifacts、Web 设置和 Agent 作用域内的渐进式工具暴露。

该包在此作为 Git 子模块跟踪，并在 [`Anionex/dsh-vision-toolkit`](https://github.com/Anionex/dsh-vision-toolkit) 独立维护。使用 `--recurse-submodules` 克隆此仓库，或在现有检出中运行 `git submodule update --init --recursive`。

<details>
<summary><b>目录</b></summary>

- [最新更新](#最新更新)
- [亮点](#亮点)
- [用例手册](#用例手册)
- [实际效果](#实际效果)
- [快速开始](#快速开始)
- [工具集](#工具集)
- [升级：无缝集成](#升级无缝集成)
- [工作原理](#工作原理)
- [配置](#配置)
- [常见问题](#常见问题)
- [捐赠](#捐赠)
- [社区](#社区)
- [关于](#关于)

</details>


## 亮点

- **不仅仅是图像描述——它捕获 LLM 真正关心的内容**：查看图像时，它会传递用户或模型的最新意图，生成当前轮次所需的细节，而非宽泛、无焦点的描述。
- **粘贴的图片和内置图像工具均可使用**：智能体既能理解直接粘贴的图片，也能理解通过其内置工具打开的图片。
- **经过实战检验的视觉任务方法论**：附带的技能教导智能体检查什么、选择哪个工具、遵循什么顺序以及如何验证最终结果。
- **一句话安装**：让你的智能体自行安装——它会端到端地遵循已验证的流程，包括工具包、技能和无缝集成。

## 用例手册

附带的 `vision-skills` 技能包含完整的示例，智能体可以直接遵循。
何时使用它们、调用工具的顺序以及如何验证结果，都记录在相应的技能指南中：

| 用例 | 智能体学习做什么 |
|---|---|
| [提取长截图、聊天记录和滚动页面](skills/vision-skills/references/long-screenshot-ocr.md) | 查找低内容剪切带，按顺序对每个块进行 OCR，保留聊天发言人/时间戳/引用，仅合并重复的重叠部分，并标记出有风险的边界以供验证。[查看 Telegram 参考运行 →](examples/long-screenshot-ocr/) |
| [从截图或设计稿重建 UI](skills/vision-skills/references/restore-ui.md) | 首先复用项目组件和资源，然后结合代码原生 UI、提取的视觉元素、渲染截图和视觉比较来对齐页面或组件。 |
| [还原图标、Logo、插画或其他图形](skills/vision-skills/references/restore-graphic.md) | 从源图像中提取透明 PNG，或在需要时重建可编辑/可缩放的 SVG，然后验证形状、颜色和 Alpha 边缘。 |
| [将草图、图表或白板转化为结构化代码](skills/vision-skills/references/restore-structure.md) | 将节点、标签、连接和方向恢复为可编辑的 Mermaid、Graphviz 或其他结构化表示。 |
| [通过截图操作 GUI](skills/vision-skills/references/gui.md) | 定位控件，执行一个操作，再次截屏，并在继续之前验证结果状态。 |
| **更多用例** | 其他逐步视觉智能体手册正在逐步添加中。 |

## 实际效果

### 信息图还原：一句话从截图到 HTML

<p align="center">
  <img src="assets/infographic-restore-reference.png" alt="展示模型训练方式的原始信息图" width="49%">
  <a href="examples/infographic-restoration/how-is-the-model-trained.html">
    <img src="assets/infographic-restore-result.png" alt="模型训练信息图的 HTML 和 CSS 重建" width="49%">
  </a>
</p>

*左：原始信息图截图。右：使用 HTML/CSS 构建的可编辑重建。[查看 HTML 源码 →](examples/infographic-restoration/how-is-the-model-trained.html)*

### UI 还原：一句话从草图到界面

<p align="center">
  <img src="assets/ui-restore-sketch.png" alt="用作 UI 还原参考的手绘 JupyterLab 界面" width="49%">
  <img src="assets/ui-restore-result.png" alt="根据手绘参考制作的还原版 JupyterLab 工作区" width="49%">
</p>

*左：手绘参考。右：根据其制作的还原版 JupyterLab 工作区。工作流程请参阅 [UI 还原手册](skills/vision-skills/references/restore-ui.md)。在 Codex 中使用 `deepseek-v4-flash` 执行。*

### 快速 UI 还原：近似初稿

<p align="center">
  <img src="assets/ui-fast-restore-reference.png" alt="用作快速 UI 还原参考的原始 YouMind 主页" width="49%">
  <img src="assets/ui-fast-restore-result.png" alt="使用快速 UI 还原模式生成的近似 YouMind 主页" width="49%">
</p>

*左：原始页面。右：保留主要布局、内容和视觉层次，同时允许近似颜色和库图标的快速重建。快速模式目标是在大约三分钟内生成第一张截图。*

<p align="center">
  <img src="assets/effect-3.jpg" alt="使用可选的 glance CLI 进行多轮图像问答" width="49%">
  <img src="assets/effect-4.jpg" alt="DeepSeek V4 通过 glance/ground 定位屏幕元素来下棋" width="49%">
</p>

*左：使用 `glance` 进行多轮图像问答。右：借助 `ground`，DeepSeek V4 定位屏幕元素自主下棋。*

<p align="center">
  <img src="assets/effect-1.jpg" alt="Codex 中的 DeepSeek 回答关于 UI 截图的样式问题" width="49%">
  <img src="assets/effect-2.jpg" alt="Codex 中的 DeepSeek 从截图调试不匹配的 UI 字段" width="49%">
</p>

*左：DeepSeek V4 通过相似样式比较回答 UI 样式问题。右：DeepSeek V4 从截图调试字段名不匹配的问题。*


## 快速开始

**最简单的安装方式是将此消息发送给你的智能体：**

> 按照 https://github.com/Anionex/agent-vision-toolkit 中的说明在本地安装视觉工具包和技能。如果视觉 API 未配置，请找到当前操作系统的配置文件，并指导我设置 `VISION_API_KEY`、`VISION_BASE_URL` 和 `VISION_MODEL`。

**如果你还想要可选的无缝集成层，请发送此消息：**

> 完整阅读 https://github.com/Anionex/agent-vision-toolkit/blob/main/AGENT_INSTALL.md，然后为我们当前使用的智能体应用安装合适的视觉代理或原生扩展/插件。如果视觉 API 未配置，请找到当前操作系统的配置文件，并指导我设置 `VISION_API_KEY`、`VISION_BASE_URL` 和 `VISION_MODEL`。

你只需要准备一个支持 OpenAI Chat Completions、OpenAI Responses 或 Anthropic Messages 的多模态 API，以及它的基础 URL、API 密钥和模型名称。智能体会指导你将它们写入相应的配置文件。

> 安装可选集成并重启智能体后，直接粘贴图片或让模型调用其内置图像工具即可。Pi、Oh My Pi 和 OpenCode 使用单文件[原生扩展](extensions/)而非代理；请参阅各智能体的文档。

<details>
<summary><b>三步手动安装</b></summary>

**1. 指向视觉 API** —— 在 `~/.config/agent-vision-toolkit/env` 中设置三个环境变量（`chmod 600`）：

```bash
VISION_API_KEY=sk-...
VISION_BASE_URL=https://openrouter.ai/api/v1
VISION_MODEL=google/gemini-3.6-flash
```

任何支持带 `image_url` 的 `/chat/completions` 的 OpenAI 兼容端点均可使用（例如阿里云 DashScope：`https://dashscope.aliyuncs.com/compatible-mode/v1` + `qwen-vl-max-latest`）。Python 客户端/代理也可以通过设置 `VISION_API_PROTOCOL=responses` 使用带 `input_image` 的 `/responses`，或通过设置 `VISION_API_PROTOCOL=anthropic` 并使用以 `/v1`（而非 `/messages`）结尾的基础 URL 来使用 Anthropic Messages。添加 `LANG=en` 可获得英文描述（默认为中文）。

**2. 将 CLI 添加到你的 PATH：**

```bash
git clone https://github.com/Anionex/agent-vision-toolkit.git
export PATH="$PWD/agent-vision-toolkit/bin:$PATH"   # 添加到你的 shell 配置文件以持久化
```

`glance` 仅需 Python 3.11+；`ground`/`detect`/`crop` 和长截图 OCR 手册需要 `pillow`；`trace` 需要 `pillow` + `numpy`（仅在其显式 `--outline` 回退时需要 `vtracer`）。仅为你要使用的工具将可选依赖安装到隔离的 venv 中。

**3. 安装技能** 以便你的智能体知道这些工具的存在以及如何组合使用它们：

```bash
npx skills add Anionex/agent-vision-toolkit --skill vision-skills -a codex -g --copy -y
```

或者将 `skills/vision-skills/` 复制到你的智能体技能目录（例如 `~/.codex/skills/`）并重启智能体。

</details>

## 工具集

一组为智能体设计的视觉工具，让它们可以根据情况自由选择：

<details>
<summary><b><code>glance</code> — “这张图片看起来像什么？”</b></summary>

直接询问关于图像的问题，或转录其文本。

```bash
glance screenshot.png -q "这张图片的主色调是什么？"
glance screenshot.png --ocr
```

```
这张图片的主色调是**白色和浅灰色，带有浅蓝色点缀。**
```

```
用户名
密码
登录
```

对于滚动截图或聊天记录，该技能包含一个工作流程，用于查找安全的剪切带、使用 `glance` 对块进行 OCR、合并重叠部分并编写边界审计：

```bash
python3 skills/vision-skills/scripts/long_screenshot_ocr.py long-chat.png --mode chat -o long-chat.ocr.md
```

</details>

<details>
<summary><b><code>ground</code> — “我想要的对象在哪里？”</b></summary>

定位对象或区域，并以原始像素坐标获取边界框：

```bash
ground screenshot.png "发送按钮"
```

```
x1: 1067, y1: 841, x2: 1108, y2: 881
```

每次调用分析一张完整图像。使用 `--region X1,Y1,X2,Y2` 仅搜索该框，但仍报告原始图像坐标——这是针对小目标的放大路径。

</details>

<details>
<summary><b><code>detect</code> — “图像中有什么，在哪里？”</b></summary>

盘点图像（或区域）的元素——带精确可见文本和像素框的编号列表：

```bash
detect page.png
detect page.png "按钮"
detect page.png --region 238,600,953,671
```

```
1. 左下角 做任何事 x1: 253, y1: 601, x2: 328, y2: 609
2. 左下角 + x1: 254, y1: 650, x2: 268, y2: 665
3. 右下角
```