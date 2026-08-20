---
specId: TASKS-<组名>-001
type: tasks
parent_spec: spec.md
plan_ref: plan.md
domain: 客服
owner: <组名 / 组员姓名>
status: 草稿          # 草稿（Coding Agent 起草）/ 待评审 / 已确认（人评审通过后）
confirmed: false       # 人评审、纠错+补充后改为 true，确认后才能开始编码
version: 0.1
updated_at:
execution_mode: sequential   # Coding Agent 按顺序逐项执行，不并行抢任务
knowledge_refs: []
related_specs: [eval.md]
score: {}
---

<!-- ──────────────────────────────────────────────────────────────
  tasks.md ｜ 任务设计（plan.md 的拆解，Coding Agent 的执行清单）

  为什么要有"verify 命令"      每个任务不是靠"看起来做完了"判断
        完成，而是靠一条能跑的命令/一个能复现的操作来自证——这也是
        为什么最后两个任务分别是"跑完全部验证"和"部署发布与结项
        回写"，验收、部署和复盘本身也是任务，不是任务清单之外的事。
        T-01 / T-06 / T-07 给了示例，其余几项照着补。**别写"手工
        验证""看一下对不对"**——那等于没有 verify；实在没法用一条
        命令自证的（比如 T-07），就写成一个别人能照做一遍的操作。

  谁写、谁评审      这份文件由 Coding Agent 基于**已确认版**的
        plan.md 起草（T-01~T-07 的任务划分 + 每项的 verify 命令），
        人评审、纠错 + 补充后确认，才能进入编码阶段——T-01~T-05 是
        实际动手编码的部分，评审 tasks.md 时就是在给编码阶段把关。

  怎么用      这份文件确认后，连同 spec.md、plan.md 一起交给
        Coding Agent，让它按 T-01 → T-07 顺序执行，每做完一项在
        "状态"列打勾，并在"说明"里简述怎么实现的。
────────────────────────────────────────────────────────────── -->

# TASKS-\<组名\>-001｜执行任务清单

## 执行期约束（每个 Task 执行前注入）

- 不允许修改 plan.md §6"不改清单"里列出的内容。
- 遇到 spec.md / plan.md 没覆盖的情况，按最小合理假设处理，并把假设记在对应任务的"说明"里，同时补一行到本文件末尾的"变更记录"（供最后 learnings.md 自动汇总用），不要擅自扩大范围。
- 每完成一个 Task，先跑该 Task 的 verify 命令，通过了再进入下一个。

## 任务清单

### T-01　项目骨架初始化

- 依赖：无
- 负责人：
- 产出物：`src/` 下可运行的项目骨架，`src/README.md` 写清依赖安装与启动命令
- 验收口径：别人 clone 下来照着 `src/README.md` 能跑起来
- verify 命令：示例 —— `cd src && pip install -r requirements.txt && python app.py`，能启动且访问首页不报错（Java 组换成 `mvn spring-boot:run`）
- 状态：⬜
- 说明：

### T-02　实现路由智能体

- 依赖：T-01
- 负责人：
- 产出物：
- 验收口径：对照 spec.md US-02 的分类场景，能正确路由
- verify 命令：
- 状态：⬜
- 说明：

### T-03　实现售前咨询智能体（≥3 场景）

- 依赖：T-02
- 负责人：
- 产出物：
- 验收口径：
- verify 命令：
- 状态：⬜
- 说明：

### T-04　实现售后咨询智能体（含退款 Golden Path）

- 依赖：T-02
- 负责人：
- 产出物：
- 验收口径：对照 spec.md US-01 Golden Path 场景
- verify 命令：
- 状态：⬜
- 说明：

### T-05　实现工具层、降级与可观测性

- 依赖：T-03, T-04
- 负责人：
- 产出物：mock 工具 + 故障注入开关 + 执行过程记录（日志/面板）
- 验收口径：对照 spec.md US-03（高可用降级）与 US-05（执行过程可解释）
- verify 命令：
- 状态：⬜
- 说明：

### T-06　跑完全部验证

- 依赖：T-01…T-05
- 负责人：
- 产出物：eval.md 已回填实测结果
- 验收口径：eval.md 全部门禁通过（或明确记录未通过项）
- verify 命令：示例 —— `cd src && python -m tests.run_eval`，输出准确率数字（对照 eval.md §1.1 的 ≥20 条用例逐条判定，不要人工一条条试）
- 状态：⬜
- 说明：

### T-07　部署发布与结项回写

- 依赖：T-06
- 负责人：
- 产出物：可被现场访问的 demo（启动命令/访问方式已写进 `src/README.md`）、Coding Agent 自动生成并经人确认的 learnings.md、tasks.md 全部状态更新
- 验收口径：照着 `src/README.md` 能把 demo 实际跑起来并演示；learnings.md 已由 Coding Agent 读取 spec/plan/tasks/eval 四份文档的变更记录生成，人评审确认
- verify 命令：这一项没法用一条命令自证，改成一次可复现的操作 —— **换一台机器（或换个干净目录）重新 clone，只照着 `src/README.md` 走一遍，能启动并跑通 Golden Path**。组内写代码那台机器上"能跑"不算数，漏写的依赖和环境变量只有这样才暴露得出来。
- 状态：⬜
- 说明：

## DoD（整体完成定义）

- [ ] T-01 ~ T-07 全部状态为 ✅
- [ ] eval.md 的判定结论（§7）不是"不通过"
- [ ] demo 已部署发布，可被现场访问
- [ ] learnings.md 已由 Coding Agent 生成并经人评审确认，至少有 L-01 ~ L-03

## 变更记录

| 时间 | 改动 | 人 |
|---|---|---|
| | 初稿 | |

---

### 给 Coding Agent 的执行说明

复制到你们与 Coding Agent 对话的第一条 prompt 里，按需修改（README.md 里还有起草 spec.md / plan.md / eval.md / tasks.md 各步骤各自的 prompt）：

```
请阅读 spec.md、plan.md、tasks.md、eval.md 四份文件（均为已确认版）。严格按
tasks.md 的 T-01 到 T-06 顺序实现，每完成一项跑一下对应的 verify 命令，通过后
把状态改成 ✅ 并在"说明"里简述实现方式。遇到 spec.md / plan.md 没覆盖的情况，
按最小合理假设处理，把假设记下来，不要擅自扩大范围或引入未提及的新功能，且
不允许改动 plan.md §6"不改清单"里列出的内容。T-06 完成后把测试结果回填进
eval.md 并给出判定结论；T-07 把 demo 部署起来（部署完成后再用 README.md 里
的 prompt ⑥ 生成 learnings.md）。
```
