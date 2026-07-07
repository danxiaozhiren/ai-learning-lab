# Agent 系统正在走向 Runtime：Anthropic Managed Agents 架构学习笔记

日期：2026 年 5 月 30 日  
原文：Anthropic Engineering, [Scaling Managed Agents: Decoupling the brain from the hands](https://www.anthropic.com/engineering/managed-agents)  
参考解读：知乎专栏，[Agent 系统正在重新走一遍 OS 和 Cloud Runtime 的老路](https://zhuanlan.zhihu.com/p/2037479092090622773)  

本文是一份学习和分享用笔记，不是逐段翻译。Anthropic 原文提供事实和架构设计，知乎文章提供系统工程视角的二次解读。阅读时应区分：原文中明确写出的内容，和本文基于原文、知乎文章所做的理解总结。

## 一、先给结论

这篇 Anthropic 工程博客真正讲的不是“怎样写一个更好的 agent prompt”，而是：当 agent 要执行长任务、接入真实工具、访问客户资源、经历失败恢复和上下文压缩时，它已经不再只是一个聊天应用，而更像一种需要专门运行时支撑的 workload。

Anthropic 的 Managed Agents 可以理解为一层 agent runtime abstraction。它把原来容易绑死在一起的部分拆开：

| 抽象 | 含义 | 工程角色 |
| --- | --- | --- |
| brain | Claude + harness | 思考、编排、决策、选择工具 |
| hands | sandbox、工具、MCP、外部执行环境 | 真正执行动作、产生副作用 |
| session | append-only event log | 持久状态、历史事实、恢复依据 |

核心设计思想是：不要把 Claude、harness、执行环境、会话状态和凭证全部塞进一个容器里。更稳的做法是让 brain、hands、session 各自通过稳定接口交互，使它们可以独立失败、独立恢复、独立替换。

## 二、问题为什么从 prompt 变成 runtime

早期理解 agent，容易把重点放在 prompt：怎样让模型拆任务、别乱调工具、遇到错误继续做。

再进一步，会发现重点变成 workflow：需要 tool loop、planner、memory、retry、evaluator、上下文压缩等机制。

但一旦 agent 开始做长任务，问题会继续下沉到 runtime 层：

1. 任务跑几个小时甚至几天，中间进程挂了怎么办？
2. sandbox 卡死、网络抖动、工具超时后，任务怎么恢复？
3. Claude 的上下文窗口满了，哪些历史能丢，哪些绝不能丢？
4. agent 执行代码时，凭证是否会暴露在同一个环境里？
5. 客户的代码、数据库、VPC、内部工具不在同一个容器里，怎么连接？
6. 不同模型能力变化很快，旧 harness 里的补丁会不会变成负担？

这些问题已经不是 prompt engineering 可以单独解决的，而是状态管理、执行隔离、权限边界、可恢复性、可观测性和接口稳定性问题。

## 三、旧设计的问题：一个容器装所有东西

Anthropic 文章提到，早期做法是把 agent 的主要组件放在同一个容器里：

- session 在容器里；
- harness 在容器里；
- sandbox 和文件系统也在容器里；
- Claude 需要执行代码、改文件、跑命令时，就在这个环境里完成。

这个设计有明显好处：

- 实现简单；
- 文件读写就是本地系统调用；
- 不需要设计复杂服务边界；
- agent 的工作目录、工具和运行循环都在同一个地方。

但生产环境下，它会变成基础设施里的“pet”问题。

“pet” 指必须手工照顾、不能随便丢的实例；“cattle” 指坏了就换、可以标准化重建的实例。单容器 agent 一旦承载 session、harness、sandbox 和用户数据，就很容易变成 pet：

- 容器挂了，session 可能丢；
- 容器卡死，工程师要进去救；
- 失败来源难判断，可能是 harness bug、网络问题、WebSocket 中断，也可能是容器离线；
- 工程师进入容器调试时，容器里可能有用户数据，调试边界很差；
- harness 默认“要处理的资源就在本容器里”，这会限制客户 VPC、远程工具、多执行环境的接入。

所以原文要解决的根本问题是：agent 不能长期依赖一个万能容器。

## 四、新设计：brain、hands、session 解耦

Anthropic 的解法是把原来耦合在同一个环境里的组件拆开。

### 1. brain：Claude 与 harness

brain 不是单指模型本身，而是 Claude 加上 harness。

harness 是 agent 的控制循环，负责：

- 调用 Claude；
- 组织上下文；
- 处理 Claude 的 tool call；
- 将 tool result 送回 Claude；
- 根据 session 记录恢复任务；
- 决定何时调用 sandbox、MCP 或其他工具。

知乎文章把 brain 类比为 control plane，这个视角很有用。brain 负责决策、编排和路由，不应该和具体执行环境的生命周期绑死。

### 2. hands：sandbox、tools、MCP、外部资源

hands 是实际执行动作的地方，包括：

- 容器或 VM sandbox；
- 代码执行环境；
- Git 仓库；
- MCP server；
- 浏览器、手机、客户 VPC 内部工具；
- 其他会产生副作用的执行资源。

原文给出的关键接口形态是：

```text
execute(name, input) -> string
```

也就是说，brain 不需要知道背后到底是容器、MCP server、客户私有网络里的服务，还是别的执行设备。它只需要通过统一接口发起动作，并接收结果。

知乎文章把 sandbox 解释成 disposable runtime，也就是“一次性执行工位”。这个说法很好：sandbox 不是家，不是长期记忆，也不是事实源。它应该可以按需 provision，坏了就丢，污染了就换。

### 3. session：持久事件日志

session 是整个设计里最容易被低估的部分。

Anthropic 明确区分了 session 和 Claude context window。Claude 的上下文窗口只是当前推理能看到的工作集，而 session 是完整、持久、可查询的事件日志。

session 中应该记录：

- 用户输入；
- Claude 的输出；
- 工具调用；
- 工具结果；
- 错误和异常；
- 文件改动；
- 中间决策；
- 用户插话；
- 恢复点。

相关接口包括：

```text
wake(sessionId)
getSession(id)
emitEvent(id, event)
getEvents()
```

如果 harness 崩溃，新的 harness 可以读取 session log，从上次事件继续执行。这样 harness 本身也可以变成 cattle，而不是必须手工救回来的 pet。

## 五、整体运行关系

下面这张图可以帮助理解三者关系：

```mermaid
flowchart TD
    U["User request"] --> S["Session log<br/>append-only facts"]
    S --> H["Harness<br/>control loop"]
    H --> C["Claude<br/>reasoning"]
    C --> H
    H -->|execute(name, input)| T["Hands<br/>sandbox / tools / MCP"]
    T -->|result or error| H
    H -->|emitEvent(id, event)| S
    H -->|context view| C
    V["Vault / auth boundary"] --> P["Tool proxy"]
    H --> P
    P --> T
```

这张图里最重要的是两个边界：

1. session 在 harness 外部，负责保存事实；
2. credentials 在 sandbox 外部，不能直接被 Claude 生成的代码读取。

## 六、harness 会腐化：过期的正确性最危险

Anthropic 原文的一个关键判断是：harness 会编码对模型能力的假设，而这些假设会随着模型进步而过期。

例如，某一代模型在上下文接近上限时会提前收尾，于是 harness 加了 context reset。下一代模型不再有这个问题，旧 reset 策略就可能变成干扰。

知乎文章把这种现象称为“过期的正确性”，这个说法很准确。很多 harness 补丁不是一开始就错，它们可能曾经救过线上问题：

- 模型不会从错误恢复，于是加 retry prompt；
- 工具返回格式不稳定，于是加解析补丁；
- sandbox 偶尔卡死，于是加 watchdog；
- 模型容易忘约束，于是加重复提示；
- 上下文快满就表现变差，于是加固定压缩策略。

问题是，模型、工具、部署环境都在变化。旧补丁一旦进入核心控制循环，后面很难删，最后 harness 就会变成历史补丁的堆积物。

因此，harness 不应被当作永远稳定的地基。更稳的架构是：让 harness 成为可替换策略层，把真正稳定的东西放到更低层接口里，例如 session、execute、provision、wake、getEvents。

## 七、context window 不是 runtime

长任务 agent 必然会超过模型上下文窗口。常见处理方式包括：

- 摘要历史；
- 裁剪旧 tool result；
- 保留关键文件；
- 使用 memory；
- 做上下文压缩；
- 做 prompt cache 优化。

这些方法都有价值，但不能把 context window 当成 source of truth。原因很简单：上下文会被裁剪，摘要会有损，模型注意力会漂移，未来需要的信息不一定是当前压缩策略认为重要的信息。

更可靠的做法是：

- session 保存完整事件；
- harness 根据当前任务需要，从 session 中取片段；
- 取出的事件可以被整理、压缩、筛选；
- 但原始事实不丢。

知乎文章把 append-only session log 类比为 event sourcing，非常贴切。它不是只存“最后状态”，而是存导致状态变化的事件序列。这样可以支持：

- 任务恢复；
- 过程审计；
- 失败复盘；
- 不同 harness 策略的对比；
- evaluator 对过程而不仅是结果进行评估；
- 从同一份历史 materialize 出不同上下文视图。

一句话：模型上下文是工作集，session log 才是事实源。

## 八、安全边界：凭证不能进入 sandbox

原文里最重要的安全设计之一，是让 token 永远不要出现在 Claude 生成代码可以访问的 sandbox 中。

如果旧架构把 untrusted code 和 credentials 放在同一个容器里，那么 prompt injection 只要诱导 Claude 读取环境变量，就可能拿到 token。拿到 token 后，攻击者可以创建新的会话、调用工具、访问外部资源。

Anthropic 提到两种处理方式：

1. Git 场景  
   初始化 sandbox 时使用仓库 access token 完成 clone，并配置好 git remote。sandbox 内可以执行 push 和 pull，但 agent 不直接接触 token。

2. MCP 或自定义工具场景  
   OAuth token 放在外部安全 vault 中。Claude 调 MCP tool 时，经过专门 proxy；proxy 根据 session 关联的 token 去 vault 取真实凭证，再调用外部服务。harness 不需要知道真实凭证。

这比“给 Claude 一个权限很窄的 token”更稳。因为随着模型能力提升，系统不应依赖“它拿到 token 也做不了什么”这种假设，而应该从结构上让 token 不可达。

## 九、性能收益：按需启动 hands

解耦还有一个直接性能收益：不需要每个任务一开始就启动完整容器。

旧设计中，brain 在容器里。每个 session 都要先付出容器准备成本：

- provision 容器；
- clone repo；
- 启动进程；
- 拉取事件；
- 准备执行环境。

但有些任务一开始并不需要 sandbox。新架构里，harness 可以先启动推理，只有当 Claude 真正需要执行动作时，再通过工具调用 provision hands。

原文给出的结果是：

- p50 TTFT 下降约 60%；
- p95 TTFT 下降超过 90%。

TTFT 是 time-to-first-token，即用户提交任务到看到第一个输出 token 的等待时间。这个指标直接影响用户体感。

## 十、many brains, many hands

解耦之后，系统可以支持两种扩展方向。

### 1. Many brains

brain 不再依赖某个特定容器，它可以变成无状态、可恢复、可水平扩展的 harness 实例。

如果某个 harness 挂掉，新的 harness 可以通过 session log 恢复；如果任务变多，可以启动更多 harness。brain 更像 control plane 工作负载。

### 2. Many hands

一个 brain 可以连接多个 hands。每个 hand 都通过工具接口暴露能力。hand 可以是 sandbox，也可以是 MCP server、浏览器、客户 VPC 里的工具、远程设备等。

这对未来 agent 很关键。越强的模型越可能同时操作多个执行环境，而不是只在一个 shell 里工作。

## 十一、这篇知乎解读补充了什么

知乎文章的价值不是提供 Anthropic 官方之外的新事实，而是把原文转成更容易理解的系统工程语言。

| 知乎解读概念 | 对原文的帮助 |
| --- | --- |
| prompt -> workflow -> runtime | 解释为什么 agent infra 的问题在下沉 |
| harness 腐化 | 解释为什么旧 harness 补丁会变成负担 |
| context window is not runtime | 强调上下文窗口不能承担持久状态 |
| append-only log 像 event sourcing | 帮助理解 session log 的工程价值 |
| brain / hands 像 control plane / data plane | 把“脑和手”的比喻转成基础设施模型 |
| sandbox 是 disposable runtime | 帮助理解 pet vs cattle 在 agent 中的含义 |
| observability 和 evaluation 还没被吃透 | 补上原文较少展开的生产化难题 |

需要注意的是，知乎文章是读后感和架构评论，不是 Anthropic 官方说明。它适合用来建立 mental model，但引用事实时仍应回到 Anthropic 原文。

## 十二、与 OS / Cloud Runtime 的类比

Anthropic 原文把 Managed Agents 类比为操作系统抽象。操作系统之所以能长期存在，是因为它没有把接口绑死在某代硬件上，而是抽象出 process、file、read 等稳定概念。

agent runtime 也在出现类似抽象：

| OS / Cloud 世界 | Agent runtime 世界 |
| --- | --- |
| process | harness / brain 实例 |
| file / storage | session log / artifacts |
| syscall | execute / getEvents / emitEvent |
| scheduler | orchestration layer |
| container / VM | sandbox / hand |
| control plane | brain / harness orchestration |
| data plane | tools / sandbox / MCP execution |
| WAL / event sourcing | append-only session log |
| IAM / secret manager | vault / proxy / scoped auth |
| logs / traces / metrics | session events / tool traces / model decisions |

这个类比不能过度使用。agent runtime 还远没有形成操作系统那样稳定的标准。但趋势是清楚的：当 agent 进入长任务和生产环境，老派系统工程问题会重新出现。

## 十三、可以沉淀成的设计原则

### 1. session 必须是事实源

不要把模型上下文、摘要、memory 文件当作唯一状态。完整事件必须能持久保存、查询和回放。

### 2. harness 应该可替换

harness 中会有大量模型代际相关的策略。它应该是策略层，不应该成为不可替换的地基。

### 3. sandbox 应该可丢弃

sandbox 是执行工位，不是长期家目录。它应该能标准化 provision，失败后能重建。

### 4. credentials 不应进入 untrusted execution

token、OAuth credential、客户资源权限应放在 vault 或 proxy 后面，不能直接暴露给 Claude 生成的代码。

### 5. tool interface 应稳定

工具背后的实现可以变化，但 brain 看到的接口应尽量稳定。`execute(name, input) -> string` 代表的是这种最小而通用的边界。

### 6. 恢复能力要靠系统，不靠模型记忆

模型可以推理，但不应该被要求“记住所有发生过的事”。恢复应依赖 session log、事件重放和明确状态。

### 7. 观测和评价要看过程

长任务 agent 不能只看最终答案。还要观察它是否走了危险路径、是否误用权限、是否在不确定时装确定、是否因为 compaction 丢失关键事实。

### 8. 副作用要有语义

工具调用可能产生外部副作用。系统需要知道动作是否已执行、失败发生在执行前还是执行后、重试是否会双提交。

## 十四、评估 agent 平台时可以问的问题

如果用这篇文章作为判断框架，评估一个 agent 平台或企业内部 agent 系统时，可以问：

1. session 状态存在哪里？是否是 append-only？是否可回放？
2. harness 崩溃后，任务能否从事件日志恢复？
3. sandbox 挂掉后，是抢救容器，还是标准化重建？
4. Claude 生成的代码是否能读取真实 token？
5. 工具调用是否有统一接口？还是每个工具都把逻辑塞进 prompt？
6. 上下文压缩前后的信息是否可追溯？
7. evaluator 能否检查过程，而不仅是最终输出？
8. 客户 VPC 或内部工具是否必须和 harness 部署在一起？
9. 工具调用产生副作用后，重试语义是否清晰？
10. 模型升级后，旧 harness 策略能否被替换或 A/B 对比？

这些问题比“agent prompt 写得好不好”更接近生产级 agent infra 的核心。

## 十五、常见误解

### 误解一：这篇文章是在说 prompt 不重要

不是。prompt 仍然重要，但它不再是唯一中心。prompt、context engineering、tool loop 都会成为 harness 策略的一部分，而不是整个系统的地基。

### 误解二：有了更长上下文，就不需要 session log

不是。更长上下文只能扩大当前工作集，不能替代持久事实源。长任务需要可审计、可恢复、可重放的历史。

### 误解三：每个 agent 都需要做成完整 OS

不是。小 demo 或短任务不一定需要这些复杂设计。只有当 agent 要进入长任务、生产环境、真实权限和多工具场景时，runtime 问题才会变成核心。

### 误解四：把所有东西放进一个强大的容器就够了

不是。单容器开始简单，但会在恢复、调试、安全、VPC 接入和扩展上付出代价。

### 误解五：只要给 token 限权就安全

限权有必要，但结构上让 token 不可达更可靠。不要让凭证出现在 Claude 生成代码可以读取的环境里。

## 十六、适合分享时使用的简短版本

如果只用几句话分享这篇文章，可以这样说：

Anthropic 的 Managed Agents 不是在讲某个更聪明的 agent prompt，而是在讲 agent runtime。它把 Claude 和 harness 视为 brain，把 sandbox、MCP 和外部工具视为 hands，把完整事件历史放进持久 session log。这样，harness 可以崩了再恢复，sandbox 可以坏了就换，凭证可以留在 vault 里，context window 不再承担持久状态。

这篇文章的真正启发是：生产级 agent 系统正在重新遇到操作系统和云运行时遇到过的问题，例如调度、隔离、权限、状态、恢复、观测和接口稳定性。未来值钱的不只是某一个 agent，而是承载 agent 长期可靠运行的那层 runtime。

## 十七、我的学习 takeaway

这篇文章最值得记住的不是某个具体 API，而是三个边界：

1. brain 和 hands 要分开：思考与执行的生命周期不同。
2. session 和 context window 要分开：事实源不能只活在模型窗口里。
3. credentials 和 sandbox 要分开：执行环境不能直接拿到真实凭证。

如果这三个边界画不清，agent 系统一开始可能跑得很快，但进入长任务、多人协作、真实权限和客户环境后，就会越来越难恢复、难观测、难调试，也难以随着模型升级继续演进。

