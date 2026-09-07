# vision.ts — OpenCode 插件

为 [OpenCode](https://github.com/sst/opencode) 中的纯文本模型赋予视觉能力：
`experimental.chat.messages.transform` 钩子会将传出历史记录中的图片文件部分替换为由您配置的视觉模型生成的、带有焦点提示的描述。

## 安装

将此文件复制到插件目录中：

```bash
# 按项目
mkdir -p .opencode/plugin && cp vision.ts .opencode/plugin/

# 全局
mkdir -p ~/.config/opencode/plugin && cp vision.ts ~/.config/opencode/plugin/
```

## 配置

与本仓库中的代理和 CLI 工具使用相同的环境变量链——通常为
`~/.config/agent-vision-toolkit/env`（权限 `0600`）：

```
VISION_API_KEY=...
VISION_BASE_URL=https://your-vision-endpoint/v1
VISION_MODEL=your-vision-model
# 可选：LANG=zh（或 en）以固定描述语言
```

`$VISION_ENV_FILE`、`%LOCALAPPDATA%/agent-vision-toolkit/env` 以及工作目录中的 `.env`
文件也会被读取；后读取的文件会覆盖先前的设置。

## 行为

- 附加/粘贴的图片（数据 URL、http URL 或本地路径——本地文件
  会被内联）会在其所属消息的文本下进行描述。
- 描述按（图片，提示）进行缓存，缓存有效期与进程生命周期一致。
- 描述失败时，会替换为明确的失败提示——原始图片
  绝不会被转发，失败也绝不会被静默忽略。

## 限制

- 转换钩子不暴露当前使用的模型，因此插件无法
  自行检测多模态主模型。当您运行支持视觉的模型时，可在环境中设置
  `VISION_REWRITE=off` 以禁用重写功能。
- 此版本仅处理用户附加的图片部分；工具结果中返回的
  图片尚未被重写。