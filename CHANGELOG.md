# 更新日志

本文件记录了 agent-vision-toolkit 所有面向用户的显著变更。

## [未发布]

### 变更

- 将内置的 agent 技能从 `vision-tools` 重命名为 `vision-skills`，使名称描述的是能力而非底层工具。

### 修复

- 将 Qwen 系列 grounding 框解析为 `x0,y0,x1,y1` 格式，同时保留 Gemini 的 `y0,x0,y1,x1` 约定，并可通过 `VISION_BOX_ORDER` 为自定义提供商进行配置。
- 拒绝被截断的边界框 JSON，而不是静默返回截断前恰好出现的完整对象。
- 当调用方传入完整的检测类别时，避免重复添加 `every distinct` 和可见文本指令。

## [0.2.0] - 2026-08-14

### 新增

- 让共享的 Python 视觉客户端能够调用 Chat Completions 或 Responses API，包括可选的推理开销和显式的 `store: false` 数据处理。
- 添加原生 Anthropic Messages 请求，支持协议特定的身份验证、图像源、可选的思考控制以及文本块响应提取。
- 通过现有的视觉描述流水线重写 OpenAI Chat Completions 的 `image_url` 块，并附带与主机无关的通道说明。
- 添加快速 UI 恢复工作流，用于在保留页面层级、可见文本和原生控件的同时生成快速粗略的初稿。
- 添加项目落地页源码，并为需要该集成的用户链接 DeepSeek Harness 视觉捆绑包。

### 变更

- 使上游出口显式且具有弹性：默认直接连接，可选地使用配置的 HTTP CONNECT 代理，并且仅在连接建立失败时才进行故障转移，而不是遵循环境中的系统代理设置。
- 使协议特定的环境配置具有权威性，并保持现有的主机身份验证和模型兼容性行为不变。

### 修复

- 从共享的 Python 视觉客户端发送浏览器兼容、可配置的 User-Agent，以便由 Cloudflare 支持的 OpenAI 兼容端点不会拒绝默认的 `Python-urllib` 签名。
- 遵循 `Retry-After` 并重试 Anthropic 529 过载响应。
- 在 Windows 上通过其解释器运行 `glance` 启动器，并在 Windows 换行行为下保留 `trace` SVG 字节输出。

## [0.1.0] - 2026-08-07

### 新增

- 五个视觉 CLI — `glance`、`ground`、`detect`、`trace` 和 `crop` — 以及 `vision-tools` agent 技能。
- 可选的无缝集成：适用于 Codex 和 Claude Code 的本地代理，以及适用于 Pi、Oh My Pi 和 OpenCode 的单文件原生扩展。
- 支持粘贴图像和工具获取的图像，提供任务感知的焦点提示、并行多图像描述、按请求缓存和诚实的失败说明。
- 提供用于长截图 OCR、UI 恢复、图形恢复、结构恢复和 GUI 操作的视觉操作手册。
- 社区贡献、行为准则、支持和安全策略。
- 结构化 Issue 表单和 Pull Request 模板。
- GitHub 赞助配置和持续集成检查。
- 双语赞助政策和赞助使用声明。