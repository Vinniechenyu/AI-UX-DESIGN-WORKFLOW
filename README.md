# AI-UX Design Workflow

AI 时代设计工作流与 Agent 体验设计的方法库，整理方法框架、交互模式、实践复盘和可复用工具，帮助设计从模糊输入走向可讨论、可验证、可交付的方案。

**[在线浏览](https://vinniechenyu.github.io/AI-UX-DESIGN-WORKFLOW/)** · [内容导航](#内容导航) · [本地预览](#本地预览) · [维护与扩展](#维护与扩展)

## 这里关注什么

- **用 AI 做设计**：如何理解业务与代码，把需求转成原型，再通过规范、状态补全和验收形成交付。
- **为 Agent 设计体验**：如何把运行机制转化为用户能理解、能介入、能验证的人机协作方式。
- **让方法可以复用**：将案例、设计判断和交付流程整理为网页材料、模式卡与独立 Skill。

适合关注 AI 产品、Agent 工作台、开发者工具和 AI Infra 的产品经理、体验设计师及前端开发者阅读。

## 内容导航

当前收录五份仓内专题材料，以及两个独立维护的 Skill 入口。首页支持分类筛选、关键词搜索和深浅色切换。

| 材料 | 主要内容 | 入口 |
| --- | --- | --- |
| **算子开发领域设计模式** | PyPTO Toolkit 的工具交互案例，加上 AI 辅助的体验维度、交互模式与界面卡片，以五条领域原则贯穿 | [在线阅读](https://vinniechenyu.github.io/AI-UX-DESIGN-WORKFLOW/operator-domain-patterns/) · [源码](operator-domain-patterns/index.html) |
| **算子开发 Agent 交互设计模式** | 从领域原语到六种交互模式，再到运行维度、模式转场与界面设计 | [在线阅读](https://vinniechenyu.github.io/AI-UX-DESIGN-WORKFLOW/operator-agent-patterns/) · [源码](operator-agent-patterns/index.html) |
| **Agent 体验架构剖面** | 从 Agent 运行机制推导协作需求、控制点与证据表达，结合算子开发和真实任务案例 | [在线阅读](https://vinniechenyu.github.io/AI-UX-DESIGN-WORKFLOW/agent-ux-anatomy/) · [源码](agent-ux-anatomy/index.html) |
| **通用 AI Design Workflow 框架** | 意图收敛、知识资产、生成流程与交付验收，组织业务无关的设计工作流 | [在线阅读](https://vinniechenyu.github.io/AI-UX-DESIGN-WORKFLOW/ai-design-workflow-report/) · [源码](ai-design-workflow-report/index.html) |
| **AI 时代的设计工作流 · 2026-06 阶段** | 从业务理解到交互原型的实践复盘，以及工具接力与设计资产沉淀 | [在线阅读](https://vinniechenyu.github.io/AI-UX-DESIGN-WORKFLOW/ai-design-workflow-2026-06/) · [源码](ai-design-workflow-2026-06/index.html) |

### 算子开发领域设计模式

把算子开发中反复出现的问题整理为对象清晰、操作可控、证据可查的交互方式：

- **工具交互 · 7 张案例**：计算图定位与上下游排查、复合搜索、差异定位、控制流下钻、代码／计算图／泳道联动、泳道测量与统计、GM 内存分配，均来自 PyPTO Toolkit。
- **AI 辅助 · 设计提案**：可感知、可理解、可控制、可信任四个体验维度；六种交互模式；规格确认、阶段进度、复现对照、偏差范围、运行与结构对照、修改审阅、复测对比七张界面卡片。
- **五条领域原则**：用覆盖矩阵对应到每一页，AI 体验维度与领域原则按颜色对应。

### 算子开发 Agent 交互设计模式

围绕算子开发中的上下文、任务推进与实验验收，建立可组合的设计模式：

- **七个领域原语**：对象锚点、证据链、验证阶梯、断言分型、可比守卫、过期传播与控制语义。
- **六种交互模式**：原位执行、上下文协作、任务委托、流程引导、产物共创与评审决策，配有界面骨架示意。
- **四个运行维度**：异步执行、主动诊断、并行编排与授权边界。
- **四个模式转场**：带上下文升级、意图结晶、证据收敛与结果回写。
- **落地方法**：界面级模式、用户旅程、场景选型、建设顺序与指标口径。

### Agent 体验架构剖面

从“Agent 怎样运行”出发，逐层推导“人怎样与 Agent 协作”：

- 七层运行架构、工具调用循环、上下文组装、等待与恢复。
- 体验需求、协作时刻、任务生命周期、注意力分层、打断与信任校准。
- 控制点、决策包、证据组织，以及对 Agent 友好的输入。
- 算子开发中的取证环节、人机调查闭环与可视化模式。
- KernelPCA 修复任务的输入、执行记录、补丁与独立评测分析。

配套底稿保留案例来源与推导边界：

- [hw-native-sys 工程体系与公开协作案例底稿](agent-ux-anatomy/sources/hw-native-sys-case-notes.md)
- [KernelPCA 修复任务的真实 Agent 运行记录与分析](agent-ux-anatomy/sources/real-agent-task-kpca.md)

其中的任务回放展示已有记录，不会重新执行命令；设计建议与原始运行事实在材料中分别标注。

### 通用 AI Design Workflow 框架

讨论如何从生成结果走向可交付设计，主要包括：

- 知识、资产、流程与判断标准构成的交付基础。
- Human-in-the-AI-Loop：意图表达与结果评估两个协作环节。
- 五层泳道与四阶段执行架构。
- Spec Drives Gen-UI 知识底座与九维意图收敛。
- 状态补全、汇报图、交互 Demo 与设计说明等交付要求。

### AI 时代的设计工作流 · 2026-06 阶段

以阶段性实践说明两类高频场景：学习代码仓和业务材料，以及生成可对齐的交互原型。材料记录了 Claude、Codex 等工具如何接力完成需求理解、模式探索、HTML／Markdown 草稿、规范落地与人工审核，并展示从项目中沉淀设计资产的方法。

## 相关 Skills

以下工具在独立仓库维护，本仓库提供导航入口。安装和使用方法以各自仓库的说明为准。

| Skill | 用途 | 仓库 |
| --- | --- | --- |
| **Report Deck Skill** | 将分散材料组织成决策导向的 16:9 HTML 汇报，覆盖叙事、组件选型、实现与交付校验 | [report-deck-skill](https://github.com/Vinniechenyu/report-deck-skill) |
| **Derive Experience Design Skill** | 从模糊问题或具体设计推导问题、体验目标、策略、设计点与用户收益 | [Derive-Experience-Design](https://github.com/Vinniechenyu/Derive-Experience-Design) |

## 建议阅读路径

| 你的目标 | 建议路径 |
| --- | --- |
| 为 Agent 或算子开发工具设计交互 | 先看「算子开发 Agent 交互设计模式」建立选型框架，再用「Agent 体验架构剖面」理解机制和控制点 |
| 建立自己的 AI 设计工作流 | 先看「通用 AI Design Workflow 框架」，再对照「2026-06 阶段」的具体实践 |
| 形成设计策略或制作汇报材料 | 结合专题内容，使用 Derive Experience Design Skill 或 Report Deck Skill |

## 本地预览

仓库由静态 HTML、CSS、JavaScript、图片和 Markdown 组成，无需安装前端依赖或运行构建步骤。使用 Git 和 Python 3 即可在本地预览：

```bash
git clone https://github.com/Vinniechenyu/AI-UX-DESIGN-WORKFLOW.git
cd AI-UX-DESIGN-WORKFLOW
python3 -m http.server 8000 --bind 127.0.0.1
```

在浏览器打开 [本地首页](http://127.0.0.1:8000/)，按 `Ctrl+C` 停止服务。也可以用任意静态文件服务器托管仓库根目录。

部分页面使用 Google Fonts 或 CDN 字体，字体加载依赖网络。分享或迁移材料时，需要一起保留对应的图片目录。

### 阅读与演示

- 首页通过分类和搜索查找材料，点击卡片进入专题或外部 Skill 仓库。
- 「算子开发 Agent 交互设计模式」和「Agent 体验架构剖面」支持翻页、概览、全屏和放大；使用页面控件，或按 `O` 打开概览、`F` 切换全屏、`Z` 切换放大。
- 「通用 AI Design Workflow 框架」支持滚动和方向键／Page Up／Page Down 翻页。
- 「2026-06 阶段」按纵向页面阅读，案例图片可点击放大。

## 仓库结构

```text
AI-UX-DESIGN-WORKFLOW/
├── README.md                              # 仓库说明与内容导航
├── index.html                             # 材料库首页与卡片配置
├── assets/                                # 首页材料预览图
├── operator-agent-patterns/
│   └── index.html                         # 算子开发 Agent 交互设计模式
├── agent-ux-anatomy/
│   ├── index.html                         # Agent 体验架构剖面
│   └── sources/                           # 案例来源与分析底稿
│       ├── hw-native-sys-case-notes.md
│       └── real-agent-task-kpca.md
├── ai-design-workflow-report/
│   └── index.html                         # 通用 AI 设计工作流
└── ai-design-workflow-2026-06/
    ├── index.html                         # 阶段性工作流复盘
    └── assets/ai-design-workflow/          # 案例截图与标识素材
```

## 维护与扩展

首页的内容配置集中在根目录 [index.html](index.html) 中：

| 配置 | 作用 |
| --- | --- |
| `ITEMS` | 材料标题、简介、标签、预览图和跳转地址；数组顺序决定卡片展示顺序 |
| `CATEGORIES` | 分类名称、标识和颜色 |
| `STATUS` | 「可阅读」「可交互」「可复用技能」等状态标签 |

新增材料时：

1. 为仓内专题创建独立目录和 `index.html`，将配套资源放入对应目录。
2. 在根目录 `assets/` 添加首页预览图。
3. 在 `ITEMS` 中添加条目，使用现有分类与状态；外部链接按已有条目设置 `ext: true`。
4. 同步更新 README 的内容导航，检查相对路径、图片、搜索和分类筛选。
5. 更新已有预览图时，如果保持同一文件名，可参考现有条目同步调整 `preview` URL 的版本参数。

案例材料应保留来源，并区分观察事实、设计推导与待验证假设；涉及运行结果时，注明验证范围和条件，便于后续复查与更新。
