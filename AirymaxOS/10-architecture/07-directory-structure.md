Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.

SPDX-License-Identifier: CC-BY-NC-4.0

# agentrt-linux 系统性目录结构设计（v2.0）

> **文档定位**：agentrt-linux（AirymaxOS，极境智能体操作系统）源码目录结构设计 SSoT 文档\
> **文档版本**：v2.0（基于乔布斯/艾夫"简约就是美"哲学重新设计）\
> **最后更新**：2026-07-18\
> **状态**：正式生效\
> **取代**：v1.1（2026-07-18 上午）\
> **上级文档**：[agentrt-linux 设计文档](README.md)\
> **设计哲学根基**：乔布斯/艾夫"简约就是美" + seL4 Liedtke minimality principle\
> **铁律依据**：IRON-9 v3 四层模型（[SC]/[SS]/[IND]/[DSL]）+ IRON-1~5 工程铁律 + OS-IRON-013 8 子仓 submodule + OS-IRON-014 [SC] 单一数据源 + OS-IRON-015 编号管理元规则\
> **SSoT 依赖声明**：本文档涉及的所有规则编号（OS-IRON-001~015、OS-KER-xxx、OS-STD-xxx 等）的**唯一权威来源**为 [`50-engineering-standards/09-ssot-registry.md`](../50-engineering-standards/09-ssot-registry.md)。本文档不是规则编号 SSoT，仅作为目录结构设计的技术阐述载体。当本文档与 SSoT 注册表冲突时，以 SSoT 注册表为准。

---

## SSoT 权威源声明

> **本文档不是 SSoT**。本文档涉及的以下内容的权威定义已迁移至专门文档，本文档仅作为目录结构的技术阐述载体：
>
> | 权威内容 | 唯一权威源 | 关系 |
> |---------|---------|------|
> | IRON-9 v3 四层模型（[SC]/[SS]/[IND]/[DSL]）、10 个 [SC] 头文件清单、四层归属 | [`10-architecture/06-iron9-shared-model.md`](06-iron9-shared-model.md) | 本文 §6 镜像其定义 |
> | Airymax Unify Design 5 模块（A-UEF/A-ULP/A-UCS/A-ULS/A-IPC）架构定位、技术选型、[DSL] 降级策略 | [`10-architecture/10-unify-design.md`](10-unify-design.md) | 本文 §6/§9 镜像其定义 |
> | [SC] 三路类型桥接规范（`uapi_compat.h`） | [`50-engineering-standards/11-sc-header-type-bridging.md`](../50-engineering-standards/11-sc-header-type-bridging.md) | 本文 §6 引用 |
> | 跨项目代码共享（magic、双端 CI） | [`50-engineering-standards/120-cross-project-code-sharing.md`](../50-engineering-standards/120-cross-project-code-sharing.md) | 本文 §6/§10 引用 |
> | 全局规则编号（OS-IRON-013/014/015、OS-FMT-001、OS-STD-062 等） | [`50-engineering-standards/09-ssot-registry.md`](../50-engineering-standards/09-ssot-registry.md) | 本文引用其编号 |
>
> 当本文档与上述 SSoT 权威源冲突时，以权威源为准。

---

## §1 设计哲学根基

### 1.1 乔布斯/艾夫"简约就是美"原文

> "为什么我们认为简单就是美？因为就产品实体而言，我们必须获得掌控感。只要在复杂中建立秩序，就可以找到方法使产品听令于你。简约不仅仅是一种视觉风格或形式上的极简主义，也不仅仅是摒弃杂乱、去繁就简后的状态。简约是对复杂事物的精髓进行深度挖掘和精准提炼的结果。要做到真正的简约，就必须做到真正的深入。例如，如果为了达到看不到一颗螺丝的目标，结果却做出一个极为烦琐的产品，反倒违背了初心。更好的方法是深入'简约'的核心，了解与之有关的一切，了解简约的产品是如何生产出来的。只有深入了解一个产品的本质和精髓，才能真正去芜存菁。"
>
> —— 史蒂夫·乔布斯

### 1.2 五条工程哲学落地原则

将乔布斯/艾夫的哲学原文落地为 agentrt-linux 目录设计的 5 条工程原则：

| # | 工程哲学原则 | 哲学原文映射 | agentrt-linux 目录设计落地 |
|---|------------|------------|------------------------|
| P1 | **产品中看不见的部分也应该像其外表一样精美** | "了解简约的产品是如何生产出来的" | 7 个桩子仓必须提供最小可编译骨架，禁止纯桩；[SC] 10 头文件必须全部物理落地 |
| P2 | **简单就是美——深入后做对，而非少做** | "简约是对复杂事物的精髓进行深度挖掘和精准提炼的结果" | 每个子仓/目录的边界必须经过"本质挖掘"后才确定；不为表面简约而合并不同本质的概念 |
| P3 | **反对为形式简约而导致的复杂实现** | "为了达到看不到一颗螺丝的目标，结果却做出一个极为烦琐的产品" | 不为减少子仓数量而强行合并不同本质的子仓；不引入符号链接等"隐藏复杂性"机制 |
| P4 | **反对过度抽象、过度设计** | "去芜存菁" | 每个新增抽象必须证明其必要性，否则删除；8 子仓数量恒定，不随意增减 |
| P5 | **深入了解产品本质以实现真正简约** | "只有深入了解一个产品的本质和精髓，才能真正去芜存菁" | daemons 命名统一为 `_d` 后缀（深入理解守护进程本质后的极简命名）；include path 采用 CMake -I 直引用（最直接的引用方式） |

### 1.3 与 IRON-9 v3 四层模型的对应关系

乔布斯/艾夫"简约就是美"哲学与 IRON-9 v3 四层模型存在"东西方简约哲学"的双重根基对应：

| IRON-9 v3 层次 | 哲学对应 | 落地体现 |
|--------------|---------|---------|
| **[SC] 共享契约层** | "对复杂事物的精髓进行深度挖掘和精准提炼" | 10 个头文件是从全量代码中提炼的精髓，单一物理宿主，禁止物理副本 |
| **[SS] 语义同源层** | "在复杂中建立秩序" | agentrt（应用层）与 agentrt-linux（OS 层）的 API 语义同源，在差异中建立秩序 |
| **[IND] 完全独立层** | "深入了解产品的本质" | 内核态与用户态实现差异是产品本质决定的，不强求统一 |
| **[DSL] 降级生存层** | "产品中看不见的部分也应精美" | 即使 [SC] 损坏，[DSL] 降级块仍提供精美的最小可运行子集，是"看不见的精美" |

### 1.4 与 seL4 Liedtke minimality 的双重根基

| 哲学源 | 视角 | 简约论证方式 |
|--------|------|------------|
| 乔布斯/艾夫 | 人文体验 | 从用户/开发者体验角度论证简约美 |
| Liedtke | 形式化验证 | 从微内核最小性原理论证简约性 |

二者在 agentrt-linux 中形成"东西方简约哲学"的双重根基：Liedtke 从形式化角度论证最小化，乔布斯从人文体验角度论证简约美。

---

## §2 v1.1 问题审视与设计决策

### 2.1 P0 级问题清单

基于前期审查，v1.1 存在以下 P0 级问题必须在 v2.0 中解决：

| # | P0 问题 | 违背原则 | 严重性 |
|---|---------|---------|--------|
| P0-1 | 7/8 子仓为纯桩（仅含 LICENSE/NOTICE/README） | IRON-2 + 哲学 P1 | 高 |
| P0-2 | 8/10 [SC] 头文件缺失（仅 error.h + log_types.h 落地） | OS-IRON-014 | 高 |
| P0-3 | services 子仓职责过宽（VFS/Net/Drivers/daemons/systemd/IPC 六大块） | 单一职责原则 | 中 |
| P0-4 | cloudnative 与 system 边界模糊 | 边界清晰原则 | 中 |
| P0-5 | daemons 命名不一致（`_superv` / `_daemon` / `_d` 三种风格） | 哲学 P5 + E-5 命名语义化 | 中 |
| P0-6 | daemons 命名在 agentrt 与 agentrt-linux 间重叠仅 16.7% | [SS] 同源性 | 中 |
| P0-7 | [SC] 头文件物理宿主与 [IND] 子仓引用关系未明确 | OS-IRON-014 落地 | 高 |

### 2.2 5 个设计决策（A/B/C/D/E）

针对上述 P0 问题，基于"简约就是美"哲学做出 5 个明确设计决策：

#### 决策 A：8 子仓结构（A1 保持不变 + 边界强化）

**决策**：**A1——保持 8 子仓结构不变**，通过"本质挖掘"强化 cloudnative 与 system 的边界定义。

**可选方案对比**：

| 方案 | 子仓数 | 哲学评估 | 决策 |
|------|--------|---------|------|
| A1：保持 8 子仓不变 | 8 | 深入理解 cloudnative（K8s/容器运行时）与 system（systemd/RPM/init）本质不同，不合并 | ✅ 采纳 |
| A2：合并 cloudnative + system | 7 | 为减少数量而合并不同本质的概念，违背 P2；破坏 OS-IRON-013 | ❌ 否决 |
| A3：拆分 services/ipc 为顶层 ipc/ | 9 | 增加子仓数量，违背 P4；ipc 是 services 内部关注点 | ❌ 否决 |
| A4：A2 + A3 组合 | 8 | 形式简约但实现复杂，违背 P3 | ❌ 否决 |

**哲学依据**：乔布斯强调"简约是对复杂事物的精髓进行深度挖掘和精准提炼的结果"。cloudnative 的本质是"云原生工作负载编排"（K8s CRD + containerd shim + CNI），system 的本质是"本地系统基底"（systemd + init + RPM + dnf + DevStation）。二者服务对象、生命周期、技术栈均不同——合并会模糊本质，而非提炼精髓。真正的简约是**清晰界定边界**，而非**强行减少数量**。

**边界强化方案**：

| 子仓 | 本质定义 | 不负责 | 边界声明 |
|------|---------|--------|---------|
| cloudnative | 云原生工作负载编排（K8s/containerd/OCI） | 本地系统初始化、包管理 | 仅负责 K8s 生态集成，不负责本地系统启动 |
| system | 本地系统基底（systemd/init/RPM/dnf） | K8s 工作负载编排 | 仅负责本地系统启动与包管理，不负责云原生编排 |

#### 决策 B：services 内部分层（B2 不拆分）

**决策**：**B2——services 子仓内部按"用户态子系统"分层，不拆分为多个子仓**。

**可选方案对比**：

| 方案 | 子仓数 | 哲学评估 | 决策 |
|------|--------|---------|------|
| B1：services 不变 | 8 | 内部混乱，违背单一职责 | ❌ 否决 |
| B2：services 内部分层 | 8 | 深入理解 services 本质（用户态系统服务集合）后内部组织，不破坏 8 子仓 | ✅ 采纳 |
| B3：拆分 services + userland | 9 | 增加子仓数量，违背 P4；userland 是 services 内部关注点 | ❌ 否决 |

**哲学依据**：乔布斯强调"深入了解产品的本质"。services 的本质是"agentrt-linux 用户态系统服务的统一物理宿主"——VFS/Net/Drivers/daemons/systemd/IPC 六大块本质上都是"用户态系统服务"，只是服务对象不同。拆分会制造人为边界（形式简约），内部分层则实现真正的清晰（本质简约）。

**services 内部分层方案**：

```
services/
├── daemons/          # 12 daemons（A-ULS/A-ULP/A-UCS 等守护进程）
├── systemd/          # systemd 集成（unit/target/generator）
└── userland/         # 用户态子系统实现
    ├── vfs/          # 用户态 VFS（FUSE 模型）
    ├── net/          # 用户态网络栈（DPDK/AF_XDP）
    ├── drivers/      # 用户态驱动框架（VFIO/libvfio）
    └── ipc/          # 用户态 IPC 库（io_uring IPC 封装）
```

#### 决策 C：daemons 命名统一为 `_d` 后缀（C1）

**决策**：**C1——所有 12 daemons 统一为 `_d` 后缀**，符合 Jobs「简约」原则。

**可选方案对比**：

| 方案 | 命名风格 | 哲学评估 | 决策 |
|------|---------|---------|------|
| C1：统一 `_d` 后缀 | macro_d, logger_d, config_d, ... | 最简约，最一致，符合 E-5 命名语义化 | ✅ 采纳 |
| C2：统一 `_daemon` 后缀 | macro_daemon, logger_daemon, ... | 冗长，违背简约 | ❌ 否决 |
| C3：保留语义化命名 | macro_superv, logger_daemon, ... | 不一致，违背 E-5 | ❌ 否决 |

**哲学依据**：乔布斯强调"用最少的接口提供最大的价值"。`_d` 后缀已足够表达"daemon"语义，`_daemon` 是冗余；`_superv` 是缩写不一致。统一为 `_d` 是深入理解守护进程本质后的极简命名。

**重命名清单**：

| v1.1 旧名 | v2.0 新名 | 语义保留 |
|----------|---------|---------|
| macro_superv | **macro_d** | 保留 Macro-Supervisor 语义（macro 前缀） |
| logger_daemon | **logger_d** | 保留 Logger 语义 |
| config_daemon | **config_d** | 保留 Config 语义 |
| gateway_d | gateway_d | 不变 |
| sched_d | sched_d | 不变 |
| vfs_d | vfs_d | 不变 |
| net_d | net_d | 不变 |
| mem_d | mem_d | 不变 |
| cogn_d | cogn_d | 不变 |
| sec_d | sec_d | 不变 |
| audit_d | audit_d | 不变 |
| dev_d | dev_d | 不变 |

#### 决策 D：[SS] 同源仅限概念 + 显式映射表（D1）

**决策**：**D1——[SS] 同源仅限"12 daemons 数量与守护进程概念"，命名不强制一致，提供显式映射表**。

**可选方案对比**：

| 方案 | 同源策略 | 哲学评估 | 决策 |
|------|---------|---------|------|
| D1：概念同源 + 显式映射表 | 数量与概念同源，命名各自演进 | 深入理解两端视角差异，提供映射 | ✅ 采纳 |
| D2：统一两侧命名 | 强制一致 | 破坏一侧稳定性，违背 P3 | ❌ 否决 |
| D3：保留差异，不声明同源 | 无映射 | 失去 [SS] 语义追溯 | ❌ 否决 |

**哲学依据**：乔布斯强调"深入了解产品的本质"。agentrt（应用层 Agent Runtime）与 agentrt-linux（OS 系统服务）的 daemons 反映不同视角：agentrt 关注 Agent 应用层能力（channel/hook/info/llm/market/monit/notify/observe/plugin/tool），agentrt-linux 关注 OS 系统服务能力（macro/logger/config/gateway/sched/vfs/net/mem/cogn/sec/audit/dev）。命名差异是产品本质决定的，强行统一会掩盖差异。真正的简约是**承认差异并提供清晰映射**。

**[SS] 同源映射表**（详见 §10.2）：

| 同源维度 | agentrt（12 daemons） | agentrt-linux（12 daemons） | 同源程度 |
|---------|---------------------|---------------------------|---------|
| 数量 | 12 | 12 | 完全同源 |
| 命名后缀 | `_d` | `_d` | 完全同源（C1 决策后） |
| 守护进程概念 | 用户态服务进程 | 用户态服务进程 | 完全同源 |
| 具体命名 | channel_d, hook_d, ... | macro_d, logger_d, ... | 概念同源，命名各自演进 |

#### 决策 E：[SC] include path 采用 CMake -I 直引用（E1）

**决策**：**E1——各子仓构建系统配置 `-I../kernel/include` 直引用 kernel 子仓的 [SC] 物理宿主**。

**可选方案对比**：

| 方案 | 引用方式 | 哲学评估 | 决策 |
|------|---------|---------|------|
| E1：CMake/Kbuild -I 直引用 | `ccflags-y += -I$(srctree)/../kernel/include` | 最直接，最简洁，"看不见的部分精美" | ✅ 采纳 |
| E2：环境变量 AIRY_SC_INCLUDE | `$AIRY_SC_INCLUDE` | 引入环境依赖，违背 P3 | ❌ 否决 |
| E3：符号链接 | `services/include/airymax -> ../../kernel/include/airymax` | 隐藏复杂性，违背 P3；符号链接易断裂 | ❌ 否决 |

**哲学依据**：乔布斯强调"产品中看不见的部分也应该像其外表一样精美"。CMake/Kbuild 的 `-I` 配置是构建系统的"看不见的部分"，应该直接、清晰、可追溯。环境变量引入隐式依赖（不可见但不精美），符号链接引入隐藏复杂性（看似简约实则烦琐）。`-I` 直引用是最直接、最可追溯、最精美的方式。

### 2.3 决策汇总表

| 决策 | 选项 | 哲学依据 | 解决的 P0 问题 |
|------|------|---------|--------------|
| A | A1（保持 8 子仓 + 边界强化） | 深入本质后清晰界定边界 | P0-4 |
| B | B2（services 内部分层） | 深入本质后内部组织 | P0-3 |
| C | C1（统一 `_d` 后缀） | 极简命名 | P0-5 |
| D | D1（概念同源 + 映射表） | 承认差异并提供映射 | P0-6 |
| E | E1（CMake -I 直引用） | 看不见的部分精美 | P0-7 |

---

## §3 6 条目录设计原则

agentrt-linux 源码目录结构遵循 6 大设计原则，每条原则都有"简约就是美"哲学阐释与源码级标杆依据：

### 原则 1：单一物理宿主（[SC] 头文件唯一）

| 维度 | 内容 |
|------|------|
| **哲学阐释** | "对复杂事物的精髓进行深度挖掘和精准提炼的结果"——10 个 [SC] 头文件是从全量代码中提炼的精髓，必须有且仅有一个物理宿主 |
| **规则编号** | OS-IRON-014 |
| **标杆依据** | Linux 6.6 `include/uapi/linux/` 单一物理宿主 |
| **落地** | `kernel/include/airymax/` 是 10 个 [SC] 头文件的唯一物理宿主；其他子仓通过 `-I../kernel/include` 引用，**禁止物理副本** |
| **CI 校验** | `ls include/airymax/*.h \| wc -l == 10`；`sc-dual-ci.yml` 逐字节校验 |

### 原则 2：Model A 完整 fork（直接写入上游目录）

| 维度 | 内容 |
|------|------|
| **哲学阐释** | "深入了解产品的本质"——Linux 6.6 内核的本质是"完整源码树"，直接写入上游目录是对该本质的尊重；patches/ 隔离层是"为形式简约而导致的复杂实现" |
| **规则编号** | OS-IRON-013（kernel 子仓采用 Model A） |
| **标杆依据** | Linux 6.6 主流发行版 fork 实践（直接写入 `mm/dynamic_pool.c` 等） |
| **落地** | kernel/ 子仓含完整 Linux 6.6 源码树（~60K 文件 1.6GB）；Airymax 修改直接写入上游目录（如 `mm/airymax_mm.c`、`kernel/sched/airy_sched.c`）；**不使用 patches/ 隔离层** |

### 原则 3：8 子仓 submodule（OS-IRON-013）

| 维度 | 内容 |
|------|------|
| **哲学阐释** | "去芜存菁"——8 子仓是按能力域去芜存菁的结果，每个子仓有且仅有一个清晰职责；不随意增减（P4） |
| **规则编号** | OS-IRON-013 |
| **标杆依据** | seL4 `src/{api,kernel,object,fastpath,arch,machine,plat}` 分层 + Linux 6.6 24 顶层目录 |
| **落地** | 8 个独立 git 仓库（leaf 仓）+ 1 个管理仓（agentrt-linux），通过 `.gitmodules` + submodule 统一管理 |
| **8 子仓清单** | kernel / services / security / memory / cognition / cloudnative / system / tests-linux |

### 原则 4：用户态子系统分层（services 内部分层）

| 维度 | 内容 |
|------|------|
| **哲学阐释** | "深入理解产品的本质"——services 的本质是"用户态系统服务集合"，内部分层是对该本质的精准组织，而非人为拆分 |
| **规则编号** | 决策 B2 |
| **标杆依据** | Linux 6.6 `kernel/` 内部 22 子目录分层组织 |
| **落地** | services/ 内部三层：`daemons/`（12 daemons）+ `systemd/`（systemd 集成）+ `userland/`（VFS + Net + Drivers + IPC） |

### 原则 5：daemons 命名统一（`_d` 后缀）

| 维度 | 内容 |
|------|------|
| **哲学阐释** | "用最少的接口提供最大的价值"——`_d` 后缀已足够表达"daemon"语义，是最简约的命名 |
| **规则编号** | 决策 C1 + E-5 命名语义化 |
| **标杆依据** | Linux 6.6 `systemd`/`auditd`/`rsyslogd` 守护进程命名惯例 |
| **落地** | 12 daemons 统一 `<name>_d` 后缀：macro_d / logger_d / config_d / gateway_d / sched_d / vfs_d / net_d / mem_d / cogn_d / sec_d / audit_d / dev_d |

### 原则 6：[SC] include path 策略（CMake -I 直引用）

| 维度 | 内容 |
|------|------|
| **哲学阐释** | "看不见的部分也应精美"——CMake/Kbuild 的 `-I` 配置是构建系统的"看不见的部分"，应该直接、清晰、可追溯 |
| **规则编号** | 决策 E1 |
| **标杆依据** | Linux 6.6 `ccflags-y += -I$(srctree)/include` 直引用惯例 |
| **落地** | 各子仓 Makefile/Kbuild/Meson 配置 `-I$(srctree)/../kernel/include` 或 `include_directories('../../kernel/include')`；**禁止环境变量**、**禁止符号链接** |

---

## §4 8 子仓职责重新定义

### 4.1 8 子仓职责矩阵

基于决策 A（A1 保持 8 子仓 + 边界强化），重新定义 8 子仓的职责矩阵：

| # | 子仓 | 中文 | 职责 | 依赖 | 内聚度 | 耦合度 |
|---|------|------|------|------|--------|--------|
| 1 | **kernel** | 极境内核 | Linux 6.6 完整 fork + Airymax 微核心原语 + [SC] 物理宿主 + Micro-Supervisor + sched_tac + io_uring IPC + 纯 C LSM | 硬件 | 高（内核态单一） | 低（仅向上提供 syscall） |
| 2 | **services** | 极境服务 | 用户态系统服务集合（12 daemons + systemd + userland 子系统） | kernel | 高（用户态服务单一） | 中（与 kernel 通过 syscall） |
| 3 | **security** | 极境安全 | capability 系统 + 纯 C LSM 策略 + Cupolas 机密计算 + 国密 | kernel | 高（安全单一） | 中（与 kernel 通过 LSM 钩子） |
| 4 | **memory** | 极境记忆 | MemoryRovol L1-L4 + CXL + PMEM + MGLRU + 持久化 | kernel | 高（记忆单一） | 中（与 kernel mm/ 协作） |
| 5 | **cognition** | 极境认知 | CoreLoopThree kthread + Thinkdual + LLM 调度 + Wasm 3.0 沙箱 | kernel, services, security, memory | 高（认知单一） | 高（多子仓协作） |
| 6 | **cloudnative** | 极境云原生 | K8s CRD + containerd-shim + CNI + 超节点 OS + SDK | kernel, services, cognition | 高（云原生单一） | 中（与 cognition 通过 CRD） |
| 7 | **system** | 极境系统 | systemd 适配 + init + RPM + dnf + DevStation + A-UCS 配置入口 | kernel, services, cloudnative | 高（系统基底单一） | 中（与 cloudnative 通过打包） |
| 8 | **tests-linux** | 极境测试 | 单元 + 集成 + 形式化 + Soak + 混沌 + 基准 + 纯 C LSM 验证 | 全部 7 子仓 | 高（测试单一） | 低（仅观察不修改） |

### 4.2 子仓间依赖图（Mermaid）

```mermaid
graph TB
    KERNEL[kernel<br/>极境内核<br/>[SC] 物理宿主 + Micro-Supervisor]
    SERVICES[services<br/>极境服务<br/>12 daemons + userland]
    SECURITY[security<br/>极境安全<br/>纯 C LSM + capability]
    MEMORY[memory<br/>极境记忆<br/>MemoryRovol + CXL]
    COGNITION[cognition<br/>极境认知<br/>CoreLoopThree + Wasm]
    CLOUD[cloudnative<br/>极境云原生<br/>K8s + containerd]
    SYSTEM[system<br/>极境系统<br/>systemd + RPM + dnf]
    TESTS[tests-linux<br/>极境测试<br/>单元 + 集成 + 形式化]

    KERNEL --> SERVICES
    KERNEL --> SECURITY
    KERNEL --> MEMORY
    KERNEL --> COGNITION
    SERVICES --> COGNITION
    SERVICES --> CLOUD
    SECURITY --> COGNITION
    MEMORY --> COGNITION
    COGNITION --> CLOUD
    SERVICES --> SYSTEM
    CLOUD --> SYSTEM

    KERNEL -.-> TESTS
    SERVICES -.-> TESTS
    SECURITY -.-> TESTS
    MEMORY -.-> TESTS
    COGNITION -.-> TESTS
    CLOUD -.-> TESTS
    SYSTEM -.-> TESTS

    style KERNEL fill:#e1f5fe,stroke:#0288d1,stroke-width:2px
    style SERVICES fill:#f1f8e9,stroke:#7cb342,stroke-width:2px
    style SECURITY fill:#ffebee,stroke:#d32f2f,stroke-width:2px
    style MEMORY fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    style COGNITION fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    style CLOUD fill:#e0f7fa,stroke:#00838f,stroke-width:2px
    style SYSTEM fill:#eceff1,stroke:#455a64,stroke-width:2px
    style TESTS fill:#fafafa,stroke:#616161,stroke-width:2px
```

**层次纪律**（S-2）：

1. 每层只依赖其直接下层的抽象接口，禁止越级访问
2. 同层之间通过 IPC 通信（基于 IORING_OP_URING_CMD），禁止直接函数调用
3. 层次之间的接口契约通过 `30-interfaces/` 文档定义
4. 任何新增跨层依赖必须通过 ADR 评审

### 4.3 决策记录

#### 4.3.1 为何保持 8 子仓（决策 A）

**决策**：保持 OS-IRON-013 定义的 8 子仓结构不变。

**理由**：
1. **尊重既有决策**：OS-IRON-013 是经过深度评审的 SSoT 决策，8 子仓按能力域清晰划分，无功能重叠
2. **哲学依据**：乔布斯强调"去芜存菁"——8 子仓是去芜存菁的结果，不随意增减（P4）
3. **避免形式简约**：合并 cloudnative + system 会模糊"云原生编排"与"本地系统基底"的本质差异，是"为形式简约而导致的复杂实现"（P3）
4. **工程稳定性**：8 子仓结构已在 v1.0/v1.1 中落地，频繁变更违背 IRON-1（禁止新特性）

#### 4.3.2 为何不拆分 services（决策 B）

**决策**：services 子仓内部按"用户态子系统"分层，不拆分为多个子仓。

**理由**：
1. **本质统一**：services 的本质是"agentrt-linux 用户态系统服务的统一物理宿主"——VFS/Net/Drivers/daemons/systemd/IPC 六大块本质上都是"用户态系统服务"
2. **避免人为边界**：拆分会制造人为边界（形式简约），内部分层则实现真正的清晰（本质简约）
3. **尊重 OS-IRON-013**：保持 8 子仓总数不变
4. **参考 Linux 6.6**：Linux 6.6 `kernel/` 内部有 22 个子目录分层组织，但不拆分为多个仓库

#### 4.3.3 为何统一 `_d` 后缀（决策 C）

**决策**：12 daemons 统一为 `_d` 后缀。

**理由**：
1. **极简命名**：`_d` 已足够表达"daemon"语义，`_daemon` 冗余，`_superv` 不一致
2. **E-5 命名语义化**：统一后缀便于自动化识别（`ls *_d` 列出所有 daemons）
3. **与 agentrt 同源**：agentrt 侧 daemons 已统一 `_d` 后缀，agentrt-linux 跟随

---

## §5 完整目录树（文件级）

### 5.1 整体目录树（管理仓 + 8 子仓）

```
agentrt-linux/                    # 管理仓（main 分支）
├── README.md                     # 英文 README（8 子仓矩阵 + 架构层次图）
├── README_zh.md                  # 中文 README
├── LICENSE                       # AGPL-3.0 + Apache-2.0 双许可证
├── NOTICE                        # 版权、商标、第三方声明
├── CONTRIBUTING.md               # 贡献指南
├── .gitmodules                   # 8 子模块定义（feature/official-hubs-01 分支）
├── .gitignore
│
├── kernel/                       # [submodule] 极境内核（Model A 完整 fork）
├── services/                     # [submodule] 极境服务（12 daemons + userland）
├── security/                     # [submodule] 极境安全（纯 C LSM + capability）
├── memory/                       # [submodule] 极境记忆（MemoryRovol + CXL）
├── cognition/                    # [submodule] 极境认知（CoreLoopThree + Wasm）
├── cloudnative/                  # [submodule] 极境云原生（K8s + containerd）
├── system/                       # [submodule] 极境系统（systemd + RPM + dnf）
├── tests-linux/                  # [submodule] 极境测试（单元 + 集成 + 形式化）
│
├── tools/                        # 跨子仓工具聚合
│   ├── checkpatch-airymax.sh     # 引用 kernel/scripts/checkpatch.pl 的聚合入口
│   ├── build-all.sh              # 跨 8 子仓统一构建脚本
│   ├── sc-sync-check.sh          # [SC] 双端同步校验聚合入口
│   └── README.md                 # 工具索引（各子仓 scripts/ 清单）
│
└── docs/                         # 软链接/引用 → umbrella docs/AirymaxOS/
                                  # （设计文档维护在 umbrella 仓库，管理仓仅引用）
```

### 5.2 8 子仓公共骨架

每个子仓根目录必须包含以下公共文件（双许可证 + 治理 + 元信息）：

```
<submodule>/
├── README.md              # 英文 README
├── README_zh.md           # 中文 README
├── LICENSE                # AGPL-3.0 + Apache-2.0 双许可证全文
├── NOTICE                 # 版权、商标、第三方声明
├── MAINTAINERS            # 维护者制度（R-03 落地，ES-OLK-8）
├── .gitignore
├── .clang-format          # C 代码格式（OS-FMT-001 Tab 8，仅 kernel/security/memory/cognition）
├── .editorconfig          # 编辑器统一配置
├── CONTRIBUTING.md        # 贡献指南（DCO + Signed-off-by + 审查流程）
└── Documentation/         # 子仓专属文档（ES-OLK-13 文档即代码）
```

**含 Rust 代码的子仓**（cognition + cloudnative）必须额外包含 `rustfmt.toml`（OS-FMT-001 4 空格）。

### 5.3 kernel/ 子仓完整目录树（Model A 完整 fork）

```
kernel/                           # kernel 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .clang-format / .editorconfig
│
├── Kbuild                        # 顶层 Kbuild（递归构建入口）
├── Kconfig                       # 顶层 Kconfig（特性配置入口）
├── Makefile                      # 顶层 Makefile（构建规则）
├── Makefile.airymaxos            # AirymaxOS 版本四元组定义
│                                 # 定义：AIRYMAX_LTS / AIRYMAX_MAJOR / AIRYMAX_MINOR / AIRYMAX_RELEASE
├── airy_core.c                   # [ES-SEL4-1] 最小内核模块入口（seL4 kernel_all.c 思想）
│                                 # 聚合 corekern/ + superv/ + log/ + ipc/ + config/ 的构建单元
│                                 # 定义 airy_init() / airy_exit()，由 init/main.c 调用
│
├── arch/                         # 架构相关代码（ES-OLK-2 + ES-SEL4-7）
│   ├── x86/                      # x86/x86_64 架构
│   │   ├── Kbuild
│   │   ├── Kconfig
│   │   ├── configs/              # x86 defconfig
│   │   │   └── airymax_x86_64_defconfig
│   │   ├── include/              # x86 架构相关头文件
│   │   ├── kernel/               # x86 内核执行逻辑
│   │   ├── mm/                   # x86 内存管理
│   │   ├── entry/                # x86 系统调用/中断入口
│   │   ├── machine/              # x86 架构相关机器原语
│   │   │   ├── capdl.c           # capability 分布描述（架构特定版）
│   │   │   ├── fpu.c             # 浮点单元
│   │   │   ├── registerset.c     # 寄存器集
│   │   │   └── hardware.c        # 硬件探测
│   │   ├── lib/                  # x86 特定库函数
│   │   └── boot/                 # x86 启动引导
│   ├── arm64/                    # ARM64 架构（结构同 x86/）
│   └── riscv/                    # RISC-V 架构（结构同 x86/）
│
├── include/                      # 公共头文件（ES-OLK-3 / ES-OLK-6）
│   ├── uapi/                     # 用户空间 ABI（永久稳定，OS-IRON-001）
│   │   └── airymax/
│   │       ├── syscall.h         # 系统调用号定义（冻结）
│   │       ├── syscall.xml       # 系统调用契约源定义（R-01）
│   │       ├── ipc.h             # IPC UAPI（128B 消息头用户可见部分）
│   │       ├── sched.h           # 调度 UAPI
│   │       ├── memory.h          # 内存 UAPI
│   │       ├── security.h        # 安全 UAPI
│   │       └── cognition.h       # 认知 UAPI
│   ├── airymax/                  # [SC] 共享契约层唯一物理宿主（OS-IRON-014，详见 §6）
│   │                             # 10 个 [SC] 头文件 + 2 个补充共享文件
│   │                             # 每个头文件底部均含 [DSL] #ifdef AIRY_SC_FALLBACK 降级块
│   ├── asm-generic/              # 通用汇编默认实现（ES-OLK-2）
│   └── kernel/                   # [SS] 语义同源层内核内部头（airy_*.h）
│
├── kernel/                       # 上游内核核心 [模型 A 完整继承上游]
│                                 # 含 22 个子目录：bpf/cgroup/debug/dma/entry/events/
│                                 # futex/gcov/irq/kcsan/livepatch/locking/module/pgo/
│                                 # power/printk/rcu/sched/time/trace 等
│                                 # Airymax 增强直接写入对应子目录（如 kernel/sched/airymax_sched.c）
│                                 # 调度采用 sched_tac（SCHED_DEADLINE/SCHED_FIFO/EEVDF，不引入 sched_ext）
│
├── corekern/                     # Airymax 微核心抽象层 [ES-SEL4-1 极简原则]
│                                 # 定位：Airymax 专属原语组织层，不替代上游 kernel/
│   ├── Kbuild
│   ├── Kconfig
│   ├── api/                      # 系统调用分发
│   │   ├── syscall_dispatch.c
│   │   └── faults.c
│   ├── sched/                    # 调度原语（sched_tac 用户态调度器）
│   │   ├── airymax_agent_sched.c # stc_agent 策略（sched_tac 落地）
│   │   ├── core_sched.c
│   │   └── ext.c
│   ├── ipc/                      # IPC 原语（io_uring + IORING_OP_URING_CMD 用户态-内核态 + kfifo kthread 间）
│   │   ├── io_uring_ipc.c
│   │   ├── kfifo_ipc.c
│   │   └── fastpath.c            # IPC fastpath 优化（ES-SEL4-4 借鉴）
│   ├── taskflow/                 # 任务流原语
│   │   ├── graph_engine.c
│   │   └── task_descriptor.c     # 任务描述符（magic 0x41475453）
│   ├── memory/                   # 内存原语接口（实现移入 memory 子仓）
│   ├── time/                     # 时间原语
│   ├── object/                   # 内核对象（cnode/tcb/endpoint/notification）
│   │   ├── cte.c                 # CTE（Capability Table Entry）管理
│   │   ├── cspace.c              # CSpace 树形寻址（guard + radix）
│   │   ├── mdb.c                 # MDB 派生树维护
│   │   ├── cnode_ops.c           # 7 种 CNode 操作（Copy/Mint/Move/Mutate/Revoke/Delete/Rotate）
│   │   ├── endpoint.c            # IPC Endpoint 状态机（Idle/Send/Recv）
│   │   ├── notification.c        # 异步通知（bitwise OR badge）
│   │   ├── tcb.c                 # TCB（Thread Control Block）扩展
│   │   └── untyped.c             # Untyped-Retype 内存模型
│   ├── locking/                  # 锁机制与同步原语
│   ├── irq/                      # 中断处理
│   └── bpf/                      # eBPF 可编程内核（kfunc + dynamic pointer + struct_ops）
│       └── struct_ops/
│           └── airymax_sched_ops.c
│
├── superv/                       # [A-ULS] Micro-Supervisor 内核冷酷执法层
│                                 # 纯 C LSM 模块（不使用 BPF LSM，对齐 openEuler）
│   ├── Kbuild
│   ├── Kconfig
│   ├── airy_superv_lsm.c         # DEFINE_LSM(airy) 注册（关联 [SC] lsm_types.h）
│   ├── airy_cap_check.c          # 纯 C LSM 钩子，Capability 检测
│   ├── airy_ipc_freeze.c         # IPC 队列冻结（ring->frozen）
│   ├── airy_die_notify.c         # die_notifier 内核崩溃通知链
│   ├── airy_eventfd.c            # eventfd 向 Macro-Supervisor 通知
│   └── airy_sched.c              # [A-ULS] sched_tac 调度策略实现
│
├── log/                          # [A-ULP] 统一日志与打印系统（内核侧）
│   ├── Kbuild
│   ├── Kconfig
│   ├── airy_log_ring.c           # Ring Buffer 内存分配（alloc_pages + mmap，非 DMA 一致性内存）
│   ├── airy_printk_bridge.c      # printk → Ring Buffer 桥接
│   └── airy_log_commit.c         # 128B 记录 reserve/memcpy/commit fastpath
│
├── ipc/                          # [A-IPC] 统一进程间通信体系（内核侧）
│   ├── Kbuild
│   ├── Kconfig
│   ├── airy_uring_cmd.c          # IORING_OP_URING_CMD 回调入口（不使用 page flipping）
│   ├── airy_ipc_ring.c           # Ring 生命周期与控制面解耦
│   ├── airy_ipc_cap.c            # Capability 离线缓存（radix tree）
│   ├── airy_ipc_reconcile.c      # Reconciliation log（数据面自治）
│   ├── airy_ipc_fastpath.c       # fastpath 冻结检查（unlikely(ring->frozen)）
│   └── airy_uring_security.c     # io_uring_disabled 控制 + opcode 白名单 + 纯 C LSM 双重校验
│
├── config/                       # [A-UCS] 统一配置管理体系（内核侧）
│   ├── Kbuild
│   ├── Kconfig                   # 编译时配置
│   ├── airy_sysctl.c             # sysctl 注册与 RCU 热重载（关联 [SS] 语义同源配置）
│   └── airy_config.c             # 配置版本号校验（AIRY_CONFIG_VERSION）
│
├── configs/                      # AirymaxOS 内核配置（跨架构聚合）
│   ├── airy_defconfig            # AirymaxOS 默认 defconfig（含 CONFIG_AIRY_SC_FALLBACK 等）
│   ├── airy_defconfig.base       # 跨架构共享基线
│   └── README.md                 # 配置矩阵说明
│
├── init/                         # 内核初始化（main.c + do_initcalls.c + version.c）
├── lib/                          # 通用库函数（string.c / ctype.c / bitmap.c / radix-tree.c）
├── machine/                      # 架构无关通用机器原语层（capdl/fpu/io/registerset/profiler）
│
├── crypto/                       # 加密算法库（含 sm2-sm4/ 国密 + aes/ + sha/）
├── io_uring/                     # 异步 I/O（ES-OLK-1 独立子系统，[A-IPC] 数据面载体）
│
├── fs/                           # 文件系统层 [模型 A 完整继承上游]
├── net/                          # 网络协议栈 [模型 A 完整继承上游]
├── block/                        # 块设备层 [模型 A 完整继承上游]
├── drivers/                      # 设备驱动 [模型 A 完整继承上游]
│
├── mm/                           # 核心内存管理 [模型 A 完整继承上游]
│                                 # 含 20+ 扁平 .c 文件（backing-dev.c/compaction.c/cma.c 等）+ damon/
│                                 # Airymax 修改直接写入（如 mm/airymax_mm.c）
│                                 # 边界：上游内核态内存管理实现在此；memory 子仓含 Airymax 专属
│                                 # 内核态扩展模块（rovol-kmod）+ 用户态工具，通过 hooks 接入上游 mm/
│
├── security/                     # LSM 框架 [模型 A 完整继承上游]
│                                 # 含 12 扁平 LSM 顶层目录（apparmor/bpf/integrity/keys/landlock/...）
│                                 # Airymax airy_lsm 直接写入 kernel/security/airy_lsm/
│                                 # 边界：security 子仓的 airy_lsm/ 是 Airymax 专属 LSM 策略 + 用户态工具
│
├── sound/                        # 音频子系统 [模型 A 完整继承上游]
├── certs/                        # 证书管理 [模型 A 完整继承上游]
├── ipc/                          # System V IPC [模型 A 完整继承上游]
├── samples/                      # 示例代码 [模型 A 完整继承上游]
├── usr/                          # 用户空间构建 [模型 A 完整继承上游]
├── virt/                         # 虚拟化 [模型 A 完整继承上游]
├── LICENSES/                     # 许可证文本 [模型 A 完整继承上游]
├── rust/                         # Rust 内核支持 [模型 A 完整继承上游]
│
├── Documentation/                # 文档即代码（ES-OLK-13）
│   ├── ABI/                      # ABI 文档（OS-IRON-001）
│   ├── airymax/                  # airymax 专属文档
│   └── process/                  # 开发流程文档
│
├── scripts/                      # 构建与检查脚本（ES-OLK-9）
│   ├── checkpatch.pl             # 编码规范检查（max_line_length=100）
│   ├── syscall_gen.py            # syscall.xml → C 代码生成（R-01）
│   ├── bitfield_gen.py           # structures.bf → C 代码生成（R-02）
│   ├── kbuild_check.py           # Kbuild/Kconfig 规范检查
│   ├── maintainer_check.py       # MAINTAINERS 完整性检查（R-03）
│   ├── sc_sync_check.sh          # [SC] 双端同步校验脚本
│   └── dts/                      # 设备树源文件
│
├── tools/                        # 内核工具与测试基础设施 [模型 A 完整继承上游]
│   ├── testing/                  # 上游测试基础设施
│   │   ├── kunit/                # KUnit 框架
│   │   ├── selftests/            # kselftest 框架
│   │   └── fault-injection/      # 故障注入框架
│   ├── perf/                     # 性能剖析工具
│   ├── build/                    # 构建工具
│   └── scripts/                  # 内核构建辅助脚本
│
└── tests/                        # Airymax 专属测试用例
    └── airymax/                  # Airymax 专属测试用例（跨子仓集成测试在 tests-linux/integration/）
```

### 5.4 services/ 子仓完整目录树（12 daemons + userland 分层）

```
services/                         # services 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .clang-format / .editorconfig
├── meson.build                   # 顶层 Meson 构建入口
│
├── daemons/                      # 12 daemons 集成（A-ULS 统一生命周期）[SS]
│                                 # 详见 §5.4.1
│
├── systemd/                      # systemd 集成（agentrt-linux 标准）[IND]
│   ├── units/                    # systemd unit 文件目录
│   │   ├── macro_d.service       # Macro-Supervisor service unit
│   │   ├── logger_d.service      # Logger Daemon service unit
│   │   ├── config_d.service      # Config Daemon service unit
│   │   ├── gateway_d.service     # Gateway daemon service unit
│   │   ├── sched_d.service       # Sched daemon service unit
│   │   ├── vfs_d.service         # VFS daemon service unit
│   │   ├── net_d.service         # Net daemon service unit
│   │   ├── mem_d.service         # Memory daemon service unit
│   │   ├── cogn_d.service        # Cognition daemon service unit
│   │   ├── sec_d.service         # Security daemon service unit
│   │   ├── audit_d.service       # Audit daemon service unit
│   │   └── dev_d.service         # Device daemon service unit
│   ├── targets/                  # systemd target 定义
│   │   ├── airymaxos.target      # AirymaxOS 总目标
│   │   ├── airymax-daemons.target # 12 daemons 集合目标
│   │   └── airymax-userland.target # userland 子系统集合目标
│   ├── generators/               # systemd generator（动态生成 unit）
│   │   └── airymax-generator.py  # AirymaxOS 专用 generator
│   └── analyze/                  # systemd-analyze 性能分析配置
│       └── blame-report.md       # 启动耗时分析报告
│
├── userland/                     # 用户态子系统实现（决策 B2 内部分层）[IND]
│   ├── vfs/                      # 用户态 VFS [IND]（seL4 服务用户态化，ADR-014）
│   │   ├── meson.build
│   │   ├── vfs-service/          # 虚拟文件系统服务（路径解析、挂载管理）
│   │   ├── fs-providers/         # 具体文件系统实现（ext4/xfs/tmpfs/btrfs）
│   │   ├── io-channel/           # io_uring channel 文件操作协议
│   │   └── vnode/                # 虚拟节点抽象
│   │
│   ├── net/                      # 用户态网络栈 [IND]（DPDK/AF_XDP）
│   │   ├── meson.build
│   │   ├── dpdk/                 # DPDK 用户态网络
│   │   ├── af_xdp/               # AF_XDP 高速包处理
│   │   ├── tcp-stack/            # 用户态 TCP 栈
│   │   ├── dns/                  # DNS 解析
│   │   └── dhcp/                 # DHCP 客户端/服务端
│   │
│   ├── drivers/                  # 用户态驱动框架 [IND]（VFIO/libvfio）
│   │   ├── meson.build
│   │   ├── libvfio/              # VFIO 用户态封装
│   │   ├── pci-drivers/          # PCI 设备驱动
│   │   ├── usb-drivers/          # USB 设备驱动
│   │   ├── gpu-drivers/          # GPU 设备驱动
│   │   └── block-drivers/        # 块设备驱动
│   │
│   └── ipc/                      # 用户态 IPC 库（io_uring IPC 封装）[SS]
│       ├── meson.build
│       ├── io-uring-ipc/         # 核心 IPC 库
│       ├── channel/              # 双向通道抽象（参考 seL4 Endpoint）
│       ├── socket/               # 面向连接的 socket 抽象
│       ├── fifo/                 # 单向 FIFO 抽象（参考 seL4 Notification）
│       └── eventpair/            # 事件同步原语
│
└── docs/                         # 子仓专属文档
```

#### 5.4.1 services/daemons/ 12 daemon 完整目录结构（统一 `_d` 后缀）

```
services/daemons/                 # 12 daemons 物理宿主（C1 决策：统一 _d 后缀）
│
├── macro_d/                      # [A-ULS] 主监管守护进程（Macro-Supervisor，用户温情裁决）
│   ├── meson.build               # Meson 构建定义
│   ├── README.md                 # daemon 设计文档
│   ├── main.c                    # daemon 拉起 + 信号处理 + 事件循环入口
│   ├── event_loop.c              # io_uring + eventfd 事件循环（接收内核故障通知）
│   ├── adjudicate.c              # 故障裁决策略（警告/降级/暂停/终止四态裁决）
│   ├── sched.c                   # [A-ULS] 调度参数注入（sched_setattr，sched_tac）
│   ├── cap_inject.c              # Capability 注入（io_uring_cmd 向内核 radix tree 写入）
│   ├── heartbeat.c               # Agent 进程状态检查（周期心跳轮询）
│   ├── reconcile.c               # 数据面 Reconciliation（与内核控制面同步）
│   └── policy.c                  # 裁决策略配置加载与热更新
│
├── logger_d/                     # [A-ULP] 日志消费守护进程
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # mmap 读取 Ring Buffer + 事件循环
│   ├── format.c                  # sprintf 格式化（Fastpath 禁止格式化，仅 slowpath）
│   ├── filter.c                  # 日志过滤（level/facility/component 三维过滤）
│   ├── sink.c                    # 落盘 + 轮转（journald + 文件双 sink）
│   └── panic_recv.c              # [DSL] Panic 生存路径接收（printk 原生回退）
│
├── config_d/                     # [A-UCS] 配置管理守护进程
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # 配置加载 + 热重载 + 事件循环
│   ├── sysctl_bridge.c           # sysctl ↔ JSON 双向同步（RCU 热重载）
│   ├── version_check.c           # AIRY_CONFIG_VERSION 校验（跨内核/用户态一致性）
│   └── schema.c                  # 配置 schema 验证（JSON Schema 格式）
│
├── gateway_d/                    # 网关守护进程（Agent 对外 API 网关）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── router.c                  # 请求路由与分发（基于 capability 的路由表）
│   ├── auth.c                    # 网关认证（Token + Capability 双因子）
│   ├── ratelimit.c               # 速率限制（令牌桶 + cgroup v2 配额）
│   └── proxy.c                   # 代理转发（io_uring 异步转发）
│
├── sched_d/                      # [A-ULS 辅助] 调度守护进程（sched_tac 策略编排）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── policy.c                  # sched_tac 策略管理（SCHED_DEADLINE/FIFO/EEVDF 组合）
│   ├── affinity.c                # CPU 亲和性管理（sched_setaffinity 注入）
│   ├── priority.c                # 优先级调整（nice + sched_setattr）
│   └── stats.c                   # 调度统计与可观测性导出
│
├── vfs_d/                        # VFS 用户态服务守护进程（FUSE 模型协调）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── mount.c                   # 挂载管理（mount/umount 协调）
│   ├── path.c                    # 路径解析协调（内核保留路径解析，用户态协调）
│   ├── cache.c                   # dentry/inode 缓存管理（与内核缓存协同）
│   └── provider.c                # FS provider 调度（ext4/xfs/tmpfs/btrfs）
│
├── net_d/                        # 网络策略守护进程（用户态网络栈协调）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── firewall.c                # 防火墙策略（nftables 规则管理）
│   ├── qos.c                     # QoS 管理（tc + cgroup v2 net_cls）
│   ├── routing.c                 # 路由策略（策略路由 + 多路径）
│   └── conntrack.c               # 连接跟踪（与内核 nf_connstat 协同）
│
├── mem_d/                        # [记忆] 记忆管理守护进程（MemoryRovol 用户态协调）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── rovol_mgr.c               # MemoryRovol L1-L4 层级管理
│   ├── tiering.c                 # 内存分层（CXL/PMEM/DIMM 三级分层）
│   ├── pmem_mgr.c                # PMEM 管理（持久化内存生命周期）
│   └── reclaim.c                 # 回收策略（MGLRU 多代 LRU 协调）
│
├── cogn_d/                       # [认知] 认知调度守护进程（A-UEF 用户态编排）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── clt_coord.c               # CoreLoopThree 三阶段协调（Sense/Plan/Act）
│   ├── llm_sched.c               # LLM 调度（推理请求排队 + 优先级）
│   ├── sandbox.c                 # Wasm 3.0 沙箱管理（cognition kthread 协调）
│   └── accel.c                   # 加速器调度（GPU/NPU 资源分配）
│
├── sec_d/                        # [A-ULS 辅助] 安全策略守护进程（纯 C LSM 策略编排）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── cap_mgr.c                 # Capability 管理（41 ID + 派生树维护）
│   ├── lsm_policy.c              # 纯 C LSM 策略（252 钩子策略编排）
│   ├── cupolas.c                 # Cupolas blob 管理（机密计算策略）
│   └── audit.c                   # 安全审计日志（与 audit_d 协同）
│
├── audit_d/                      # 审计守护进程（独立审计链）
│   ├── meson.build
│   ├── README.md
│   ├── main.c                    # daemon 拉起 + 事件循环
│   ├── collect.c                 # 审计事件收集（auditd + io_uring 双通道）
│   ├── filter.c                  # 审计过滤（基于规则的事件过滤）
│   ├── store.c                   # 审计存储（追加写 + 签名 + 轮转）
│   └── report.c                  # 审计报告（合规性报告生成）
│
└── dev_d/                        # 设备驱动守护进程（用户态驱动框架协调）
    ├── meson.build
    ├── README.md
    ├── main.c                    # daemon 拉起 + 事件循环
    ├── hotplug.c                 # 热插拔管理（udev 事件协调）
    ├── vfio_mgr.c                # VFIO 管理（libvfio 生命周期）
    ├── pci.c                     # PCI 设备管理（枚举 + 分配）
    └── usb.c                     # USB 设备管理（枚举 + 策略）
```

**12 daemon 源文件统计**：

| daemon | 源文件数 | 关键职责 | Unify Design 模块 |
|--------|---------|---------|------------------|
| macro_d | 9 | Macro-Supervisor 用户温情裁决 | A-ULS |
| logger_d | 6 | Ring Buffer 消费 + Panic 回退 | A-ULP |
| config_d | 5 | 配置热重载 + 版本校验 | A-UCS |
| gateway_d | 6 | Agent API 网关 | — |
| sched_d | 6 | sched_tac 策略编排 | A-ULS（辅助） |
| vfs_d | 6 | VFS 用户态协调 | — |
| net_d | 6 | 网络策略 | — |
| mem_d | 6 | MemoryRovol 协调 | 记忆 |
| cogn_d | 6 | CoreLoopThree 编排 | A-UEF |
| sec_d | 6 | 纯 C LSM 策略 | A-ULS（辅助） |
| audit_d | 6 | 独立审计链 | — |
| dev_d | 6 | 用户态驱动协调 | — |
| **合计** | **74** | — | — |

### 5.5 security/ 子仓完整目录树

```
security/                         # security 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .clang-format / .editorconfig
├── Kbuild                        # 内核态 Kbuild 入口
├── meson.build                   # 用户态 Meson 入口
│
├── airy_lsm/                     # Airymax 专属纯 C LSM 模块（不使用 BPF LSM）
│   ├── Kbuild
│   ├── Kconfig
│   ├── airy_lsm.c                # DEFINE_LSM(airy) 注册 + LSM_HOOK_INIT 宏注册
│   ├── airy_cap.c                # Capability 检查（41 ID）
│   ├── airy_hooks.c              # 252 LSM 钩子实现
│   ├── airy_blob.c               # Cupolas blob 布局管理
│   └── airy_policy.c             # 策略加载与热更新
│
├── common/                       # 公共安全原语
│   ├── cap_id.c                  # Capability ID 分配
│   ├── cap_deriv.c               # Capability 派生树（MDB）
│   └── verdict.c                 # 策略裁决 4 值（ALLOW/DENY/AUDIT/COMPLAIN）
│
├── integrity/                    # 完整性度量（IMA 扩展）
│   ├── ima_airymax.c             # Airymax IMA 扩展
│   └── hash_chain.c              # SHA-256 哈希链审计日志
│
├── keys/                         # 密钥管理
│   ├── keyring_airymax.c         # Airymax keyring
│   └── sm_key.c                  # 国密 SM2/SM3/SM4 密钥
│
├── capability/                   # Capability 系统（seL4 风格）
│   ├── cte.c                     # CTE（Capability Table Entry）管理
│   ├── cspace.c                  # CSpace 树形寻址（guard + radix）
│   ├── mdb.c                     # MDB 派生树维护
│   ├── cnode_ops.c               # 7 种 CNode 操作（Copy/Mint/Move/Mutate/Revoke/Delete/Rotate）
│   └── badge.c                   # Badge 机制与 Agent 身份
│
├── cupolas/                      # Cupolas 机密计算策略
│   ├── cupolas_engine.c          # 策略引擎
│   ├── cupolas_blob.c            # Blob 布局管理
│   └── cupolas_verdict.c         # 4 值裁决（ALLOW/DENY/AUDIT/COMPLAIN）
│
├── include/                      # 内部头文件（非 [SC]）
│   └── airymax/security/
│       ├── capability_internal.h
│       ├── lsm_internal.h
│       └── cupolas_internal.h
│
├── tools/                        # 用户态工具
│   ├── capctl/                   # Capability 控制工具
│   ├── lsmctl/                   # LSM 策略控制工具
│   └── auditctl/                 # 审计控制工具
│
└── Documentation/                # 子仓专属文档
    ├── airy-lsm-design.md        # 纯 C LSM 设计
    ├── capability-model.md       # Capability 模型
    └── cupolas-design.md         # Cupolas 机密计算
```

### 5.6 memory/ 子仓完整目录树

```
memory/                           # memory 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .clang-format / .editorconfig
├── Kbuild                        # 内核态 Kbuild 入口
│
├── memoryrovol/                  # MemoryRovol L1-L4 记忆卷载
│   ├── Kbuild
│   ├── rovol_core.c              # MemoryRovol 核心
│   ├── rovol_l1.c                # L1 热数据层
│   ├── rovol_l2.c                # L2 温数据层
│   ├── rovol_l3.c                # L3 冷数据层
│   ├── rovol_l4.c                # L4 PMEM 持久化层
│   ├── rovol_snapshot.c          # 快照/恢复
│   └── rovol_migrate.c           # 跨节点迁移
│
├── cxl/                          # CXL 内存池化
│   ├── Kbuild
│   ├── cxl_pool.c                # CXL 内存池
│   ├── cxl_switch.c              # CXL Switch
│   └── cxl_tier.c                # CXL 分层策略
│
├── pmem/                         # PMEM 持久化内存
│   ├── Kbuild
│   ├── pmem_core.c               # PMEM 核心
│   ├── pmem_layout.c             # PMEM 布局
│   └── pmem_reclaim.c            # PMEM 回收
│
├── mglru/                        # MGLRU 多代 LRU 增强
│   ├── Kbuild
│   ├── mglru_enhance.c           # MGLRU 增强
│   └── mglru_tier.c              # MGLRU 分层
│
├── vfs-persist-kern/             # VFS 持久化内核模块
│   ├── Kbuild
│   └── vfs_persist.c             # VFS 持久化接口
│
├── rovol-kmod/                   # MemoryRovol 内核模块（[IND] 内核态扩展）
│   ├── Kbuild
│   ├── rovol_kmod.c              # 内核模块入口
│   └── rovol_hooks.c             # 与上游 mm/ 集成的 hooks
│
├── include/                      # 内部头文件（非 [SC]）
│   └── airymax/memory/
│       ├── memoryrovol_internal.h
│       ├── cxl_internal.h
│       └── pmem_internal.h
│
├── tools/                        # 用户态工具
│   ├── rovolctl/                 # MemoryRovol 控制工具
│   └── memstat/                  # 内存统计工具
│
└── Documentation/                # 子仓专属文档
    ├── memoryrovol-design.md     # MemoryRovol 设计
    ├── cxl-pool-design.md        # CXL 内存池设计
    └── pmem-design.md            # PMEM 设计
```

### 5.7 cognition/ 子仓完整目录树

```
cognition/                        # cognition 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .clang-format / .editorconfig / rustfmt.toml
├── Cargo.toml                    # Rust 项目入口
│
├── coreloopthree/                # CoreLoopThree 三阶段认知循环
│   ├── Cargo.toml
│   ├── src/
│   │   ├── lib.rs
│   │   ├── percept.rs            # 感知阶段（Sense）
│   │   ├── think.rs              # 思考阶段（Plan）
│   │   └── act.rs                # 行动阶段（Act）
│   └── tests/
│
├── thinkdual/                    # Thinkdual 双系统协同
│   ├── Cargo.toml
│   ├── src/
│   │   ├── lib.rs
│   │   ├── system1.rs            # System-1（快思考）
│   │   └── system2.rs            # System-2（慢思考）
│   └── tests/
│
├── llm/                          # LLM 调度与推理
│   ├── Cargo.toml
│   ├── src/
│   │   ├── lib.rs
│   │   ├── scheduler.rs          # LLM 调度器
│   │   ├── inference.rs          # 推理引擎接口
│   │   └── token_tracker.rs      # Token 能效追踪
│   └── tests/
│
├── compute-accel/                # 计算加速（GPU/NPU）
│   ├── Kbuild
│   ├── accel_core.c              # 加速核心
│   ├── gpu_sched.c               # GPU 调度
│   ├── npu_access.c              # NPU 访问
│   └── accel_drv/                # 加速器驱动
│       ├── Kbuild
│       ├── gpu_drv.c
│       └── npu_drv.c
│
├── wasm-sandbox/                 # Wasm 3.0 沙箱
│   ├── Cargo.toml
│   ├── src/
│   │   ├── lib.rs
│   │   ├── runtime.rs            # Wasm 运行时
│   │   └── capability.rs         # Capability 集成
│   └── tests/
│
├── kthread/                      # cognition kthread（内核态）
│   ├── Kbuild
│   ├── cogn_kthread.c            # 认知循环 kthread
│   └── cogn_notify.c             # 内核通知接口
│
├── include/                      # 内部头文件（非 [SC]）
│   └── airymax/cognition/
│       ├── clt_internal.h
│       ├── llm_internal.h
│       └── thinkdual_internal.h
│
├── tools/                        # 用户态工具
│   ├── cognctl/                  # 认知控制工具
│   └── llmctl/                   # LLM 控制工具
│
└── Documentation/                # 子仓专属文档
    ├── coreloopthree-design.md   # CoreLoopThree 设计
    ├── thinkdual-design.md       # Thinkdual 设计
    └── wasm-sandbox-design.md    # Wasm 沙箱设计
```

### 5.8 cloudnative/ 子仓完整目录树

```
cloudnative/                      # cloudnative 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .editorconfig / rustfmt.toml
├── go.mod                        # Go Modules 入口
│
├── k8s-crd/                      # Kubernetes CRD
│   ├── go.mod
│   ├── api/
│   │   └── v1alpha1/
│   │       ├── agent_types.go    # Agent CRD
│   │       ├── cognition_types.go # Cognition CRD
│   │       └── groupversion_info.go
│   ├── controllers/
│   │   ├── agent_controller.go   # Agent 控制器
│   │   └── cognition_controller.go
│   └── crd/
│       ├── agent.yaml            # Agent CRD 定义
│       └── cognition.yaml        # Cognition CRD 定义
│
├── containerd-shim/              # containerd shim
│   ├── go.mod
│   ├── cmd/
│   │   └── containerd-shim-airy/
│   │       └── main.go           # shim 入口
│   ├── pkg/
│   │   ├── service.go            # shim 服务
│   │   └── runtime.go            # 运行时
│   └── configs/
│       └── config.toml           # shim 配置
│
├── cni/                          # CNI 插件
│   ├── go.mod
│   ├── pkg/
│   │   ├── plugin/
│   │   │   ├── airy_cni.go       # AirymaxOS CNI 插件
│   │   │   └── skel.go           # CNI skel
│   │   └── ipam/
│   │       └── airy_ipam.go      # IPAM
│   └── configs/
│       └── 10-airymacos.conflist # CNI 配置
│
├── sdk/                          # 多语言 SDK
│   ├── go/
│   │   ├── go.mod
│   │   └── airyclient/
│   │       ├── client.go         # Go SDK
│   │       └── types.go
│   ├── python/
│   │   ├── setup.py
│   │   └── airyclient/
│   │       ├── __init__.py
│   │       └── client.py         # Python SDK
│   ├── rust/
│   │   ├── Cargo.toml
│   │   └── src/
│   │       └── lib.rs            # Rust SDK
│   └── typescript/
│       ├── package.json
│       └── src/
│           └── index.ts          # TypeScript SDK
│
├── agentctl/                     # agentctl 命令行工具（与 kubectl 兼容）
│   ├── go.mod
│   ├── cmd/
│   │   └── agentctl/
│   │       └── main.go
│   └── pkg/
│       └── cmd/
│           ├── root.go
│           ├── apply.go
│           └── get.go
│
└── Documentation/                # 子仓专属文档
    ├── k8s-crd-design.md         # K8s CRD 设计
    ├── containerd-shim-design.md # containerd shim 设计
    └── sdk-design.md             # SDK 设计
```

### 5.9 system/ 子仓完整目录树

```
system/                           # system 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .editorconfig
├── Kbuild                        # 内核态 Kbuild 入口
├── meson.build                   # 用户态 Meson 入口
│
├── systemd-adapter/              # systemd 适配层
│   ├── meson.build
│   ├── src/
│   │   ├── adapter.c             # systemd 适配核心
│   │   ├── unit_gen.c            # unit 文件生成器
│   │   └── target_gen.c          # target 文件生成器
│   └── configs/
│       └── airymaxos.preset      # systemd preset
│
├── init/                         # 系统初始化
│   ├── Kbuild
│   ├── airy_init.c               # AirymaxOS init 入口
│   └── airy_boot.c               # 启动流程
│
├── commons/                      # 公共系统工具
│   ├── meson.build
│   ├── src/
│   │   ├── airy_setup.c          # 系统设置
│   │   ├── airy_status.c         # 状态查询
│   │   └── airyctl.c             # 统一控制工具
│   └── configs/
│       └── airymaxos.conf        # 系统配置
│
├── packaging/                    # 打包系统
│   ├── rpm/
│   │   ├── airy-kernel.spec      # kernel RPM spec
│   │   ├── airy-services.spec    # services RPM spec
│   │   ├── airy-security.spec    # security RPM spec
│   │   ├── airy-memory.spec      # memory RPM spec
│   │   ├── airy-cognition.spec   # cognition RPM spec
│   │   ├── airy-cloudnative.spec # cloudnative RPM spec
│   │   └── airy-system.spec      # system RPM spec
│   ├── dnf/
│   │   ├── airymaxos.repo        # dnf 仓库配置
│   │   └── dnf-plugins/          # dnf 插件
│   └── comps/
│       └── airymaxos-comps.xml   # dnf 组定义
│
├── devstation/                   # DevStation 开发环境
│   ├── meson.build
│   ├── src/
│   │   ├── devstation.c          # DevStation 入口
│   │   ├── env_setup.c           # 环境配置
│   │   ├── debugger.c            # 调试工具集成
│   │   └── analyzer.c            # 分析工具集成
│   └── configs/
│       ├── devstation.json       # DevStation 配置
│       └── toolchain.toml        # 工具链配置
│
├── config/                       # A-UCS 配置入口
│   ├── meson.build
│   ├── src/
│   │   ├── sysctl_default.c      # sysctl 默认值
│   │   ├── kconfig_default.c     # Kconfig 默认值
│   │   └── config_sync.c         # 配置同步
│   └── configs/
│       ├── sysctl.airymaxos.conf # sysctl 默认配置
│       └── kconfig.airymaxos     # Kconfig 默认配置
│
└── Documentation/                # 子仓专属文档
    ├── systemd-integration.md    # systemd 集成
    ├── packaging-guide.md        # 打包指南
    └── devstation-guide.md       # DevStation 指南
```

### 5.10 tests-linux/ 子仓完整目录树

```
tests-linux/                      # tests-linux 子仓（feature/official-hubs-01 分支）
├── README.md / README_zh.md / LICENSE / NOTICE / MAINTAINERS / CONTRIBUTING.md
├── .gitignore / .editorconfig
├── CMakeLists.txt                # CTest 入口
│
├── unit/                         # 单元测试（对齐 Linux 6.6 tools/testing/kunit/）
│   ├── CMakeLists.txt
│   ├── kernel/
│   │   ├── test_sched_tac.c      # sched_tac 单元测试
│   │   ├── test_ipc_uring.c      # io_uring IPC 单元测试
│   │   ├── test_capability.c     # Capability 单元测试
│   │   └── test_superv.c         # Micro-Supervisor 单元测试
│   ├── services/
│   │   ├── test_macro_d.c        # macro_d 单元测试
│   │   ├── test_logger_d.c       # logger_d 单元测试
│   │   └── test_config_d.c       # config_d 单元测试
│   ├── security/
│   │   ├── test_lsm.c            # 纯 C LSM 单元测试
│   │   └── test_cap_deriv.c      # Capability 派生单元测试
│   ├── memory/
│   │   ├── test_rovol.c          # MemoryRovol 单元测试
│   │   └── test_cxl.c            # CXL 单元测试
│   └── cognition/
│       ├── test_clt.c            # CoreLoopThree 单元测试
│       └── test_llm_sched.c      # LLM 调度单元测试
│
├── integration/                  # 集成测试（跨子仓）
│   ├── CMakeLists.txt
│   ├── kernel_services/
│   │   ├── test_syscall_ipc.c    # syscall + IPC 集成
│   │   └── test_daemon_lifecycle.c # daemon 生命周期集成
│   ├── security_cognition/
│   │   └── test_cap_wasm.c       # Capability + Wasm 集成
│   └── memory_cognition/
│       └── test_rovol_clt.c      # MemoryRovol + CoreLoopThree 集成
│
├── kunit/                        # KUnit 框架（对齐 Linux 6.6）
│   └── README.md
│
├── selftests/                    # kselftest 框架（对齐 Linux 6.6）
│   └── README.md
│
├── fault-injection/              # 故障注入测试
│   ├── CMakeLists.txt
│   ├── fi_ipc.c                  # IPC 故障注入
│   ├── fi_sched.c                # 调度故障注入
│   └── fi_mem.c                  # 内存故障注入
│
├── formal-verification/          # 形式化验证（seL4 三段式结构）
│   ├── spec/                     # 抽象规约（内核"应该做什么"的数学描述）
│   │   ├── ipc_endpoint_spec.thy # IPC Endpoint 状态机规约
│   │   ├── cap_deriv_spec.thy    # Capability 派生规约
│   │   └── sched_spec.thy        # 调度器规约
│   ├── proof/                    # Isabelle/HOL 证明（C 实现满足抽象规约）
│   │   ├── ipc_endpoint_proof.thy
│   │   ├── cap_deriv_proof.thy
│   │   └── sched_proof.thy
│   └── capdl/                    # capability 分布语言（系统初始 capability 状态描述）
│       └── airymaxos.capdl       # AirymaxOS 初始 capability 状态
│
├── soak/                         # Soak 长时测试
│   ├── CMakeLists.txt
│   ├── soak_7day.c               # 7 天长时运行
│   └── soak_leak_detect.c        # 内存泄漏检测
│
├── chaos/                        # 混沌工程
│   ├── CMakeLists.txt
│   ├── chaos_kill_daemon.c       # 杀死 daemon 测试
│   └── chaos_net_partition.c     # 网络分区测试
│
├── benchmark/                    # 性能基准
│   ├── CMakeLists.txt
│   ├── bench_ipc_latency.c       # IPC 延迟基准
│   ├── bench_sched_throughput.c  # 调度吞吐基准
│   └── bench_mem_tiering.c       # 内存分层基准
│
└── Documentation/                # 子仓专属文档
    ├── testing-strategy.md       # 测试策略
    ├── formal-verification-guide.md # 形式化验证指南
    └── benchmark-guide.md        # 基准测试指南
```

### 5.11 形式化验证工程标准（tests-linux 子仓）

**OS-TEST-010**：tests-linux 子仓必须包含 `formal-verification/` 目录，采用 seL4 三段式结构（ES-SEL4-5）：

```
formal-verification/
├── spec/        # 抽象规约（内核"应该做什么"的数学描述）
├── proof/       # Isabelle/HOL 证明（C 实现满足抽象规约）
└── capdl/       # capability 分布语言（系统初始 capability 状态描述）
```

形式化验证范围（1.0.1 阶段）：仅覆盖关键路径（调度器核心、IPC fastpath、capability 派生模型），不覆盖全部内核代码。

---

## §6 [SC] 10 头文件清单与 include path 策略

### 6.1 [SC] 头文件物理宿主（OS-IRON-014 落地）

`kernel/include/airymax/` 目录是 [SC] 共享契约层的**唯一物理宿主**，承载 **10 个 [SC] 头文件** + 2 个补充共享文件。每个 [SC] 头文件底部均包含 [DSL] 降级块（详见 §8）。

### 6.2 10 个 [SC] 头文件完整清单

| # | 头文件 | 物理路径 | 共享内容 | 关联 Unify Design 模块 | 契约文档 |
|---|--------|---------|---------|----------------------|---------|
| 1 | `error.h` | `include/airymax/error.h` | `AIRY_E*` 错误码（5 子空间 300 码）+ `AIRY_FAULT_*` 故障码（0x1000+）+ `airy_err_t` 类型 + [DSL] 降级块 | **A-UEF** | [30-interfaces/08-sc-error-contract.md](../30-interfaces/08-sc-error-contract.md) |
| 2 | `log_types.h` | `include/airymax/log_types.h` | `AIRY_LOG_MAGIC`（0x414C4F47）+ 128B 记录 + 5 级日志枚举（LOG_DEBUG~LOG_FATAL）+ printk 8 级映射 + [DSL] 降级块 | **A-ULP** | [30-interfaces/09-sc-log-types-contract.md](../30-interfaces/09-sc-log-types-contract.md) |
| 3 | `ipc.h` | `include/airymax/ipc.h` | IPC magic 0x41524531 + 128B 消息头（Layout C）+ SQE/CQE 操作码与标志位 + [DSL] 降级块 | **A-IPC** | [30-interfaces/02-ipc-protocol.md](../30-interfaces/02-ipc-protocol.md) |
| 4 | `sched.h` | `include/airymax/sched.h` | Agent 8 态生命周期 + sched_tac 调度参数 + `MAC_MAX_AGENTS` + 任务描述符 magic 0x41475453 + [DSL] 3 态降级 | **A-ULS** | [30-interfaces/10-sc-sched-extension.md](../30-interfaces/10-sc-sched-extension.md) |
| 5 | `memory_types.h` | `include/airymax/memory_types.h` | MemoryRovol L1-L4 数据结构 + GFP 掩码语义 + PMEM 接口 + [DSL] 降级块 | 记忆 | [120-cross-project-code-sharing.md](../50-engineering-standards/120-cross-project-code-sharing.md) |
| 6 | `security_types.h` | `include/airymax/security_types.h` | POSIX capability 41 ID + LSM 钩子 252 ID + Cupolas blob 布局 + [DSL] 降级块 | 安全 | [03-capability-model.md](../110-security/03-capability-model.md) |
| 7 | `cognition_types.h` | `include/airymax/cognition_types.h` | `airy_q16_t` Q16.16 定点数 + CoreLoopThree 三阶段 + Thinkdual 模式 + [DSL] 降级块 | 认知 | [120-cross-project-code-sharing.md](../50-engineering-standards/120-cross-project-code-sharing.md) |
| 8 | `syscalls.h` | `include/airymax/syscalls.h` | Syscall 编号体系（12 核心 + 12 预留 = 24 槽位）+ [DSL] 降级块 | 全部 | [30-interfaces/01-syscalls.md](../30-interfaces/01-syscalls.md) |
| 9 | `uapi_compat.h` | `include/airymax/uapi_compat.h` | 三路类型桥接（`__KERNEL__` / `__linux__` / `#else`）+ [DSL] 降级块 | IRON-9 | [50-engineering-standards/11-sc-header-type-bridging.md](../50-engineering-standards/11-sc-header-type-bridging.md) |
| 10 | `lsm_types.h` | `include/airymax/lsm_types.h` | 纯 C LSM 类型定义 + `DEFINE_LSM(airy)` 骨架 + Capability 缓存结构 + [DSL] 降级块 | **A-ULS/A-IPC** | [110-security/07-airy-lsm-design.md](../110-security/07-airy-lsm-design.md) |

### 6.3 8 个缺失 [SC] 头文件补齐方案（解决 P0-2）

v1.1 仅有 `error.h` + `log_types.h` 落地，其余 8 个缺失。v2.0 提供补齐方案：

| # | 头文件 | 职责 | 内容大纲 | 补齐优先级 |
|---|--------|------|---------|----------|
| 3 | `ipc.h` | IPC 契约 | `AIRY_IPC_MAGIC 0x41524531` + `