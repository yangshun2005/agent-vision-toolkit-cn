# 为 agent-vision-toolkit 做贡献

感谢您帮助改进 agent-vision-toolkit。我们欢迎有针对性的修复、测试、主机集成、可视化工作流以及文档改进。

参与即表示您同意遵守[行为准则](CODE_OF_CONDUCT.md)。

## 开始之前

1. 阅读 [README.md](README.md)，如果涉及部署变更，请阅读 [AGENT_INSTALL.md](AGENT_INSTALL.md)。
2. 在开始重复工作之前，先搜索现有的 Issue 和 Pull Request。
3. 在进行广泛的架构更改、新的协议行为或新的主机集成之前，请先开启一个 Issue。
4. 保持更改范围集中。不要将无关的重构与修复或功能混在一起。

## 项目范围与不变量

该仓库包含两层：

1. 五个独立的 CLI（`glance`、`ground`、`detect`、`trace` 和 `crop`）以及 `vision-skills` 技能。
2. 通过本地代理或单文件原生扩展实现的可选无缝集成。

贡献必须保持这些边界：

- 不要添加通用的单击安装程序、卸载程序、迁移框架或配置编辑框架。安装仍由代理主导且因机器而异。
- 保持代理仅使用标准库。可选的 CLI 依赖必须与代理隔离。
- 精确保留普通文本、模型名称和认证头。唯一固定的显示别名是 `gpt-5.2` 到 `deepseek-v4-flash`。
- 保持默认的 SSE 转发为增量式。仅缓冲需要兼容性处理的特定响应路径。
- 绝不记录请求体、图像、提示词、对话、API 密钥或其他凭据。
- 保持 Pi / Oh My Pi 和 OpenCode 扩展为单文件且自包含。失败必须保持可见，而不是被静默吞掉。
- 保持 `glance`、`ground` 和其他 CLI 独立于代理请求路径。
- 不要修改 `assets/` 下的效果图像，除非贡献专门针对这些资源。

## 开发环境设置

- Python 3.11 或更高版本是 Python 入口点和核心测试所必需的。
- Node.js 24 或更高版本，或 Bun，仅用于扩展测试。
- 核心测试会模拟其网络依赖；它们不需要真实的 `VISION_API_KEY`。
- 仅在测试需要这些工具时，才在隔离环境中安装可选依赖，如 Pillow 和 vtracer。

## 必需的验证

每次更改后运行核心检查：

```bash
python3 -m py_compile vision_proxy.py vision_client.py ground.py detect.py bin/glance bin/trace bin/crop
python3 tests/test_image_rewrite_shapes.py
python3 tests/test_focus_hint.py
python3 tests/test_anthropic_rewrite.py
python3 tests/smoke_test_proxy.py
python3 tests/test_vision_client.py
git diff --check
```

为每个更改的区域添加针对性的检查：

| 更改区域 | 附加检查 |
|---|---|
| `ground.py` / `bin/ground` | `python3 tests/test_ground.py` |
| `detect.py` / `bin/detect` | `python3 tests/test_detect.py` |
| `bin/glance` | `python3 tests/test_glance_region.py` |
| `bin/trace` | `python3 tests/test_trace.py` |
| `bin/crop` | `python3 tests/test_crop.py` |
| `skills/vision-skills/scripts/html_shot.py` | `python3 tests/test_html_shot.py` |
| `skills/vision-skills/scripts/dominant_colors.py` | `python3 tests/test_dominant_colors.py` |
| `skills/vision-skills/scripts/extract_fg.py` | `python3 tests/test_extract_fg.py` |
| `skills/vision-skills/scripts/long_screenshot_ocr.py` | `python3 tests/test_long_screenshot_ocr.py` |
| `extensions/**/*.ts` | `node tests/test_extensions.mjs` |

某些针对性测试在其外部依赖不可用时会跳过可选的 CLI 用例。请在 Pull Request 中提及任何跳过的检查。

## 文档

- 在更改共享产品行为或设置说明时，保持 `README.md` 和 `README_CN.md` 对齐。
- 保留现有的产品文案和结构；进行更改所需的最小补丁。
- 将详细的部署步骤放在 `AGENT_INSTALL.md` 中，可复用的可视化工作流放在技能参考中，评估证据放在 `research/` 中，而不是无限扩展 README。
- 对仓库文件使用相对链接，以便 Fork 和本地副本继续正常工作。

## Pull Request

Pull Request 应包含：

- 具体问题或用例；
- 所选实现方案的简明解释；
- 确切的验证命令和结果；
- 仅在能实质性验证视觉行为时才提供截图或测试夹具；
- 针对用户可见更改的文档更新。

维护者可能会要求将宽泛的 Pull Request 拆分为更小的更改，或先将范围外的想法移至讨论中。