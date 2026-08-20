# 招商证券 AI Coding 实战工作坊 · 0.5 天分组实战

题目：《企业级高可用多智能体客服 AI 智能体系统》。详细需求见 [`prd.md`](./prd.md)。

## 这是什么

2 天工作坊的最后 3 小时，每组从 `prd.md` 出发，完整走一遍 SDD（Spec-Driven Development）链路。**五份产出文档全部由 Coding Agent 起草**，人不从零手写，只做评审（纠错 + 补充），确认后再进入下一步：

```mermaid
flowchart TD
    P[prd.md<br/>已给] -->|Coding Agent: 需求理解| S1[spec.md 草稿]
    S1 -->|人评审: 纠错+补充| S2[spec.md 确认]
    S2 -->|Coding Agent: 方案设计| PL1[plan.md 草稿]
    S2 -->|Coding Agent: 起草 §0/1/2/4| EV1[eval.md 草稿<br/>测试用例部分]
    PL1 -->|人评审: 纠错+补充| PL2[plan.md 确认]
    EV1 -->|人评审: 纠错+补充| EV2[eval.md §0/1/2/4 确认]
    PL2 -->|Coding Agent: 补齐 §3/5| EV3[eval.md 草稿<br/>降级+不改清单部分]
    PL2 -->|Coding Agent: 任务设计| T1[tasks.md 草稿]
    EV3 -->|人评审: 纠错+补充| EV4[eval.md 全部确认]
    T1 -->|人评审: 纠错+补充| T2[tasks.md 确认]
    T2 -->|Coding Agent: 编码实现| CODE["代码（写在 src/ 下）"]
    CODE -->|基于 eval.md 评测验收| EVAL[eval.md 回填结果]
    EVAL --> DEPLOY[部署发布]
    DEPLOY -->|Coding Agent: 自动读取四份文档的评审记录<br/>+ eval.md 的门禁结果/遗留问题/判定理由| L1[learnings.md 草稿]
    L1 -->|人评审: 纠错+补充| L2[learnings.md 确认]
```

`eval.md` 不是单一来源：测试用例部分（§0/§1/§2/§4）只依赖确认版的 `spec.md`，跟 `plan.md` 是并行的两条分支，不用等它；但降级测试和不改清单核对部分（§3/§5）结构上依赖 `plan.md`（§3 核对 plan.md §5 的降级设计，§5 核对 plan.md §6 的不改清单），要等 `plan.md` 确认后再补——不用等到编码完成，也不卡编码开始。每一轮评审的纠错和补充，记在对应文档自己的"变更记录"里就够了——`learnings.md` 最后由 Coding Agent 自动读取这四处生成，人不需要在过程中另外维护一份日志。

## 怎么开始

1. 通读 [`prd.md`](./prd.md)，尤其是 §2（需求分类，至少选 2 类）、§4（验收标准）、§5（五份产出文档的定义与硬规则）。
2. 把 `templates/` 下的五个模板复制到你们组的工作分支根目录，去掉文件名里的 `.template`（这些是给 Coding Agent 起草时参照的结构，不是要你们手工填空）：

   ```
   templates/spec.template.md      → spec.md
   templates/plan.template.md      → plan.md
   templates/tasks.template.md     → tasks.md
   templates/eval.template.md      → eval.md
   templates/learnings.template.md → learnings.md
   ```

3. 按下面的分阶段 prompt，让 Coding Agent 起草 `spec.md`，评审确认后并行起草 `plan.md` 和 `eval.md`（此时 `eval.md` 只能先写测试用例部分 §0/§1/§2/§4）。每一份起草完都先人工评审：重点盯 `spec.md` §2/§5 有没有写错，以及 §2.1、§3、`plan.md` §6 后面那几张**盲区表**——Agent 拿不准的地方都列在那儿等你们勾选裁决，那是评审时最该花时间的对象。纠错和补充记进该文档自己的"变更记录"，改完再进入下一阶段。
4. `plan.md` 确认后，让 Coding Agent 补齐 `eval.md` 的 §3/§5（依赖 plan.md 的降级设计和不改清单），同时起草 `tasks.md`；两者评审确认。
5. `tasks.md` 确认后，用 prompt ④ 让 Coding Agent 按 T-01→T-06 连续执行：T-01~T-05 编码实现（**代码写在 `src/` 下**），T-06 跑测试并把结果回填进 `eval.md`。人工滚动 review、纠偏。
6. `eval.md` §7 的判定结论由人来签（`verdict_by` 填人名），判"通过"或"有条件通过"才进入下一步。
7. T-07 上半段：部署发布——把 demo 实际跑起来，确认照着 `src/README.md` 能被现场访问。
8. T-07 下半段：让 Coding Agent 自动读取 `spec.md`/`plan.md`/`tasks.md`/`eval.md` 的变更记录（以及 `eval.md` 的门禁结果、遗留问题、判定理由）生成 `learnings.md`，人评审确认，准备现场演示。

## 怎么喂给 Coding Agent（分阶段 prompt）

**① 起草 spec.md**

```
请阅读 prd.md，做需求理解，参照 templates/spec.template.md 的九节结构和
frontmatter 字段起草 spec.md。§2（消歧）、§3（范围与场景）、§5（验收标准，
用 US-编号，prd §4 五条验收标准要条条有 US 兜住）、§6（非功能口径）、§9
（变更记录）这五节都不能省——后面 eval.md 和 learnings.md 直接从这里取数。
不要涉及架构设计（用几个智能体、用什么框架），那是下一步 plan.md 的事。

另外：凡是 PRD 里没有依据、你只能靠常识猜的地方，不要默默猜完写死。
- §2.1 列出你替我们做了哪些裁决、哪些其实拿不准，标上暂定结论和依据；
- §3 的 Out of Scope 附一张盲区表，列出"我不确定要不要划到范围外"的东西，
  说明为什么不确定。
宁可多列几条让我们勾掉，也不要交一份看起来很干净、其实缺口在暗处的稿子——
我们只能审你写出来的东西，审不出你没写的。
```

**② spec.md 人工确认后，并行起草 plan.md 和 eval.md**

```
spec.md 已经过人工评审确认。请阅读确认版的 spec.md 和 prd.md，做方案设计，
参照 templates/plan.template.md 的八节结构起草 plan.md，§6"不改清单"要
列清楚哪些是硬约束、编码时不能碰。

§6 后面附一张盲区表，列出你不确定该不该锁的东西，各写一句"锁了的代价 /
不锁的风险"让我们裁决。这一节漏一条，编码阶段就少一道防线，而漏掉的那条
不会以任何形式出现在文件里——我们审不出你没写的，所以宁可多列几条。
```

```
spec.md 已经过人工评审确认。请阅读确认版的 spec.md，做验收判定设计，参照
templates/eval.template.md 起草 eval.md 的 §0、§1（AC 覆盖矩阵 + §1.1 路由
准确率测试用例，至少 20 条，对照 spec.md §5 每条 US 编号）、§2（验证范围）、
§4（非功能门槛），不用等 plan.md / tasks.md 存在。§3（系统级回归确认）和
§5（不改清单核对）依赖 plan.md，先留空，等 plan.md 确认后再补。

§1.1 的测试用例：先把 test-cases.md 的 15 条（简单/较复杂/复杂各 5）搬进来，
再按我们组选的场景补足到 20 条以上。注意评委现场会另用一套未公开的语句抽测，
所以补的时候要覆盖场景本身，不要只围着这 15 条打转。
```

**③ plan.md 人工确认后，起草 tasks.md，并补齐 eval.md §3/§5**

```
plan.md 已经过人工评审确认。请阅读确认版的 plan.md，做任务拆解，参照
templates/tasks.template.md 的 T-01~T-07 结构起草 tasks.md，每项要有明确的
verify 命令。同时把 eval.md 的 §3（系统级回归确认，对照 plan.md §5 的降级
设计填"期望降级行为"）和 §5（不改清单核对，把 plan.md §6 的条目原样搬过来）
补齐。
```

**④ tasks.md 人工确认后，开始编码**

```
spec.md、plan.md、tasks.md、eval.md 均已经过人工评审确认。请严格按 tasks.md
的 T-01 到 T-06 顺序实现，每完成一项跑一下对应的 verify 命令，通过后把状态
改成 ✅ 并在"说明"里简述实现方式。

代码一律写在 src/ 目录下（结构按 plan.md §3 定的来），五份产出文档留在仓库
根目录不要动；T-01 要同时把依赖安装和启动命令写进 src/README.md。密钥统一走
根目录的 .env（已在 .gitignore 里），需要新增配置项就同步补进 .env.example，
任何情况下都不要把真实 Key 写进代码或提交进仓库。

遇到 spec.md / plan.md 没覆盖的情况，按最小合理假设处理并说明假设，记进对应
文档的"变更记录"，不要擅自扩大范围，且不允许改动 plan.md §6"不改清单"里列出
的内容。T-06 完成后把测试结果回填进 eval.md 并给出判定结论。
```

**⑤ eval.md 判定通过后，部署发布**

```
eval.md 判定结论为"通过"或"有条件通过"后，请完成 tasks.md 的 T-07：把 demo
跑起来，并确认 src/README.md 里的启动说明是最新的——评委会照着它现场启动，
所以要保证别人 clone 下来能按说明一次跑通。
```

**⑥ 部署完成后，生成 learnings.md**

```
请自动读取以下内容，提炼出 L-01 起的若干条（至少 3 条，问题/根因/修复/回流/
可复用性），参照 templates/learnings.template.md 的结构起草 learnings.md：
- spec.md §9、plan.md §8、tasks.md、eval.md §8 各自的"变更记录"（人评审时
  纠错和补充了什么；eval.md 有两轮评审，都要读）
- tasks.md 各 Task"说明"里记的最小合理假设
- eval.md §3/§4 里没通过的门禁、§6 遗留问题与未覆盖场景、§7 判定理由
后面这几项是"实际测出来什么"，比"评审时改了什么"更能说明问题，别漏掉。
挑真实发生、有具体落点的记录，不用为了凑满 5 条而注水。
```

## 目录结构

```
prd.md                          业务需求（已给，不要修改）
test-cases.md                   功能测试用例 15 条（简单/较复杂/复杂 各 5），自测用，扩进 eval.md §1.1
assets/ui-reference.svg         PRD 引用的 UI 参考图
templates/                      五份产出文档的结构模板（Coding Agent 起草时参照，人评审后重命名去掉 .template）
  spec.template.md
  plan.template.md
  tasks.template.md
  eval.template.md
  learnings.template.md
src/                            代码放这里（内部结构各组自己定，见 src/README.md）
.env.example                    环境变量模板：cp .env.example .env 后填真实 Key
.gitignore                      已挡掉 .env / venv / target 等，别绕过它提交密钥

你们组做完之后，仓库根目录还会多出这五份文档：
spec.md  plan.md  tasks.md  eval.md  learnings.md
```

**文档放根目录，代码放 `src/`。** 五份产出文档是给人评审的，跟代码分开更容易 review；`src/` 里怎么组织由各组在 `plan.md` 里自己决定（框架不限定，Python 和 Java 的结构差别很大），但 `src/README.md` 必须写清楚怎么装依赖、怎么启动、怎么跑测试——评委会照着它现场启动。

## 验收标准

见 [`prd.md` §4](./prd.md#4-验收标准与交付物)。核心是：基准场景（Golden Path）跑通、两大类各 ≥3 个场景正确路由、路由准确率 ≥85%、能演示一次高可用降级、执行过程可解释。
