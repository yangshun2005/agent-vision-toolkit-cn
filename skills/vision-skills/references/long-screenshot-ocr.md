# 对长截图进行OCR而不丢失边界文本

**适用场景**：垂直滚动截图、聊天记录、长网页、需求对话、日志视图，或其他在单次视觉调用中会被过度缩小的图像。对于PDF或导出的文档，建议使用支持格式的文档解析器，而非截图OCR。

## 运行工作流程

从持久副本开始，然后选择内容模式：

```bash
# 网页、文档、日志、表格及其他通用内容
python3 scripts/long_screenshot_ocr.py work/page.png -o work/page.ocr.md

# 聊天记录：保留消息分组、发送者、时间戳和引用
python3 scripts/long_screenshot_ocr.py work/chat.png --mode chat -o work/chat.ocr.md
```

如果不使用 `-o`，则写入 `<输入文件名>.ocr.md`；除非使用 `--chunks-dir` 覆盖，否则将分块、辅助文件、清单和审计文件存储在 `<输入文件名>_chunks/` 目录中。

该脚本执行四项操作：

1. 测量每行的内容密度，并在目标高度附近寻找低内容切割带。
2. 仅在不存在安全切割带时添加像素重叠，以确保跨越风险切割的文本同时出现在相邻的两个分块中。
3. 使用现有的 `VISION_*` 配置对分块运行 `glance`；聊天模式请求结构化消息，而通用模式使用逐字OCR。
4. 仅合并置信度高的重复行或消息，并在分块旁写入 `manifest.json` 和 `ocr_audit.md`。

在运行中断后使用 `--resume`。仅当分块的图像、模式和自定义提示指纹仍然匹配时，才会复用该分块：

```bash
python3 scripts/long_screenshot_ocr.py work/chat.png --mode chat --resume -o work/chat.ocr.md
```

当需要在消耗视觉调用之前检查或调整分块时，使用 `--split-only`：

```bash
python3 scripts/long_screenshot_ocr.py work/page.png --split-only
```

如果默认参数产生的分块不理想，可使用 `--target-height`、`--min-height`、`--max-height` 或 `--overlap` 重新运行。保持足够的高度以保留局部上下文；除非源文本异常小，否则不要生成过小的OCR图块。

## 交付前验证

1. 从上到下阅读合并后的 Markdown，并将其开头和结尾行与源图像进行比较。
2. 打开 `ocr_audit.md`。对照两个相邻的 `chunk_*.png` 文件，审查每个标记为 `yes` 的边界。标记的边界使用了像素重叠或模糊文本匹配，不能盲目接受。
3. 检查发送者变化、时间戳、引用消息、表格行断行、代码缩进以及跨分块边界的段落。
4. 对于任何可疑文本，对相关分块或裁剪区域运行定向OCR：

   ```bash
   glance work/page_chunks/chunk_002.png --ocr "请仔细重新检查最后五行。"
   glance work/page_chunks/chunk_002.png --region X1,Y1,X2,Y2 --ocr
   ```

5. 保持可见的拼写和标点原样。对于仍然无法辨认的文本，写入 `[unreadable]`；不要静默猜测或进行编辑性修复。

## 输出约定

- 将合并后的 `.ocr.md` 文件作为主要结果返回。
- 在验证完成前保留分块目录；它是排序和边界决策的依据。
- 报告未解决的 `[unreadable]` 文本以及所有仍需人工审查的边界。
- 当截图的首尾边缘明显裁剪了内容时，不要声称已完成完整转录。