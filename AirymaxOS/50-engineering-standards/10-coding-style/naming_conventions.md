# Airymax 命名规范 (P4.4.2)

本文档定义了 Airymax 项目中所有关键组件、文件、函数、类型、常量、错误码及 API 的命名约定，确保整个代码库的一致性与可读性。

---

## 1. 核心组件命名

### 1.1 Airymax

**Airymax**（极境）是产品名。**AgentRT/** 是代码仓库目录路径，为 **Agent** **R**un**t**ime（即"Agent 运行时"）的缩写。Airymax 是一个 OS 级的 AI Agent 运行时平台，为 AI Agent 提供操作系统级别的调度、资源管理和生命周期管理能力。

"RT" 的命名参照了传统操作系统中的"Runtime"概念（如 Windows Runtime / WinRT），强调 Airymax 不是上层框架，而是位于操作系统层面的基础运行时。

### 1.2 agentrt

**agentrt** = **agent** + **rt**（Runtime，全小写，无空格），即 Airymax 内部的"Agent 运行时层"。它是整个 Airymax 平台的核心基础设施层，提供进程管理、内存管理、IPC 通信、文件系统等底层能力。

- 所有 C 源代码文件位于 `agentrt/` 目录下
- 代码中的命名空间前缀统一使用 `airy_`（如 `airy_err_t`、`airy_log_write`）
- **注意**：目录名与代码前缀使用小写，与代码仓库目录 `AgentRT/` 的 PascalCase 形成层次区分（产品名为 Airymax）

### 1.3 Atoms

**Atoms** = 原子（复数），即"原子构建块"。Atoms 是 Airymax 中最小的计算单元，代表不可再分的原子操作。

- 每个 Atom 是一个独立的、无副作用的计算单元
- 多个 Atoms 可以组合成更复杂的 TaskFlow
- 命名灵感来源于函数式编程中的原子操作概念

### 1.4 CoreKern

**CoreKern** = **Core** + **Kern**el，即"原子核心"。它是 Airymax 的中央调度器和分发器（Central Scheduler and Dispatcher）。

- 采用微核心（MicroCoreRT）架构设计
- 负责所有 Cupolas 服务的生命周期管理
- 实现 CoreLoopThree 认知循环的调度
- 目录名为 `corekern/`（小写），代码前缀为 `airy_core_*`
- **注**: MicroCoreRT（微核心）是 CoreKern 的架构概念层名称，两者共享同一目录和代码前缀，非独立模块

### 1.5 CoreLoopThree

**CoreLoopThree** = **Core** + **Loop** + **Three**，即"三层认知循环"。它描述了 Airymax 的认知处理流程：

```
Cognition（认知）→ Planning（规划）→ Action（执行）
```

- 第一阶段 **Cognition**：感知环境、理解上下文、提取意图
- 第二阶段 **Planning**：制定策略、分解任务、资源预估
- 第三阶段 **Action**：执行操作、调用工具、产生输出
- 循环往复，形成持续运行的认知回路

### 1.6 Cupolas

**Cupolas** = 穹顶（复数），即"安全穹顶"。命名灵感来源于大教堂建筑顶部的穹顶结构（Cupola），寓意每个 Cupola 是一个独立、自包含、向上拱起的安全服务单元。

- 每个 Cupola 是一个独立的服务进程，通过 IPC Bus 与 CoreKern 通信
- Cupola 之间相互隔离，通过消息传递进行协作
- 典型的 Cupola 包括：llm_d、tool_d、info_d、observe_d 等
- 目录名为 `cupolas/`（小写），代码前缀为 `cupolas_`

### 1.7 TaskFlow

**TaskFlow** = **Task** + **Flow**，即"任务编排流水线"。它表示任务在系统中的流动和处理过程。

- 任务从入口经过 CoreKern 调度，流经各个 Cupola 服务
- 每个阶段对任务进行特定的加工和处理
- 支持串行、并行、条件分支等编排模式
- 目录名为 `taskflow/`（小写），代码前缀为 `taskflow_`

### 1.8 HeapStore

**HeapStore** = **Heap** + **Store**，即"堆式持久化存储"。它结合了"堆"（Heap，动态内存）和"存储"（Store，持久化）两个概念。

- 提供动态分配的持久化存储能力
- 支持内存堆和磁盘存储的透明切换
- 自动管理数据的序列化和反序列化
- 目录名为 `heapstore/`（小写），代码前缀为 `heapstore_`

### 1.9 MemoryRovol

**MemoryRovol** = **Memory** + **Rovol**（**Roll** + **Volume** 的融合造词），即"记忆卷载"。它实现了 Airymax 的 4 层记忆系统：

| 层级 | 名称 | 说明 |
|------|------|------|
| L1 | Raw（原始记忆） | 未经处理的原始输入数据 |
| L2 | Feature（特征记忆） | 从原始数据中提取的关键特征 |
| L3 | Structure（结构记忆） | 特征之间的结构化关系 |
| L4 | Pattern（模式记忆） | 高层次的行为模式和知识 |

- 目录名为 `memoryrovol/`（小写），代码前缀为 `memoryrovol_`

### 1.10 ARE 标准接口前缀（`are_*`）

> **SSoT 声明**：本节补充 ARE（Airymax Runtime Environment）标准接口的命名契约，消除 `are_*` 前缀在命名规范中的缺失。

**ARE** = **A**irymax **R**untime **E**nvironment，是 Airymax 对外公开的**标准化运行时接口层**。`are_*` 前缀用于 L1/L2/L3 标准接口（见 `50-engineering-standards/30-runtime-interfaces/`），确保第三方可在任意 POSIX/Windows 平台上实现本接口集，无源码改动接入 ARE 生态。

| 前缀 | 层次 | 用途 | ABI 稳定性 | 桥接关系 |
|------|------|------|-----------|---------|
| `are_*` | L1/L2/L3 标准接口 | 对外公开的可移植 API（如 `are_ipc_send()`、`are_mem_alloc()`） | 同一 MAJOR 版本内 ABI 兼容 | 标准层——由适配层桥接至原生实现 |
| `airy_*` | 原生实现（通用） | Airymax atoms 层原生符号（如 `airy_log_write`、`airy_err_t`） | 内部 API，不保证稳定 | 实现层——`are_*` 的参考实现 |
| `airy_core_*` | 原生实现（CoreKern 专属） | CoreKern 模块专属函数（如 `airy_core_start()`、`airy_core_stop()`） | 内部 API，不保证稳定 | 实现层——CoreKern 专属原生符号 |

**桥接契约**：`are_*` 标准接口与 `airy_*`/`airy_core_*` 原生实现之间通过**适配层（adaptation layer）**桥接。适配层负责：

1. **符号映射**：`are_ipc_send()` → `airy_ipc_send()`（或 `airy_core_ipc_send()`）
2. **类型转换**：`are_error_t` ↔ `airy_err_t`（均为 `int32_t`，值空间一致）
3. **ABI 隔离**：`are_*` 接口的 ABI 稳定性由标准层保证；`airy_*` 内部 API 可自由变更，不影响 `are_*` 调用方

> **注意**：`are_*` 不是 `airy_*` 的别名——两者是不同的 API 层次。`are_*` 是面向第三方的标准接口，`airy_*`/`airy_core_*` 是 Airymax 内部的参考实现。第三方实现者可直接实现 `are_*` 接口，无需依赖 `airy_*` 实现。

---

## 2. 用户态服务（Daemon）命名

所有 Airymax 的后台用户态服务使用 `*_d` 后缀命名，遵循 Unix 用户态服务命名惯例（`d` = daemon）。

### 2.1 完整 Daemon 列表

| Daemon 名 | 完整名称 | 职责描述 |
|-----------|---------|---------|
| `gateway_d` | Gateway Daemon | 外部请求入口，协议适配，认证鉴权，速率限制 |
| `llm_d` | LLM Daemon | LLM 推理调度，模型路由，Token 管理，流式输出 |
| `tool_d` | Tool Daemon | 工具调用管理，工具注册表，执行沙箱，结果校验 |
| `sched_d` | Scheduler Daemon | 任务调度引擎，优先级队列，资源分配，负载均衡 |
| `info_d` | Information Daemon | 信息聚合服务，知识检索，上下文注入，数据转换 |
| `observe_d` | Observability Daemon | 可观测性服务，Metrics 采集，Tracing，健康检查 |
| `monit_d` | Monitor Daemon | 监控告警服务，阈值检测，告警路由，自愈策略 |
| `market_d` | Market Daemon | 能力市场，计费计量，配额管理 |
| `channel_d` | Channel Daemon | 通道管理服务，长连接管理，流式通道，会话保持 |
| `notify_d` | Notification Daemon | 通知推送服务，消息推送，多渠道分发，投递保证 |

### 2.2 Daemon 命名规则

```
<service_name>_d
```

- 服务名使用小写英文单词，多个单词间用下划线分隔
- 统一以 `_d` 后缀结尾
- 对应的 C 源码文件使用相同的命名（如 `gateway_d.c`）

---

## 3. 文件命名

### 3.1 C 源文件：snake_case

所有 C 源文件（`.c`）和头文件（`.h`）使用 **snake_case** 命名。

```
agentrt/commons/utils/observability/src/logger.c
agentrt/commons/utils/observability/include/logger.h
agentrt/commons/platform/include/platform.h
agentrt/commons/utils/quality/airy_quality.h
agentrt/commons/utils/config_unified/include/core_config.h
```

### 3.2 目录名：PascalCase（顶层）/ snake_case（代码层）

- **顶层概念目录** 使用 PascalCase：`Docs/`、`Tests/`、`Scripts/`
- **代码层目录** 使用 snake_case：`agentrt/`、`commons/`、`protocols/`、`cupolas/`、`corekern/`

### 3.3 配置文件：kebab-case

所有配置文件使用 **kebab-case**（小写字母 + 连字符）命名。

```
.env.example
.editorconfig
.gitattributes
```

### 3.4 脚本文件：snake_case / kebab-case

Shell 脚本和 Python 脚本使用 snake_case 或 kebab-case：

```
scripts/ci/pipeline/run-tests.sh
scripts/ci/pipeline/security-scan.sh
scripts/ci/quality/check-quality.sh
scripts/dev/utils/run_all_fixes.sh
```

---

## 4. 函数命名

### 4.1 基本规则：`module_action_object()`

所有函数遵循 `module_action_object()` 的命名规范，使用 snake_case。

```
<模块前缀>_<动作>_<对象>()
```

### 4.2 代码示例

```c
// agentrt 模块 - 日志操作
const char *airy_log_set_trace_id(const char *trace_id);
const char *airy_log_get_trace_id(void);
void        airy_log_write(int level, const char *file, int line, const char *fmt, ...);

// config 模块 - 配置值操作
config_value_t *config_value_create_null(void);
config_value_t *config_value_create_bool(bool value);
config_value_t *config_value_create_int(int32_t value);
config_value_t *config_value_get_type(const config_value_t *value);
void            config_value_destroy(config_value_t *value);

// config 模块 - 配置上下文操作
config_context_t *config_context_create(const char *name);
void              config_context_destroy(config_context_t *ctx);
config_error_t    config_context_set(config_context_t *ctx, const char *key, config_value_t *value);
const config_value_t *config_context_get(const config_context_t *ctx, const char *key);

// heapstore 模块
int heapstore_init(const heapstore_config_t *config);
int heapstore_put(const char *key, const void *data, size_t size);
int heapstore_get(const char *key, void **data, size_t *size);

// CoreKern 模块（代码前缀 airy_core_）
int airy_core_start(void);
int airy_core_stop(void);
int airy_core_register_cupola(const char *name, cupola_entry_t entry);
```

### 4.3 常见动作动词

| 动词 | 含义 | 示例 |
|------|------|------|
| `create` | 创建/分配资源 | `config_value_create_null` |
| `destroy` | 销毁/释放资源 | `config_value_destroy` |
| `get` | 获取值（不修改状态） | `config_value_get_type` |
| `set` | 设置值 | `config_context_set` |
| `init` | 初始化 | `heapstore_init` |
| `start` | 启动 | `airy_core_start` |
| `stop` | 停止 | `airy_core_stop` |
| `write` | 写入 | `airy_log_write` |
| `clone` | 克隆/深拷贝 | `config_value_clone` |
| `has` | 检查是否存在 | `config_context_has` |
| `delete` | 删除 | `config_context_delete` |
| `clear` | 清空 | `config_context_clear` |
| `count` | 计数 | `config_context_count` |
| `lock` | 加锁 | `config_context_lock` |
| `unlock` | 解锁 | `config_context_unlock` |

### 4.4 函数前缀体系

> **SSoT 声明**：本节是 Airymax 函数前缀体系的**语义层权威来源**。三层 API 前缀架构（`are_*`/`airy_*`/`airy_core_*`）的契约定义见 §1.10；本节聚焦于模块前缀清单、项目隔离规则与选择决策。

#### 4.4.1 三层 API 前缀架构（回顾 §1.10）

Airymax 函数 API 分为三个层次，层次关系与 ABI 稳定性约定如下（完整定义见 §1.10）：

| 层次 | 前缀 | 用途 | ABI 稳定性 |
|------|------|------|-----------|
| 标准接口层 | `are_*` | L1/L2/L3 对外公开的可移植 API | 同一 MAJOR 版本内 ABI 兼容 |
| 原生实现层（通用） | `airy_*` | Airymax atoms 层原生符号 | 内部 API，不保证稳定 |
| 原生实现层（CoreKern） | `airy_core_*` | CoreKern 模块专属函数 | 内部 API，不保证稳定 |

**桥接契约**：`are_*` 标准接口与 `airy_*`/`airy_core_*` 原生实现之间通过**适配层**桥接。适配层负责符号映射（`are_ipc_send()` → `airy_ipc_send()`）、类型转换（`are_error_t` ↔ `airy_err_t`）与 ABI 隔离。

#### 4.4.2 项目隔离前缀规则

agentrt（用户态运行底座）与 agentrt-linux（智能体操作系统）共享 IRON-9 v2 三层模型，前缀按 IRON-9 v2 层次隔离：

| 前缀 | IRON-9 v2 层次 | 适用项目 | 用途 | 示例 |
|------|---------------|---------|------|------|
| `airy_*` | [SC] / [SS] | agentrt + agentrt-linux 共享 | 同源 API 与共享契约层符号 | `airy_ipc_send()`、`airy_log_write()` |
| `airymaxos_*` | [IND] | agentrt-linux 专属 | 内核态/发行版专属 API | `airymaxos_kthread_create()`、`airymaxos_ipc_recv()` |
| `AIRY_*` | [SC] | agentrt + agentrt-linux 共享 | 共享宏与常量 | `AIRY_API`、`AIRY_SYS_CALL` |
| `AIRYMAXOS_*` | [IND] | agentrt-linux 专属 | 内核态/发行版专属宏与常量 | `AIRYMAXOS_CONFIG_*` |

**选择规则**：

1. **同源符号**（[SC]/[SS] 层）使用 `airy_*` 前缀——两端代码 byte-identical 或语义同源
2. **agentrt-linux 专属符号**（[IND] 层）使用 `airymaxos_*` 前缀——内核驱动、Kbuild、发行版工具链专属
3. **禁止混用**——`airy_*` 前缀不得用于 agentrt-linux 专属功能；`airymaxos_*` 前缀不得用于 agentrt 用户态

#### 4.4.3 模块前缀清单总表

> **SSoT 声明**：本节为 Airymax 项目**模块前缀注册表**的唯一权威来源（SSoT）。所有模块前缀必须在本节注册后方可使用（注册流程见 §4.4.6）。其他文档（包括 `01-coding-standards.md`、`07-maintainers-and-governance.md`）仅可引用本节编号，**禁止重新定义**模块前缀。

`airy_*` 通用前缀通过 **2-4 字符模块子前缀**区分子系统。模块子前缀遵循 `airy_<module>_` 格式，其中 `<module>` 标识所属子系统。下表按功能域分组列出全部已注册模块前缀：

**A. 核心运行时层（Core Runtime）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_core_*` | CoreKern / MicroCoreRT | [SS] | `airy_core_start()`、`airy_core_register_cupola()` |
| `airy_sys_*` | 系统调用路由层 | [SS] | `airy_sys_task_submit()`、`airy_sys_memory_write()` |
| `airy_ipc_*` | AgentsIPC 进程间通信 | [SS] | `airy_ipc_send()`、`airy_ipc_recv()` |
| `airy_sched_*` | 调度器通用接口 | [SS] | `airy_sched_enqueue()`、`airy_sched_yield()` |
| `airy_sched_agent_*` | Agent 调度策略（SCHED_EXT） | [SS] | `airy_sched_agent_compute_weight()` |
| `airy_task_*` | 任务管理 | [SS] | `airy_task_create()`、`airy_task_destroy()` |
| `airy_taskflow_*` | TaskFlow 任务编排 | [SS] | `taskflow_graph_build()`、`taskflow_execute()` |

**B. 认知与智能层（Cognition & Intelligence）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_cognition_*` | 认知引擎 | [SS] | `airy_cognition_perceive()`、`airy_cognition_act()` |
| `airy_loop_*` | CoreLoopThree 三层认知循环 | [SS] | `airy_loop_run()`、`airy_loop_next_phase()` |
| `airy_thinking_*` | 思考引擎 | [SS] | `airy_thinking_fast()`、`airy_thinking_slow()` |
| `airy_intent_*` | 意图识别 | [SS] | `airy_intent_extract()`、`airy_intent_classify()` |
| `airy_plan_*` | 规划引擎 | [SS] | `airy_plan_create()`、`airy_plan_execute()` |
| `airy_llm_*` | LLM 推理 | [SS] | `airy_llm_infer()`、`airy_llm_route()` |
| `airy_prompt_*` | Prompt 模板加载 | [SS] | `airy_prompt_loader_init()`、`airy_prompt_render()` |
| `airy_token_*` | Token 管理 | [SS] | `airy_token_count()`、`airy_token_budget()` |

**C. 双思考系统（Thinkdual）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `tc_*` | Thinkdual Core | [SS] | `tc_select_mode()`、`tc_switch()` |
| `mc_*` | Multi-agent Cognition | [SS] | `mc_coordinate()`、`mc_dispatch()` |
| `sc_*` | Slow Cognition（System 2） | [SS] | `sc_deliberate()`、`sc_analyze()` |

**D. 安全与隔离层（Security & Isolation）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_cap_*` | Capability 令牌系统 | [SS] | `airy_cap_mint()`、`airy_cap_revoke()` |
| `airy_sandbox_*` | 沙箱隔离 | [SS] | `airy_sandbox_create()`、`airy_sandbox_enter()` |
| `airy_security_*` | 安全策略 | [SS] | `airy_security_check()`、`airy_security_enforce()` |
| `cupolas_*` | Cupolas 安全穹顶服务 | [SS] | `cupolas_init()`、`cupolas_config_load()` |
| `cupolas_sig_*` | Cupolas 签名验证 | [SS] | `cupolas_sig_verify()`、`cupolas_sig_generate()` |
| `cupolas_net_*` | Cupolas 网络安全 | [SS] | `cupolas_net_check()`、`cupolas_net_block()` |
| `cupolas_ent_*` | Cupolas 权限授权 | [SS] | `cupolas_ent_check()`、`cupolas_ent_grant()` |

**E. 记忆与存储层（Memory & Storage）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_mem_*` | 内存管理通用 | [SS] | `airy_mem_alloc()`、`airy_mem_free()` |
| `airy_mempool_*` | 内存池 | [SS] | `airy_mempool_create()`、`airy_mempool_alloc()` |
| `airy_slab_*` | Slab 分配器 | [SS] | `airy_slab_create()`、`airy_slab_alloc()` |
| `airy_arena_*` | Arena 分配器 | [SS] | `airy_arena_create()`、`airy_arena_reset()` |
| `airy_tcache_*` | 线程本地缓存 | [SS] | `airy_tcache_get()`、`airy_tcache_put()` |
| `airy_mr_*` | MemoryRovol 记忆卷载 | [SS] | `airy_mr_init()`、`airy_mr_query()` |
| `airy_checkpoint_*` | 检查点与快照 | [SS] | `airy_checkpoint_save()`、`airy_checkpoint_restore()` |
| `airy_oom_*` | OOM 处理 | [SS] | `airy_oom_handle()`、`airy_oom_register_cb()` |
| `heapstore_*` | HeapStore 堆式持久化存储 | [SS] | `heapstore_init()`、`heapstore_put()` |
| `memoryrovol_*` | MemoryRovol（模块直称） | [SS] | `memoryrovol_init()`、`memoryrovol_query()` |

**F. 平台与运行时基础设施（Platform & Runtime Infrastructure）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_platform_*` | 平台抽象层 | [SS] | `airy_platform_thread_create()`、`airy_platform_thread_join()` |
| `airy_thread_*` | 线程管理 | [SS] | `airy_thread_id()`、`airy_thread_sleep()` |
| `airy_mtx_*` | 互斥锁 | [SS] | `airy_mtx_init()`、`airy_mtx_lock()` |
| `airy_cond_*` | 条件变量 | [SS] | `airy_cond_init()`、`airy_cond_wait()` |
| `airy_rwlock_*` | 读写锁 | [SS] | `airy_rwlock_rdlock()`、`airy_rwlock_wrlock()` |
| `airy_sem_*` | 信号量 | [SS] | `airy_sem_init()`、`airy_sem_post()` |
| `airy_atomic_*` | 原子操作 | [SS] | `airy_atomic_load()`、`airy_atomic_store()` |
| `airy_time_*` | 时间工具 | [SS] | `airy_time_now()`、`airy_time_diff()` |
| `airy_timer_*` | 定时器 | [SS] | `airy_timer_create()`、`airy_timer_start()` |
| `airy_uuid_*` | UUID 生成 | [SS] | `airy_uuid_generate()`、`airy_uuid_parse()` |

**G. 网络与通信层（Network & Communication）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_sock_*` | Socket 抽象 | [SS] | `airy_sock_create()`、`airy_sock_connect()` |
| `airy_net_*` | 网络管理 | [SS] | `airy_net_init()`、`airy_net_cleanup()` |
| `airy_http_*` | HTTP 客户端/服务端 | [SS] | `airy_http_get()`、`airy_http_post()` |
| `airy_conn_*` | 连接管理 | [SS] | `airy_conn_create()`、`airy_conn_close()` |

**H. 可观测性与运维（Observability & Operations）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_log_*` | 日志系统 | [SS] | `airy_log_write()`、`airy_log_set_trace_id()` |
| `airy_trace_*` | 链路追踪 | [SS] | `airy_trace_start()`、`airy_trace_span()` |
| `airy_metrics_*` | 指标采集 | [SS] | `airy_metrics_inc()`、`airy_metrics_set()` |
| `airy_observability_*` | 可观测性聚合 | [SS] | `airy_observability_init()`、`airy_observability_export()` |
| `airy_telemetry_*` | 遥测数据 | [SS] | `airy_telemetry_metrics()`、`airy_telemetry_traces()` |
| `airy_event_*` | 事件系统 | [SS] | `airy_event_publish()`、`airy_event_subscribe()` |
| `airy_hook_*` | Hook 系统 | [SS] | `airy_hook_register()`、`airy_hook_unregister()` |

**I. 配置与工具（Configuration & Tools）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_config_*` | Airymax 配置框架 | [SS] | `airy_config_load()`、`airy_config_get()` |
| `config_*` | Config 模块（历史保留） | [SS] | `config_value_create()`、`config_context_get()` |
| `airy_yaml_*` | YAML 解析 | [SS] | `airy_yaml_parse()`、`airy_yaml_dump()` |
| `airy_cjson_*` | cJSON 封装 | [SS] | `airy_cjson_parse()`、`airy_cjson_print()` |
| `airy_validate_*` | 验证工具 | [SS] | `airy_validate_string()`、`validate_schema()` |
| `airy_version_*` | 版本管理 | [SS] | `airy_version_get()`、`airy_version_check()` |

**J. 网关与协议（Gateway & Protocol）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_gw_*` | Gateway 网关（短前缀） | [SS] | `airy_gw_route()`、`airy_gw_authenticate()` |
| `airy_gateway_*` | Gateway 网关（全称） | [SS] | `airy_gateway_init()`、`airy_gateway_start()` |
| `airy_proto_*` | 协议适配 | [SS] | `airy_proto_init()`、`airy_proto_handle_request()` |
| `airy_protocol_*` | 协议扩展框架 | [SS] | `airy_protocol_register()`、`airy_protocol_invoke()` |
| `airy_router_*` | 路由引擎 | [SS] | `airy_router_select_provider()` |
| `airy_provider_*` | Provider 管理 | [SS] | `airy_provider_register()`、`airy_provider_list()` |

**K. Agent 管理与执行（Agent Management & Execution）**

| 模块前缀 | 所属模块 | IRON-9 v2 | 示例 |
|---------|---------|-----------|------|
| `airy_agent_*` | Agent 生命周期 | [SS] | `airy_agent_spawn()`、`airy_agent_terminate()` |
| `airy_session_*` | 会话管理 | [SS] | `airy_session_create()`、`airy_session_close()` |
| `airy_daemon_*` | Daemon 管理 | [SS] | `airy_daemon_start()`、`airy_daemon_stop()` |
| `airy_browser_*` | 浏览器自动化 | [SS] | `airy_browser_launch()`、`airy_browser_close()` |
| `airy_execution_*` | 执行单元 | [SS] | `airy_execution_unit_create()` |
| `airy_dispatcher_*` | 任务分发 | [SS] | `airy_dispatcher_select()`、`airy_dispatcher_dispatch()` |
| `airy_coordinator_*` | 多智能体协调 | [SS] | `airy_coordinator_register()`、`airy_coordinator_dispatch()` |
| `airy_delegate_*` | 委托执行 | [SS] | `airy_delegate_create()`、`airy_delegate_invoke()` |
| `airy_compensation_*` | 补偿事务 | [SS] | `airy_compensation_register()`、`airy_compensation_rollback()` |
| `airy_resource_*` | 资源管理 | [SS] | `airy_resource_alloc()`、`airy_resource_free()` |
| `airy_context_*` | 上下文管理 | [SS] | `airy_context_create()`、`airy_context_switch()` |
| `airy_error_*` / `airy_err_*` | 错误处理 | [SS] | `airy_error_to_string()`、`airy_err_t` |

#### 4.4.4 函数名长度约束

| 约束项 | 上限 | 说明 |
|--------|------|------|
| 模块子前缀 | ≤ 12 字符 | `airy_` (5) + 模块名 (≤ 7) = ≤ 12；如 `airy_sched_agent_` 属例外（长前缀仅用于 sched_ext BPF 策略） |
| 函数动作部分 | ≤ 15 字符 | `<action>_<object>` 组合长度 ≤ 15 字符 |
| 完整函数名总长 | ≤ 30 字符 | 模块前缀 + 动作部分 ≤ 30 字符 |
| 超长处理 | 缩写或分层 | 超过 30 字符时拆分为辅助函数或使用约定缩写 |

**长度计算示例**：

```
airy_ipc_send                  → 5 + 3 + 4  = 12 字符 ✓
airy_core_register_cupola      → 5 + 4 + 14 = 23 字符 ✓
airy_sched_agent_compute_weight → 5 + 11 + 14 = 30 字符 ✓（边界）
airy_mr_checkpoint_restore    → 5 + 3 + 18 = 26 字符 ✓
```

**超长函数名修正策略**：

1. **拆分**：将复合动作拆分为两个函数（如 `airy_mr_restore_checkpoint()` → `airy_mr_restore()`）
2. **缩写**：使用约定缩写（如 `init` 代替 `initialize`、`cfg` 代替 `config`、`ctx` 代替 `context`）
3. **分层**：引入中间层函数（如 `airy_mem_ckpt_save()` 代替 `airy_memory_checkpoint_save()`）

#### 4.4.5 前缀选择决策规则

新增函数时，按以下顺序确定前缀：

1. **是否为对外公开标准接口？**
   - 是 → 使用 `are_*` 前缀（§1.10），并通过适配层桥接至 `airy_*` 实现
   - 否 → 进入下一步

2. **是否为 agentrt-linux 内核态/发行版专属功能？**
   - 是 → 使用 `airymaxos_*` 前缀（[IND] 层）
   - 否 → 进入下一步

3. **是否为 CoreKern 模块专属函数？**
   - 是 → 使用 `airy_core_*` 前缀
   - 否 → 进入下一步

4. **选择模块子前缀**
   - 从 §4.4.3 模块前缀清单总表中选择对应功能域的前缀
   - 若功能域跨多个模块，选择最贴近的主模块前缀
   - 若无对应前缀，向工程规范委员会申请注册新前缀（流程见 §4.4.6）

5. **验证长度约束**
   - 按 §4.4.4 规则验证完整函数名 ≤ 30 字符
   - 超长时按修正策略调整

#### 4.4.6 新前缀注册流程

新增模块前缀须经过以下流程注册：

1. **RFC**：在 GitHub Discussions 发起 RFC，说明前缀字符串、所属模块、IRON-9 v2 层次、适用范围
2. **评审**：经至少一名顶级子系统维护者审查，公示 14 天接受异议
3. **注册**：通过评审后，由工程规范委员会将前缀写入本节 §4.4.3 模块前缀清单总表，并同步更新 [TERMINOLOGY.md 标准化名称映射总表](../../TERMINOLOGY.md#四标准化名称映射总表)

#### 4.4.7 前缀使用禁忌

| 禁忌 | 说明 | 正确做法 |
|------|------|---------|
| 混用 `airy_` 与 `airymaxos_` | 同一功能在两端使用不同前缀导致符号冲突 | [SC]/[SS] 层用 `airy_`，[IND] 层用 `airymaxos_` |
| 省略模块子前缀 | `airy_send()` 无法识别所属模块 | 必须带模块子前缀：`airy_ipc_send()` |
| 使用未注册前缀 | `airy_foo_*` 未在清单中注册 | 先按 §4.4.6 注册，再使用 |
| 模块前缀过长 | `airy_internationalization_*` 超长 | 缩写为 `airy_i18n_*` 并注册 |
| `are_*` 与 `airy_*` 混淆 | 将内部实现函数标记为 `are_*` | `are_*` 仅用于对外公开标准接口 |
| Thinkdual 前缀误用 | `tc_`/`mc_`/`sc_` 用于非 Thinkdual 模块 | Thinkdual 前缀仅限双思考系统使用 |

---

## 5. 类型命名

### 5.1 基本规则：`module_type_t`

所有类型定义遵循 `module_type_t` 的命名规范，使用 snake_case，以 `_t` 后缀结尾。

```c
<模块前缀>_<类型名>_t
```

### 5.2 代码示例

```c
// 基础类型
typedef int32_t airy_err_t;       // agentrt 错误码类型

// 结构体类型
typedef struct config_value    config_value_t;     // 配置值
typedef struct config_context  config_context_t;   // 配置上下文
typedef struct config_schema   config_schema_t;    // 配置模式

// 枚举类型
typedef enum {
    CONFIG_TYPE_NULL = 0,
    CONFIG_TYPE_BOOL = 1,
    CONFIG_TYPE_INT  = 2,
    // ...
} config_value_type_t;

typedef enum {
    CONFIG_SUCCESS = 0,
    CONFIG_ERROR_INVALID_ARG = 1,
    // ...
} config_error_t;

// heapstore 模块类型
typedef struct heapstore_config heapstore_config_t;
typedef struct heapstore_entry  heapstore_entry_t;

// memoryrovol 模块类型
typedef struct memoryrovol_layer memoryrovol_layer_t;
typedef enum   memoryrovol_level memoryrovol_level_t;
```

### 5.3 枚举值命名

枚举值使用 `MODULE_UPPER_SNAKE_CASE` 格式：

```c
typedef enum {
    AIRY_MEM_LAYER1_RAW = 0,       // 注意：不是全大写，而是模块前缀 + 含义
    AIRY_MEM_LAYER2_FEATURE = 1,
    AIRY_MEM_LAYER3_EPISODIC = 2,
    AIRY_MEM_LAYER4_PATTERN = 3,
} airy_memory_layer_t;

typedef enum {
    CONFIG_TYPE_NULL = 0,
    CONFIG_TYPE_BOOL = 1,
    CONFIG_TYPE_INT = 2,
    CONFIG_TYPE_INT64 = 3,
    CONFIG_TYPE_DOUBLE = 4,
    CONFIG_TYPE_STRING = 5,
    CONFIG_TYPE_ARRAY = 6,
    CONFIG_TYPE_OBJECT = 7,
    CONFIG_TYPE_BINARY = 8,
} config_value_type_t;
```

---

## 6. 常量与宏命名

### 6.1 宏定义：UPPER_SNAKE_CASE

所有预处理器宏使用 **UPPER_SNAKE_CASE**（全大写 + 下划线分隔）。

```c
// 日志级别宏
#define AIRY_LOG_LEVEL_DEBUG 0
#define AIRY_LOG_LEVEL_INFO  1
#define AIRY_LOG_LEVEL_WARN  2
#define AIRY_LOG_LEVEL_ERROR 3
#define AIRY_LOG_LEVEL_FATAL 4

// 平台检测宏
#define AIRY_PLATFORM_LINUX   1
#define AIRY_PLATFORM_MACOS   1
#define AIRY_PLATFORM_WINDOWS 1
#define AIRY_PLATFORM_NAME    "Linux"
#define AIRY_PLATFORM_BITS    64
#define AIRY_PLATFORM_POSIX   1

// 质量保证宏
#define AIRY_CHECK_NULL(ptr, error_code)       ...
#define AIRY_CHECK_NULL_GOTO(ptr, label, code) ...
#define AIRY_CHECK_CONDITION(cond, error_code) ...
#define AIRY_CHECK_RANGE(value, min, max, err) ...

// 头文件保护宏
#define AIRY_TYPES_H
#define AIRY_UTILS_LOGGER_H
#define AIRY_CORE_CONFIG_H
#define AIRY_PLATFORM_H
#define AIRY_QUALITY_H
```

### 6.2 模块前缀规则

所有宏必须以模块前缀开头，避免全局命名冲突：

```
AIRY_<类别>_<名称>
```

---

## 7. 错误码命名

### 7.1 基本规则：`MODULE_ERR_CATEGORY`

错误码的命名遵循 `MODULE_ERR_CATEGORY` 的格式：

| 模块 | 格式 | 示例 |
|------|------|------|
| agentrt（通用） | `AIRY_E<CODE>` | `AIRY_EINVAL`、`AIRY_ENOMEM` |
| config | `CONFIG_ERROR_<NAME>` | `CONFIG_ERROR_INVALID_ARG`、`CONFIG_ERROR_NOT_FOUND` |

### 7.2 通用错误码（agentrt 层）— 已废弃

> **⚠️ 本节已废弃**
>
> 本节中的错误码数值定义与 AirymaxOS 错误码体系对齐工作未同步更新，存在数值冲突与不一致。
> **权威错误码定义请见 [错误码参考文档](../70-project-erp/02-error-code-reference.md) §6.1**，该文档已与 AirymaxOS 体系对齐。
>
> 以下旧定义仅作历史参考，**禁止在新代码中使用本节的数值**。正确数值以 `agentrt/commons/utils/error/include/error.h` 及上述参考文档为准。

```c
// ⚠️ 以下数值已过时，权威定义见 error-code-reference.md §6.1
#define AIRY_EOK      (0)   // 成功（权威值一致）
#define AIRY_EINVAL       (-2)  // 参数无效 — 已废弃，权威值 -1
#define AIRY_ENOMEM       (-4)  // 内存不足 — 已废弃，权威值 -2
#define AIRY_EBUSY        (-17)  // 资源忙碌 — 已废弃，权威值 -9
#define AIRY_ENOENT       (-4)  // 资源不存在 — 已废弃，权威值 -5
#define AIRY_EPERM        (-10)  // 权限不足 — 已废弃，权威值 -4
#define AIRY_ETIMEOUT    (-6)  // 操作超时 — 已废弃，权威值 -11
#define AIRY_EIO          (-7)  // I/O 错误 — 已废弃，权威值 -19
#define AIRY_EEXIST       (-8)  // 资源已存在 — 已废弃，权威值 -18
#define AIRY_ENOTINIT     (-9)  // 引擎未初始化
#define AIRY_ECANCELED   (-10) // 操作已取消 — 已废弃，权威值 -16
#define AIRY_ENOTSUP      (-11) // 操作不支持 — 已废弃，权威值 -10
#define AIRY_EOVERFLOW    (-12) // 溢出错误 — 已废弃，权威值 -14
#define AIRY_EPROTO       (-13) // 协议错误
#define AIRY_ENOTCONN     (-14) // 未连接
#define AIRY_ECONNRESET   (-15) // 连接重置
#define AIRY_ENOSYS       (-16) // 函数未实现 — 已废弃，权威值 -3
#define AIRY_EFAIL        (-17) // 通用失败 — 已废弃，由 AIRY_ENOSYS 统一
#define AIRY_ENOTFOUND    (-18) // 资源未找到 — 已废弃，由 AIRY_ENOENT 统一
#define AIRY_EPLATFORM    (-27) // 平台未初始化
#define AIRY_EPROTONOSUPPORT (-28) // 协议/命令不支持
#define AIRY_ESERVICE     (-29) // 服务不可用
#define AIRY_EUNKNOWN     (-99) // 未知错误 — 已废弃，权威值 -15
```

### 7.3 模块级错误码（config 模块示例）

```c
typedef enum {
    CONFIG_SUCCESS = 0,              // 成功
    CONFIG_ERROR_INVALID_ARG = 1,    // 参数无效
    CONFIG_ERROR_NOT_FOUND = 2,      // 配置项不存在
    CONFIG_ERROR_TYPE_MISMATCH = 3,  // 类型不匹配
    CONFIG_ERROR_OUT_OF_MEMORY = 4,  // 内存不足
    CONFIG_ERROR_IO = 5,             // I/O 错误
    CONFIG_ERROR_PARSE = 6,          // 解析错误
    CONFIG_ERROR_VALIDATION = 7,     // 验证失败
    CONFIG_ERROR_LOCKED = 8,         // 配置被锁定
    CONFIG_ERROR_UNSUPPORTED = 9,    // 不支持的操作
    CONFIG_ERROR_THREAD = 10,        // 线程操作失败
} config_error_t;
```

### 7.4 错误码设计原则

1. **通用层**（agentrt）使用类 POSIX 风格的宏定义（`AIRY_E*`），所有错误码为负值，`0` 表示成功
2. **模块层**（如 config）使用枚举类型，正值表示错误类别，`0` 表示成功
3. 错误码语义应与 POSIX errno 保持一致的直觉（如 `EINVAL` = 无效参数）
4. 每个模块的错误码转换为字符串的函数命名为 `module_error_to_string()`

---

## 8. API 版本化

### 8.1 `@since` 标签

所有公开 API 在 Doxygen 注释中使用 `@since vX.Y.Z` 标签标注引入版本，遵循语义化版本（Semantic Versioning）。

### 8.2 代码示例

```c
/**
 * @brief 创建空配置值
 * @return 配置值对象，失败返回 NULL
 * @since v0.1.0
 */
config_value_t *config_value_create_null(void);

/**
 * @brief 创建布尔配置值
 * @param value 布尔值
 * @return 配置值对象，失败返回 NULL
 * @since v0.1.0
 */
config_value_t *config_value_create_bool(bool value);

/**
 * @brief 获取配置上下文中的值
 * @param ctx 配置上下文
 * @param key 键名
 * @return 配置值对象，不存在时返回 NULL
 * @since v0.1.0
 */
const config_value_t *config_context_get(const config_context_t *ctx, const char *key);

/**
 * @brief 设置跨平台线程局部存储变量
 * @param key 变量键名
 * @param value 变量值
 * @return AIRY_EOK 成功，AIRY_ENOMEM 内存不足
 * @since v0.2.0
 */
int airy_tls_set(const char *key, void *value);
```

### 8.3 版本号规则

- 主版本号（MAJOR）：不兼容的 API 修改
- 次版本号（MINOR）：向下兼容的功能新增
- 修订号（PATCH）：向下兼容的问题修正

---

## 9. 命名速查表

| 类别 | 规则 | 示例 |
|------|------|------|
| 平台名 | PascalCase | `Airymax` |
| 操作系统层 | 全小写 | `agentrt` |
| 核心组件 | PascalCase | `CoreKern`、`CoreLoopThree`、`TaskFlow`、`HeapStore`、`MemoryRovol` |
| 复数组件 | PascalCase（复数） | `Atoms`、`Cupolas` |
| 用户态服务 | snake_case + `_d` | `gateway_d`、`llm_d`、`tool_d` |
| C 源文件 | snake_case | `logger.c`、`core_config.c` |
| 头文件 | snake_case | `logger.h`、`platform.h` |
| 代码目录 | snake_case | `agentrt/`、`commons/`、`protocols/` |
| 顶层目录 | PascalCase | `Docs/`、`Tests/`、`Scripts/` |
| 配置文件 | kebab-case | `.editorconfig`、`.env.example` |
| 函数 | `module_action_object()` | `heapstore_init`、`config_value_create` |
| 三层 API 前缀 | `are_*` / `airy_*` / `airy_core_*` | `are_ipc_send` / `airy_ipc_send` / `airy_core_start`（§1.10、§4.4） |
| 项目隔离前缀 | `airy_*`（[SC]/[SS]） / `airymaxos_*`（[IND]） | `airy_ipc_send` / `airymaxos_kthread_create`（§4.4.2） |
| 模块子前缀 | `airy_<module>_*` | `airy_ipc_*`、`airy_log_*`、`airy_mem_*`（§4.4.3） |
| 函数名长度 | ≤ 30 字符 | `airy_ipc_send`（12 字符）、`airy_sched_agent_compute_weight`（30 字符，边界）（§4.4.4） |
| 类型 | `module_type_t` | `airy_err_t`、`config_value_t` |
| 枚举 | `MODULE_UPPER` | `CONFIG_TYPE_INT`、`AIRY_MEM_LAYER1_RAW` |
| 宏 | `UPPER_SNAKE_CASE` | `AIRY_LOG_LEVEL_DEBUG`、`AIRY_CHECK_NULL` |
| 通用错误码 | `AIRY_E*` | `AIRY_EINVAL`、`AIRY_ENOMEM` |
| 模块错误码 | `MODULE_ERROR_*` | `CONFIG_ERROR_NOT_FOUND` |
| 头文件保护 | `UPPER_SNAKE_CASE` | `AIRY_TYPES_H`、`AIRY_PLATFORM_H` |
| API 版本 | `@since vX.Y.Z` | `@since v0.1.0` |

---

## 10. 常见错误与反模式

### 10.1 应避免的命名

| 反模式 | 问题 | 正确写法 |
|--------|------|---------|
| `agentrtLogWrite` | 混用 camelCase，C 代码应使用 snake_case | `airy_log_write` |
| `ConfigValue` | C 类型不应使用 PascalCase | `config_value_t` |
| `AIRY_log_level` | 宏不应混用大小写 | `AIRY_LOG_LEVEL` |
| `gatewayDaemon` | Daemon 名应使用 `_d` 后缀 | `gateway_d` |
| `core_kern` | 组件名应使用 PascalCase | `CoreKern` |
| `heap-store.h` | C 头文件应使用 snake_case | `heapstore.h` |

### 10.2 关键原则

1. **一致性优先**：当不确定时，参考已有代码中的命名模式
2. **模块前缀必须**：所有公开符号必须带模块前缀，避免链接冲突
3. **语义明确**：命名应准确表达其用途，避免缩写歧义（除非是广泛接受的缩写如 `init`、`cfg`）
4. **C 惯例为准**：由于 Airymax 核心以 C 语言实现，命名以 C 社区惯例为基准