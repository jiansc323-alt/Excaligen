# Excaligen

> **Excali**draw + **Gen**erate — Turn text, articles, or images into editable Obsidian Excalidraw diagrams with one click.

[English](#english) | [中文](#chinese)

---

<a name="english"></a>

## 🇬🇧 English

### What is Excaligen?

**Excaligen** (Excalidraw + Generate) is an automated pipeline that turns natural language or images into editable Excalidraw diagrams:

```
Your input (text / article / image)
        │
        ▼
    LLM (Large Language Model)
  ← system-prompt.md + chart-specs.md
        │
        ▼
  ExcalidrawElementSkeleton[] JSON
        │
        ▼
  skeleton-to-file.py (bridge script)
        │
        ▼
  .excalidraw file ──→ Drop into Obsidian, edit freely
```

Describe what you want, and Excaligen handles the rest. The generated `.excalidraw` file can be placed directly into your Obsidian vault and opened with the Excalidraw plugin.

### Quick Start

#### 1. Prerequisites

- An LLM with tool-use / structured-output capability (Claude, GPT, etc.)
- Python 3.8+
- [Obsidian](https://obsidian.md/) + [Excalidraw plugin](https://github.com/zsviczian/obsidian-excalidraw-plugin)

#### 2. Generate the Diagram JSON

Send `prompts/system-prompt.md` as the system message to your LLM, followed by your request:

> "Draw a user login flowchart"

For specific chart types, inject the relevant section from `prompts/chart-specs.md` into the prompt.

The LLM returns a pure JSON array (ExcalidrawElementSkeleton[]).

#### 3. Convert to .excalidraw

```bash
# From file
python bridge/skeleton-to-file.py output.json -o my-chart.excalidraw

# From stdin
echo '[{"type":"rectangle","x":100,"y":100,"label":{"text":"Start"}}]' | python bridge/skeleton-to-file.py -o my-chart.excalidraw
```

#### 4. Open in Obsidian

Place the `.excalidraw` file anywhere in your Obsidian vault. Click to open and edit with the Excalidraw plugin.

### Supported Chart Types (22)

| Type | Identifier | Type | Identifier |
|------|-----------|------|-----------|
| Flowchart | `flowchart` | Swimlane | `swimlane` |
| Mind Map | `mindmap` | Concept Map | `concept` |
| Org Chart | `orgchart` | Fishbone / Ishikawa | `fishbone` |
| Sequence Diagram | `sequence` | SWOT Analysis | `swot` |
| UML Class Diagram | `class` | Pyramid | `pyramid` |
| ER Diagram | `er` | Funnel | `funnel` |
| Gantt Chart | `gantt` | Venn Diagram | `venn` |
| Timeline | `timeline` | Matrix | `matrix` |
| Tree Diagram | `tree` | Infographic | `infographic` |
| Network Topology | `network` | Data Flow Diagram | `dataflow` |
| Architecture Diagram | `architecture` | State Diagram | `state` |

Each type has precise visual specifications (shape constraints, color schemes, layout guides) defined in `prompts/chart-specs.md`.

### Project Structure

```
Excaligen/
├── bridge/
│   └── skeleton-to-file.py    # Core bridge: JSON → .excalidraw
├── prompts/
│   ├── system-prompt.md       # LLM system prompt (element spec, design principles)
│   └── chart-specs.md         # Visual specs for 22 chart types
└── README.md
```

### Key Features

- **Zero dependencies**: `skeleton-to-file.py` uses only the Python standard library
- **Smart enrichment**: Auto-fills `id`, `seed`, `version`, and other runtime fields for skeleton elements
- **Label splitting**: Converts LLM-friendly `label` format into Obsidian Excalidraw's required `boundElements` + `containerId` format
- **Binding resolution**: Handles arrow `start`/`end` bindings (by `type` or `id`)
- **CJK text support**: Text dimension estimation supports both Chinese and English characters
- **22 chart types**: Covering flowcharts, architecture diagrams, mind maps, and more

### FAQ

**Q: Text positioning looks wrong in Excalidraw?**

A: Make sure your prompt instructs the LLM to use the `label` field rather than writing `text` directly onto container elements. The bridge script splits `label` into standalone text elements bound via `containerId` — the format Obsidian Excalidraw expects.

**Q: Fonts look blurry?**

A: Use `fontFamily: 2` (Helvetica, sans-serif) for technical diagrams. `fontFamily: 1` (Virgil, hand-drawn) is only suitable for sketch-style drawings.

**Q: Can I generate hand-drawn style?**

A: Yes. Request `roughness: 1` in your prompt. The default `roughness: 0` (crisp edges) is designed for formal documents.

---

<a name="chinese"></a>

## 🇨🇳 中文

### 这是什么？

**Excaligen** 是一条从「自然语言/图片」到「Excalidraw 可编辑图表」的自动化流水线：

```
你的需求（文字 / 文章 / 图片）
        │
        ▼
    LLM（大语言模型）
  ← system-prompt.md + chart-specs.md
        │
        ▼
  ExcalidrawElementSkeleton[] JSON
        │
        ▼
  skeleton-to-file.py（桥接脚本）
        │
        ▼
  .excalidraw 文件 ──→ 拖入 Obsidian 直接编辑
```

你只需要描述你想要什么图，剩下的全部自动完成。生成的 `.excalidraw` 文件可以直接放入 Obsidian vault，用 Excalidraw 插件打开、编辑、继续美化。

### 快速开始

#### 1. 准备工作

- 一个支持函数调用的大语言模型（Claude、GPT 等）
- Python 3.8+
- [Obsidian](https://obsidian.md/) + [Excalidraw 插件](https://github.com/zsviczian/obsidian-excalidraw-plugin)

#### 2. 生成图表 JSON

将 `prompts/system-prompt.md` 作为系统提示词发送给 LLM，然后附上你的需求，例如：

> "画一张用户登录流程图"

如果生成特定类型的图表，可以将 `prompts/chart-specs.md` 中对应类型的视觉规范也一并注入。

LLM 会返回一个纯 JSON 数组（ExcalidrawElementSkeleton[]）。

#### 3. 转换为 .excalidraw 文件

```bash
# 方式一：从文件读取
python bridge/skeleton-to-file.py output.json -o my-chart.excalidraw

# 方式二：管道输入
echo '[{"type":"rectangle","x":100,"y":100,"label":{"text":"开始"}}]' | python bridge/skeleton-to-file.py -o my-chart.excalidraw
```

#### 4. 在 Obsidian 中打开

将生成的 `.excalidraw` 文件放入 Obsidian vault 任意位置，点击即可用 Excalidraw 插件打开编辑。

### 支持的图表类型（22 种）

| 类型 | 标识 | 类型 | 标识 |
|------|------|------|------|
| 流程图 | `flowchart` | 泳道图 | `swimlane` |
| 思维导图 | `mindmap` | 概念图 | `concept` |
| 组织架构图 | `orgchart` | 鱼骨图 | `fishbone` |
| 时序图 | `sequence` | SWOT 分析 | `swot` |
| UML 类图 | `class` | 金字塔图 | `pyramid` |
| ER 图 | `er` | 漏斗图 | `funnel` |
| 甘特图 | `gantt` | 韦恩图 | `venn` |
| 时间线 | `timeline` | 矩阵图 | `matrix` |
| 树形图 | `tree` | 信息图 | `infographic` |
| 网络拓扑图 | `network` | 数据流图 | `dataflow` |
| 架构图 | `architecture` | 状态图 | `state` |

每种类型都有精确的视觉规范（形状约束、配色方案、布局指南），定义在 `prompts/chart-specs.md` 中。

### 目录结构

```
Excaligen/
├── bridge/
│   └── skeleton-to-file.py    # 核心桥接脚本：JSON → .excalidraw
├── prompts/
│   ├── system-prompt.md       # LLM 系统提示词（元素规范、设计原则）
│   └── chart-specs.md         # 22 种图表类型的视觉规范
└── README.md
```

### 核心特性

- **零依赖**：`skeleton-to-file.py` 仅使用 Python 标准库，无需安装任何第三方包
- **智能补全**：自动为 skeleton 元素补全 `id`、`seed`、`version` 等运行时字段
- **标签拆分**：将 LLM 友好的 `label` 格式自动转换为 Obsidian Excalidraw 要求的 `boundElements` + `containerId` 格式
- **绑定解析**：自动处理箭头的 `start`/`end` 绑定（支持按 `type` 或 `id` 绑定）
- **中英文混排**：文本尺寸估算同时支持中英文字符
- **22 种图表类型**：覆盖流程图、架构图、思维导图等常见场景

### 常见问题

**Q: 为什么生成的文件在 Excalidraw 中文字位置不对？**

A: 确认 prompt 中要求 LLM 使用 `label` 字段而非直接写 `text` 到容器元素上。桥接脚本会自动将 `label` 拆分为独立的文本元素并通过 `containerId` 绑定——这是 Obsidian Excalidraw 插件要求的格式。

**Q: 字体看起来模糊？**

A: 技术文档类图表推荐 `fontFamily: 2`（Helvetica 无衬线体）。`fontFamily: 1`（Virgil 手绘体）仅适合手绘风格草图。

**Q: 可以生成手绘风格吗？**

A: 可以。在 prompt 中要求 `roughness: 1` 即可。默认 `roughness: 0`（直角直边）适合正式文档。

---

## License

MIT
