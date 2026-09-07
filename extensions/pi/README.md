# vision.ts — Pi / Oh My Pi 扩展

为 [Pi](https://github.com/badlogic/pi-mono) 或 [Oh My Pi](https://github.com/can1357/oh-my-pi) 中的纯文本模型赋予视觉能力：一个 `context` 钩子会将传出历史记录中的每个图像块替换为由您配置的视觉模型生成的、带有焦点提示的描述。原生支持图像的模型则不受影响。

该钩子在请求流水线的第一步运行——在 Oh My Pi 的非视觉门控将图像替换为占位符之前——并且能够看到内部消息格式，因此一个文件即可同时服务于两个宿主及所有提供商。

## 安装

将这一个文件复制到宿主的扩展目录中：

```bash
# Pi
mkdir -p ~/.pi/agent/extensions && cp vision.ts ~/.pi/agent/extensions/

# Oh My Pi
mkdir -p ~/.omp/agent/extensions && cp vision.ts ~/.omp/agent/extensions/
```

## 配置

与本仓库中的代理和 CLI 工具使用相同的环境变量链——通常为 `~/.config/agent-vision-toolkit/env`（权限 `0600`）：

```
VISION_API_KEY=...
VISION_BASE_URL=https://your-vision-endpoint/v1
VISION_MODEL=your-vision-model
# 可选：LANG=zh（或 en）用于固定描述语言
```

`$VISION_ENV_FILE`、`%LOCALAPPDATA%/agent-vision-toolkit/env` 以及工作目录中的 `.env` 文件也会被读取；后读取的文件会覆盖先前的文件。

## 行为

- 粘贴的图片会在其所属消息的文本下进行描述；工具获取的图片会在助手陈述的查看原因下进行描述。
- 描述会按（图片，提示词）在进程生命周期内进行缓存，因此重放历史记录不会产生额外开销。
- 描述失败时，会替换为明确的失败提示——原始图片绝不会被转发，失败也绝不会被静默忽略。
- 重写操作在每次调用的历史记录副本上进行；存储的会话保留原始图片，因此切换到多模态模型即可恢复真实的视觉能力。