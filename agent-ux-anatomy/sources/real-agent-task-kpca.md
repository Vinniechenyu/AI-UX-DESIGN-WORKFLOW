# 案例 D：一次 KernelPCA 修复任务的真实 Agent 运行

核验日期：2026-11-19。对应演示稿最后的第四章（34–40 页）。

## 时序图呈现方式

- 第 36 页按“任务输入—Agent—工具 / 仓库—独立评测”绘制时序。将 30 步汇总为 6 个阶段：01–05、06–10、11–15、16–24、25–29、30；点击阶段后可选择其中每一步，查看命令首行与执行反馈摘要。
- 动态回放支持播放 / 暂停、1–30 步进度拖动与 1× / 2× / 4× 动画速度。移动标记区分发出调用和收到返回，右侧同步显示该步已记录的命令与结果；第 30 步之后单独演示补丁进入独立评测。它仅回放已有记录，不会重新执行命令。
- 切换页面、打开概览或页面不可见时暂停；开启减少动态效果时，保留步骤推进和高亮，省去标记的平移。所有动画间隔均为讲解节奏，不推断真实调用时长。
- 实线表示请求，虚线表示返回，纵向仅表示顺序，不编码实际时长。阶段中的成对箭头汇总该阶段的多轮调用，不将 30 次命令缩减成 6 次调用。
- 第 37 页可切换 03–05、12–14、29–30 三段局部时序。独立评测显示在 Agent 提交补丁之后，不伪造评测结果回填给已结束的 Agent。
- 正负号矩阵从步骤 03 / 05 的原始 observation 提取：每次打印结果中第 1 个样本的 7 维符号。两个矩阵都展示前 3 次运行；步骤 05 实际共运行 10 次。蓝色 + 为正值，橙色 − 为负值，不比较数值大小。
- 下方虚线框标为“设计建议”，不代表原始运行发生过人工确认。所有 30 步的文字索引继续保留在本底稿末尾。

## 选型与范围

主线是一个具体任务：修复 scikit-learn KernelPCA 重复运行时输出符号不一致的问题。选择该案例是因为同一次运行同时有任务输入、完整命令/反馈、最终 diff 和独立评测日志，且包含未复现、路径错误、命令格式纠正、编辑遗漏等真实过程。

SWE-agent 是执行框架；Claude 3.5 Sonnet 是提交目录与 README 标注的模型。这里不是框架介绍，也不据此推荐它优于其他 Agent。

这是在评测环境中对历史真实 Issue 的自动执行，**不是原 Issue 作者与 Agent 的实时对话**。没有中途人工确认、暂停、回滚或上线的记录。演示中的人工介入与产品控制建议都标为“设计推导”。没有生成虚构耗时、信心曲线或审批事件。

提交目录中的日期、元数据模型标签和轨迹内环境日期并不完全对应；本稿不推断精确运行日期或模型快照。`history` 中带 `is_demo: true` 的演示不计入本次任务；执行步骤只取 `trajectory` 的 30 个条目，且只分析 action / observation，不展示推理文字。

## 可核对的原始来源

- [用户 Issue #8798](https://github.com/scikit-learn/scikit-learn/issues/8798)：异常描述、调用代码、预期输出、三组实际结果、用户报告的版本 0.18.1。
- [SWE-bench 提交目录](https://github.com/SWE-bench/experiments/tree/main/evaluation/lite/20240620_sweagent_claude3.5sonnet)：SWE-agent + Claude 3.5 Sonnet。
- [完整 trajectory](https://swe-bench-submissions.s3.amazonaws.com/lite/20240620_sweagent_claude3.5sonnet/trajs/scikit-learn__scikit-learn-13241.traj)：实际任务输入、30 步命令及返回、最终 submission。
- [最终 patch.diff](https://swe-bench-submissions.s3.amazonaws.com/lite/20240620_sweagent_claude3.5sonnet/logs/scikit-learn__scikit-learn-13241/patch.diff)：与轨迹中的 submission 去除首尾空白后逐字一致。
- [独立评测 eval.sh](https://swe-bench-submissions.s3.amazonaws.com/lite/20240620_sweagent_claude3.5sonnet/logs/scikit-learn__scikit-learn-13241/eval.sh)：恢复基线测试，再应用评测测试补丁并执行。
- [report.json](https://swe-bench-submissions.s3.amazonaws.com/lite/20240620_sweagent_claude3.5sonnet/logs/scikit-learn__scikit-learn-13241/report.json)：`resolved: true`，1 项 FAIL_TO_PASS、54 项 PASS_TO_PASS，均无失败。
- [test_output.txt](https://swe-bench-submissions.s3.amazonaws.com/lite/20240620_sweagent_claude3.5sonnet/logs/scikit-learn__scikit-learn-13241/test_output.txt)：公开评测输出 `55 passed`。本轮未安装或重跑该版本 scikit-learn。

实例 ID `scikit-learn__scikit-learn-13241` 中的 13241 对应历史修复 PR，不是用户 Issue 号。历史 [PR #13241](https://github.com/scikit-learn/scikit-learn/pull/13241) 由人类开发者提交，不能当作这次 Agent 补丁的合并记录。

## 输入和执行规则

原始用户希望相同数据多次降维得到一致结果；提供 `KernelPCA(n_components=7, kernel='rbf', copy_X=False, n_jobs=-1)` 和三组符号不同的输出，但没有给出数据集 X。

评测任务追加：在仓库根目录解决问题；可编辑文件和运行检查；每次只执行一个命令并等待反馈；不用交互式终端程序；完成后通过 `submit` 收集补丁。这些是评测环境的指令，不冒充用户原话。

## 关键证据与推导边界

1. 步骤 03：100 × 10、重复 3 次，打印结果一致。步骤 04–05：1000 × 50、重复 10 次，出现符号翻转。这里的 shape 属于 Agent 构造的复现数据，不是用户数据集。
2. 步骤 06 / 25：搜索不存在的 `src` / `tests` 目录；随后读取目录并在 `sklearn` 中找到文件。
3. 步骤 12 / 16：将文件路径传给只接受行范围的 `edit`，返回用法。后续通过 open + 行范围完成编辑。
4. 多次编辑返回 `/root/commands/_split_string` 的 SyntaxError，同时也返回更新后的文件和写入成功说明。因此应描述为辅助命令报错与文件写入并存，不能简单称“编辑没有执行”。
5. 步骤 11：在 fit_transform 中按每列绝对值最大的元素决定是否翻转整个特征向量。
6. 步骤 15：十次打印的符号一致。脚本比较的是 `abs(result_a) - abs(result_b)`，并只取每次输出的第一行；这些证据不能证明所有输入均正确。
7. 步骤 29：新增测试同样先对结果取绝对值；可由代码推出它无法检测纯符号翻转。此外，`edit 233:233` 替换了旧测试中的 `assert_equal(train_score, 1.0)`。轨迹后续直接 submit，没有新增测试的执行记录。
8. 步骤 30：提交 3 个文件的 diff，包括复现脚本、实现和测试。只证明补丁被收集，不证明人工审核、合并或上线。
9. 独立评测会恢复基线测试文件，再加入自己的测试。它重复运行 arpack / dense 并比较带符号输出，报告 55 passed。因此评测通过不等于 Agent 修改的测试文件质量已获确认。

## 体验设计推导

- 将输入、调整条件、动作、反馈、产物和验收关联到同一个任务；所有结论保留来源。
- 把写入状态与检查状态分开，避免遇到混合错误时重复产生副作用。
- 授权预算内的读写和命令格式纠正可以继续；范围、资源或交付风险变化时，提供明确决定点。
- 交付前检查完整 diff、保留旧测试、对原始问题做独立验收；合并或发布遵循授权流程。
- 五个亲和条件是分析框架：本例部分体现命令参数约定、路径/行号定位、有限输出窗口、可解释反馈和独立评测；业务 schema、稳定对象 ID、完整预算控制和整体回滚不能据此宣称已经实现。

## 取证校验

已核对原始 trajectory 的动作条数为 30，最终 patch 与评测保存的 patch 内容一致。以下 SHA-256 固定本轮核验的文件内容：

| 文件 | SHA-256 |
|---|---|
| patch.diff | `5f156dc0a3c9e6af23b2d56f3bc5c0b883c9e6f35f0d1d7e14ae61f0322e33b6` |
| report.json | `389bfd2271e671a557c4139db89ba579580381113a2609c9777e2d154d88d7c3` |
| test_output.txt | `b22ec2b59c498ec7939a2090a8bd8d0ca5936f2bbb0dd17d64ce6de7c38795be` |
| eval.sh | `ad5e8c891a876f65c6e3abdbd3b7a9ad421462560321c42804c8608c8c11eb37` |

## 全部动作索引

| 步骤 | 动作摘要 | 返回与状态摘要 |
|---|---|---|
| 01 | create 复现脚本 | 创建 reproduce_kpca_issue.py。 |
| 02 | edit 写入初版 | 构造 100 × 10 数据，重复 3 次；文件写入后伴随辅助命令报错。 |
| 03 | python 运行初版 | 3 次打印结果相同，尚未复现符号翻转。 |
| 04 | edit 扩大样本 | 改为 1000 × 50、重复 10 次；辅助命令仍报错，文件已更新。 |
| 05 | python 再次运行 | 多次输出出现正负号变化，复现用户描述的现象。 |
| 06 | find_file 查找源码 | 在 src 中查找；返回目录不存在。 |
| 07 | ls -l 查看目录 | 读取仓库根目录，看到 sklearn。 |
| 08 | find_file 重新查找 | 在 sklearn 中找到 kernel_pca.py。 |
| 09 | open 打开文件 | 返回文件路径、总行数及带行号的首屏。 |
| 10 | goto 200 定位计算 | 查看特征分解与后续处理的源码窗口。 |
| 11 | edit 修改 fit_transform | 按每列绝对值最大的元素统一符号；文件更新并返回内容。 |
| 12 | edit 更新复现脚本 | 错误地传入文件名；返回 edit 的行范围用法。 |
| 13 | open 切到复现脚本 | 明确当前打开的文件，读取原有内容。 |
| 14 | edit 1:26 正确写入 | 改用行范围替换，增加差异打印；辅助命令仍报错。 |
| 15 | python 验证修改 | 10 次打印的符号一致；脚本只比较绝对值，检查仍有缺口。 |
| 16 | edit 补源码注释 | 再次传入文件名；返回行范围用法，未按该次指令编辑。 |
| 17 | open 回到源码 | 打开 sklearn/decomposition/kernel_pca.py。 |
| 18 | goto 250 定位修改 | 读取 fit / fit_transform 附近的源码。 |
| 19 | edit 278:282 补注释 | 使用正确行范围，补充符号规范化说明。 |
| 20 | goto 18 查看类说明 | 切到 KernelPCA 类的文档位置。 |
| 21 | edit 23:23 加文档 | 插入确定性说明，同时产生重复的文档段落。 |
| 22 | edit 19:30 整理 | 重写类说明开头，继续校正段落。 |
| 23 | edit 19:31 再调整 | 扩大替换范围，清理重复内容。 |
| 24 | edit 19:32 对齐参数 | 重新接上 n_components 参数说明。 |
| 25 | find_file 查找测试 | 在 tests 中查找；返回目录不存在。 |
| 26 | ls 查看目录 | 重新确认仓库结构。 |
| 27 | find_file 重新查找 | 在 sklearn 中找到 test_kernel_pca.py。 |
| 28 | open 打开测试 | 读取已有测试文件与行号。 |
| 29 | edit 233:233 加测试 | 新增符号一致性检查，却替换掉已有断言；之后未见运行该测试。 |
| 30 | submit 收集补丁 | 输出涉及 3 个文件的 diff，结束这次 Agent 运行。 |
