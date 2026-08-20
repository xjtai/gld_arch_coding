# src/｜代码目录

Coding Agent 按 `tasks.md` 的 T-01~T-07 实现的代码放这里。**五份产出文档（`spec.md` / `plan.md` / `tasks.md` / `eval.md` / `learnings.md`）放在仓库根目录，不要放进这里**——它们是给人评审的，跟代码分开更容易 review。

## 内部结构自己定

本次实战不限定框架（Spring AI、Spring AI Alibaba、LangChain、LangGraph、AutoGen、自研 Router + Function Calling 都可以），语言和构建工具也就不一样，所以 `src/` 里怎么组织由各组在 `plan.md` 里自己决定，写清楚就行。给两个参考：

**Python（LangChain / LangGraph / AutoGen 等）**

```
src/
  agents/          路由智能体、售前咨询智能体、售后咨询智能体……
  tools/           mock 工具（订单查询、退款等），含故障注入开关
  data/            mock 数据（订单、商品、物流）
  app.py           入口：Web 服务或命令行 Demo
  requirements.txt
  README.md        启动方式（覆盖本文件）
```

**Java（Spring AI / Spring AI Alibaba）**

```
src/
  pom.xml
  src/main/java/...    按 Spring 项目常规结构
  src/main/resources/  mock 数据、配置
  README.md            启动方式（覆盖本文件）
```

## 必须留下的东西

T-01 完成时就要保证：**别人 clone 下来照着说明能跑起来**。把启动方式直接覆盖写进本文件（`src/README.md`），至少包含：

- 依赖怎么装
- 环境变量怎么配：仓库根目录有 `.env.example`，`cp .env.example .env` 后填真实值。模型 Key 由讲师现场分发，**不在仓库里**。
  - `.env` 已被 `.gitignore` 挡掉，别绕过它（别 `git add -f`、别把 Key 写进代码或写进 `.env.example`）——Key 一旦推上远端，撤销重发是唯一补救。
  - 代码里统一 `os.getenv("DEEPSEEK_API_KEY")` 这样读，不要硬编码，也不要打印到日志里（"执行流程"面板要展示工具调用参数，注意别把 Key 一起打出来）。
- 怎么启动、启动后访问哪个地址
- `eval.md` 里的测试怎么跑

这几条也是 `tasks.md` T-07"部署发布"的验收依据——评委会照着这份说明现场启动。
