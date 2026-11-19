# hw-native-sys：工程体系与公开协作案例底稿

核验日期：2026-11-19。本底稿保留 hw-native-sys 研究背景与第三章 RMSNorm 源码入口。公开 PR 只有协作片段，已从第四章移出；第四章当前采用 [KernelPCA 完整任务运行](real-agent-task-kpca.md)。

本底稿来自公开源码、PR 讨论与 GitHub Actions 元数据。本轮没有编译项目、运行测试或上板。
下文 PR #3432 是研究阶段的公开审查与修复片段；没有完整编码 Agent 会话，不重建不可见的工具调用、Token 消耗或用户信心。当前案例 D 指 KernelPCA 运行，不再指本 PR。

## 叙事位置

- 第三章开头：RMSNorm 源码与执行入口 → 原有四类取证环节。
- 第三章继续完成算子体验映射、调查闭环、交接包与可视化 Pattern。
- 全稿最后独立第四章「一次真实任务：从输入到交付」：KernelPCA 用户输入 → 全部 30 步执行 → 补丁与验收 → 控制点 → 五个亲和条件。
- 案例 A / B 仍为独立协作示例，C 为独立编译前估算；新 RMSNorm 源码入口与实采方案不沿用它们的参数或数据。

## 仓库分工与来源

| 仓库 | 分工 | 来源 |
|---|---|---|
| pypto-serving | 模型服务、请求调度、KV Cache、HTTP API | https://github.com/hw-native-sys/pypto-serving/blob/8dbb2e7faff1c9a260c10710f01614a7ea02d1a7/README.md |
| pypto-lib | 模型、算子、golden 验证 | https://github.com/hw-native-sys/pypto-lib/blob/3daf7cbb754af48b49957e365a78fae0f467e33c/README.md |
| pypto | Python DSL、IR、Pass、代码生成 | https://github.com/hw-native-sys/pypto/blob/6375c408c0f485ed695c0dfecee43f1a473498d4/docs/en/dev/ir/00-overview.md |
| PTOAS | PTO IR 解析、优化与底层代码生成 | https://github.com/hw-native-sys/PTOAS/blob/master/README.md |
| pto-isa | Tile 虚拟指令实现 | https://github.com/hw-native-sys/pto-isa |
| simpler | 任务依赖、调度、Worker、设备生命周期 | https://github.com/hw-native-sys/simpler/blob/ef8ee09c0b4b5912e959c42e8bc892e04ca7f5f0/README.md |
| pypto-tools | 工具分发与文档；VS Code 扩展源码不在本仓 | https://github.com/hw-native-sys/pypto-tools/blob/c96ceddd6e4413e97480e125d385df661c9eb681/README.zh.md |
| pypto-skills | 跨仓 Agent 技能 | https://github.com/hw-native-sys/pypto-skills/blob/32425375690df33944eb95a4cb00c6b5f12d931a/README.md |

仓库按工作过程理解，不宣称所有仓库是线性依赖。PyPTO 生成内核和编排两类产物。
编译与验证流程：https://github.com/hw-native-sys/pypto-lib/blob/3daf7cbb754af48b49957e365a78fae0f467e33c/docs/run-and-validate/compile-runtime-workflow.md

硬件 L0–L6 与本稿 Agent 七层不同：
https://github.com/hw-native-sys/simpler/blob/ef8ee09c0b4b5912e959c42e8bc892e04ca7f5f0/docs/hierarchical-level-runtime.md

## RMSNorm 源码入口

https://github.com/hw-native-sys/pypto-lib/blob/3daf7cbb754af48b49957e365a78fae0f467e33c/examples/intermediate/rms_norm.py

- ROWS=512，HIDDEN=512；FP32 输入/输出。
- ROW_TILE=64，HIDDEN_TILE=64；EPS=1e-6。
- `pl.parallel` 按行分块；第一个列循环累计平方和；第二个列循环归一化并乘 gamma。
- golden 为 PyTorch 参考；`run` 传入 `rtol=1e-2, atol=1e-2`。
- 两遍遍历来自源码，不是本轮发现的编译器变换，也没有实测性能结论。

## 研究背景：PR #3432（不在当前案例章展示）

任务：增加命名核间同步操作 `set_intra_block` / `wait_intra_block`。
PR：https://github.com/hw-native-sys/pypto/pull/3432

所有下列时间均为 UTC。时间线是选取的公开节点，不代表完整活动或 Agent 工作耗时。

| 时间 | 参与者与提交 | 可确认事实 | 原始来源 |
|---|---|---|---|
| 2026-11-15 13:54:11 | github-actions[bot]，自建 Codex 工作流；7181fea | 报告未发现 P0–P2，未执行测试 | https://github.com/hw-native-sys/pypto/pull/3432#pullrequestreview-5911047718 |
| 2026-11-16 03:34:03 | chatgpt-codex-connector[bot]；b100c03 | P1：命名事件可能被 Pipeline IO 重排越过；说明根据源码推断、未运行验证 | https://github.com/hw-native-sys/pypto/pull/3432#discussion_r4581724180 |
| 2026-11-16 04:53:10 | 开发者 zzjc1234；e5e298b | 回复修复并报告 720 passed / 1 xfailed；该时点 TaskQueue 维护阻塞设备分配 | https://github.com/hw-native-sys/pypto/pull/3432#discussion_r4582092175 |
| 2026-11-16 04:57:30 | github-actions[bot]，自建 Codex 工作流；e5e298b | 复审确认此前顺序问题已处理；该轮未发现 P0–P2；未执行测试 | https://github.com/hw-native-sys/pypto/pull/3432#pullrequestreview-5915220493 |

修复提交已读取其源码差异：
https://github.com/hw-native-sys/pypto/commit/e5e298b021274a7fee8dc3ea8d9cdea28ee0920c

`SkewCrossCoreMutator` 统计命名事件，发现循环包含事件时保守降为 Sequential，包含 stage=1 与嵌套控制。
图示只表达生产者 store→set、消费者 wait→load 的必要顺序，以及审查指出的重排风险；不是设备实测时序图。

对应成功的 Actions：
https://github.com/hw-native-sys/pypto/actions/runs/43806679098

已读取其 jobs：prepare、review、publish 成功。核验时 artifact 列表仅保留路由标识 `codex-target-43806679098-1`；没有取得完整 review events。
工作流将 Codex JSONL 重定向到本地文件，完成后删除；结果和讨论快照短期保存，持久化会话位于运行器卷。
https://github.com/hw-native-sys/pypto/blob/6375c408c0f485ed695c0dfecee43f1a473498d4/.github/workflows/codex-review.yml

### 展示边界

- 不把两套 Codex 审查入口说成同一个连续会话。
- 不把开发者账号提交的修复说成已证实由 Agent 独立完成。
- 720 / 1 来自作者回复，未独立重跑。
- “硬件受阻”只描述选取时点；PR 后续包含其他硬件验证报告，不能称其最终仍受阻。
- “未发现问题”绑定审查快照，不等于证明程序没有问题。
- 版本绑定、来源展示、等待对象等设计回应是案例推导，未做用户研究验证。

## 其他材料与实采入口

- 仓内 Gemini 设计问答，没有工具执行证据：
  https://github.com/hw-native-sys/pypto_top_level_documents/blob/31bbf531444e399f256b298ff5804d3e58ee86ce/Gemini_conversation.md
- mini-swe-agent 客户端 PR #277：核验时 open、非 draft、未合并。声明当前 CPU 检查通过，NPU smoke 未重跑：
  https://github.com/hw-native-sys/pypto-serving/pull/277
- 客户端 README 声明每次会话保存 `trajectory.json` 和配置的验证记录，输出是本地 artifact、不提交到 Git：
  https://github.com/zmnobug/pypto-serving/blob/d8f61811493e495b9b8e75ea6b473ba6ddbe96f5/examples/mini_swe_agent/README.md

下一次 RMSNorm 实采仅为方案：固定 commit、环境、输入和阈值；记录原始会话、工具输出、人工干预、diff、编译产物、设备记录及数值/性能验收；接受无收益或失败结论。
