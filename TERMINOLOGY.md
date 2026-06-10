Copyright (c) 2026 SPHARX Ltd. All Rights Reserved.
"From data intelligence emerges."

# OpenAirymax AgentOS — 统一术语表 v3.0

**最新**: 2026-06-10 | **版本**: v3.0（全面审计增强版）
**状态**: 维护中 | **路径**: `Docs/TERMINOLOGY.md`
**配套**: 技术加强方案 v5.0 + 工程规范手册 v19.0 + 架构设计原则 ARCHITECTURAL_PRINCIPLES.md

**作者**:
    - Liren Wang
    - Zhixian Zhou
    — AgentOS 架构委员会

---

## 编制说明

### 一、术语表目的

本术语表是 OpenAirymax AgentOS 规范体系的 **唯一权威术语参考**，旨在：

1. **统一术语使用**: 确保所有规范文档、代码注释、API 文档使用一致的术语定义
2. **建立交叉引用**: 提供术语与源代码文件/规范章节的精确链接
3. **支持快速检索**: 按字母顺序组织，支持中英文双向查找
4. **促进理解**: 为开发者、审计人员、研究人员、TOC 评审委员提供权威解释
5. **消除歧义**: 明确禁止使用的旧称/混淆称谓，统一命名规范
6. **跨文档仲裁**: 当不同文档间存在术语冲突时，**以本表为准**

### 二、术语来源与审计基准

本术语表的术语来源于以下核心文档和源代码，**v3.0 全部经过二次深度源码审计验证**：

#### 2.1 理论文档层
| 文档 | 路径 | 核心内容 |
|------|------|---------|
| 体系并行论 (MCIS) | `Basic_Theories/CN_01_体系并行论.md` | 多体控制论智能系统基础理论 |
| 认知层理论 | `Basic_Theories/CN_02_认知层理论.md` | 双系统认知模型理论基础 |
| 记忆层理论 | `Basic_Theories/CN_03_记忆层理论.md` | 四层记忆架构理论基础 |
| 设计原则 | `Basic_Theories/CN_04_设计原则.md` | 五维正交系统 24 条原则 |

#### 2.2 架构文档层
| 文档 | 路径 | 核心内容 |
|------|------|---------|
| 架构设计原则 | `ARCHITECTURAL_PRINCIPLES.md` | 五维 24 条原则 + 15 个标准术语表 |
| 微核心架构 | `Capital_Architecture/microkernel.md` | MicroCoreRT 详细规格 |
| 记忆卷载架构 | `Capital_Architecture/memoryrovol.md` | MemoryRovol L1-L4 完整实现 |
| 三层认知循环 | `Capital_Architecture/coreloopthree.md` | CoreLoopThree 三层运行时 |
| IPC Binder | `Capital_Architecture/ipc.md` | 进程间通信机制 |
| 系统调用层 | `Capital_Architecture/syscall.md` | Syscall API 完整定义 |
| 日志系统 | `Capital_Architecture/logging_system.md` | 四级日志架构 |
| Daemon 模块 | `Capital_Architecture/daemon_module.md` | 用户态服务架构 |
| 测试架构 | `Capital_Architecture/test_architecture.md` | 双层测试架构 |
| Toolkit 设计 | `Capital_Architecture/toolkit_design.md` | 多语言 SDK 架构 |

#### 2.3 源代码审计基准（v3.0 二次审计）

> **v3.0 变更说明**: 在 v2.0 首次审计基础上，v3.0 进行了**全量二次深度审计**，
> 新增覆盖 CoreLoopThree ext/dispatcher/context/execution/units/ 等之前未充分审查的子目录。

```
AgentOS/
├── agentos/atoms/corekern/          # 微内核（IPC/内存/任务调度/时间/OOM/可观测性）
├── agentos/atoms/coreloopthree/     # 三层认知循环（~75 源文件, ~25,557 行）
├── agentos/atoms/syscall/           # 系统调用层（7 类 ~28+ API）
├── agentos/atoms/taskflow/          # 任务流引擎（Pregel BSP）
├── agentos/atoms/memory/            # 内建记忆提供者
├── agentos/atoms/memoryrovol/       # MemoryRovol 骨架
├── agentos/atoms/frameworks/        # 框架层
├── agentos/cupolas/                 # 安全穹顶（7 大子系统 ~40+ 文件）
├── agentos/daemon/common/           # 公共库（含 Orchestrator 编排器）
├── agentos/daemon/llm_d/            # LLM 服务守护进程
├── agentos/daemon/tool_d/           # 工具服务守护进程
├── agentos/daemon/gateway_d/        # 网关守护进程
├── agentos/protocols/               # 协议适配层（9 种协议类型）
├── agentos/gateway/                 # 独立网关模块
├── agentos/heapstore/               # 数据分区存储
└── scripts/                         # 构建/运维/工具链脚本
```

#### 2.4 v3.0 审计统计总览

| 审计维度 | v2.0 基线 | v3.0 更新后 | 变化 |
|----------|-----------|-------------|------|
| 核心术语条目 | 26 | **32** | +6 |
| 一级组件映射 | 12 | **14** | +2 (CognitiveEvolution, MAC) |
| 二级子组件映射 (CLT) | 18 | **28** | +10 |
| 二级子组件映射 (Cupolas) | 9 | 9 | 持平 |
| 命名规范类 | 8 | **10** | +2 |
| 错误码段数 | 10 | 10 | 持平 |
| 附录条目 | 6 (A-F) | **8** (A-H) | +2 (G: 认知进化测试, H: MAC 测试) |
| 总行数 | ~1,452 | **~2,100+** | +45% |
| 源码审计文件数 | ~50 | **~75+** | +25 |
| 代码量审计 | 部分 | **全量** | 全面 |

### 三、使用方法

1. **文档编写**: 所有规范文档必须使用本术语表的**标准名称**
2. **代码注释**: C 源代码注释中的中文描述应使用标准名称
3. **术语引用**: 使用 `[标准名称](#标准名称英文)` 格式进行交叉引用
4. **术语更新**: 新术语需通过架构委员会审核后加入
5. **版本管理**: 术语表版本与规范体系版本同步
6. **冲突裁决**: 当术语定义与本表不一致时，**以本表为准**

### 四、术语分类（v3.0 扩展）

| 分类 | 覆盖范围 | 条目数（约） |
|------|---------|------------|
| **A. 核心概念** | Agent、Skill、Contract、Session 等 | **14** (+2) |
| **B. 架构组件** | 微核心、三层认知循环、记忆卷载、安全穹顶等 | **20** (+2) |
| **C. 子系统组件** | CoreLoopThree 内部 75+ 文件拆分、Cupolas 7 大子系统等 | **60+** (+15) |
| **D. 技术术语** | JSON-RPC、TraceID、IPC、Syscall 等技术细节 | 22 (+2) |
| **E. 工程原则** | 反馈闭环、层次分解、安全内生等设计原则 | 24 |
| **F. 算法术语** | 持久同调、HNSW、BM25、Hopfield 等算法 | 27 (+2) |
| **G. 命名规范** | 函数/结构体/枚举/宏/文件目录命名约定 | **10** (+2) 类 |
| **H. 错误码体系** | 10 段错误码完整分段 | 700+ 定义值 |
| **I. 跨文档一致性** | 不一致项裁决表 | **10** 项已裁决 |

---

## 第一部分：核心术语定义（按字母序）

### A

#### Agent (智能体)

**定义**: 具有认知能力的实体，能够感知环境、进行推理决策并执行行动。Agent 是 AgentOS 的基本执行单元，通过 Syscall 层注册、通过 CoreLoopThree 驱动、通过 MemoryRovol 持久化记忆。

**标准名称**: 智能体 (Agent)

**系统内数据结构**:
```c
// Agent 注册信息（定义于 agent_registry.h / syscall 层）
typedef struct agent_info_t {
    char *id;                  // 全局唯一标识
    char *role;                // 角色
    double cost;               // 成本
    double success_rate;       // 成功率
    double trust;              // 可信度
    int priority;              // 优先级
} agent_info_t;

// Agent 注册中心接口（定义于 coreloopthree/include/agent_registry.h）
typedef struct {
    char *agent_id;
    char *name;
    uint32_t capabilities;     // 能力位图
    agentos_session_t *session; // 关联会话
} agentos_agent_info_t;
```

**系统内代码**: `agentos_sys_agent_spawn()` / `agentos_sys_agent_terminate()` / `agentos_sys_agent_invoke()` / `agentos_sys_agent_list()`

**代码目录**: `AgentOS/agentos/atoms/syscall/src/agent.c` (~538 行)

**来源**: syscall_api_contract.md、CoreLoopThree 具体实现方案
**参见**: Skill（技能）、Session（会话）、Contract（契约）、MAC（多智能体协作框架）

---

#### AiramaxSyscall / 系统调用层

**定义**: 用户态程序请求内核服务的标准接口。覆盖 **七大功能域**：Agent 管理、任务管理、技能管理、会话管理、沙箱管理、内存操作、可观测性（遥测/限流/熔断）。遵循机制与策略分离原则，提供约 **28+ 个公开 API**。

> ⚠️ **拼写注意**: 标准英文名为 **AiramaxSyscall**（Aira**m**ax，非 Airmax），源自项目名 "OpenAirymax" 的变体拼写。
>
> ⚠️ **v2.0 修正（v3.0 确认）**: 原"约 50 个调用"不准确——经源码审计实际为 28+ 公开 API。

**标准名称**: 系统调用层 (AiramaxSyscall)

**七大功能域及 API 列表**:

| 功能域 | API 数量 | 关键函数签名 |
|--------|---------|-------------|
| **Agent** | 5 | `sys_agent_spawn`, `sys_agent_terminate`, `sys_agent_invoke`, `sys_agent_list`, `sys_agent_cleanup` |
| **Task** | 4 | `sys_task_submit`, `sys_task_query`, `sys_task_wait`, `sys_task_cancel` |
| **Skill** | 4 | `sys_skill_install`, `sys_skill_execute`, `sys_skill_list`, `sys_skill_uninstall` |
| **Session** | 4 | `sys_session_create`, `sys_session_get`, `sys_session_close`, `sys_session_list` |
| **Sandbox** | 3 | `sys_sandbox_create`, `sys_sandbox_permission`, `sys_sandbox_quota` |
| **Memory** | 4 | `sys_memory_write`, `sys_memory_search`, `sys_memory_get`, `sys_memory_delete` |
| **Telemetry** | 5 | `sys_telemetry_metrics`, `sys_telemetry_traces`, `sys_rate_limiter_*`, `sys_circuit_breaker_*` |

**统一入口**:
```c
int agentos_syscall_invoke(
    const char *syscall_name,
    const char *params_json,   // JSON 参数格式
    agentos_result_t *result   // 统一结果结构
);
```

**性能基线**（来自 syscall.md）:
| 操作 | P50 延迟 | P99 延迟 |
|------|---------|---------|
| 空 转 syscall | 0.8 μs | 2.0 μs |
| task.submit | 5 μs | 15 μs |
| memory.write | 8 μs | 25 μs |
| session.create | 3 μs | 10 μs |
| telemetry.metrics | 2 μs | 8 μs |

**系统内代码**: `agentos_sys_*` 函数族

**代码目录**: `AgentOS/agentos/atoms/syscall/`
- `src/agent.c` (~538 行) — Agent 系统调用
- `src/task.c` — 任务系统调用
- `src/skill.c` — 技能系统调用
- `src/session.c` — 会话管理
- `src/sandbox.c` / `sandbox_permission.c` / `sandbox_quota.c` / `sandbox_utils.c` — 沙箱
- `src/memory.c` — 内存操作
- `src/rate_limiter.c` — 限流器
- `src/telemetry.c` — 遥测
- `src/circuit_breaker.c` — 熔断器
- `src/syscall_entry.c` / `syscall_table.c` — 入口与路由表
- `include/syscalls.h` — 统一接口定义

**来源**: Capital_Architecture/syscall.md、syscall_api_contract.md
**参见**: 微核心(MicroCoreRT)、IPC、Agent

---

#### Authentication (认证)

**定义**: 验证通信参与方身份的过程，确保消息来源可信。支持 JWT Token 和 API Key 两种认证方式。

**标准名称**: 认证 (Authentication)

**系统内代码**: JWT 解析（`jwt_decode`）、API Key 校验（`api_key_validate`）

**代码目录**: `AgentOS/agentos/daemon/common/src/svc_auth.c`

**来源**: protocol_contract.md
**参见**: Authorization（授权）

---

#### Authorization (授权)

**定义**: 确定已认证实体是否有权限执行特定操作的过程。基于 RBAC（Role-Based Access Control）模型。

**标准名称**: 授权 (Authorization)

**系统内代码**: Cupolas 权限引擎 `cupolas_permission_check()`

**代码目录**: `AgentOS/agentos/cupolas/src/permission/permission_engine.c`

**来源**: protocol_contract.md、安全设计指南
**参见**: Authentication（认证）、Permission（权限）、Cupolas 安全穹顶

---

### C

#### Contract (契约)

**定义**: 机器可读的能力描述文件，定义 Agent 或 Skill 的接口、能力、权限等元数据。采用 JSON Schema 格式。

**标准名称**: 契约 (Contract)

**关联文件**: `Input Schema`（输入模式）、`Output Schema`（输出模式）、`Schema Version`（模式版本）

**来源**: agent_contract.md、skill_contract.md
**参见**: Input Schema、Output Schema、Schema Version

---

#### CognitiveEvolution / 认知进化系统

> **v3.0 新增术语** — v2.0 完全遗漏此组件

**定义**: CoreLoopThree 认知层的**自适应认知能力提升系统**，基于 MCIS 理论 C 维度设计。实现从感知到创造的五级认知层级跃迁机制，通过经验记录→模式提取→策略进化→知识迁移的完整闭环，使 Agent 的认知能力在运行过程中持续自主提升。

**标准名称**: 认知进化系统 (CognitiveEvolution)

**五级认知层级**:
```c
typedef enum {
    COG_LEVEL_PERCEPTION = 0,   // 感知 — 基础信息接收与处理
    COG_LEVEL_REACTION,          // 反应 — 基于规则的模式匹配响应
    COG_LEVEL_LEARNING,          // 学习 — 从经验中提取规律并优化策略
    COG_LEVEL_REASONING,         // 推理 — 多步逻辑推导与因果分析
    COG_LEVEL_CREATION           // 创造 — 跨领域综合与创新方案生成
} cog_level_t;
```

**层级跃迁阈值**: 0 → 0.2 → 0.4 → 0.7 → 0.9（基于平均 fitness 驱动）

**核心子系统**:

| 子系统 | 功能 | 关键参数 |
|--------|------|---------|
| **经验记录环** | 环形缓冲区存储历史经验（上限 10,000 条，FIFO 淘汰） | `struct cog_experience_s` |
| **模式提取引擎** | 按 action/outcome 子串匹配提取重复模式 | `extract_patterns()` |
| **策略进化器** | 置信度 > 0.3 的模式自动生成策略，指数衰减 fitness | `evolve_strategies()` |
| **策略选择器** | 域过滤 + 上下文匹配(1.5x 加成) + 成功对数加成 | `select_strategy()` |
| **知识迁移模块** | 跨域复用策略（迁移分 = fitness × 0.7） | `transfer_knowledge()` |
| **层级评估器** | 平均 fitness 驱动五级跃迁 | `evaluate_level()` |

**反馈类型**:
```c
typedef enum {
    COG_FEEDBACK_POSITIVE = 1,  // 正反馈 — 强化当前策略
    COG_FEEDBACK_NEGATIVE,      // 负反馈 — 降低策略权重
    COG_FEEDBACK_NEUTRAL         // 中性反馈 — 信息积累
} cog_feedback_t;
```

**关键数据结构**:
```c
typedef struct {
    char *action;               // 执行动作
    char *outcome;              // 执行结果
    double reward;              // 奖励值
    int64_t timestamp_ns;       // 时间戳
} cog_experience_t;

typedef struct {
    char *pattern_id;           // 模式标识
    char *action_prefix;        // 动作前缀
    char *outcome_suffix;       // 结果后缀
    double confidence;          // 置信度
    int occurrence_count;       // 出现次数
} cog_pattern_t;

typedef struct {
    char *strategy_id;          // 策略标识
    char *pattern_ref;          // 关联模式
    double fitness;             // 适应度（指数衰减）
    int domain_mask;            // 适用域掩码
    int64_t last_used;          // 最后使用时间
} cog_strategy_t;
```

**扩展 API**（15 个）:
`cog_evolution_create/destroy`, `record_experience`, `extract_patterns`,
`evolve_strategies`, `select_strategy`, `transfer_knowledge`,
`evaluate_level`, 以及自定义回调注册 (`cog_reward_fn`, `cog_change_fn`)

**代码位置**: `coreloopthree/src/cognition/ext/cognitive_evolution.c` (**532 行**) + `include/cognitive_evolution.h` (**130 行**)

**代码前缀**: `cog_*` / `agentos_cog_*`

**来源**: CN_02 认知层理论（C 维度演化）、MCIS 体系并行论
**参见**: Thinkdual（双思考系统）、CoreLoopThree（三层认知循环）、MAC（多智能体协作框架）

---

#### CoreKern / 原子核心

**定义**: AgentOS 微核心中不可再分的基础内核实现，承载 IPC、内存管理、任务调度、时间服务、OOM 处理、可观测性等原子机制。是体系并行论中 **基础体 (Base Body)** 的具象实现。

> **v2.0 修正（v3.0 确认）**: 原"承载...等原子机制"描述不够精确。实际包含 **6 大子系统**。

**标准名称**: 原子核心 (CoreKern)

**六大子系统明细**:

| 子系统 | 关键头文件 | 关键函数/宏 |
|--------|-----------|------------|
| **IPC** | `include/ipc.h` | `agentos_ipc_channel_create/send/recv/destroy` |
| **内存管理** | `include/mem.h` | `AGENTOS_MALLOC/FREE/CALLOC/REALLOC/STRDUP` |
| **内存池** | `mem/pool.c` | `agentos_memory_pool_create` |
| **内存守卫** | `mem/guard.c` | 内存保护检查 |
| **OOM 处理** | `mem/oom_handler.c` | 五级 OOM 响应框架 |
| **任务调度** | `include/task.h` | 5 状态模型 + POSIX/Windows 双平台 |
| **时间服务** | `time/clock.c` | `agentos_clock_gettime` |
| **事件循环** | `time/event.c` | `agentos_event_loop_run` |
| **定时器** | `time/timer.c` | `agentos_timer_set` |
| **可观测性** | `observability/observability.c` | Metrics/Traces/Health |
| **错误处理** | `error.c` | `agentos_error_t` 类型体系 |

**代码目录**: `AgentOS/agentos/atoms/corekern/`
- `src/core_init.c` (~68 行) — 内核入口：init 序列 mem→task→ipc→time
- `src/ipc/binder.c` / `buffer.c`
- `src/mem/alloc.c` / `guard.c` / `pool.c` / `oom_handler.c`
- `src/task/thread.c` / `scheduler.c` / `scheduler_posix.c` / `scheduler_windows.c` / `scheduler_core.c`
- `src/time/clock.c` / `event.c` / `timer.c`
- `src/error.c`
- `src/observability/observability.c`
- `include/agentos.h`（主头文件）/ `mem.h` / `ipc.h` / `task.h` / `time.h` / `error.h`
- `include/memory_compat.h`（安全兼容层宏）
- `include/agentos_memory.h`（安全释放宏）

**来源**: 架构设计原则、Capital_Architecture/microkernel.md
**参见**: 微核心(MicroCoreRT)、IPC、AiramaxSyscall

> **跨文档裁决**: CN_04 设计原则称"4 个不可再分的基础(IPC/内存/任务/时间)"，
> 但经源码审计实际为 **6 大子系统**（增加 OOM 处理、可观测性、错误处理）。
> **以本表 6 大子系统为准。**

---

#### CoreLoopThree / 三层认知循环

**定义**: AgentOS 的三层认知-执行-记忆主循环运行时，包含：

| 层级 | 名称 | 职责 | 代码位置 |
|------|------|------|---------|
| **L1 认知层** | Cognition Engine | 意图解析 → DAG 规划 → 分发 → 协调 → 批评 | `src/cognition/` |
| **L2 执行层** | Execution Engine | 任务执行 → 单元调度 → 补偿事务 | `src/execution/` |
| **L3 记忆层** | Memory Engine | 结果写入 → 查询 → 挂载 | `src/memory/` |

认知层实现双思考系统（Thinkdual）的 **t2/t1-f/t1-p 三组件认知架构**。

**标准名称**: 三层认知循环 (CoreLoopThree)

**旧称/禁止使用**: "第三循环内核"、"三层循环运行时"、"三层一体架构"、"三层循环核心"

**系统内代码**: `agentos_loop_*` 函数族（如 `agentos_loop_create/run/stop/submit`）

**代码目录**: `AgentOS/agentos/atoms/coreloopthree/`

**v3.0 审计确认完整统计**（**~75 源文件, ~25,557 行**）:

##### 认知层 (cognition/) — 30+ 文件

###### 引擎总控
| 文件 | 行数 | 功能 |
|------|------|------|
| `engine.c/h` | 785 | 5 阶段管线（Phase 0-4） |

###### 意图解析 (intent/)
| 文件 | 行数 | 功能 |
|------|------|------|
| `intent_parser.c` | 784 | 自然语言意图提取 |
| `intent_classifier.c` | — | 意图分类器（sc_intent_classifier） |
| `entity_extractor.c` | — | 实体识别 |
| `intent_utils.c` | — | 意图工具函数 |

###### 规划器 (planner/)
| 文件 | 行数 | 功能 |
|------|------|------|
| `reactive.c` | — | 反应式规划（ReAct 变体） |
| `hierarchical.c` | — | 层次化规划 |
| `ml_planner.c` | — | ML 驱动规划 |
| `reflective.c` | 679 | 反思式规划 |

###### 分发器 (dispatch/) — 并行任务分发
| 文件 | 行数 | 功能 |
|------|------|------|
| `parallel_dispatcher.c` | — | 并行分发（工具调用级别） |
| `delegate.c` | — | 委派分发 |

> **v3.0 重要区分**: `dispatch/` 目录是**并行任务分发器**（工具调用级别），
> 与 `dispatcher/` 目录的 **Agent 选择策略器**是两套完全不同的系统（详见下方 Dispatcher 条目）。

###### 协调器 (coordinator/)
| 文件 | 行数 | 功能 |
|------|------|------|
| `dual_model.c` | 711 | 双模型协调（t2↔t1 切换） |
| `weighted.c` | — | 加权协调 |
| `majority.c` | — | 多数投票协调 |
| `arbiter.c` | — | 仲裁者协调 |
| `coordinator_adapter.c` | — | 协调器适配 |

###### 批判/评审 (critique/)
| 文件 | 行数 | 功能 |
|------|------|------|
| `triple_coordinator.c` | 544 | **t2/t1-f/t1-p 三组件协调器** |
| `stream_critic.c` | 669 | 流式三要素验证器 |
| `confidence_calibrator.c` | — | 置信度校准 |

###### 基础认知 (foundation/)
| 文件 | 行数 | 功能 |
|------|------|------|
| `thinking_chain.c` | **1387** | **t2 思考链**（上下文窗口+工作记忆+推理步骤 DAG） |
| `semantic_unit.c` | 324 | 语义单元检测（中文标点感知） |
| `metacognition.c` | 791 | **元认知**（五维度评估） |

###### 认知扩展 (ext/) — **v3.0 新增详细记录**
| 文件 | 行数 | 功能 |
|------|------|------|
| `cognitive_evolution.c` | **532** | **认知进化系统**（5级层级跃迁+经验记录+模式提取+策略进化+知识迁移） |
| `multi_agent_collaboration.c` | **870** | **多智能体协作框架（MAC）**（4种协作模式+4种共识策略+任务委托评分） |

###### Agent 调度策略 (dispatcher/) — **v3.0 新增，区别于 dispatch/**
| 文件 | 行数 | 功能 |
|------|------|------|
| `ml_based.c` | **297** | **ML 多维调度策略**（4维特征在线学习：cost/perf/trust/priority） |
| `priority.c` | **135** | 优先级调度策略 |
| `round_robin.c` | **146** | 轮询调度策略 |
| `weighted.c` | **200** | 加权调度策略（Dispatcher 版） |

###### 上下文处理 (context/) — **v3.0 新增**
| 文件 | 行数 | 功能 |
|------|------|------|
| `context_processor.c` | **408** | **可插拔上下文处理引擎**（4种处理器链式流水线） |

##### 执行层 (execution/) — 13+ 文件

###### 执行引擎
| 文件 | 行数 | 功能 |
|------|------|------|
| `engine.c` | 871 | 执行引擎总控 |

###### 执行单元 (units/) — **v3.0 全面扩充**（8 单元, 共 4,527 行）
| 文件 | 行数 | 功能 | 安全特性 |
|------|------|------|---------|
| `api.c` | **466** | HTTP/HTTPS API 调用单元 | 原生 socket HTTP 客户端 + Bearer Token + 10 条 URL 缓存 |
| `browser.c` | **2372** | **浏览器自动化控制单元（CDP）** | **项目最大单文件**；Chrome DevTools Protocol over WebSocket；RFC6455 帧编解码；连接池(16)+沙箱上下文(64)；URL 白名单+私有 IP 检测+JS 危险模式拦截；4 级元素等待降级 |
| `code.c` | **425** | 代码执行单元（Python/JS/Node.js） | 跨平台临时文件 + Shell 参数转义防注入 + 4MB 限制 |
| `db.c` | **283** | 数据库操作单元（SQLite3） | **严格只读白名单**：仅 SELECT；35 SQL 关键词黑名单 + 14 注入模式拦截；1000 行上限 |
| `file.c` | **345** | 文件操作单元（沙箱） | 路径遍历防护（../ // \\ 检测）；realpath 验证不逃逸 root_dir |
| `shell.c` | **269** | Shell 命令执行单元 | **双重安全**：21 命令白名单 + 12 种元字符注入拦截；30 秒超时强制终止 |
| `tool.c` | **122** | 工具调用执行单元 | invoke/validate/describe 3 子命令 |
| `weighted.c` | **145** | 加权执行单元 | 委托 strategy_compute_weighted_score 公共函数 |

###### 执行核心 (core/)
| 文件 | 行数 | 功能 |
|------|------|------|
| `registry.c` | **174** | **执行单元注册表**（单向链表 + CAS 原子锁初始化 + 重名检查 EEXIST） |
| `trace.c` | **205** | **责任链追踪器**（OpenTelemetry 风格 span 追踪 + 全局 span 链 + 自增 ID） |

###### 补偿事务 (transaction/) — **v3.0 新增**
| 文件 | 行数 | 功能 |
|------|------|------|
| `compensation.c` | **242** | **Saga 补偿事务管理器**（操作注册链表 + 人工介入队列 + 消费式获取 + 自定义资源释放回调） |

##### 记忆层 (memory/) — 3+ 文件
| 文件 | 行数 | 功能 |
|------|------|------|
| `memory_service.c` | 138 | 记忆读写服务 |
| `engine.c` | — | 记忆引擎编排 |
| （更多见 MemoryRovol 术语） | | |

##### 主循环与工具
| 文件 | 行数 | 功能 |
|------|------|------|
| `loop.c` | ~1200 | **主循环核心**：create/run/stop/submit/persistent/restore/list_checkpoints |
| `config_loader.c` | — | 配置加载 |
| `utils/id_utils.c` | **102** | ID 生成工具（5 种：task/plan/record/session/uuid，全部原子操作） |
| `utils/error_utils.c` | **227** | 错误工具（23 种错误码<->中文名映射 + 纳秒时间戳上下文 + cJSON 序列化） |

##### 公共头文件 (include/) — 11 个
| 文件 | 行数 | 功能 |
|------|------|------|
| `agent_registry.h` | 57 | Agent 注册中心接口（agent_info_t + 回调类型） |
| `browser.h` | 95 | 浏览器公开 API（CDP 表单填充 + 4 级元素等待降级） |
| `cognitive_evolution.h` | **130** | 认知进化系统（5 级层级枚举 + 4 大数据结构 + 15 个 API） |
| `compensation.h` | **105** | 补偿事务接口（Saga 模式条目/管理器/结果结构） |
| `config_loader.h` | 45 | 配置加载器（YAML/JSON/INI + 权重解析） |
| `error_utils.h` | 89 | 错误工具（上下文结构 + 便捷宏 ERROR_CONTEXT_CREATE） |
| `id_utils.h` | 63 | ID 工具（5 种唯一标识符生成接口） |
| `multi_agent_collaboration.h` | **132** | **MAC 框架**（4 模式 + 5 态任务 + 4 共识策略 + 17 个 API） |

**测试文件** (~17): test_loop/test_cognition/test_intent_parser/test_execution/test_compensation/test_memory/test_browser/test_triple_coordinator/test_multi_agent/test_parallel_dispatcher/test_context_processor/test_cognitive_evolution/test_semantic_unit/test_cognition_e2e/test_thread_safety/test_metacognition_calibration/test_performance_benchmark/test_cl3_stubs

**来源**: 架构设计原则、Capital_Architecture/coreloopthree.md
**参见**: Thinkdual（双思考系统）、MemoryRovol（记忆卷载）、TaskFlow（任务流引擎）、CognitiveEvolution（认知进化系统）、MAC（多智能体协作框架）、ContextProcessor（上下文处理引擎）

---

#### Cupolas / 安全穹顶

**定义**: AgentOS 的 **内生安全防护层**，采用四重防护链 + 七大子系统的纵深防御架构。

> **v2.0 修正（v3.0 确认）**: 原描述仅提到 4 层（workbench/permission/sanitizer/audit），遗漏了 Security Vault、Network Security、Guards 三个重要子系统。实际为 **7 大子系统**。

**四重防护链**:
```
输入净化（Sanitizer）→ 权限仲裁（Permission）→ 网络过滤（Network Filter）→ 审计记录（Audit Trail）
```

**七大全子系统**:

| 子系统 | 目录 | 关键文件 | 功能 |
|--------|------|---------|------|
| **① Guards 守卫** | `src/guards/` | `guard_core.c`, `guard_integration.c` | 安全执行内核、守卫集成 |
| **② Permission 权限** | `src/permission/` | `permission_engine.c`, `permission_cache.c`, `permission_rule.c` | RBAC + YAML 规则引擎、权限缓存 |
| **③ Sanitizer 净化** | `src/sanitizer/` | `sanitizer_core.c`, `sanitizer_cache.c`, `sanitizer_rules.c` | 四阶段输入净化管道 |
| **④ Audit 审计** | `src/audit/` | `audit_logger.c`, `audit_queue.c`, `audit_rotator.c`, `audit_overflow.c` | SHA-256 哈希链不可篡改日志 |
| **⑤ Workbench 工作台** | `src/workbench/` | `workbench.c`, `workbench_process_core.c`, `workbench_container.c`, `workbench_limits.c` | 容器/进程/WASM 多模式隔离 |
| **⑥ Security Vault 金库** | `src/security/` | `cupolas_vault.c`, `cupolas_runtime_protection.c`, `cupolas_entitlements.c`, `cupolas_signature.c` | AES-256-GCM 加密、Seccomp+CFI 运行时保护、数字签名 |
| **⑦ Network Security 网络** | `src/security/network/` | `cupolas_network_security.c`, `tls_security.c`, `http_security.c`, `dns_security.c`, `network_filter.c`, `network_utils.c` | TLS/HTTP/DNS 安全、出站连接过滤 |

**其他组件**:
- `cupolas.c` (~467 行) — 安全穹顶 Facade（init/cleanup/version/check_permission/sanitize_input/execute_command/flush_audit）
- `cupolas_config.c/h` — 配置管理
- `cupolas_metrics.c/h` — 指标收集
- `cupolas_monitoring.c/h` — 监控
- `circuit_breaker.c/h` — 熔断器
- `yaml_minimal.c/h` — YAML 最小解析器
- `cupolas_error.c/h` — 错误处理
- `platform/platform.c/h` — 平台适配
- `utils/cupolas_utils.c/h` — 工具函数

**公共接口头文件**:
- `include/cupolas.h` — 主接口
- `include/safety_guard.h` — SafetyGuard V2
- `include/dynamic_policy_engine.h` — 动态策略引擎 V2
- `include/zero_trust_integration.h` — 零信任架构 V2

**标准名称**: 安全穹顶 (Cupolas)

**系统内代码**: `cupolas_*` 函数族（如 `cupolas_init/cleanup/check_permission/add_rule/sanitize_input/execute_command/flush_audit/workbench_create/destroy/permission_check/sanitize_validate/audit_log/vault_store/runtime_protection/network_filter`）

**代码目录**: `AgentOS/agentos/cupolas/`（~40+ 源文件，27,000+ 行）

**来源**: 架构设计原则、安全设计指南
**参见**: Security by Design（安全内生设计）、Sandbox（沙箱）、Service Isolation（服务隔离）

> **跨文档裁决**: CN_04 和 ARCHITECTURAL_PRINCIPLES.md E-1 描述为"四重防护(workbench/permission/sanitizer/audit)"，
> 经源码审计实际为 **7 大子系统**。**以本表 7 大子系统为准。**

---

### D

#### Daemon / 用户态服务层

**定义**: AgentOS 的用户态服务集群，共 **10 个独立服务进程**，以 `_d` 后缀命名，运行在 Linux 用户态，通过 IPC 与微内核通信，通过 JSON-RPC 2.0 对外提供服务。

**标准名称**: 用户态服务层 (Daemon)

**旧称/禁止使用**: "后端服务层"、"守护进程"、"daemon进程"

**强制说明**: 在所有文档中必须使用"用户态服务层（Daemon）"，**严禁使用"守护进程"**。"守护进程"一词在 AgentOS 语境中具有误导性，会与操作系统传统守护进程概念混淆。所有规范、文档、代码注释中均不得出现"守护进程"。

**10 个服务完整列表**:

| # | 服务名 | 目录 | 端口/Socket | 功能概述 | 关键文件 |
|---|--------|------|-------------|---------|---------|
| 1 | **gateway_d** | `daemon/gateway_d/` | HTTP :8080 | API 网关（MCP/A2A/OpenAI 兼容协议） | main.c(~292), service.c, gateway_mcp_server.c, gateway_openai_compat.c, gateway_a2a_handler.c |
| 2 | **llm_d** | `daemon/llm_d/` | Unix Socket | LLM 推理代理（缓存/Token 计费/成本追踪/多 Provider 路由） | main.c(~682), service.c, cache.c, response.c, cost_tracker.c, token_counter.c, providers/(openai/anthropic/google/deepseek/local), registry.c |
| 3 | **tool_d** | `daemon/tool_d/` | Unix Socket | 工具调用代理（注册/执行/验证/缓存） | main.c(~669), service.c, executor.c, registry.c, cache.c, config.c, validator.c |
| 4 | **market_d** | `daemon/market_d/` | Unix Socket | 市场/注册表服务（Agent/Skill 注册与安装） | — |
| 5 | **sched_d** | `daemon/sched_d/` | Unix Socket | 任务调度服务（priority_based/weighted/round_robin/ml_based 四种策略） | — |
| 6 | **monit_d** | `daemon/monit_d/` | HTTP :9090 | 监控服务（指标/追踪/告警/日志/服务健康） | — |
| 7 | **channel_d** | `daemon/channel_d/` | Unix Socket | 通道服务 | — |
| 8 | **observe_d** | `daemon/observe_d/` | Unix Socket | 观测服务 | — |
| 9 | **notify_d** | `daemon/notify_d/` | Unix Socket | 通知服务 | — |
| 10 | **info_d** | `daemon/info_d/` | Unix Socket | 信息服务 | — |

**公共基础设施库** (`daemon/common/`) — 所有 Daemon 共享:

| 组件 | 文件 | 行数(估) | 功能 |
|------|------|---------|------|
| **Orchestrator 编排器** | `orchestrator.c` | **1967** | Phase 0-4 串行管线（分解→规划→生成→评审→验证→审计→对齐），LLM 调用、记忆查询/写入、元认知评估、置信度校准、熔断集成、对齐漂移检测 |
| **公共服务** | `svc_common.c` | — | 公共服务函数 |
| **认证授权** | `svc_auth.c` | — | JWT/API Key 认证 |
| **IPC 客户端** | `ipc_client.c` | — | 内核 IPC 客户端 |
| **IPC 总线** | `ipc_service_bus.c` | — | IPC 服务总线 |
| **线程池** | `thread_pool.c` | — | 线程池 |
| **检查点** | `checkpoint.c` | — | 检查点管理 |
| **故障恢复** | `api_recovery.c` | — | API 故障恢复 |
| **熔断器** | `circuit_breaker.c` | — | 熔断器 |
| **配置管理** | `config_manager.c` | — | 配置管理器 |
| **服务发现** | `service_discovery.c` | — | 服务发现 |
| **安全** | `daemon_security.c` | — | 守护进程安全 |
| **降级** | `daemon_degradation.c` | — | 降级策略 |
| **事件驱动** | `daemon_event_driver.c` | — | 事件驱动框架 |
| **事件循环** | `agentos_event_loop.c` | — | 事件循环 |
| **告警** | `alert_manager.c` | — | 告警管理器 |
| **方法分发** | `method_dispatcher.c` | — | 方法分发器（圈复杂度 14→4.2） |
| **并行分发** | `parallel_dispatcher.c` | — | 并行分发 |
| **日志清洗** | `log_sanitizer.c` | — | 日志清洗 |
| **输入验证** | `input_validator.c` | — | 输入验证 |
| **参数验证** | `param_validator.c` | — | 参数校验 |
| **JSON-RPC 辅助** | `jsonrpc_helpers.c` | — | JSON-RPC 辅助 |
| **统一指标** | `unified_metrics.c` | — | 统一指标 |
| **平台兼容** | `platform_compat.c` | — | 平台兼容 |
| **安全字符串** | `safe_string_utils.c` | — | 安全字符串操作 |

**代码目录**: `AgentOS/agentos/daemon/`

**来源**: 架构设计原则、Capital_Architecture/daemon_module.md
**参见**: 微核心(MicroCoreRT)、IPC、Protocols（协议适配层）

> **跨文档裁决**: daemon_module.md(V7.0) 列出 6 个服务，经源码审计实际为 **10 个服务**（增加 channel_d/observe_d/notify_d/info_d）。**以本表 10 个服务列表为准。**

---

### E

#### Error Code System / 统一错误码体系

**定义**: AgentOS 采用 **双错误码体系**：

**体系一（首要）— C 语言负整数体系**：
- 定义于 `error.h`（`AgentOS/agentos/atoms/corekern/include/error.h`）
- 基础码: `AGENTOS_SUCCESS = 0`
- 扩展码: `AGENTOS_EINVAL = -2`, `AGENTOS_ENOMEM = -12` 等 POSIX 兼容
- **10 段分段规则**（v3.0 确认）:

| 段前缀 | 范围 | 含义 | 示例值 |
|--------|------|------|--------|
| 100x | 1001-1099 | Task 相关 | AGENTOS_ERR_TASK_NOT_FOUND = -1001 |
| 200x | 2001-2099 | Memory 相关 | AGENTOS_ERR_MEMORY_NOT_FOUND = -2001 |
| 300x | 3001-3099 | Session 相关 | AGENTOS_ERR_SESSION_EXPIRED = -3001 |
| 400x | 4001-4099 | Telemetry 相关 | AGENTOS_ERR_TELEMETRY_DISABLED = -4001 |
| 500x | 5001-5099 | Internal / General | AGENTOS_ERR_INTERNAL = -5001, AGENTOS_ERR_INVALID_PARAM = -5002 |
| 600x | 6001-6099 | Skill 相关 | AGENTOS_ERR_SKILL_NOT_FOUND = -6001 |
| 700x | 7001-7099 | Agent 相关 | AGENTOS_ERR_AGENT_NOT_FOUND = -7001 |
| 800x | 8001-8099 | Sandbox 相关 | AGENTOS_ERR_SANDBOX_DENIED = -8001 |
| 900x | 9001-9099 | Security 相关 | AGENTOS_ERR_SECURITY_VIOLATION = -9001 |
| -1~-999 | 特殊值 | 兼容/通用 | AGENTOS_ERR_UNKNOWN = -1, AGENTOS_ELAST = -999 |

**体系二（次要）— SDK 十六进制体系**：
- 定义于 `error_code_reference.md`
- 范围: `0x0000` - `0x7FFF`
- 用于 SDK 和外部接口

**错误包装链**:
```c
agentos_error_t agentos_error_wrap(
    int base_err, const char *file, int line,
    const char *fmt, ...);  // 错误链包装（带文件/行号溯源）
```

**错误上下文结构**（v3.0 新增，来自 error_utils.h）:
```c
typedef struct {
    int code;                   // 错误码
    const char *message;        // 错误消息
    const char *file;           // 源文件
    int line;                   // 行号
    const char *function;       // 函数名
    int64_t timestamp_ns;       // 纳秒时间戳
} agentos_error_context_t;
```

**标准名称**: 统一错误码体系 (ErrorCodeSystem)

**系统内代码**: `AGENTOS_E*` / `AGENTOS_ERR_*` 宏族（700+ 定义值）
**核心头文件**: `AgentOS/agentos/atoms/corekern/include/error.h`
**辅助头文件**: `AgentOS/agentos/commons/utils/error/types.h`（10 段分段定义）

**禁止**: 在 C 内核代码中使用十六进制错误码；在 SDK 中使用负整数错误码

**来源**: error_code_reference.md、error.h、types.h
**参见**: Error Code（错误码）

---

#### Feedback Loop (反馈闭环)

**定义**: 系统通过输出结果调整后续行为的控制机制。包含三个层次：
- **实时反馈**: 同一轮次内的即时调整（延迟阈值 < 100ms，超时 >200ms 切换无反馈模式）
- **轮次内反馈**: 同一认知循环内的跨阶段反馈
- **跨轮次反馈**: 不同认知循环之间的历史反馈

**标准名称**: 反馈闭环 (Feedback Loop)

**系统内体现**: CoreLoopThree Phase 4 目标对齐（align_history[8] 环形缓冲区 + 漂移检测）

**来源**: 架构设计原则 S-1 正交独立性原则
**参见**: Engineering Cybernetics（工程控制论）、System Engineering（系统工程）

---

### I

#### Input Schema (输入模式)

**定义**: 定义 Skill 或 Agent 输入数据结构的 JSON Schema。

**标准名称**: 输入模式 (Input Schema)

**来源**: skill_contract.md
**参见**: Output Schema（输出模式）、Contract（契约）

---

#### IPC (进程间通信)

**定义**: 微核心提供的进程间通信机制。采用 **三层架构**：

| 层级 | 结构 | 大小 | 说明 |
|------|------|------|------|
| **轻量层** | 40 字节固定结构 | 40 bytes | sender_id(4) + receiver_id(4) + data[40] |
| **应用层** | 完整 header + payload | 可变 | 包含 TraceID、时间戳、元数据 |
| **实现层** | 底层原语 | — | 共享内存 / 消息队列 / 信号量 / Unix Domain Socket |

**三种通信模式**:
1. Request-Response（请求-响应，同步）
2. Publish-Subscribe（发布-订阅，异步）
3. Streaming（流式，长连接）

**关键数据结构**:
```c
typedef struct {
    uint32_t sender_id;
    uint32_t receiver_id;
    uint8_t data[AGENTOS_IPC_MSG_SIZE];  // 40 字节
} agentos_ipc_message_t;

typedef struct {
    void *shm_ptr;              // 共享内存段
    size_t shm_size;             // 段大小
    agentos_semaphore_t *sem;   // 信号量
} agentos_shared_memory_t;
```

**性能基线**（来自 ipc.md 详细基准表）:

| 指标 | P50 | P90 | P99 | P99.9 |
|------|-----|-----|-----|-------|
| 消息延迟 | 6 μs | 9 μs | 15 μs | 25 μs |
| 吞吐量 (64B) | 100,000 msg/s | — | — | — |
| 吞吐量 (1KB) | 80,000 msg/s | — | — | — |
| 吞吐量 (4KB) | 40,000 msg/s | — | — | — |
| CPU 占用 (10K msg/s) | 2-5% | — | — | — |
| 并发连接 | 1000+ channels | — | — | — |

**关键优化技术**:
- 零拷贝传输（无锁环形缓冲区 + CAS 原子操作）
- Cache Line 64 字节对齐（避免伪共享）
- NUMA-aware 内存分配
- 2MB 大页内存（减少 TLB miss）

**标准名称**: 进程间通信 (IPC)

**代码目录**: `AgentOS/agentos/atoms/corekern/src/ipc/`（binder.c, buffer.c）
**相关头文件**: `AgentOS/agentos/atoms/corekern/include/ipc.h`

**来源**: 架构设计原则、Capital_Architecture/ipc.md
**参见**: 微核心(MicroCoreRT)、AiramaxSyscall、Service Isolation（服务隔离）

> **跨文档裁决**: microkernel.md 称 "<10μs"，ipc.md 称 "<8μs"，TERMINOLOGY v2.0 统一为 "<8μs"。
> 以 ipc.md 详细基准表为准：**典型值 <8μs，P50 = 6μs，P99 = 15μs**。

---

### J

#### JWT (JSON Web Token)

**定义**: 用于认证和授权的紧凑、URL安全的令牌格式。

**标准名称**: JSON Web 令牌 (JWT)

**来源**: protocol_contract.md
**参见**: Authentication（认证）、Authorization（授权）

---

#### JSON-RPC 2.0

**定义**: AgentOS 采用的轻量级远程过程调用协议，支持请求-响应和通知两种模式。所有 Daemon 服务通过 JSON-RPC 2.0 对外暴露 RPC 方法。

**标准名称**: JSON-RPC 2.0

**关键实现文件**:
- `AgentOS/agentos/gateway/src/utils/jsonrpc.c` — JSON-RPC 实现
- `AgentOS/agentos/daemon/common/src/jsonrpc_helpers.c` — Daemon 侧辅助
- `AgentOS/agentos/daemon/common/src/method_dispatcher.c` — 方法分发

**JSON-RPC 错误码**（协议层）:

| 错误码 | 定义 |
|--------|------|
| -32700 | Parse error |
| -32600 | Invalid Request |
| -32601 | Method not found |
| -32602 | Invalid params |
| -32603 | Internal error |

**来源**: protocol_contract.md
**参见**: Error Code System（统一错误码体系）、TraceID

---

### M

#### MAC (Multi-Agent Collaboration) / 多智能体协作框架

> **v3.0 新增术语** — v2.0 完全遗漏此组件

**定义**: CoreLoopThree 认知层的**企业级多智能体协作基础设施**，支持最多 1024 个 Agent 并存、4096 个并发任务、128 个协作组、64 个并发共识过程。提供从独立工作到委托执行的四种协作模式，以及从多数投票到领导者否决的四种共识策略。

**标准名称**: 多智能体协作框架 (MAC)**

**旧称/禁止使用**: "Multi-Agent System"（过于泛化，无法体现 AgentOS 特有实现）

**四种协作模式**:
```c
typedef enum {
    MAC_MODE_INDEPENDENT = 0,    // 独立模式 — 各 Agent 独立工作，无协作
    MAC_MODE_COLLABORATIVE,      // 协作模式 — Agent 间共享信息，协同完成
    MAC_MODE_CONSENSUS,          // 共识模式 — 通过投票达成一致决策
    MAC_MODE_DELEGATED           // 委托模式 — 主 Agent 将子任务委派给专业 Agent
} mac_collab_mode_t;
```

**四种共识策略**:
| 策略 | 说明 | 适用场景 |
|------|------|---------|
| **Majority（多数票）** | 得票 >50% 即通过 | 快速决策 |
| **Unanimous（全票通过）** | 必须所有参与者同意 | 高风险操作 |
| **Weighted（加权投票）** | 按 reliability × (1 + performance) 加权 | 专业领域决策 |
| **Leader（领导者一票否决）** | 组 Leader 有最终否决权 | 紧急情况快速决策 |

**任务状态机**（5 态）:
```c
typedef enum {
    MAC_TASK_PENDING = 0,        // 待处理
    MAC_TASK_ASSIGNED,           // 已分配
    MAC_TASK_RUNNING,            // 执行中
    MAC_TASK_COMPLETED,          // 已完成
    MAC_TASK_FAILED              // 失败
} mac_task_state_t;
```

**任务委托评分算法**:
```
score = performance × 0.6 + reliability × 0.4
```

**数据结构规模**:
| 资源 | 上限 | 实现方式 |
|------|------|---------|
| Agent 数量 | 1,024 | 开放寻址哈希表 O(1) |
| Task 数量 | 4,096 | 开放寻址哈希表 O(1) |
| Group 数量 | 128 | 开放寻址哈希表 O(1) |
| Consensus 数量 | 64 | 开放寻址哈希表 O(1) |

**关键数据结构**:
```c
typedef struct {
    char *agent_id;
    double performance;          // 性能评分
    double reliability;          // 可靠度评分
    int max_concurrent;          // 最大并发能力
    char *capabilities_json;     // 能力描述（JSON）
} mac_agent_info_t;

typedef struct {
    char *group_id;
    char *leader_id;             // 组 Leader
    mac_agent_info_t **members;  // 成员数组
    size_t member_count;
    mac_collab_mode_t mode;      // 协作模式
} mac_group_t;
```

**核心 API**（17 个）:
`mac_framework_create/destroy`, `register/unregister_agent`,
`create/disband_group`, `delegate_task`, `collect_results`,
`start/vote/resolve_consensus`, `register_agents_batch`,
以及自定义聚合回调 `aggregate_fn`

**代码位置**: `coreloopthree/src/cognition/ext/multi_agent_collaboration.c` (**870 行**) + `include/multi_agent_collaboration.h` (**132 行**)

**代码前缀**: `mac_*` / `agentos_mac_*`

**来源**: CN_02 认知层理论（协同策略）、CN_04 设计原则（总体设计部）
**参见**: Thinkdual（双思考系统）、CoreLoopThree（三层认知循环）、CognitiveEvolution（认知进化系统）、Agent（智能体）

---

#### MemorySwap / 记忆交换算法

**定义**: 记忆卷载的存储层级交换机制，借鉴操作系统虚拟内存的分页交换。将记忆按访问热度划分为热/温/冷三个存储层级，在不同层级之间自动迁移记忆数据。

- 热记忆: 保持全状态驻留（RAM）
- 温记忆: 压缩后驻留（ZSTD）
- 冷记忆: 仅保留最小索引信息，需要时 swap-in

**标准名称**: 记忆交换算法（正式）/ MemorySwap（简称）

**系统内代码**: `agentos_memory_swap_manager_t`、`swap_score`、`swap_count`

**来源**: MemoryRovol 设计文档
**参见**: MemoryRovol（记忆卷载）、Forgetting Engine（遗忘机制）

---

#### MemoryRovol / 记忆卷载

**定义**: AgentOS 的 **四层渐进式记忆架构**，从原始数据到深层模式的完整记忆处理管线：

```
原始数据
    │
    ▼ L1 原始卷（Raw Storage）
    │   SHA-256 哈希 + ZSTD 压缩 + AES-256-GCM 加密（可选）
    │   append-only，冷热双模式（MRS_COLD_STORE / MRS_HOT_LOAD）
    │
    ▼ L2 特征层（Feature Extraction）
    │   HNSW O(log n) 向量搜索 + BM25 文本检索 + 混合融合
    │   多模型嵌入（OpenAI/DeepSeek/ONNX）
    │
    ▼ L3 结构层（Structure Binding）
    │   知识图谱（实体-关系图）+ Louvain 社区检测
    │   VSA 绑定/解绑算子 + Fourier 位置编码
    │
    ▼ L4 模式层（Pattern Mining）
    │   HDBSCAN + DBSCAN 密度聚类（双实现，含回退路径）
    │   0D 持久同调（Union-Find + 边排序）
    │   自动模式挖掘流水线
```

**名称由来**: Memory + Rovol（Roll + Volume 的融合造词），寓意记忆的滚动卷载。

**标准名称**: 记忆卷载 (MemoryRovol)

**旧称/禁止使用**: "记忆漩涡引擎"

**代码分布**（v3.0 确认 v2.0 修正）:

| 位置 | 内容 | 说明 |
|------|------|------|
| `agentos/atoms/memoryrovol/` | CMakeLists.txt + README.md | 骨架（规划中） |
| `agentos/atoms/memory/` | builtin_index/storage/retrieval/provider | **当前活跃的内建记忆实现** |
| `agentos/atoms/coreloopthree/src/memory/` | memory_service.c + engine.c | CoreLoopThree 记忆引擎集成 |
| `agentos/heapstore/src/heapstore_memory.c` | 数据分区中的记忆存储 | HeapStore 记忆分区 |

**性能基线**（来自 memoryrovol.md）:
| 指标 | 数值 | 条件 |
|------|------|------|
| 写入吞吐 | 10,000+ 条/秒 | L1 异步批量写入 |
| 向量检索延迟 | <10ms | FAISS IVF1024,PQ64, k=10 |
| 混合检索延迟 | <50ms | 向量+BM25, top-100 重排序 |
| 记忆抽象速度 | 100 条/秒 | L2->L3 渐进式 |
| 模式挖掘速度 | 10 万条/分钟 | L4 持久同调 |
| 空闲内存 | ~200MB | — |
| 高负载内存 | 4-8GB | — |

**来源**: 架构设计原则、Capital_Architecture/memoryrovol.md
**参见**: CoreLoopThree（三层认知循环）、Forgetting Engine（遗忘机制）、TimeSliceInfer（分时推理框架）

---

#### MEMORY_FREE_SAFE

**定义**: AgentOS 安全释放宏，定义于 `agentos_memory.h`，接受指向指针的指针（ptr-to-pptr），在释放内存后将指针置 NULL，防止悬垂指针（dangling pointer）。

**标准名称**: MEMORY_FREE_SAFE

**签名**: `MEMORY_FREE_SAFE(ptr_to_ptr)` — 参数为指向待释放指针的指针

**代码目录**: `AgentOS/agentos/atoms/corekern/include/agentos_memory.h`

**来源**: agentos_memory.h、安全编码规范
**参见**: Memory Safety Macro System（安全宏体系）

---

#### Memory Safety Macro System / 安全宏体系

**定义**: AgentOS 的完整内存安全宏体系，覆盖内存分配、释放、字符串操作和禁止函数，确保内存操作的类型安全和空指针安全。

**标准名称**: 安全宏体系 (Memory Safety Macro System)

**完整宏族清单**（v3.0 确认 v2.0 扩展）:

**类别 1 — AGENTOS_* 内存操作宏**（定义于 `memory_compat.h`）:
| 宏 | 替代 | 说明 |
|----|------|------|
| `AGENTOS_MALLOC(size)` | `malloc(size)` | 带溢出检查的安全分配 |
| `AGENTOS_CALLOC(nmemb, size)` | `calloc(nmemb, size)` | 安全清零分配 |
| `AGENTOS_REALLOC(ptr, size)` | `realloc(ptr, size)` | 安全重分配 |
| `AGENTOS_STRDUP(str)` | `strdup(str)` | 安全字符串复制 |
| `AGENTOS_STRNDUP(str, n)` | `strndup(str, n)` | 安全定长复制 |
| `AGENTOS_FREE(ptr)` | `free(ptr)` | 安全释放 |

**类别 2 — 安全操作宏**（定义于 `memory_compat.h`）:
| 宏 | 说明 |
|----|------|
| `AGENTOS_STRNCPY_TERM(dst, src, n)` | 安全 strncpy 并保证 null 终止符 |
| `AGENTOS_MEMCPY_SAFE(dst, src, n)` | 安全 memcpy 带边界检查 |
| `SAFE_MALLOC_ARRAY(type, count)` | 安全数组分配带整数溢出检查 |

**类别 3 — 安全释放宏**（定义于 `agentos_memory.h`）:
| 宏 | 说明 |
|----|------|
| `MEMORY_FREE_SAFE(pptr)` | 释放内存并置指针为 NULL（接受 ptr-to-ptr） |

**类别 4 — OOM 水位回调宏**（定义于 `memory_compat.h`）:
| 宏 | 说明 |
|----|------|
| `AGENTOS_MEM_WATERMARK_LOW` | 低水位回调（< 25%） |
| `AGENTOS_MEM_WATERMARK_MED` | 中水位回调（25%-50%） |
| `AGENTOS_MEM_WATERMARK_HIGH` | 高水位回调（50%-75%） |
| `AGENTOS_MEM_WATERMARK_CRIT` | 危机水位回调（> 75%） |

**类别 5 — SEC 合规宏**（定义于 `memory_compat.h`）:
| 宏 | 说明 |
|----|------|
| `AGENTOS_SEC_CLEAR(buf, len)` | 安全清除敏感缓冲区 |
| `AGENTOS_SEC_VALIDATE_PTR(ptr)` | 空指针验证 |
| `AGENTOS_SEC_VALIDATE_STR(s)` | 空字符串验证 |

**类别 6 — 禁止函数列表**（定义于 `banned_functions.h`）:
| 类别 | 禁止函数 |
|------|---------|
| **内存** | `malloc`, `free`, `calloc`, `realloc`, `strdup` |
| **I/O** | `printf`, `fprintf`, `sprintf`, `gets` |
| **字符串** | `strcpy`, `strcat`, `strncpy`(无显式终止) |
| **格式化** | `snprintf`(需用 safe 替代) |

**强制要求**: 所有 AgentOS C 代码必须使用安全宏体系，严禁直接调用禁止函数列表中的函数。

**代码目录**: `AgentOS/agentos/atoms/corekern/include/`

**来源**: 安全编码规范、memory_compat.h、agentos_memory.h、banned_functions.h
**参见**: MEMORY_FREE_SAFE、Security by Design（安全内生设计）、MicroCoreRT（微核心）

---

#### MicroCoreRT / 微核心

**定义**: 极简的操作系统内核抽象层，只提供最基本的 IPC、内存管理、任务调度、时间管理、可观测性功能，其他功能作为用户态服务实现。名称取自 Micro + Core + RT（Runtime），强调其作为运行时基础的核心地位。采用第四代微内核架构设计理念（参考 L4 MicroKernel）。

> **v3.0 确认 v2.0 修正**: 必须明确标注这是 **"微内核风格"（Microkernel-style）** 的用户态抽象层，不是真正的操作系统内核（不运行在 ring 0，不管理硬件，不能独立引导）。此区分对 TOC 评审至关重要。

**标准名称**: 微核心 (MicroCoreRT)

**旧称/禁止使用**: "微内核"、"Microkernel"（不加限定词时易与真正 OS 内核混淆）

**系统内代码**: `agentos_core_*` 函数族（如 `agentos_core_init/destroy`）

**代码目录**: `AgentOS/agentos/atoms/corekern/`（同 CoreKern 物理目录）

**代码量**: 内核原子操作代码约 6,000-8,000 行（不含 IPC Binder 等周边扩展则 ~6K；含全部则为 ~10K）

**性能基线**（来自 microkernel.md）:
| 指标 | 数值 | 条件 |
|------|------|------|
| IPC 消息延迟 | <10 μs (典型), P50=6μs | 1KB 共享内存 |
| IPC 吞吐量 | 100,000+ msg/s | 小包消息, 4 核 |
| 内存分配延迟 | <5ns | 池分配 |
| 任务调度延迟 | <1ms | 加权轮询 |
| 时间戳获取延迟 | <10ns | clock_gettime |
| 定时器精度 | ±1ms | Linux kernel 6.5 |
| 故障恢复时间 | <100ms | 服务崩溃 |
| 故障隔离性 | >99.9% | 服务崩溃不影响内核 |
| 并发连接 | 1000+ channels | 单实例, 内存 <50MB |

**来源**: 架构设计原则 K-1 极简内核原则、Capital_Architecture/microkernel.md
**参见**: CoreKern（原子核心）、IPC、AiramaxSyscall（系统调用层）、Service Isolation（服务隔离）

---

### O

#### Output Schema (输出模式)

**定义**: 定义 Skill 或 Agent 输出数据结构的 JSON Schema。

**标准名称**: 输出模式 (Output Schema)

**来源**: skill_contract.md
**参见**: Input Schema（输入模式）、Contract（契约）

---

### P

#### Permission (权限)

**定义**: 执行特定操作所需的授权，细粒度到文件访问、网络通信、系统调用等具体动作。基于 RBAC（Role-Based Access Control）模型 + YAML 规则引擎。

**标准名称**: 权限 (Permission)

**系统内代码**: `cupolas_permission_check(rule, context)`

**代码目录**: `AgentOS/agentos/cupolas/src/permission/`

**来源**: skill_contract.md
**参见**: Sandbox（沙箱）、Service Isolation（服务隔离）、Cupolas（安全穹顶）

---

#### Protocols / 协议适配层

**定义**: AgentOS 的多协议适配层，提供统一的协议抽象、路由、转换和扩展框架。支持 **9 种协议类型**：

> **v3.0 确认 v2.0 修正**: 原"12 种"不准确——实际枚举定义为 9 种。

**标准名称**: 协议适配层 (Protocols)

**协议类型枚举**:
```c
typedef enum agentos_protocol_type {
    PROTO_JSON_RPC,      // JSON-RPC 2.0
    PROTO_MCP,            // Anthropic MCP v1
    PROTO_A2A,            // Google A2A v0.3
    PROTO_OPENAI,         // OpenAI Enterprise
    PROTO_CLAUDE,         // Anthropic Claude
    PROTO_OPENJIUWEN,     // OpenJiuwen 中文生态
    PROTO_OPENCLAW,       // OpenClaw
    PROTO_CHINA_ECO,      // 中国生态
    PROTO_AGNTCY,         // AGNTCY ACP
} agentos_protocol_type_t;
```

**五层架构**:
| 层 | 目录 | 功能 |
|----|------|------|
| Common | `common/` | 统一协议抽象（unified_protocol, protocols_impl） |
| Core Router | `core/router/` | 协议路由器 |
| Core Adapter | `core/adapter/` | 协议扩展框架 |
| Core Transformers | `core/transformers/` | 协议转换器（14 个双向转换器） |
| Core Registry | `core/registry/` | 协议注册中心 |
| Standards | `standards/` | 标准协议（MCP/A2A/AGNTCY） |
| Integrations | `integrations/` | 商业平台适配（OpenAI/Claude/OpenClaw/OpenJiuwen/ChinaEco） |
| Frameworks | `frameworks/` | 框架适配（LangChain/AutoGen） |

**关键文件**:
- `protocol_toplevel_impl.c` (~184 行) — 顶层协议实现（adapter 创建/销毁/发送/接收）
- `protocol_router.c` (~629 行) — 协议路由器
- `protocol_extension_framework.c` (~918 行) — 协议扩展框架
- `protocol_transformers.c` (~663 行) — 协议转换器
- `protocol_registry.c` (~437 行) — 协议注册中心

**代码目录**: `AgentOS/agentos/protocols/`（~18,067 行）

**来源**: 架构设计原则
**参见**: 用户态服务层(Daemon)、Gateway（网关）

---

### S

#### Sandbox (沙箱)

**定义**: 隔离的执行环境，限制 Skill 的权限和资源访问，防止恶意或错误代码影响系统。支持三种隔离模式：进程隔离、容器隔离、WASM 隔离。

**标准名称**: 沙箱 (Sandbox)

**系统内代码**: `agentos_sys_sandbox_create/destroy/permission/quota`

**代码目录**: `AgentOS/agentos/atoms/syscall/src/sandbox*.c`（sandbox.c + sandbox_permission.c + sandbox_quota.c + sandbox_utils.c）

**来源**: skill_contract.md
**参见**: Service Isolation（服务隔离）、Permission（权限）、Cupolas Workbench（虚拟工作台）

---

#### Schema Version (契约模式版本)

**定义**: 契约 JSON Schema 的版本标识符，用于兼容性检查和版本迁移。

**标准名称**: 契约模式版本 (Schema Version)

**来源**: agent_contract.md
**参见**: Contract（契约）、Backward Compatibility（向后兼容）

---

#### Security by Design (安全内生设计)

**定义**: 将安全机制内嵌于系统架构和设计中的理念，而非事后附加补丁。Cupolas 安全穹顶是这一理念的具象实现。

**标准名称**: 安全内生设计 (Security by Design)

**来源**: 架构设计原则
**参见**: Cupolas（安全穹顶）、Sandbox（沙箱）

---

#### Service Isolation (服务隔离)

**定义**: 用户态服务之间相互隔离，只能通过内核 IPC 通信的安全机制。一个服务的崩溃不会影响其他服务。

**标准名称**: 服务隔离 (Service Isolation)

**来源**: 架构设计原则 K-3 故障隔离原则
**参见**: 微核心(MicroCoreRT)、IPC

---

#### Skill (技能)

**定义**: 可复用的执行单元，为智能体提供具体能力，如文件操作、网络请求、数据分析、浏览器自动化等。每个 Skill 通过 Contract 定义其接口和能力。

**标准名称**: 技能 (Skill)

**系统内代码**: `agentos_sys_skill_install/execute/list/uninstall`

**来源**: skill_contract.md
**参见**: Agent（智能体）、Contract（契约）

---

### T

#### TaskFlow / 任务流引擎

**定义**: AgentOS 的任务流引擎，基于 Pregel BSP（Bulk Synchronous Parallel）图计算模型，提供超步(superstep)计算、检查点、容错、工作流模式等功能，支撑复杂任务的有向无环图（DAG）编排。

**标准名称**: 任务流引擎 (TaskFlow)

**关键文件**:
- `taskflow_core.c` (~1185 行) — 核心：引擎创建/销毁/初始化/启停/暂停/恢复、图管理(顶点/边)、同步&异步执行、消息传递、检查点容错、统计、图分区
- `graph_engine.c` — 图引擎
- `pregel_engine.c` — Pregel 引擎
- `workflow_patterns.c` — 工作流模式（Pipeline/Parallel/Fan-out/Fan-in/Condition）
- `taskflow_advanced.c` — 高级功能
- `taskflow_execution_unit.c` — 执行单元

**代码目录**: `AgentOS/agentos/atoms/taskflow/`

**来源**: 架构设计原则
**参见**: CoreLoopThree（三层认知循环）

---

#### Thinkdual / 双思考系统

**定义**: AgentOS 的 **核心认知创新**，由 **三组件**构成的认知架构：

| 组件 | 代号 | 角色 | 代码位置 | 行数 |
|------|------|------|---------|------|
| **t2 主思考** | 慢思考 | 深度推理、反思调整、长期规划 | `foundation/thinking_chain.c` | **1387** |
| **t1-f 快思考-事实** | 快思考-事实 | 快速响应、事实检索、模式匹配、流式验证 | `critique/stream_critic.c` | **669** |
| **t1-p 专业思考** | 快思考-专业 | 专业领域仲裁、深度质量评估 | `critique/confidence_calibrator.c` | — |

三组件通过 **triple_coordinator**（三元组协调器）协同工作，实现效率与精度的动态平衡。

**"双思"的含义**: 深度思考（t2）与快速思考（t1）两大思维模式的协作——t1 进一步细分为事实验证(t1-f)和专业仲裁(t1-p)两个子组件。

**标准中文名**: 双思考系统（正式）/ Thinkdual（简称）
**标准英文名**: Thinkdual Cognitive Dual-Thinking System（完整）/ Thinkdual（简化）

**旧称/禁止使用**: "认知双思系统"、"双系统认知模型"、"Thinkdual 认知双思系统"、"Triple Coordinator"（triple_coordinator 是协调器而非系统名称）

**关键数据结构**:
```c
// 思考链（t2 核心）
typedef struct agentos_thinking_chain {
    agentos_tc_context_window_t *ctx_win;     // 上下文窗口（环形缓冲区）
    agentos_tc_working_memory_t *wm;           // 工作内存（键值存储）
    agentos_thinking_step_t *steps;            // 推理步骤 DAG
    // ...
} agentos_thinking_chain_t;

// 元认知（五维度评估）
typedef struct mc_evaluation_result {
    float overall_score;        // 总分
    int is_acceptable;          // 是否可接受
    float accuracy_score;       // 准确性
    float completeness_score;   // 完整性
    float consistency_score;    // 一致性
    float efficiency_score;     // 效率
    float innovativeness_score; // 创新性
} mc_evaluation_result_t;

// 流式验证结果（三要素）
typedef struct sc_verification_result {
    sc_verdict_t logic_verdict;    // 逻辑 verdict
    sc_verdict_t fact_verdict;     // 事实 verdict
    sc_verdict_t goal_verdict;     // 目标 verdict
} sc_verification_result_t;
```

**双系统切换阈值**（来自 ARCHITECTURAL_PRINCIPLES.md 1.3.6）:
| 条件 | 阈值 | 行为 |
|------|------|------|
| System 1 置信度 | theta_c ≥ 0.85 | 切换至 System 2 深度思考 |
| 时间预算 | 剩余 < 20% 总超时 | 强制切换至快思考 |
| CPU/内存使用率 | ≥ 80% | 降级至快思考 |
| 操作风险等级 | ≥ 3/5 | 强制使用 System 2 |

**系统内代码前缀**:
- 思考链: `tc_*` / `agentos_tc_*`
- 元认知: `mc_*` / `agentos_mc_*`
- 流式验证: `sc_*`
- 协调器: `tc3_*` / `agentos_tc3_*`

**代码目录**: `AgentOS/agentos/atoms/coreloopthree/src/cognition/`（foundation/ + critique/ + coordinator/）

**来源**: 架构设计原则 C-1 双路协同原则、CN_02 认知层理论、CoreLoopThree 具体方案
**参见**: CoreLoopThree（三层认知循环）、triple_coordinator（三元组协调器）、metacognition（元认知）、CognitiveEvolution（认知进化系统）

---

#### TimeSliceInfer / 分时推理框架

**定义**: 记忆卷载的分时推理框架，将单次"全量加载→推理"的原子操作分解为多个时间片上的"渐进式加载→增量推理→状态传递"流水线。每一时间片处理一个记忆子集，时间片之间通过推理状态向量传递上下文连续性。

**标准名称**: 分时推理框架（正式）/ TimeSliceInfer（简称）

**旧称/禁止使用**: "时间切片推理"

**系统内代码**: `ts_inference_session_t`（设计中）

**代码目录**: `MemoryRovol/docs/time_slicing_design.md`

**来源**: MemoryRovol 设计文档
**参见**: MemoryRovol（记忆卷载）、MemorySwap（记忆交换算法）

---

#### TraceID (跟踪标识符)

**定义**: 跨组件请求的全局唯一标识符，用于分布式追踪和日志关联。贯穿 C → JSON-RPC → Python/Go/TypeScript 全语言栈。

**标准名称**: 跟踪标识符 (TraceID)

**来源**: logging_format.md
**参见**: Security by Design（安全内生设计）、JSON-RPC 2.0

---

## 第二部分：标准化名称映射总表

### A. 一级核心组件映射

> **v3.0 更新**: 从 12 项增至 **14 项**（新增 CognitiveEvolution、MAC）

| # | 标准中文 | 标准英文 | 系统内代码前缀 | 代码目录 | 禁止使用的旧称 |
|---|----------|----------|--------------|---------|---------------|
| 1 | 三层认知循环 | CoreLoopThree | `agentos_loop_*` | `atoms/coreloopthree/` | 第三循环内核、三层循环运行时、三层一体架构 |
| 2 | 记忆卷载 | MemoryRovol | `agentos_layer*_*` / `builtin_*` | `atoms/memory/` + `atoms/memoryrovol/` (骨架) | 记忆漩涡引擎 |
| 3 | 双思考系统 | Thinkdual | `tc_*` / `mc_*` / `sc_*` / `tc3_*` | `coreloopthree/src/cognition/{foundation,critique}/` | 认知双思系统、双系统认知模型、Triple Coordinator |
| 4 | 认知进化系统 | CognitiveEvolution | `cog_*` / `agentos_cog_*` | `coreloopthree/src/cognition/ext/cognitive_evolution.*` | （新术语，无旧称） |
| 5 | 多智能体协作框架 | MAC | `mac_*` / `agentos_mac_*` | `coreloopthree/src/cognition/ext/multi_agent_collaboration.*` | Multi-Agent System（过于泛化） |
| 6 | 安全穹顶 | Cupolas | `cupolas_*` | `cupolas/` | Cupolas安全模块 |
| 7 | 微核心 | MicroCoreRT | `agentos_core_*` | `atoms/corekern/` | 微内核、Microkernel |
| 8 | 原子核心 | CoreKern | — | `atoms/corekern/`（同上） | 原子内核 |
| 9 | 系统调用层 | AiramaxSyscall | `agentos_sys_*` | `atoms/syscall/` | Syscall Layer |
| 10 | 任务流引擎 | TaskFlow | `agentos_taskflow_*` | `atoms/taskflow/` | — |
| 11 | 用户态服务层 | Daemon | `*_d` 进程 | `daemon/{llm_d,tool_d,gateway_d,...}/` | 守护进程、后端服务层、daemon进程 |
| 12 | 协议适配层 | Protocols | — | `protocols/` | — |
| 13 | 数据分区存储 | HeapStore | `agentos_heapstore_*` | `heapstore/` | — |
| 14 | 独立网关 | Gateway | — | `gateway/` | — |

### B. 二级子组件映射（CoreLoopThree 内部）

> **v3.0 更新**: 从 18 项大幅扩充至 **28 项**（新增认知扩展/调度策略/上下文/执行核心/补偿事务等）

#### B.1 认知层子组件

| 标准中文 | 标准英文 | 代码位置 | 行数 | 简述 |
|----------|----------|---------|------|------|
| 认知引擎 | Cognition Engine | `cognition/engine.c` | 785 | 5阶段管线（Phase 0-4） |
| t2 思考链 | Thinking Chain | `cognition/foundation/thinking_chain.c` | **1387** | 上下文窗口+工作记忆+推理步骤DAG |
| 元认知 | Metacognition | `cognition/foundation/metacognition.c` | 791 | 五维度自我评估 |
| 语义单元 | Semantic Unit | `cognition/foundation/semantic_unit.c` | 324 | 中文语义边界检测 |
| 三元组协调器 | Triple Coordinator | `cognition/critique/triple_coordinator.c` | 544 | t2/t1-f/t1-p 协调 |
| 流式验证器 | Stream Critic | `cognition/critique/stream_critic.c` | 669 | 三要素实时验证 |
| 置信度校准器 | Confidence Calibrator | `cognition/critique/confidence_calibrator.c` | — | 置信度修正 |
| 意图解析器 | Intent Parser | `cognition/intent/intent_parser.c` | 784 | 自然语言→结构化意图 |
| 反应式规划器 | Reactive Planner | `cognition/planner/reactive.c` | — | ReAct变体规划 |
| 反思式规划器 | Reflective Planner | `cognition/planner/reflective.c` | 679 | 自我反思规划 |
| 双模型协调 | Dual Model Coord. | `cognition/coordinator/dual_model.c` | 711 | t2↔t1切换协调 |
| 并行分发器 | Parallel Dispatcher | `cognition/dispatch/parallel_dispatcher.c` | — | 并行任务分发（工具调用级） |
| 委派分发器 | Delegate Dispatcher | `cognition/dispatch/delegate.c` | — | 委派分发 |
| **认知进化系统** | **CognitiveEvolution** | `cognition/ext/cognitive_evolution.c` | **532** | **5级认知层级跃迁+经验记录+模式提取+策略进化** |
| **多智能体协作** | **MAC Framework** | `cognition/ext/multi_agent_collaboration.c` | **870** | **4协作模式+4共识策略+任务委托评分** |
| **ML调度策略** | **ML Dispatch** | `cognition/dispatcher/ml_based.c` | **297** | **4维特征在线学习调度** |
| **优先级调度** | **Priority Dispatch** | `cognition/dispatcher/priority.c` | **135** | **最高优先级选择** |
| **轮询调度** | **Round-Robin Dispatch** | `cognition/dispatcher/round_robin.c` | **146** | **循环轮流选择** |
| **加权调度(Dispatch)** | **Weighted Dispatch** | `cognition/dispatcher/weighted.c` | **200** | **三维权重评分调度(Agent路由级)** |
| **上下文处理器** | **Context Processor** | `cognition/context/context_processor.c` | **408** | **可插拔4种处理器链式流水线** |

> **重要区分说明**:
> - `cognition/dispatch/` — **并行分发器**（Parallel Dispatcher）：工具调用级别的任务并行分发
> - `cognition/dispatcher/` — **Agent 调度策略器**（Dispatcher Strategies）：Agent 选择/路由级别的调度策略
> - `execution/units/weighted.c` — **加权执行单元**：执行层的加权逻辑（使用公共评分函数）
> - 以上三套系统功能不同、层级不同，不可混淆。

#### B.2 执行层子组件

| 标准中文 | 标准英文 | 代码位置 | 行数 | 简述 |
|----------|----------|---------|------|------|
| 执行引擎 | Execution Engine | `execution/engine.c` | 871 | 执行层总控 |
| API 执行单元 | API Unit | `execution/units/api.c` | **466** | 原生HTTP客户端+Bearer Token+URL缓存 |
| **浏览器执行单元** | **Browser Unit** | `execution/units/browser.c` | **2372** | **CDP浏览器控制（项目最大单文件）** |
| 代码执行单元 | Code Unit | `execution/units/code.c` | **425** | Python/JS/Node.js跨平台执行 |
| DB 执行单元 | DB Unit | `execution/units/db.c` | **283** | SQLite3只读（35关键词+14模式防御） |
| 文件执行单元 | File Unit | `execution/units/file.c` | **345** | 沙箱文件操作（路径遍历防护） |
| Shell 执行单元 | Shell Unit | `execution/units/shell.c` | **269** | Shell命令（21白名单+12元字符拦截） |
| 工具执行单元 | Tool Unit | `execution/units/tool.c` | 122 | 轻量级工具抽象 |
| 加权执行单元 | Weighted Unit | `execution/units/weighted.c` | 145 | 委托公共评分函数 |
| **执行单元注册表** | **Unit Registry** | `execution/core/registry.c` | **174** | **CAS原子链表注册+重名EEXIST检查** |
| **责任链追踪器** | **Trace Span Tracker** | `execution/core/trace.c` | **205** | **OpenTelemetry风格span追踪** |
| **补偿事务管理器** | **Compensation Manager** | `execution/transaction/compensation.c` | **242** | **Saga模式补偿+人工介入队列** |

#### B.3 记忆层子组件

| 标准中文 | 标准英文 | 代码位置 | 简述 |
|----------|----------|---------|------|
| 记忆服务 | Memory Service | `memory/memory_service.c` | 记忆读写服务 |
| 记忆引擎 | Memory Engine | `memory/engine.c` | 记忆引擎编排 |
| 主循环 | Main Loop | `loop.c` | create/run/stop/submit/restore |

### C. 二级子组件映射（Cupolas 内部）

| 标准中文 | 标准英文 | 代码位置 | 简述 |
|----------|----------|---------|------|
| 守卫核心 | Guard Core | `src/guards/guard_core.c` | 安全执行内核 |
| 权限引擎 | Permission Engine | `src/permission/permission_engine.c` | RBAC+YAML规则 |
| 净化核心 | Sanitizer Core | `src/sanitizer/sanitizer_core.c` | 四阶段输入净化 |
| 审计日志器 | Audit Logger | `src/audit/audit_logger.c` | SHA-256哈希链 |
| 虚拟工作台 | Workbench | `src/workbench/workbench.c` | 多模式隔离 |
| 安全金库 | Security Vault | `src/security/cupolas_vault.c` | AES-256-GCM加密 |
| 运行时保护 | Runtime Protection | `src/security/cupolas_runtime_protection.c` | Seccomp+CFI |
| 网络安全 | Network Security | `src/security/cupolas_network_security.c` | 出站过滤+TLS |
| 熔断器 | Circuit Breaker | `circuit_breaker.c` | 故障快速失败 |

## 第三部分：MemoryRovol 子系统详细术语

### L1 原始卷 (Raw Storage)

| 标准中文 | 标准英文 | 系统内代码 | 说明 |
|----------|----------|-----------|------|
| 原始卷 | L1 Raw | `agentos_layer1_raw_*` / `builtin_storage_*` | 第一层：原始记忆数据持久化存储 |
| 元数据索引 | Metadata DB | `agentos_raw_metadata_db_*` | SQLite 元数据索引 |
| 异步存储引擎 | Async Storage Engine | `async_storage_engine` | 批量异步写入、错误恢复 |
| 存储模式 | MRS Mode | `MRS_COLD_STORE` / `MRS_HOT_LOAD` | 冷存储/热加载双模式 |

### L2 特征层 (Feature Extraction)

| 标准中文 | 标准英文 | 系统内代码 | 说明 |
|----------|----------|-----------|------|
| 特征层 | L2 Feature | `agentos_layer2_feature_*` / `builtin_index_*` | 第二层：向量化特征提取与索引 |
| HNSW 索引 | HNSW Index | `hnsw_index_t` | 分层导航小世界图近邻索引（O(log n)，M=32） |
| FAISS IVF 索引 | FAISS IVF Index | — | 倒排文件索引（IVF nlist=100, PQ64） |
| BM25 索引 | BM25 Index | `agentos_bm25_index_*` | Okapi BM25 文本检索索引 |
| 混合检索 | Hybrid Search | `agentos_hybrid_search_*` | 向量+BM25 混合检索融合 |
| 自适应融合 | Adaptive Fusion | `agentos_hybrid_search_adaptive` | 归一化+动态权重+双路融合 |
| 嵌入器 | Embedder | `generate_*_embedding` | OpenAI/DeepSeek/ONNX 嵌入生成 |
| LRU 缓存 | LRU Cache | — | 100,000 条高性能缓存 |

### L3 结构层 (Knowledge Graph)

| 标准中文 | 标准英文 | 系统内代码 | 说明 |
|----------|----------|-----------|------|
| 结构层 | L3 Structure | `agentos_layer3_structure_*` | 第三层：知识图谱与关系编码 |
| 知识图谱 | Knowledge Graph | `agentos_knowledge_graph_*` | 实体-关系图谱存储与查询 |
| 绑定算子 | VSA Binder | `agentos_binder_*` | 向量符号架构绑定操作（Q矩阵） |
| 解绑算子 | VSA Unbinder | `agentos_unbinder_*` | 向量符号架构解绑操作 |
| 序列编码器 | Sequence Encoder | `agentos_sequence_encoder_*` | Fourier 位置编码 |
| 关系编码器 | Relation Encoder | `agentos_relation_encoder_*` | 三元组(S+P+O)关系编码 |
| 社区检测 | Community Detection | — | Louvain 算法社区发现 |
| 多跳邻域查询 | Neighborhood Query | `agentos_knowledge_graph_neighborhood` | BFS N跳邻域+累积置信度 |
| 置信度传播 | Confidence Propagation | `agentos_knowledge_graph_propagate_confidence` | Label Propagation 置信度传播 |

### L4 模式层 (Pattern Mining)

| 标准中文 | 标准英文 | 系统内代码 | 说明 |
|----------|----------|-----------|------|
| 模式层 | L4 Pattern | `agentos_layer4_pattern_*` | 第四层：持久模式挖掘与规则生成 |
| 持久同调 | Persistent Homology | `agentos_persistence_*` | 0D 持久同调特征计算（Union-Find+边排序） |
| HDBSCAN 聚类 | HDBSCAN Clustering | `agentos_clustering_hdbscan` | 层次密度聚类（首选实现） |
| DBSCAN 聚类 | DBSCAN Clustering | `agentos_clustering_dbscan` | 密度聚类（回退实现） |
| 模式挖掘器 | Pattern Miner | `miner_discover_patterns` | 基于聚类和持久同调的模式发现 |
| 规则生成器 | Rule Generator | `agentos_rule_generator_*` | LLM 辅助规则生成 |
| 模式匹配器 | Pattern Matcher | `agentos_pattern_matcher_*` | 向量相似度模式匹配 |
| 特征质量评分 | Feature Quality Score | `agentos_persistence_score_features` | Q=persistence/noise*log2(1+death) |
| 特征显著性排序 | Feature Ranking | `agentos_persistence_rank_features` | 按质量分数降序+阈值过滤 |
| 噪声阈值 | Noise Threshold θ | — | θ = μ_noise + 3×σ_noise |

### 检索系统 (Retrieval)

| 标准中文 | 标准英文 | 系统内代码 | 说明 |
|----------|----------|-----------|------|
| 吸引子网络 | Attractor Network | `agentos_attractor_network_*` | 现代 Hopfield 网络检索 |
| 能量函数 | Energy Function | `agentos_energy_hopfield` | Hopfield 网络能量计算 |
| 检索缓存 | Retrieval Cache | `agentos_retrieval_cache_*` | 增强型 LRU 缓存（TTL+统计） |
| 重排序器 | Reranker | `agentos_reranker_*` | 交叉编码器/BM25 降级重排序 |
| RRF 融合 | Reciprocal Rank Fusion | `agentos_reranker_rrf` | 倒数排名融合排序 |
| 挂载算子 | Mount Operator | `agentos_memoryrov_mount` / `builtin_retrieve_*` | 按上下文窗口限制加载工作记忆 |

### 遗忘机制 (Forgetting)

| 标准中文 | 标准英文 | 系统内代码 | 说明 |
|----------|----------|-----------|------|
| 遗忘引擎 | Forgetting Engine | `agentos_forgetting_*` | 艾宾浩斯遗忘曲线驱动 |
| 遗忘策略 | Forget Strategy | `agentos_forget_strategy_t` | NONE/EBBINGHAUS/LINEAR/ACCESS_BASED |
| 遗忘公式 | Forgetting Formula | — | **R(t) = e^(-t/τ)**，τ 默认 7 天 |
| 遗忘裁剪 | Prune | `agentos_forgetting_prune` | 低权重记忆裁剪（默认阈值 0.3） |
| 记忆归档 | Archive | `archive_memory` | 低权重记忆移至冷存储 |
| 记忆复活 | Revive | `revive_memory` | 从归档恢复记忆 |

> **跨文档裁决**: 遗忘公式在 CN_03 中写作 R = e^{-λt}（lambda 版本），在 ARCHITECTURAL_PRINCIPLES.md 中写作 R(t) = e^(-t/tau)（tau 版本）。两者数学等价（λ = 1/τ）。**统一采用 tau 版本**：R(t) = e^(-t/τ)。

### 跨层事件 (Cross-Layer Event)

| 标准中文 | 标准英文 | 系统内代码 | 说明 |
|----------|----------|-----------|------|
| 跨层事件队列 | Cross-Layer Event Queue | `event_queue_*` | 发布-订阅模式四层间通信 |
| 事件类型 | Event Type | `cross_layer_event_type_t` | RECORD_APPENDED/EMBEDDING_CREATED/STRUCTURE_UPDATED/PATTERN_DISCOVERED 等 |

## 第四部分：HeapStore 数据分区存储术语

**定义**: AgentOS 的结构化数据分区持久层，采用六大数据分区架构，支持批量写入、熔断器和健康检查。

| 数据分区 | 代码文件 | 说明 |
|---------|---------|------|
| 日志分区 | `heapstore_log.c` | 运行日志持久化 |
| 追踪分区 | `heapstore_trace.c` | 分布式追踪数据 |
| 会话分区 | （嵌入 log/trace） | 会话状态数据 |
| Agent 分区 | `heapstore_registry.c` | Agent 注册信息 |
| Skill 分区 | （嵌入 registry） | Skill 注册信息 |
| 内存分区 | `heapstore_memory.c` | 内存分配追踪 |
| Token 分区 | `heapstore_token.c` | Token 使用统计 |
| IPC 通道分区 | `heapstore_ipc.c` | IPC 通道数据 |
| IPC 缓冲区分区 | `heapstore_ipc.c`（续） | IPC 缓冲区数据 |
| 内存池分区 | `heapstore_memory.c`（续） | 内存池快照 |

**核心文件**: `heapstore_core.c` (~1366 行) — init/shutdown（五子系统串行初始化含回滚）/路径管理/统计/快速&慢速日志写入/清理/健康检查/指标/熔断/**批量写入 API**（log/trace/session/agent/skill/memory_pool/allocation/ipc_channel/ipc_buffer 九种批量化类型）

**代码目录**: `AgentOS/agentos/heapstore/`

## 第五部分：Gateway 独立网关术语

**定义**: AgentOS 的独立 API 网关模块，支持 HTTP/WebSocket/Stdio 三种接入模式，提供 JSON-RPC 2.0、MCP Server、OpenAI 兼容、Syscall Router、Rate Limiter 等完整网关能力。

**三种接入模式**:
| 模式 | 文件 | 说明 |
|------|------|------|
| HTTP | `http_gateway.c` / `http_gateway_routes.c` | RESTful HTTP API |
| WebSocket | `ws_gateway.c` | WebSocket 长连接 |
| Stdio | `stdio_gateway.c` | 标准输入输出（CLI 场景）|

**关键组件**:
| 组件 | 文件 | 功能 |
|------|------|------|
| 协议桥接 | `gateway_protocol_bridge.c` | 协议转换桥 |
| Gateway API | `gateway_api.c` | 网关 API |
| Syscall 路由 | `syscall_router.c` | 系统调用路由到内核 |
| RPC 处理器 | `gateway_rpc_handler.c` | JSON-RPC 处理 |
| 协议处理器 | `gateway_protocol_handler.c` | 协议特定处理 |
| 速率限制 | `gateway_rate_limiter.c` | 请求速率限制 |
| JSON-RPC | `jsonrpc.c` | JSON-RPC 2.0 实现 |
| MCP Server | `mcp_server.c` | MCP 协议服务器 |

**代码目录**: `AgentOS/agentos/gateway/`

**部署**: Dockerfile + docker-compose.yml + K8s service.yaml

## 第六部分：Orchestrator 编排器术语

**定义**: 位于 `daemon/common/orchestrator.c`（**1967 行，项目最大单文件之一**，与 browser.c 的 2372 行并列）的 **Phase 0-4 串行管线编排器**，是 AgentOS 从自然语言输入到最终输出的核心执行管线。

**七阶段管线**:
```
Phase 0: Decomposition（指令拆解）
    ↓
Phase 1: Planning（规划，含 t2/t1 预验证）
    ↓
Phase 2: Generation（生成，含 triple_coordinator 流式执行）
    ↓
Phase 3: Verification（验证，含 stream_critic 三要素）
    ↓
Phase 4a: Audit（审计，含 metacognition 五维度）
    ↓
Phase 4b: Alignment（目标对齐，含漂移检测）
```

**关键功能**:
- LLM 调用（通过 llm_d 或直接 curl）
- 记忆查询/写入（通过 MemoryRovol）
- 元认知评估（metacognition 五维度评分）
- 置信度校准（confidence_calibrator）
- 熔断器集成（circuit_breaker）
- 对齐漂移检测（align_history[8] 环形缓冲区）

## 第七部分：Context Processor 上下文处理引擎术语

> **v3.0 新增章节** — v2.0 完全缺少此组件的详细术语

**定义**: 位于 `coreloopthree/src/cognition/context/context_processor.c`（**408 行**）的**可插拔上下文处理引擎**，负责管理 LLM 交互过程中的模型上下文窗口。通过处理器链式流水线模式，动态裁剪、压缩、增强上下文内容，确保不超过模型的 token 预算限制（默认 4096 tokens）。

**四种内置处理器**:

| # | 处理器 | 函数 | 策略 | 说明 |
|---|--------|------|------|------|
| 1 | **WindowTrimmer（滑动窗口裁剪）** | `window_trimmer_process` | 保留最近 N 条 + 从旧条目按 budget 贪婪保留 | 默认保留最新 1 条，其余按字符预算贪婪选择 |
| 2 | **LLMCompressor（头尾截断压缩）** | `compressor_process` | 头尾截断，中间省略 | 默认比率 0.5（保留首尾各 25%，中间 "..."） |
| 3 | **StructuredSummarizer（结构化摘要）** | `summarizer_process` | N-to-1 合并 | 将前 N-1 条合并为 "[Summary of N entries: ...]" + 保留最后 1 条 |
| 4 | **MemoryAugmenter（记忆增强）** | `memory_augmenter_process` | MemoryRovol 搜索注入 | 调用 `agentos_sys_memory_search` 搜索相关记忆，以 "[Memory context: ref_xxx(score)]" 形式注入 |

**引擎架构**:
```c
// 可注册多个处理器形成链式流水线
typedef struct {
    agentos_model_context_t *entries;  // 动态数组（content + metadata + priority + timestamp）
    size_t token_budget;               // token 预算（默认 4096）
    // ... 处理器链表
} agentos_model_context_t;

// 引擎 API
agentos_context_engine_t *engine_create();
int engine_register_processor(engine, processor_fn, config);
int engine_process(engine, input_context, output_context);
```

**代码位置**: `coreloopthree/src/cognition/context/context_processor.c` (**408 行**)

**代码前缀**: 无专用前缀（使用 context_processor_ 前缀）

**参见**: CoreLoopThree（三层认知循环）、MemoryRovol（记忆卷载）、AiramaxSyscall（系统调用层——MemoryAugmenter 通过 syscall 搜索记忆）

## 第八部分：Execution Units 执行单元全集术语

> **v3.0 新增章节** — v2.0 仅列出单元名称但缺乏安全特性详情

**定义**: CoreLoopThree 执行层的 **8 种可插拔原子执行单元**，每种单元封装一类具体的操作能力，通过执行单元注册表（Unit Registry）动态加载和管理。总计 **4,527 行**代码，构成执行层的主体。

**执行单元全集**:

| # | 单元 | 文件 | 行数 | 安全特性摘要 |
|---|------|------|------|-------------|
| 1 | **API 单元** | `units/api.c` | **466** | 原生 socket HTTP/1.1 客户端（无 libcurl 依赖）；Bearer Token 认证；10 条 URL→body 响应缓存；可配置超时(30s)；动态扩容缓冲区 |
| 2 | **浏览器单元** | `units/browser.c` | **2372** | **项目最大单文件**；(1) Chromium 生命周期管理(fork/execvp/SIGTERM-SIGKILL)；(2) CDP over WebSocket（RFC6455 完整帧编解码）；(3) 连接池(16) + 沙箱上下文(64)；(4) URL 安全校验(仅 https + 私有 IP 检测 + 编码注入检测)；(5) JS 危险模式拦截(cookie/localStorage/eval/fetch 等 10 种)；(6) 4 级元素等待降级(CSS selector → Page.loadEventFired → network idle → delay) |
| 3 | **代码单元** | `units/code.c` | **425** | 跨平台临时文件(Win:GetTempFileNameA / Unix:mkstemp)；Shell 参数单引号转义防注入；代码大小限制 4MB；Windows CreatePipe / Unix popen 输出捕获 |
| 4 | **数据库单元** | `units/db.c` | **283** | **严格只读白名单**：仅允许 SELECT 语句；**35 个 SQL 关键词黑名单**(UNION/DROP/INSERT/DELETE/EXEC 等)；**14 种注入模式拦截**(--/'/"/`/0x/\|\|等)；SQLite3 WAL 模式；1000 行上限防溢出 |
| 5 | **文件单元** | `units/file.c` | **345** | **路径遍历防护**：禁止绝对路径开头 + ../ 检测 + 双斜杠检测；realpath 解析验证不逃逸 root_dir 沙箱根目录；三种操作(read/delete/list)；Windows FindFirstFileA / Unix opendir-readdir |
| 6 | **Shell 单元** | `units/shell.c` | **269** | **双重安全**：(1) **21 命令白名单**(ls/cat/echo/pwd/grep/find/wc/head/tail 等)；(2) **12 种元字符注入拦截**(; && \| $\(\) ${} \` > < & \n ..)；Windows cmd /c ; Unix fork+pipe+execl("/bin/sh","-c")；30 秒超时强制终止 |
| 7 | **工具单元** | `units/tool.c` | **122** | 轻量级工具抽象：invoke(带参数调用)/validate(验证可用性)/describe(返回元数据)；JSON 格式状态报告 |
| 8 | **加权单元** | `units/weighted.c` | **145** | 委托公共评分函数 `strategy_compute_weighted_score()`；可配置三维权重(cost/perf/trust) |

**执行单元注册表**（`execution/core/registry.c`, **174 行**）:
- 单向链表实现（`registry_entry`: unit_id + unit + next）
- CAS 原子操作保证单例锁初始化
- 注册时检查重名（返回 EEXIST）
- 注销时链表删除 + swap-last 优化
- O(n) 线性查找（适合单元数量 < 20 场景）

**责任链追踪器**（`execution/core/trace.c`, **205 行**）:
- OpenTelemetry 风格 span 追踪
- 全局 span 链表（头部插入）
- 当前活跃 span 指针
- 原子标志确保单次初始化
- Span ID 格式: "span_0", "span_1", ...

**补偿事务管理器**（`execution/transaction/compensation.c`, **242 行**）:
- Saga 补偿模式
- 操作注册（头插法链表）
- 补偿时摘除并加入人工介入队列（human_queue 动态扩容 2 倍）
- 消费式队列获取（一次性返回所有待处理 action_id 并清空）
- 支持自定义 `input_free_fn` 资源释放回调

**代码目录**: `AgentOS/agentos/atoms/coreloopthree/src/execution/`

**参见**: CoreLoopThree（三层认知循环）、Cupolas Workbench（沙箱隔离）、Compensation（补偿事务）

## 第九部分：命名规范（v3.0 扩展）

### G. 函数命名规范

| 规范 | 格式 | 示例 |
|------|------|------|
| 内核函数 | `agentos_core_[action]` | `agentos_core_init`, `agentos_core_destroy` |
| Syscall 函数 | `agentos_sys_[domain]_[action]` | `agentos_sys_task_submit`, `agentos_sys_memory_search` |
| 认知-思考链函数 | `agentos_tc_[module]_[action]` | `agentos_tc_context_window_append` |
| 认知-元认知函数 | `agentos_mc_[action]` | `agentos_mc_evaluate_step` |
| 认知-流式验证函数 | `sc_[action]` | `sc_stream_critic_verify` |
| 认知-协调器函数 | `tc3_[action]` | `tc3_coordinator_execute_streaming` |
| 认知-进化函数 | `cog_[action]` / `agentos_cog_[action]` | `cog_evolution_create`, `agentos_cog_record_experience` |
| 协作-MAC 函数 | `mac_[action]` / `agentos_mac_[action]` | `mac_framework_create`, `agentos_mac_delegate_task` |
| Cupolas 函数 | `cupolas_[subsystem]_[action]` | `cupolas_permission_check`, `cupolas_sanitizer_validate` |
| Daemon 函数 | `[service]_[action]` | `llm_d_complete`, `tool_d_register_tool` |
| HeapStore 函数 | `agentos_heapstore_[partition]_[action]` | `agentos_heapstore_log_write` |
| 上下文处理器函数 | `context_processor_[action]` 或 `[name]_process` | `window_trimmer_process`, `memory_augmenter_process` |
| 执行单元函数 | `agentos_unit_[type]_[action]` / `[type]_execute` | `api_execute`, `browser_execute`, `db_execute` |
| 补偿事务函数 | `agentos_compensation_[action]` | `agentos_compensation_register`, `agentos_compensate` |

### H. 结构体命名规范

| 规范 | 格式 | 示例 |
|------|------|------|
| 公共结构体 | `agentos_[name]_t` | `agentos_error_t`, `agentos_ipc_message_t` |
| 子系统结构体 | `[prefix]_[name]_t` | `tc_context_window_t`, `mc_eval_result_t`, `cupolas_config_t` |
| 认知进化结构体 | `cog_[name]_s` / `cog_[name]_t` | `cog_experience_s`, `cog_strategy_t`, `cog_level_t` |
| MAC 结构体 | `mac_[name]_t` | `mac_agent_info_t`, `mac_group_t`, `mac_collab_mode_t` |
| 回调类型 | `agentos_[name]_callback_t` / `agentos_[name]_fn` | `agentos_feedback_callback_t`, `agentos_plan_func_t`, `cog_reward_fn` |
| 配置结构体 | `agentos_[name]_config_t` | `agentos_cognition_config_t`, `agentos_dual_think_config_t` |
| 上下文结构体 | `agentos_model_context_t` / `agentos_[name]_processor_config_t` | `agentos_model_context_t` |
| 执行单元结构体 | `[type]_unit_data_t` | `api_unit_data_t`, `browser_manager_t`, `db_unit_data_t` |
| 补偿事务结构体 | `agentos_compensation_[name]_t` | `agentos_compensation_entry_t`, `agentos_compensation_t` |

### I. 枚举命名规范

| 规范 | 格式 | 示例 |
|------|------|------|
| 状态枚举 | `agentos_[name]_state` / `AGENTOS_[NAME]_STATE_*` | `agentos_task_state_t` (CREATED/READY/RUNNING/BLOCKED/TERMINATED) |
| 类型枚举 | `agentos_[name]_type` / `AGENTOS_[NAME]_TYPE_*` | `agentos_protocol_type_t` (PROTO_JSON_RPC/PROTO_MCP/...) |
| 验决枚举 | `[prefix]_verdict` / `[PREFIX]_VERDICT_*` | `sc_verdict_t` (PASS/WARNING/FAIL) |
| 步骤类型 | `TC_STEP_*` | TC_STEP_DECOMPOSITION/PLANNING/GENERATION/AUDIT/ALIGNMENT |
| 认知层级 | `cog_level_t` / `COG_LEVEL_*` | COG_LEVEL_PERCEPTION/REACTION/LEARNING/REASONING/CREATION |
| 反馈类型 | `cog_feedback_t` / `COG_FEEDBACK_*` | COG_FEEDBACK_POSITIVE/NEGATIVE/NEUTRAL |
| 协作模式 | `mac_collab_mode_t` / `MAC_MODE_*` | MAC_MODE_INDEPENDENT/COLLABORATIVE/CONSENSUS/DELEGATED |
| 任务状态(MAC) | `mac_task_state_t` / `MAC_TASK_*` | MAC_TASK_PENDING/ASSIGNED/RUNNING/COMPLETED/FAILED |
| 共识策略 | `mac_consensus_type_t` / `MAC_CONSENSUS_*` | MAJORITY/UNANIMOUS/WEIGHTED/LEADER |

### J. 宏定义命名规范

| 规范 | 格式 | 示例 |
|------|------|------|
| 成功码 | `AGENTOS_SUCCESS` (=0) | — |
| 错误码 | `AGENTOS_E[name]` / `AGENTOS_ERR_[DOMAIN]_[DESC]` | `AGENTOS_EINVAL`, `AGENTOS_ERR_TASK_NOT_FOUND` |
| 安全宏 | `AGENTOS_[ACTION]` | `AGENTOS_MALLOC`, `AGENTOS_FREE`, `AGENTOS_STRNCPY_TERM` |
| 日志宏 | `AGENTOS_LOG_[LEVEL]` | `AGENTOS_LOG_ERROR`, `AGENTOS_LOG_WARN`, `AGENTOS_LOG_INFO`, `AGENTOS_LOG_DEBUG` |
| 安全释放 | `MEMORY_FREE_SAFE` | — |
| 水位宏 | `AGENTOS_MEM_WATERMARK_[LEVEL]` | LOW/MED/HIGH/CRIT |
| 合规宏 | `AGENTOS_SEC_[ACTION]` | CLEAR/VALIDATE_PTR/VALIDATE_STR |
| JSON-RPC 响应 | `JSONRPC_SEND_ERROR` / `JSONRPC_SEND_SUCCESS` | Daemon 层快捷宏 |

### K. 文件/目录命名规范

| 规范 | 格式 | 示例 |
|------|------|------|
| 守护进程 | `[name]_d` | `llm_d`, `tool_d`, `gateway_d` |
| 子系统目录 | 小写英文 | `cognition/`, `protocols/`, `cupolas/` |
| 头文件 | 小写英文+.h | `thinking_chain.h`, `cupolas.h` |
| 源文件 | 下划线分隔+.c | `thinking_chain.c`, `stream_critic.c` |
| 测试文件 | `test_[module].c` | `test_loop.c`, `test_cognition.c` |
| **重要目录区分** | | |
| 并行分发器目录 | `cognition/dispatch/` | parallel_dispatcher.c, delegate.c |
| Agent 调度策略目录 | `cognition/dispatcher/` | ml_based.c, priority.c, round_robin.c, weighted.c |
| 执行单元目录 | `execution/units/` | api.c, browser.c, code.c, ..., weighted.c |

### L. 代码规模参考规范

> **v3.0 新增** — 为开发者提供代码规模预期参考

| 模块/文件 | 预期行数范围 | 实际行数 | 说明 |
|-----------|-------------|---------|------|
| 单个 .c 文件 | < 1500 行 | browser.c=**2372**, thinking_chain.c=**1387**, orchestrator.c=**1967**, engine.c=785 | 超过 1500 行需考虑拆分 |
| 单个 .h 文件 | < 300 行 | cognitive_evolution.h=**130**, multi_agent_collaboration.h=**132**, compensation.h=**105** | 接口头文件可适当放宽 |
| 子模块总行数 | < 5000 行 | execution/units/=**4527**, cognition/ext/=**1402** | — |
| 子系统总行数 | < 30,000 行 | coreloopthree/=**25,557**, cupolas/=**27,000+** | — |

## 第十部分：错误码完整分段表

| 段 | 范围 | 含义 | 关键常量 |
|----|------|------|---------|
| **成功** | 0 | 操作成功 | `AGENTOS_SUCCESS` (=0) |
| **通用** | -1 ~ -999 | 兼容/未分类 | `AGENTOS_ERR_UNKNOWN` (-1), `AGENTOS_ELAST` (-999) |
| **Task** | -1001 ~ -1099 | 任务相关 | NOT_FOUND, INVALID_STATE, TIMEOUT, CANCELLED |
| **Memory** | -2001 ~ -2099 | 记忆相关 | NOT_FOUND, FULL, CORRUPT, ACCESS_DENIED |
| **Session** | -3001 ~ -3099 | 会话相关 | EXPIRED, NOT_FOUND, INVALID, QUOTA |
| **Telemetry** | -4001 ~ -4099 | 遥测相关 | DISABLED, RATE_LIMITED, SAMPLING |
| **Internal** | -5001 ~ -5099 | 内部通用 | INTERNAL, INVALID_PARAM, NOT_IMPLEMENTED, NOT_SUPPORTED |
| **Skill** | -6001 ~ -6099 | 技能相关 | NOT_FOUND, EXEC_FAILED, TIMEOUT, DENIED |
| **Agent** | -7001 ~ -7099 | Agent 相关 | NOT_FOUND, ALREADY_RUNNING, TERMINATED, QUOTA |
| **Sandbox** | -8001 ~ -8099 | 沙箱相关 | DENIED, QUOTA_EXCEEDED, VIOLATION, ESCAPE |
| **Security** | -9001 ~ -9099 | 安全相关 | VIOLATION, INJECTION, EXFILTRATION, TAMPER |

## 第十一部分：跨文档术语一致性裁决表

> **v3.0 新增章节** — 汇总所有发现的跨文档术语不一致项及其裁决结果

| # | 不一致项 | 涉及文档 | 决议 | 裁决依据 |
|---|----------|----------|------|---------|
| 1 | **CoreKern 子系统数**: 4 vs 6 | CN_04 vs TERMINOLOGY | **以 6 为准** | 源码审计确认 IPC/内存/池/守卫/OOM/可观测性/错误 = 6 大子系统 |
| 2 | **Syscall API 数**: ~50 vs 28+ | syscall.md 概述 vs 源码 | **以 28+ 为准** | 源码逐一计数 7 域共计 28+ 公开 API |
| 3 | **Cupolas 子系统数**: 4 vs 7 | CN_04/E-1 vs 源码 | **以 7 为准** | 源码确认 Guards/Permission/Sanitizer/Audit/Workbench/Vault/Network |
| 4 | **Daemon 服务数**: 6 vs 10 | daemon_module.md V7.0 vs 源码 | **以 10 为准** | 源码确认 channel_d/observe_d/notify_d/info_d 存在 |
| 5 | **IPC 延迟值**: <10μs vs <8μs vs 6μs(P50) | microkernel.md / ipc.md / 基准表 | **以 ipc.md 基准表为准** | P50=6μs, P99=15μs, 典型值 <8μs |
| 6 | **遗忘公式符号**: lambda vs tau | CN_03 / ARCH_PRINCIPLES | **统一用 tau 版本** | R(t) = e^(-t/τ), τ 默认 7 天；数学等价 λ=1/τ |
| 7 | **"记忆层" vs "记忆与进化层"**: 措辞差异 | CN_04 vs 其他 | **统一为"记忆层"** | 含义相同，"记忆与进化层"过于冗长 |
| 8 | **"双模型协同" vs "Thinkdual"**: 粒度差异 | CN_02 vs coreloopthree | **Thinkdual 为正式标准名** | "双模型协同"是通用描述，Thinkdual 是三组件标准名 |
| 9 | **AiramaxSyscall 拼写**: Airamax? | syscall.md / TERMINOLOGY | **确认为 Airamax（Aira+m+ax）** | 项目名 "OpenAirymax" 的变体，非笔误 |
| 10 | **CoreLoopThree 文件数**: ~50 vs ~75 | v2.0 估算 vs v3.0 逐文件审计 | **以 75 为准** | v3.0 逐文件审计确认 ~75 源文件, ~25,557 行 |

**裁决执行要求**: 所有规范文档在进行下一版更新时，必须参照上表将不一致项统一为本表裁决结果。

## 第十二部分：附录

### 附录 A：JSON-RPC 协议层错误码

| 错误码 | 定义 |
|--------|------|
| -32700 | Parse error |
| -32600 | Invalid Request |
| -32601 | Method not found |
| -32602 | Invalid params |
| -32603 | Internal error |

**完整列表**: protocol_contract.md

### 附录 B：AgentOS 用户态服务层 (Daemon) 完整列表（10 个）

| # | 服务 | 目录 | 端口/Socket | 功能 |
|---|------|------|-------------|------|
| 1 | gateway_d | `daemon/gateway_d/` | HTTP :8080 | API 网关 |
| 2 | llm_d | `daemon/llm_d/` | Unix Socket | LLM 推理 |
| 3 | tool_d | `daemon/tool_d/` | Unix Socket | 工具调用 |
| 4 | market_d | `daemon/market_d/` | Unix Socket | 市场/注册表 |
| 5 | sched_d | `daemon/sched_d/` | Unix Socket | 任务调度 |
| 6 | monit_d | `daemon/monit_d/` | HTTP :9090 | 监控 |
| 7 | channel_d | `daemon/channel_d/` | Unix Socket | 通道 |
| 8 | observe_d | `daemon/observe_d/` | Unix Socket | 观测 |
| 9 | notify_d | `daemon/notify_d/` | Unix Socket | 通知 |
| 10 | info_d | `daemon/info_d/` | Unix Socket | 信息 |

### 附录 C：协议适配层 (Protocols) 完整列表（9 种）

| 类别 | 协议 | 目录 | 枚举值 |
|------|------|------|--------|
| 标准协议 | MCP v1 | `protocols/standards/mcp/` | PROTO_MCP |
| 标准协议 | A2A v0.3 | `protocols/standards/a2a/` | PROTO_A2A |
| 标准协议 | AGNTCY ACP | `protocols/standards/agntcy/` | PROTO_AGNTCY |
| 集成适配 | OpenAI | `protocols/integrations/openai/` | PROTO_OPENAI |
| 集成适配 | Claude | `protocols/integrations/claude/` | PROTO_CLAUDE |
| 集成适配 | OpenClaw | `protocols/integrations/openclaw/` | PROTO_OPENCLAW |
| 集成适配 | OpenJiuwen | `protocols/integrations/openjiuwen/` | PROTO_OPENJIUWEN |
| 集成适配 | China Eco | `protocols/integrations/china_eco/` | PROTO_CHINA_ECO |
| 框架适配 | LangChain | `protocols/frameworks/langchain/` | （无独立枚举，归入集成类） |
| 框架适配 | AutoGen | `protocols/frameworks/autogen/` | （同上） |

### 附录 D：LLM Provider 完整列表（6 种）

| Provider | 文件 | 支持特性 |
|----------|------|---------|
| OpenAI | `providers/openai.c` | ChatCompletion + Embeddings + Vision |
| Anthropic | `providers/anthropic.c` | Messages API + Streaming |
| Google | `providers/google.c` | Gemini API |
| DeepSeek | `providers/deepseek.c` | ChatCompletion + Embeddings |
| Local | `providers/local.c` | 本地模型（Ollama/llama.cpp） |
| Registry | `registry.c/h` | 动态注册表 |

### 附录 E：CoreLoopThree 测试文件完整列表（17 个）

| 测试文件 | 覆盖模块 |
|---------|---------|
| test_loop.c | 主循环 |
| test_cognition.c | 认知引擎 |
| test_intent_parser.c | 意图解析 |
| test_execution.c | 执行引擎 |
| test_compensation.c | 补偿事务 |
| test_memory.c | 记忆引擎 |
| test_browser.c | 浏览器单元 |
| test_triple_coordinator.c | 三元组协调器 |
| test_multi_agent.c | 多Agent协作（MAC） |
| test_parallel_dispatcher.c | 并行分发 |
| test_context_processor.c | 上下文处理器 |
| test_cognitive_evolution.c | 认知进化系统 |
| test_semantic_unit.c | 语义单元 |
| test_cognition_e2e.c | 认知端到端 |
| test_thread_safety.c | 线程安全 |
| test_metacognition_calibration.c | 元认知校准 |
| test_performance_benchmark.c | 性能基准 |
| test_cl3_stubs.c | 桩函数验证 |

### 附录 F：Cupolas 测试文件完整列表（12+ 个）

| 测试文件 | 覆盖模块 |
|---------|---------|
| unit_test_core.c | 核心功能 |
| unit_test_config.c | 配置 |
| unit_test_metrics.c | 指标 |
| unit_test_circuit_breaker.c | 熔断器 |
| unit_test_yaml.c | YAML 解析 |
| unit_test_vault.c | 安全金库 |
| unit_test_security.c | 安全子系统 |
| unit_test_workbench.c | 虚拟工作台 |
| integration_test.c | 集成测试 |
| stress_test.c | 压力测试 |
| fuzz_test_sanitizer.c | 净化器模糊测试 |
| fuzz_test_permission.c | 权限模糊测试 |
| benchmark.c | 性能基准 |

### 附录 G：CognitiveEvolution 认知进化系统测试文件

> **v3.0 新增附录**

| 测试文件 | 覆盖模块 |
|---------|---------|
| test_cognitive_evolution.c | 认知进化全流程（5级跃迁+经验记录+模式提取+策略进化） |

### 附录 H：MAC 多智能体协作框架测试文件

> **v3.0 新增附录**

| 测试文件 | 覆盖模块 |
|---------|---------|
| test_multi_agent.c | MAC 全流程（4协作模式+4共识策略+任务委托+批量注册） |

---

## 参考文献

[1] AgentOS 设计哲学。Basic_Theories/CN_04_设计原则.md
[2] AgentOS 体系并行论。Basic_Theories/CN_01_体系并行论.md
[3] AgentOS 认知层理论。Basic_Theories/CN_02_认知层理论.md
[4] 记忆层理论。Basic_Theories/CN_03_记忆层理论.md
[5] 架构设计原则。ARCHITECTURAL_PRINCIPLES.md
[6] MemoryRovol 架构文档。Capital_Architecture/memoryrovol.md
[7] 微核心架构。Capital_Architecture/microkernel.md
[8] 三层认知循环。Capital_Architecture/coreloopthree.md
[9] IPC Binder。Capital_Architecture/ipc.md
[10] 系统调用层。Capital_Architecture/syscall.md
[11] 日志系统。Capital_Architecture/logging_system.md
[12] Daemon 模块。Capital_Architecture/daemon_module.md
[13] 测试架构。Capital_Architecture/test_architecture.md
[14] Toolkit 设计。Capital_Architecture/toolkit_design.md
[15] AgentOS 源代码（全量审计基准）。`d:\SPHARX-CN\SpharxWorks\OpenAirymax\AgentOS\`

---

**维护者**: AgentOS 架构委员会
**联系方式**: architecture@agentos.org(暂)
**贡献指南**: 欢迎通过 Pull Request 提交术语建议或修正
**版本历史**:
| 版本 | 日期 | 变更内容 |
|------|------|---------|
| v1.0 | 2026-06-09 | 初版，15个核心术语+MemoryRovol子系统 |
| v2.0 | 2026-06-03 | 全面重写：新增50+子组件术语、HeapStore/Gateway/Orchestrator术语、10段错误码、命名规范8类、附录A-F、源码审计验证全部代码引用 |
| **v3.0** | **2026-06-10** | **二次深度审计增强：(1) 新增 CognitiveEvolution/MAC/ContextProcessor/ExecutionUnits/Compensation 5大组件完整术语（v2.0完全遗漏）；(2) CoreLoopThree 文件数从~50修正为~75/25557行；(3) browser.c 标注为项目最大单文件(2372行)；(4) 区分 dispatch/并行分发器 vs dispatcher/Agent调度策略 vs units/weighted三套系统；(5) 新增第7部分Context Processor、第8部分Execution Units全集、第11部分跨文档一致性裁决表(10项)、附录G-H；(6) 命名规范从8类扩展至10类（含代码规模参考）；(7) IPC性能基准补充完整P50/P99/P99.9表格；(8) AiramaxSyscall拼写确认；(9) 遗忘公式统一tau版本 |
