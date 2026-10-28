# Linked PRD Prototype

一个用于生成“左侧产品原型 + 右侧 PRD”的 Codex Skill。输入一段产品需求描述后，Skill 会生成可直接打开的 HTML 工作台，并让原型中的功能按钮与 PRD 对应章节联动。

## 能力

- 根据需求描述同时生成 PRD 和可交互原型
- 原型点击功能、按钮或导航项时，定位到右侧对应的 PRD 章节
- PRD 支持目录结构、章节展开和收起
- 原型区和 PRD 区各自滚动，互不影响
- 原型页面支持响应式布局，适配桌面和窄屏
- 工作台右上角支持分别下载 PRD Markdown 文件和原型 HTML 文件

## 使用方式

使用本 Skill 时，先提供需求描述，例如：

> 创建一个客户工单管理系统，支持工单列表、工单详情、状态流转、分配处理人和数据统计。

Skill 会根据需求生成：

- `workbench.html`：联动工作台，左侧为原型，右侧为 PRD
- `prd.md`：可单独保存和编辑的 PRD Markdown 文件
- `prototype.html`：可单独打开的原型 HTML 文件
- `feature-map.json`：原型功能与 PRD 锚点的映射关系

## 目录结构

```text
linked-prd-prototype/
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
├── assets/
│   └── linked-workbench-starter.html
└── references/
    └── interaction-contract.md
```

## 交互约定

原型和 PRD 使用稳定的 `featureId` 建立关联。原型交互元素通过 `data-feature-id` 标记，PRD 对应章节通过同名锚点标记。工作台监听点击事件后，只滚动 PRD 面板，并高亮目标章节。

详细约定见 [`references/interaction-contract.md`](references/interaction-contract.md)。

## 开始定制

如需调整工作台的视觉风格或原型布局，可基于 [`assets/linked-workbench-starter.html`](assets/linked-workbench-starter.html) 修改。生成新项目时应保留独立面板滚动、目录展开收起、下载按钮和 `featureId` 联动逻辑。
