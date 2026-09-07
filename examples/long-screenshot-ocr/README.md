# Telegram 长截图 OCR 演示

**[打开完整的 780 x 31,186 Telegram 截图 →](telegram-chat-long.png)**

## 构建产物

| 文件 | 用途 |
|---|---|
| [`telegram-chat.html`](telegram-chat.html) | 确定性的离线英文对话源文件。 |
| [`telegram-chat-long.png`](telegram-chat-long.png) | 生成的 780 x 31186 输入截图。 |
| [`telegram-chat-ocr.md`](telegram-chat-ocr.md) | 长截图 OCR 工作流生成的合并 Markdown 转录文本。 |

## 已检入的参考运行结果

**2026 年 8 月 6 日**的参考运行产生了以下结果：

| 检查项 | 结果 |
|---|---:|
| 源记录数 | 180 |
| 提取记录数 | 180 |
| OCR 分块数 | 21 |
| 分割边界数 | 20 |
| 回退重叠 | 0 px |
| 缺失或多余记录 | **0** |
| 说话人、时间戳和回复字段差异 | **0** |
| 空白/标点规范化后的内容匹配 | **180/180** |
| 仅展示层面的差异 | 5（2 处智能引号替换，3 处空行选择） |

源文件特意采用了更具辨识度的 Telegram 风格移动端布局：Android 状态栏和应用栏、置顶消息横幅、涂鸦壁纸、头像、接收/发送气泡尾部、双勾标记、日期和服务胶囊、未读分隔线、回复、表情回应、代码块、文件、投票、照片卡片和语音消息卡片。这充分测试了安全分割选择和结构化聊天合并能力，而非仅测试纯文本段落的 OCR。

## 复现步骤

在仓库根目录下，首先配置常规的 `VISION_*` 变量，然后运行：

```bash
python3 skills/vision-skills/scripts/long_screenshot_ocr.py \
  examples/long-screenshot-ocr/telegram-chat-long.png \
  --mode chat \
  --chunks-dir work/telegram-chat-ocr \
  --jobs 4 \
  -o work/telegram-chat-ocr.md
```

分块目录将包含 21 张图片、结构化 OCR 辅助文件、`manifest.json` 和 `ocr_audit.md`。视觉模型输出可能存在差异，因此请将新运行结果与已检入的参考转录文本进行比较，并检查审计标记的每个需要审查的边界。

要从离线 HTML 源文件重新生成输入截图：

```bash
python3 skills/vision-skills/scripts/html_shot.py \
  examples/long-screenshot-ocr/telegram-chat.html \
  --width 390 \
  --height 15593 \
  --scale 2 \
  --wait-ms 100 \
  -o examples/long-screenshot-ocr/telegram-chat-long.png
```