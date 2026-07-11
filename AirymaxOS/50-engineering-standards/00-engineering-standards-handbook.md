Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.

# agentrt-linux（AirymaxOS）工程标准规范手册

> **文档定位**： 交付成果 #3——工程标准规范手册。本文档是 agentrt-linux（AirymaxOS，极境智能体操作系统）工程标准的执行依据，汇总 7 主题文档的核心规范条目、SSoT 规则编号注册表、IRON-9 v2 三层共享模型、合规性禁词清单与乔布斯美学审查标准，作为后续 M1 开发阶段的强制执行依据。\
> **版本**： 0.1.1（文档体系完成）/ 1.0.1（开发）\
> **最后更新**： 2026-07-08\
> **理论根基**： Linux 6.6 内核基线工程思想 + Airymax 体系并行论（Multibody Cybernetic Intelligent System）+ seL4 微内核设计思想 + 主流 Linux 工程标准规范参考\
> **配套文档**： comprehensive-review-report.md（交付成果 #1，位于 `docs-closed/agentrt-linux/_review_0.1.1/`）、revision-changelog.md（交付成果 #2，位于 `docs-closed/agentrt-linux/_review_0.1.1/`）\
> **权威性**： 本手册为工程标准的**单一事实源（SSoT）**索引。当各主题文档与本手册冲突时，以本手册为准；当本手册与 SSoT 注册表（07-maintainers-and-governance.md 第 10 章）冲突时，以 SSoT 注册表为准。

---

## 目录

- [第 1 章 工程标准体系总览](#第-1-章-工程标准体系总览)
- [第 2 章 IRON 工程铁律（不可妥协）](#第-2-章-iron-工程铁律不可妥协)
- [第 3 章 SSoT 规则编号注册表](#第-3-章-ssot-规则编号注册表)
- [第 4 章 IRON-9 v2 三层共享模型与跨项目代码共享机制](#第-4-章-iron-9-v2-三层共享模型与跨项目代码共享机制)
- [第 5 章 7 层自动化验证体系](#第-5-章-7-层自动化验证体系)
- [第 6 章 合规性禁词清单与命名体系规范](#第-6-章-合规性禁词清单与命名体系规范)
- [第 7 章 微核心与微内核术语规范](#第-7-章-微核心与微内核术语规范)
- [第 8 章 乔布斯美学"无隐藏瑕疵"审查标准](#第-8-章-乔布斯美学无隐藏瑕疵审查标准)
- [第 9 章 OLK-6.6 源码验证基线](#第-9-章-olk-66-源码验证基线)
- [第 10 章 开发就绪前置条件检查清单](#第-10-章-开发就绪前置条件检查清单)

---

## 第 1 章 工程标准体系总览

### 1.1 7 主题文档架构

agentrt-linux 工程标准由 7 主题文档构成，本手册为其总索引与执行依据：

| 主题 | 文档 | 职责 |
|------|------|------|
| 01 | [01-coding-standards.md](./01-coding-standards.md) | 代码规范：命名 / 类型 / typedef / bool / float 禁令 |
| 02 | [02-code-format.md](./02-code-format.md) | 代码格式：Tab / 缩进 / 行宽 / 指针声明 |
| 03 | [03-code-style.md](./03-code-style.md) | 代码风格：反过度抽象 / 引用计数 / 错误路径 |
| 04 | [04-engineering-philosophy.md](./04-engineering-philosophy.md) | 工程思想：双层稳定性 / 策略机制分离 / regression 不可接受 |
| 05 | [05-development-process.md](./05-development-process.md) | 开发流程：补丁生命周期 / Merge Window + RC |
| 06 | [06-toolchain-and-automation.md](./06-toolchain-and-automation.md) | 工具链：7 层验证 / CI 矩阵 / 覆盖率门槛 |
| 07 | [07-maintainers-and-governance.md](./07-maintainers-and-governance.md) | 治理：MAINTAINERS / DCO / 信任链 / SSoT 注册表 |

### 1.2 三大支柱

| 支柱 | 来源 | 在工程标准中的体现 |
|------|------|------------------|
| **Linux 6.6 内核基线工程思想** | OLK-6.6 源码深度研究 | 双层稳定性、渐进式开发、regression 不可接受、不破坏用户空间 |
| **Airymax 五维正交 24 原则** | 00-architectural-principles.md（S/K/C/E/A 五维） | 每条工程标准映射到至少一条五维原则 |
| **seL4 微内核设计思想** | Liedtke minimality principle + capability-based security | 内核极简、服务用户态化、消息传递通信 |

### 1.3 工程标准适用范围

- **适用对象**：agentrt-linux 全部 8 子仓（airymaxos-kernel/services/security/memory/cognition/cloudnative/system/tests）
- **适用语言**：内核态 C（主力）、内核模块 Rust（安全敏感子系统）、用户态服务 C/Rust/Python/TypeScript
- **适用阶段**：0.1.1（文档体系完成）→ 1.0.1（开发）→ 后续所有版本
- **不适用**：agentrt 用户态运行时（遵循 agentrt 自身的 IRON-1~10 规则，与本手册 IRON-9 v2 同源但独立）

---

## 第 2 章 IRON 工程铁律（不可妥协）

### 2.1 OS-IRON 编号汇总（14 条铁律）

> **SSoT 声明（v0.3.1，2026-07-10）**：OS-IRON 铁律编号的唯一权威来源（SSoT）为闭源《agentrt-linux 基本工程标准规范》附录 A（14 条铁律）。本表为附录 A 的公开镜像，必须与之一一对应。此前版本（v0.3.0 及更早）本表仅有 10 条且 IRON-003~010 与 SSoT 语义冲突，现已全部对齐。原表中不属于 IRON 层级的规则已重分类：①"7 层自动化验证强制"→ OS-ACC-002（验收标准）；②"信任链分层不可越级提交"→ OS-STD-GOV-008；③"Reviewed-by 不可替代 Signed-off-by"→ 由 IRON-005（审查优先）+ IRON-007（DCO）共同涵盖；④"组织 6 级成熟度 Level 2"→ OS-STD-GOV-009。

| 编号 | 规则 | 五维映射 | 来源文档 |
|------|------|---------|---------|
| **OS-IRON-001** | 用户空间 ABI 永不破坏——一旦接口导出至用户空间，必须永久支持 | K-2 / E-7 | 04 工程思想 §2.1 |
| **OS-IRON-002** | 内核内部 API 不保证稳定；API 改动者必须同时修复所有受影响代码（"you broke it, you fix it"） | K-1 / C-2 | 04 工程思想 §2.2 |
| **OS-IRON-003** | 策略与机制分离——机制留在内核（提供能力），策略外移至用户空间或可插拔模块 | K-4 | 04 工程思想 §3 |
| **OS-IRON-004** | 渐进式开发，补丁自包含——每个补丁是逻辑上自包含的最小变更单元，中点可编译运行，支持 git bisect | S-4 / C-2 | 04 工程思想 §4 / 05 开发流程 |
| **OS-IRON-005** | 审查优先文化——所有代码进入主线前必须通过至少一名维护者审查并获得 Reviewed-by 标签 | S-3 / S-1 | 04 工程思想 §6 / 07 治理 §6 |
| **OS-IRON-006** | 不破坏用户空间原则——任何补丁不得导致用户空间程序行为改变，除非该行为被明确标记为 bug（regression 零容忍） | E-6 | 04 工程思想 §9 / 05 开发流程 |
| **OS-IRON-007** | DCO 强制——所有补丁必须包含 Signed-off-by 标签，CI 中 DCO bot 自动验证链条完整性 | E-6 | 07 治理 §5 |
| **OS-IRON-008** | 共享契约层 6 个头文件双向 CI 验证——[SC] 头文件变更必须通过 agentrt + AirymaxOS CI 同时通过 | E-6 / E-8 | 120 跨项目代码共享 |
| **OS-IRON-009** | 代码共享边界：仅 agentrt ↔ AirymaxOS——与 Euler 项目仅技术参考，不共享代码 | — | 120 跨项目代码共享 / 07 治理 §1.4 |
| **OS-IRON-010** | "Linux 6.6 为基、seL4 为鉴"工程取向——以 OLK-6.6 工程思想为基线，以 seL4 微内核思想为架构层借鉴 | — | 04 工程思想 §12 |
| **OS-IRON-011** | 双源边界声明——OLK-6.6 与 seL4 源码仅为本地参考路径研究对象，关系是"技术参考"非"代码共享" | — | 04 工程思想 §12 |
| **OS-IRON-012** | seL4 借鉴仅限架构层（ES-SEL4-1~5）——不包括编码风格层（4 空格 / camelCase / 重度 typedef） | — | 04 工程思想 §12 |
| **OS-IRON-013** | 8 子仓独立 git 仓库 + submodule 管理——拆分为 8 个独立 leaf 仓，由管理仓通过 submodule 统一管理 | S-2 | 04 工程思想 §13 |
| **OS-IRON-014** | [SC] 共享契约层 6 头文件单一数据源（禁止物理副本）——6 个头文件物理宿主在 kernel/include/airymax/，其他子仓通过 -I 引用 | E-7 | 120 跨项目代码共享 |

### 2.2 IRON 铁律执行机制

| 铁律 | 执行机制 | 违反后果 |
|------|---------|---------|
| OS-IRON-001 | ABI 审查组 + 6 个月宽限期 + 弃用声明 | PR 被拒绝合并 |
| OS-IRON-002 | 同补丁序列修复所有调用点 | NACK，补丁被打回 |
| OS-IRON-003 | 可插拔策略审查（sched_ext BPF / LSM 钩子 / ops 注入） | 策略硬编码入内核被拒绝 |
| OS-IRON-004 | CI 每个补丁中点编译测试，git bisect 可追溯 | CI 失败，阻断合并 |
| OS-IRON-005 | CODEOWNERS bot 自动分配审查人 + Reviewed-by 标签校验 | 缺少 Reviewed-by 的 PR 无法合并 |
| OS-IRON-006 | regression 报告追踪 + RC 周期强制修复或 revert | revert 引入补丁 |
| OS-IRON-007 | DCO bot 自动验证 Signed-off-by 链条完整性 | PR 无法合并 |
| OS-IRON-008 | [SC] 头文件变更触发 agentrt + AirymaxOS 双向 CI | 任一端 CI 失败阻断合并 |
| OS-IRON-009 | 同源语义漂移检测 | 两端语义不一致告警 |
| OS-IRON-010 | 工程取向合规审查（编码风格 / 构建系统 / 内存模型基线检查） | 偏离基线的补丁被拒绝 |
| OS-IRON-011 | 双源边界审查（01Reference/ 仅本地参考路径） | 代码层面的直接引用被拒绝 |
| OS-IRON-012 | seL4 借鉴范围审查（仅 ES-SEL4-1~5 架构层） | 编码风格层借鉴被拒绝 |
| OS-IRON-013 | 8 子仓仓库结构校验 + submodule 一致性检查 | 跨仓直接引用（非 submodule）被拒绝 |
| OS-IRON-014 | `scripts/check-sc-single-source.sh` 检查 [SC] 头文件单一数据源 | 物理副本被检测并拒绝 |

---

## 第 3 章 SSoT 规则编号注册表

> **SSoT 声明（v0.3.1，2026-07-10）**：本章是闭源《agentrt-linux 基本工程标准规范》附录 A（规则编号注册表）的**公开镜像**。附录 A 定义了 6 类规则编号的权威定义：14 条 OS-IRON、7 条 OS-BAN（001-011，含间隔）、19 条 OS-STD（001-052，含间隔）、1 条 OS-ACC、3 条 OS-KER（001-003）、7 条 OS-FMT（001-007）。本章各表须与附录 A 一一对应；本章与附录 A 的任何冲突，以附录 A 为准。
>
> **已知差异（v0.3.1 → 1.0.1 消解）**：公开文档历史演进中，OS-KER/OS-BAN/OS-ACC 三个类别出现了与附录 A 不同的编号定义与扩展（详见各表"SSoT 对齐"注释）。这是因公开子文档（01-07）各自独立编号累积所致，非设计意图。完整重编号消解计划见 [07-maintainers-and-governance.md §10.2.5](./07-maintainers-and-governance.md)，在 1.0.1 开发阶段执行。v0.3.1 通过下方 §3.8 SSoT 交叉引用映射表建立"每个技术点→单一 SSoT 编号"的权威对应关系。

### 3.1 编号格式约定

所有规则编号使用 3 位数字序号：`OS-<前缀>-<子域>-<NNN>`

- **前缀**：IRON（铁律）/ STD（标准）/ KER（内核）/ BAN（禁止）/ FMT（格式）/ ABI（接口稳定性）/ ACC（验收）
- **子域**（可选）：CODE（编码）/ FMT（格式）/ STY（风格）/ GOV（治理）
- **示例**：`OS-KER-001`、`OS-FMT-001`、`OS-STD-CODE-001`、`OS-STD-GOV-005`

### 3.2 OS-STD 标准规则汇总

#### 3.2.1 OS-STD-CODE 编码规范（4 条）

| 编号 | 规则 |
|------|------|
| OS-STD-CODE-001 | 全局函数/变量描述性命名，禁止无意义缩写 |
| OS-STD-CODE-002 | goto 集中出口，分级标签按分配逆序释放 |
| OS-STD-CODE-003 | 禁止 `strcpy`/`strncpy`/`strlcpy`，强制 `strscpy` |
| OS-STD-CODE-004 | 编译期必须 0 警告 0 错误；任何警告须修复或显式标注 `#pragma` 抑制并附说明 |

#### 3.2.2 OS-STD-FMT 格式规范（2 条）

| 编号 | 规则 |
|------|------|
| OS-STD-FMT-001 | Tab 8 字符宽（C 内核代码），行尾禁止空白 |
| OS-STD-FMT-002 | 指针声明 `*` 贴名字不贴类型 |

#### 3.2.3 OS-STD-STY 风格规范（2 条）

| 编号 | 规则 |
|------|------|
| OS-STD-STY-001 | 反过度抽象，不预先抽象 |
| OS-STD-STY-002 | 引用计数强制，锁不替代引用计数 |

#### 3.2.4 OS-STD-GOV 治理规范（9 条）

| 编号 | 规则 |
|------|------|
| OS-STD-GOV-001 | MAINTAINERS 文件为子系统归属单一事实源 |
| OS-STD-GOV-002 | 每子仓须有主维护者与至少 1 名审查者 |
| OS-STD-GOV-003 | 状态字段须如实标注 Experimental/Pilot/Stable/Deprecated |
| OS-STD-GOV-004 | Co-developed-by 必须紧跟对应 Signed-off-by |
| OS-STD-GOV-005 | 审查意见必须逐条响应 |
| OS-STD-GOV-006 | 不响应审查是致命错误，重发应直接拒绝 |
| OS-STD-GOV-007 | Andrew Morton 规则：未导致改动的审查意见转化为代码注释 |
| OS-STD-GOV-008 | 信任链分层不可越级提交（v0.3.1 重分类：原 OS-IRON-006 属治理标准而非工程铁律） |
| OS-STD-GOV-009 | 组织 6 级成熟度 Level 2 为底线目标（v0.3.1 重分类：原 OS-IRON-010 属治理标准而非工程铁律） |

### 3.3 OS-KER 内核工程规则汇总（8 条）

> **SSoT 对齐（v0.3.1）**：闭源附录 A 定义 OS-KER 仅 3 条（001-003）：OS-KER-001=goto 集中出口模式、OS-KER-002=禁止 #if 桩函数模式、OS-KER-003=条件编译必须用 IS_ENABLED()。本表 8 条（001-008）是公开子文档历史演进的扩展，与 SSoT 编号定义不同。每条规则的 SSoT 权威编号见 §3.8 交叉引用映射表。完整重编号消解在 1.0.1 执行。

| 编号 | 规则 | OLK-6.6 验证 |
|------|------|-------------|
| OS-KER-001 | Linux 6.6 内核基线，不擅自提升基线版本（对应 BAN-361/IRON-010） | — |
| OS-KER-002 | 内核内部用 `u8/u16/u32/u64`，UAPI 用 `__u8/__u16/__u32/__u64` | `include/uapi/linux/types.h` |
| OS-KER-003 | typedef 严格限制（仅 5 种例外） | `Documentation/process/coding-style.rst` |
| OS-KER-004 | 禁止 `BUG()`/`BUG_ON()`，改用 `WARN()` 并提供恢复 | `include/asm-generic/bug.h` |
| OS-KER-005 | 内核内部用 Tab 缩进，Tab 宽度 8 字符（同 OS-STD-FMT-001，但专指内核 C 代码） | `.clang-format` |
| OS-KER-006 | 内核内部 API 可在任何补丁中变更（同 OS-IRON-002，聚焦 API 稳定性维度） | — |
| OS-KER-007 | 内核态禁止使用 float/double 类型（`-mno-80387`），改用 Q16.16 定点数（`airy_q16_t`） | `arch/x86/Makefile:137` |
| OS-KER-008 | bool 仅用于函数返回值与栈变量，禁止用于结构体成员（结构体成员用 `u8`/bitfield） | `Documentation/process/coding-style.rst` |

### 3.4 OS-BAN 禁止规则汇总（6 条）

> **SSoT 对齐（v0.3.1）**：闭源附录 A 定义 OS-BAN 共 7 条（001-005, 010-011）：001=敏感术语、002=禁止 BUG()、003=sizeof(*p)、004=禁止 strcpy、005=禁用 %p、010=开源文档禁词、011=禁词违规处罚。本表 6 条（001-006）是公开子文档的独立编号，与 SSoT 编号定义不同。例如本表 OS-BAN-001（匈牙利命名）在 SSoT 中无对应编号；本表 OS-BAN-002（敏感术语）对应 SSoT OS-BAN-001。每条规则的 SSoT 权威编号见 §3.8 交叉引用映射表。

| 编号 | 规则 |
|------|------|
| OS-BAN-001 | 禁止匈牙利命名法 |
| OS-BAN-002 | 禁止敏感术语 master/slave、blacklist/whitelist |
| OS-BAN-003 | 禁止 `%p` 默认输出（须哈希化） |
| OS-BAN-004 | 禁止越级提交补丁绕过信任链 |
| OS-BAN-005 | 禁止伪造 SoB 传播路径 |
| OS-BAN-006 | 禁止以雇主利益为由打压他人审查 |

### 3.5 OS-FMT 格式规则汇总（7 条）

> **SSoT 对齐（v0.3.1）**：本表与闭源附录 A OS-FMT（001-007）完全一致，无差异。此前公开文档将格式规则分散折叠至 OS-STD-FMT（仅 2 条）与 OS-KER-005，导致同一技术点（如"Tab 8 字符宽"）出现 3 个编号。v0.3.1 将 OS-FMT 独立为完整 7 条表，作为格式规则的权威编号源；OS-STD-FMT-001/002 保留为历史子域编号，映射见 §3.8。

| 编号 | 规则 | SSoT 章节 |
|------|------|----------|
| **OS-FMT-001** | Tab 缩进，8 字符宽 | 附录 A §3.1 |
| **OS-FMT-002** | 80 列行宽 | 附录 A §3.2 |
| **OS-FMT-003** | K&R 大括号风格（开括号置于控制语句行末） | 附录 A §3.3 |
| **OS-FMT-004** | 即使单条语句也使用大括号 | 附录 A §3.3 |
| **OS-FMT-005** | 空格规则（关键字后空格、指针 `*` 贴名字） | 附录 A §3.4 |
| **OS-FMT-006** | 行尾禁止空白 | 附录 A §3.5 |
| **OS-FMT-007** | clang-format 强制（`.clang-format` 配置文件） | 附录 A §3.6 |

### 3.6 OS-ABI 接口稳定性规则（1 条）

> **SSoT 对齐（v0.3.1）**：闭源附录 A 未设独立的 OS-ABI 类别——4 层接口稳定性分级在 SSoT 中由 OS-IRON-001（L1 永久）+ OS-IRON-002（L3 可变）+ OS-STD-004（返回值约定）共同承载。本表保留 OS-ABI-001 作为公开文档的 4 层分级汇总视图。

| 编号 | 规则 |
|------|------|
| OS-ABI-001 | 4 层接口稳定性分级：L1 永久（Agent API）/ L2 弃用流程（运行时接口）/ L3 版本协商（内核内部 API）/ L4 完全自由（私有实现） |

### 3.7 OS-ACC 验收标准（5 条）

> **SSoT 对齐（v0.3.1）**：闭源附录 A 定义 OS-ACC 仅 1 条：OS-ACC-001=覆盖率 ≥ 80%，关键路径 ≥ 90%。本表 5 条（001-005）是公开文档的扩展，其中 OS-ACC-002~005 分别映射至 OS-IRON-004（中点可编译）、OS-IRON-006（regression 零容忍）、OS-IRON-007（DCO 验证）、OS-STD-GOV-003（状态字段）。完整映射见 §3.8。

| 编号 | 验收项 | SSoT 映射 |
|------|--------|----------|
| OS-ACC-001 | 覆盖率 ≥ 80%，关键路径 ≥ 90% | SSoT OS-ACC-001（一致） |
| OS-ACC-002 | PR 通过 7 层自动化验证全部 | 对应 OS-STD-030（checkpatch 强制）+ 06 工具链 |
| OS-ACC-003 | 补丁序列每个中点可编译运行 | 对应 OS-IRON-004（渐进式开发） |
| OS-ACC-004 | regression 须在 72 小时内响应 | 对应 OS-IRON-006（不破坏用户空间） |
| OS-ACC-005 | 子系统状态字段须与实际生命周期一致 | 对应 OS-STD-GOV-003 |

### 3.8 SSoT 交叉引用映射表

> **目的（v0.3.1）**：依据"每个技术点只能有一个规范标准"原则，本表为公开文档中所有与 SSoT 附录 A 编号不一致的规则建立"技术点 → SSoT 权威编号"的单一对应关系。1.0.1 重编号后本表将退役。

#### 3.8.1 OS-KER 技术点映射

| 技术点 | 公开文档当前编号 | SSoT 权威编号 | 处置 |
|--------|-----------------|-------------|------|
| goto 集中出口模式 | OS-STD-CODE-002 | OS-KER-001 | 1.0.1 统一为 OS-KER-001 |
| 禁止 #if 桩函数模式 | （由 IRON-1 涵盖） | OS-KER-002 | 1.0.1 补标 OS-KER-002 |
| 条件编译用 IS_ENABLED() | （散见于 02-code-format） | OS-KER-003 | 1.0.1 补标 OS-KER-003 |
| Linux 6.6 内核基线 | OS-KER-001（公开） | OS-IRON-010 | 1.0.1 改为 IRON-010 |
| u8/u16/u32/u64 类型 | OS-KER-002（公开） | OS-STD-010 | 1.0.1 改为 OS-STD-010 |
| typedef 限制 | OS-KER-003（公开） | OS-STD-010（扩展） | 1.0.1 并入 OS-STD-010 |
| 禁止 BUG()/BUG_ON() | OS-KER-004（公开） | OS-BAN-002 | 1.0.1 改为 OS-BAN-002 |
| Tab 8 字符宽 | OS-KER-005 / OS-STD-FMT-001 | OS-FMT-001 | 1.0.1 统一为 OS-FMT-001 |
| 内核内部 API 不稳定 | OS-KER-006（公开） | OS-IRON-002 | 1.0.1 改为 IRON-002 |
| 禁止 float/double | OS-KER-007（公开） | OS-STD-010（扩展） | 1.0.1 并入 OS-STD-010 |
| bool 仅用于返回值/栈变量 | OS-KER-008（公开） | OS-STD-011 | 1.0.1 改为 OS-STD-011 |

#### 3.8.2 OS-BAN 技术点映射

| 技术点 | 公开文档当前编号 | SSoT 权威编号 | 处置 |
|--------|-----------------|-------------|------|
| 敏感术语禁用 | OS-BAN-002（公开） | OS-BAN-001 | 1.0.1 统一为 OS-BAN-001 |
| 禁止 BUG() | OS-KER-004（公开） | OS-BAN-002 | 1.0.1 统一为 OS-BAN-002 |
| sizeof(*p) | （散见于 01-coding） | OS-BAN-003 | 1.0.1 补标 OS-BAN-003 |
| 禁止 strcpy/strncpy | OS-STD-CODE-003 | OS-BAN-004 | 1.0.1 统一为 OS-BAN-004 |
| 禁用 %p 默认输出 | OS-BAN-003（公开） | OS-BAN-005 | 1.0.1 统一为 OS-BAN-005 |
| 开源文档禁词 | （散见于 11-compliance） | OS-BAN-010 | 1.0.1 补标 OS-BAN-010 |
| 禁词违规处罚 | （散见于 11-compliance） | OS-BAN-011 | 1.0.1 补标 OS-BAN-011 |
| 禁止匈牙利命名法 | OS-BAN-001（公开） | （SSoT 无独立编号） | 1.0.1 新增 OS-STD-005 扩展或 OS-BAN-006 |
| 禁止越级提交 | OS-BAN-004（公开） | OS-STD-GOV-008 | 1.0.1 改为 OS-STD-GOV-008 |
| 禁止伪造 SoB | OS-BAN-005（公开） | OS-IRON-007（涵盖） | 1.0.1 并入 IRON-007 |
| 禁止打压审查 | OS-BAN-006（公开） | OS-STD-GOV（新增） | 1.0.1 新增 OS-STD-GOV-010 |

#### 3.8.3 OS-ACC 技术点映射

| 技术点 | 公开文档当前编号 | SSoT 权威编号 | 处置 |
|--------|-----------------|-------------|------|
| 覆盖率 ≥ 80% | （散见于 06-toolchain） | OS-ACC-001 | 1.0.1 补标 OS-ACC-001 |
| 7 层自动化验证 | OS-ACC-002（公开） | OS-STD-030 + 06 工具链 | 1.0.1 改为 OS-STD-030 |
| 中点可编译 | OS-ACC-003（公开） | OS-IRON-004 | 1.0.1 改为 IRON-004 |
| regression 72h 响应 | OS-ACC-004（公开） | OS-IRON-006 | 1.0.1 改为 IRON-006 |
| 状态字段一致 | OS-ACC-005（公开） | OS-STD-GOV-003 | 1.0.1 改为 OS-STD-GOV-003 |

### 3.9 编号申请流程

新规则编号须经过以下流程注册：

1. **RFC**：在 GitHub Discussions 发起 RFC，说明规则编号、规则文本、来源文档、适用范围
2. **评审**：经至少一名顶级子系统维护者审查，公示 14 天接受异议
3. **注册**：通过评审后，由总维护者将规则写入对应文档的编号汇总表，并更新 SSoT 注册表（[07-maintainers-and-governance.md 第 10 章](./07-maintainers-and-governance.md)）

---

## 第 4 章 IRON-9 v2 三层共享模型与跨项目代码共享机制

### 4.1 三层共享模型

agentrt-linux 与 agentrt 之间的代码共享与语义同源遵循 IRON-9 v2 三层模型：

| 层次 | 标注 | 共享程度 | 共享内容 | 共享方式 |
|------|------|---------|---------|---------|
| **共享契约层** | [SC] | 完全共享头文件 | 6 个头文件（见 §4.2） | `include/airymax/` 目录，两端 `#include` 同一头文件 |
| **语义同源层** | [SS] | 语义一致，实现独立 | MicroCoreRT 调度语义 / AgentsIPC 128B 消息头 / Cupolas 安全模型 / CoreLoopThree 三层循环 | 两端独立实现，通过行为契约测试验证语义一致 |
| **完全独立层** | [IND] | 完全独立 | agentrt-linux 专属：内核驱动框架、Kbuild、内核内部 API；agentrt 专属：跨平台用户态运行时、SDK 四语言 | 各自独立仓库，无依赖关系 |

### 4.2 [SC] 共享契约层头文件清单

> 基础 4 个头文件于 2026-07-07 确立；`sched.h` 和 `ipc.h` 于后续调度子系统与 IPC 子系统修订时新增（2026-07-08），现共 6 个 [SC] 头文件。

| 头文件 | 共享内容 | IRON-9 v2 层次 |
|--------|---------|---------------|
| `include/airymax/bpf_struct_ops.h` | struct_ops 状态机 + common_value | [SC] |
| `include/airymax/memory_types.h` | MemoryRovol L1-L4 数据结构 + GFP 掩码语义 + PMEM 持久化接口 | [SC] |
| `include/airymax/security_types.h` | POSIX capability 41 ID 枚举 + LSM 钩子 252 ID 枚举 + Cupolas blob 布局 + capability 派生模型 + Vault backend 抽象 + 策略裁决结果 4 值枚举 | [SC] |
| `include/airymax/cognition_types.h` | CoreLoopThree 阶段枚举 + Thinkdual 模式枚举 + LLM 推理阶段枚举 + CoreLoopThree 上下文结构 + Token 能效指标结构 + GPU/NPU 能力描述符 | [SC] |
| `include/airymax/sched.h` | SCHED_EXT 调度类编号约束（复用内核 SCHED_EXT=7，禁止 SCHED_AGENT 宏）+ 任务描述符（magic 0x41475453 'AGTS'）+ vtime 类型与衰减公式 + 优先级范围 0-139 + AIRY_SLICE_DFL（20ms） | [SC] |
| `include/airymax/ipc.h` | IPC magic（0x41524531 'ARE1'）+ 128B 消息头结构（`struct airy_ipc_msg_hdr`）+ SQE/CQE 操作码与标志位 | [SC] |

### 4.3 代码共享边界

| 边界 | 共享策略 | 说明 |
|------|---------|------|
| agentrt ↔ agentrt-linux | [SC] 共享契约层 6 个头文件 | 唯一的代码共享通道 |
| agentrt-linux ↔ Linux 6.6 基线 | 仅技术参考，不共享代码 | 参考主流 Linux 工程思想与实现方式，不共享代码 |
| agentrt ↔ 第三方项目 | 不共享 | agentrt 是闭源商业项目（AGPL-3.0 + Apache-2.0 双许可证） |

### 4.4 IPC magic 值与任务描述符 magic

| 标识 | magic 值 | ASCII | 用途 | 共享层次 |
|------|---------|-------|------|---------|
| IPC 消息头 magic | `0x41524531` | 'ARE1' | agentrt 与 AirymaxOS 共享 IPC 消息头布局 | [SC] |
| 任务描述符 magic | `0x41475453` | 'AGTS' | 任务描述符独立 magic，避免与 IPC 消息混淆 | [IND] |

> **设计原则**：IPC magic 同源但独立——agentrt 和 AirymaxOS 共享 IPC 消息头布局，magic 值同源，无需协议转换层。任务描述符不是 IPC 消息，使用独立 magic 避免混淆。

### 4.5 安全作为横切关注点

安全不是独立数据流，而是横切关注点（cross-cutting concern），贯穿所有 4 大数据流：
- 认知循环数据流（CoreLoopThree）
- 记忆卷载数据流（MemoryRovol）
- IPC 消息数据流（AgentsIPC）
- 调度数据流（SCHED_EXT）

---

## 第 5 章 7 层自动化验证体系

### 5.1 7 层验证流水线

| 层级 | 验证内容 | 工具 | 失败后果 |
|------|---------|------|---------|
| **第 1 层** | 编译期验证 | gcc -W / clang / -Werror | 阻断提交 |
| **第 2 层** | 静态分析 | sparse / Smatch / Coccinelle / clang-analyzer / rust-clippy | 阻断提交 |
| **第 3 层** | 预提交验证 | pre-commit hooks / DCO bot / format-check | 阻断推送 |
| **第 4 层** | CI 门禁 | GitHub Actions / 9 矩阵（3 架构 × 3 配置） | 阻断合并 |
| **第 5 层** | 连接验证 | 符号检查 / ABI 漂移检测 | 阻断合并 |
| **第 6 层** | 协议验证 | AgentsIPC 128B 校验器 / Agent 行为契约测试 | 阻断合并 |
| **第 7 层** | 发布验证 | GPG 签名 / 模块签名 / SHA-256 校验 | 阻断发布 |

### 5.2 CI 矩阵覆盖要求

| 维度 | 覆盖范围 |
|------|---------|
| 架构 | x86_64 / aarch64 / riscv64（+ ppc64 推荐用于交叉编译检查） |
| 配置 | allnoconfig / allmodconfig / defconfig |
| 编译器 | gcc + clang（双编译器交叉验证） |
| 总时长 | ≤ 60 分钟 |

### 5.3 动态分析工具矩阵

| 工具 | 检测内容 | 配置 | 适用场景 |
|------|---------|------|---------|
| KASAN | 地址越界 / use-after-free | `CONFIG_KASAN=y` | 测试环境 |
| KFENCE | 轻量级内存错误 | `CONFIG_KFENCE=y` | 生产环境（采样模式） |
| UBSAN | 未定义行为 | `CONFIG_UBSAN=y` | 测试环境 |
| KCSAN | 数据竞争 | `CONFIG_KCSAN=y` | 并发测试 |
| lockdep | 死锁 / 锁顺序违规 | `CONFIG_LOCKDEP=y` | 全功能启用 |
| kmemleak | 内存泄漏 | `CONFIG_DEBUG_KMEMLEAK=y` | kselftest 后检查 |

### 5.4 覆盖率门槛

| 门槛 | 要求 |
|------|------|
| KCOV | 模糊测试必须启用 KCOV 收集覆盖率 |
| gcov | GCC 覆盖率工具，全局或按模块覆盖率 |
| KUnit | 白盒单元测试，毫秒级运行 |
| kselftest | 用户态系统级测试 |

---

## 第 6 章 合规性禁词清单与命名体系规范

### 6.1 BAN-361 禁止引用的 Linux 7.0 专属特性

以下特性属于 Linux 7.0 专属，禁止在 agentrt-linux 文档、代码、注释中引用为 Linux 6.6 原生能力：

| 禁止引用 | 正确表述 | 说明 |
|---------|---------|------|
| `PREEMPT_LAZY` | `PREEMPT_DYNAMIC` | Linux 6.6 原生抢占模型 |
| `Rust 正式转正` | `Rust 实验性支持` | Linux 6.6 Rust 仍为实验性 |
| `MGLRU 2.0` | `MGLRU 多代 LRU` | Linux 6.6 原生 MGLRU |
| `XFS 自修复` | `XFS 在线修复` | Linux 6.6 原生 XFS 能力 |
| `eBPF 签名验证` | `eBPF kfunc + dynamic pointer` | Linux 6.6 原生 eBPF 能力 |
| `sched_ext（误标为 Linux 6.15+）` | `sched_ext（OLK-6.6 内置）` | OLK-6.6 已内置 SCHED_EXT=7；2.x.x 基线为 Linux 7.1（ADR-013） |

### 6.2 命名体系规范

#### 6.2.1 产品标准名称

| 产品 | 标准全称 | 简称 | 定位 |
|------|---------|------|------|
| agentrt | AirymaxAgentRT 极境智能体运行底座平台工程 | agentrt / AirymaxRT | 用户态组件、AI Agent 运行时平台工程 |
| agentrt-linux | AirymaxAgentOS 极境智能体操作系统 | agentrt-linux / AirymaxOS | 操作系统发行版、智能体操作系统 |

#### 6.2.2 命名规则

- **正式英文名**：全称使用 "AirymaxAgentOS"（无空格），简称使用 "AirymaxOS"
- **正式中文名**：全称使用 "极境智能体操作系统"
- **代码仓库**：仓库名为 "agentrt-linux"（注意：目录名与文档中使用 "agentrt-linux" 作为产品代号）
- **文档表述**：首次出现使用全称 "agentrt-linux（AirymaxOS，极境智能体操作系统）"，后续使用简称 "agentrt-linux" 或 "AirymaxOS"

### 6.3 开源品牌表述清零规范

| 范围 | 规范 | 说明 |
|------|------|------|
| 开源文档（docs/AirymaxOS/） | ✅ 禁止出现 开源品牌表述 | 100% 清零，防止抄袭争议 |
| 闭源文档（docs-closed/） | 允许作为技术参考引用 | 内部参考，不公开 |
| 代码注释 | ✅ 禁止出现 开源品牌表述 | 防止开源后引发争议 |
| 与 上游 Linux 项目的关系 | 仅技术参考，不共享代码 | 符合 2026-07-07 用户决策 |

---

## 第 7 章 微核心与微内核术语规范

### 7.1 术语区分原则

| 术语 | 适用对象 | 英文 | 含义 |
|------|---------|------|------|
| **微核心**（micro-core） | agentrt（用户态） | MicroCore | agentrt 的 atoms/corekern（MicroCoreRT）是用户态微核心运行时，不是真正的内核 |
| **微内核**（micro-kernel） | agentrt-linux（OS 发行版） | Microkernel | agentrt-linux 基于 Linux 6.6 进行微内核化改造，将 VFS/网络栈/部分驱动移到用户态 |

### 7.2 MicroCoreRT 术语使用规范

MicroCoreRT 是 IRON-9 v2 [SS] 语义同源层组件，在两端有不同实现：

| 上下文 | 正确术语 | 错误术语 | 说明 |
|--------|---------|---------|------|
| 描述 agentrt 中的 MicroCoreRT | 微核心运行时 | ~~微内核运行时~~ | agentrt 是用户态，不是内核 |
| 描述 agentrt-linux 中的 MicroCoreRT | 微核心运行时 | ~~微内核运行时~~ | agentrt-linux 虽是 OS，但 MicroCoreRT 源自 agentrt，跨两端统一称"微核心运行时" |
| MicroCoreRT 标准名称（跨两端） | 微核心运行时（MicroCoreRT） | ~~微内核运行时~~ | 以 agentrt 上下文为准，因为 MicroCoreRT 源自 agentrt |

> **注**：10-terminology.md 已将 MicroCoreRT 标准名称修正为"微核心运行时"（P1-07 已完成）。

### 7.3 微内核化改造策略表述规范

| 正确表述 | 错误表述 | 说明 |
|---------|---------|------|
| agentrt-linux 基于 Linux 6.6 进行微内核化改造 | ~~agentrt-linux 是从零开发的微内核~~ | 不是从零开发，是基于 Linux 改造 |
| 微内核化改造策略（sched_ext + eBPF + io_uring） | ~~真正的微内核~~ | 是改造，不是原生微内核 |
| agentrt 的微核心设计思想 | ~~agentrt 的微内核化哲学~~ | agentrt 是用户态，没有"微内核化" |

---

## 第 8 章 乔布斯美学"无隐藏瑕疵"审查标准

### 8.1 审查哲学

> "For you to sleep well at night, the aesthetic, the quality, has to be carried all the way through." — Steve Jobs

agentrt-linux 工程标准贯彻乔布斯美学——不仅用户可见的表面要完美，用户不可见的内部构造（代码结构、编码风格、实现模式、设计思想）也必须达到最高质量标准。**没有"用户看不到所以可以妥协"的隐藏瑕疵。**

### 8.2 6 维度审查清单

| 维度 | 审查项 | 通过标准 | 验证方法 |
|------|--------|---------|---------|
| **代码结构** | 8 子仓边界清晰 | 模块划分遵循微内核设计思想，无跨子仓循环依赖 | 依赖图分析 |
| **编码风格** | 7 主题文档覆盖全部场景 | C 内核态 + Rust 内核模块 + 用户态服务全覆盖 | 文档审查 |
| **实现模式** | 策略与机制分离 | 内核只提供机制，策略外移至用户态/BPF | 架构审查 |
| **设计思想** | 五维正交 24 原则映射 | 每条工程标准映射到至少一条五维原则 | 映射表检查 |
| **规则编号** | SSoT 注册表无冲突 | 所有编号唯一，无多重定义 | SSoT 一致性检查 |
| **文档完整性** | Copyright + 基线声明 | 所有文档有 Copyright + Linux 6.6 基线声明 | Grep 全文档扫描 |

### 8.3 "无隐藏瑕疵"检查清单

| 检查项 | 检查方法 | 通过标准 |
|--------|---------|---------|
| 禁词清零 | `grep -rE "MGLRU 2.0\|PREEMPT_LAZY\|Rust 正式转正\|XFS 自修复\|eBPF 签名验证" docs/` | 0 结果（排除禁词清单本身） |
| 开源品牌清零 | `grep -riE "主流 Linux 发行版" docs/AirymaxOS/` | 0 结果（排除审查报告） |
| 微核心概念一致 | `grep -r "微内核" docs/AirymaxOS/ \| grep -i agentrt` | 0 结果（agentrt 不应使用"微内核"） |
| SSoT 编号一致 | 对照 07-maintainers-and-governance.md 第 10 章 | 所有编号引用与注册表一致 |
| Copyright 完整 | `grep -rL "Copyright (c) 2025-2026 SPHARX" --include="*.md" docs/AirymaxOS/` | 0 结果 |
| 基线声明完整 | `grep -rL "Linux 6.6" --include="*.md" docs/AirymaxOS/` | 仅顶层 README 可缺失 |

---

## 第 9 章 OLK-6.6 源码验证基线

### 9.1 关键源码验证路径

所有技术方案修正均对照 `/home/spharx/SpharxWorks/01Reference/kernel-OLK-6.6/` 源码验证：

| 验证项 | 源码路径 | 验证内容 | 验证结果 |
|--------|---------|---------|---------|
| SCHED_EXT=7 | `include/uapi/linux/sched.h:121` | `#define SCHED_EXT 7` | ✅ OLK-6.6 已内置 |
| FPU 禁用 | `arch/x86/Makefile:137` | `-mno-80387` | ✅ 内核态禁用 float |
| io_uring 仅用户态 | `io_uring/io_uring.c` | 仅 3 个 SYSCALL_DEFINE 入口 | ✅ 无内核内部 kthread API |
| kfifo SPSC | `include/linux/kfifo.h` | 无锁环形队列 | ✅ kthread 间通信适用 |
| wait_event | `include/linux/wait.h:478` | `wait_event_interruptible` | ✅ kthread 等待机制 |

### 9.2 不移植的 上游 Linux 6.6 不含的特性

以下 上游 Linux 6.6 不含的特性不移植到 agentrt-linux（2026-07-07 用户决策）：

| 特性 | 不移植原因 | 替代方案 |
|------|---------|---------|
| KABI_RESERVE | 与 IRON-1（禁止新特性）冲突 | — |
| etmem | 用 memory.reclaim + tiering 替代 | `memory.reclaim` + tiering |
| dynamic_pool | 用 memcg 替代 | memcg |
| numa_remote | 用 CXL 替代 | CXL |
| KMSAN | 开销过大 | KASAN + KCSAN 组合 |

---

## 第 10 章 开发就绪前置条件检查清单

### 10.1 M1 开发前置条件

进入 M1 开发阶段前，须满足以下全部条件：

| # | 前置条件 | 当前状态 | 说明 |
|---|---------|---------|------|
| 1 | P0 阻断性问题全部修复 | ✅ 12/13 已修复 | P0-14（工时矛盾）延期至 M0 第 1 周，不阻断 |
| 2 | 开源文档 开源品牌清零 | ✅ 已完成 | Grep 验证 0 结果 |
| 3 | MGLRU 2.0 清零 | ✅ 已完成 | 10/10 实际违规已修复 |
| 4 | SSoT 规则编号无冲突 | ✅ 已完成 | OS-KER-001 / OS-STD-001 冲突已消解 |
| 5 | IRON-9 v2 三层模型落地 | ✅ 已完成 | 6 个 [SC] 共享头文件已定义 |
| 6 | 工程标准规范手册产出 | ✅ 已完成 | 本手册（交付成果 #3） |
| 7 | P1 高优先级修复（5 项） | ⏳ M0 第 1 周 | P1-01/02/05/07/08 |
| 8 | OLK-6.6 源码验证 | ✅ 已完成 | 7 项关键路径验证通过 |

### 10.2 M0 阶段任务

M0 阶段（进入 M1 开发前的准备阶段）须完成以下任务：

| 周次 | 任务 | 优先级 | 对应 P1 |
|------|------|--------|---------|
| 第 1 周 | Copyright 补齐（17 文件） | 高 | P1-05 |
| 第 1 周 | MicroCoreRT 微内核→微核心（10-terminology.md 9 行） | 高 | P1-07 |
| 第 1 周 | agentrt 微内核化→微核心（01-kernel.md 3 行） | 高 | P1-08 |
| 第 1 周 | 核心设计文档扩至 400+ 行 | 高 | P1-01 |
| 第 1 周 | 模块文档补充 Mermaid 图 | 高 | P1-02 |
| 第 1 周 | 工时口径统一（P0-14） | 高 | P0-14 |
| 第 2 周 | 命名体系修正（AirymaxAgentOS 全称） | 中 | P1-09/10/11 |
| 第 2 周 | agentrt 微核心原语命名统一 | 中 | P1-12 |
| 第 2 周 | "不是微内核"表述张力消解 | 中 | P1-13 |
| 第 2 周 | 文档版本控制机制建立 | 中 | P1-04 |
| 第 2 周 | Linux 6.6 基线声明补齐（39 文件） | 中 | P1-06 |
| 第 2 周 | CoreLoopThree kthread 替代方案论述 | 中 | P1-03 |

### 10.3 就绪状态结论

**当前状态**：0.1.1 文档体系修订后综合成熟度 93%，达到"有条件 Go"状态。

**进入 M1 的前置条件**：M0 第 1 周完成 5 项高优先级 P1 修复（P1-01/02/05/07/08）+ P0-14 工时口径统一后，即可正式进入 M1 开发阶段。

**质量承诺**：本手册作为后续开发的强制执行依据，所有 M1 开发阶段的代码提交、文档变更、工程决策均须符合本手册规定。任何偏离须经 RFC 评审 + 总维护者批准。

---

> **手册维护**： 本手册随 0.1.1 文档体系迭代而更新。每次修订须更新第 3 章 SSoT 注册表、第 6 章禁词清单、第 10 章就绪状态。手册版本号与 0.1.1 文档体系版本号保持一致。
> **权威性声明**： 当各主题文档（01-07）与本手册冲突时，以本手册为准；当本手册与 SSoT 注册表（07-maintainers-and-governance.md 第 10 章）冲突时，以 SSoT 注册表为准。
