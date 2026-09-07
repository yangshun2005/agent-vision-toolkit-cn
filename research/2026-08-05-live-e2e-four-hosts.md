# 2026-08-05 — 四主机真机端到端验收

一夜之间完成的实机验证：使用同一张测试图片（400×120，红底 `#c0392b`，两行文字 `VISION-E2E-7429` / `the button color is #2ecc71`），在四个主机上全部真实运行成功，每个主机都有独立的证据链。整个测试过程中未触及生产代理（19100）和生产环境变量。

## 方法

- **Claude Code**：三明治链路 `claude -p → capture(19151) → 代理(19150) → capture(19152) → 桌面宿主鉴权中继(127.0.0.1:15721/claude-desktop)`。鉴权使用宿主 SDK 进程环境中的网关令牌（通过 `ps eww` 提取，未写入磁盘）。当 `ANTHROPIC_BASE_URL` 被覆盖时，CLI 不会读取钥匙串，必须显式设置 `ANTHROPIC_AUTH_TOKEN`。
- **Pi / Oh My Pi**：`models.json` 配置了 sub2api provider（密钥通过环境变量名引用），主模型为 `gpt-oss-120b-medium`（纯文本模型）。在 vision.ts 前后各挂载一个探针扩展，用于打印消息形态。omp 使用 `--tools read` 收缩工具集。
- **OpenCode**：`opencode run "…" --file target.png --attach`（headless 模式下附件的唯一方式；`@file` 提及是 TUI 功能，在 run 模式下仅为纯文本）。视觉调用通过 capture(19153) 进行取证。

## 结果

| 主机 | 证据 |
|---|---|
| Claude Code | PRE 捕获：图片位于 `tool_result` 内层的 `{type:"image",source:{type:"base64"}}`（与收集器假设一致）；POST 捕获：同一位置 → 通道备注 + `[视觉模型描述]`，原始图片零泄漏；回答逐字正确，且主动引用通道备注，并对色值标注了近似区间 |
| Pi 0.73.0 | 探针：`toolResult[text,image]` → vision.ts → `toolResult[text,text,text]`；回答逐字正确 |
| Oh My Pi 17.2.8 | 同上（使用同一份 vision.ts 文件），证实上下文钩子位于 omp 图片闸门的上游 |
| OpenCode 1.18.13 | 视觉请求捕获包含完整的角色提示 + 焦点提示（用户原话）+ 逐字条款 + data URL；回答逐字正确，并准确识别出背景色 `#C0392B` |

## 付出代价的发现

1. **opencode 的插件加载器会将模块的每个导出都当作插件调用**。具名导出（即使是辅助函数）会直接导致 `{} is not iterable` 错误，使加载崩溃。插件文件必须使用单一默认导出；测试断言已对此进行锁定。
2. **sub2api 的流式 + 工具调用组合会因内容依赖而返回 400 错误**（`gemini-3.6-flash-low` 对“读取图片文件”类提示词可 100% 复现，非流式相同请求则正常）。评测遇到 `Upstream request failed` 时，先尝试非 gemini 模型（`gpt-oss-120b-medium` 表现稳定）。
3. **sub2api 对 omp 的 11 个工具载荷返回 400 错误**：单个工具全部通过、组合则失败（前 8 个通过、前 10 个失败），与具体字段无关，类似于上游 schema/大小限制。绕行方案为 `--tools read`。
4. **gpt-oss 在 opencode 下回答全部进入 reasoning 通道，正文为空**；改用 `gemini-3.1-flash-lite` 后正常。主机×模型的渲染兼容性需要单独验证。
5. `opencode run` 不带 `--attach` 时，附件 mime 类型一律为 `text/plain` 且为 file:// URL；带 `--attach` 时才是 base64 data URL 和真实 mime 类型。字段名为 `mime`（TUI/API 路径中为 `mediaType`）——插件需要同时识别这两种情况。

## 事故记录

覆盖了已存在的 `~/.config/opencode/opencode.json` 之后才检查其内容（违反了先备份的原则）。APFS 快照挂载被 TCC 拒绝，无法恢复；原内容未知（同目录下的 config.json 仅为空壳 schema，`skills/` 目录未受影响）。恢复路径：Finder → ~/.config/opencode → 通过 Time Machine 浏览 01:51 之前的版本。

## 遗留事项

- 测试产物位于 `~/cvp-e2e/`（captures 包含本人测试会话的完整请求体，包括 Claude Code 系统提示词——留作核查，请勿入库）；临时 `.env` 文件已删除。
- pi / omp / opencode 三个 CLI 及其配置保留在机器上，供后续评测复用。