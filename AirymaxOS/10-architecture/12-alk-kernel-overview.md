Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.

# 极境内核 ALK-6.6 总体架构

> **文档定位**：极境内核（Airymax Linux Kernel 6.6，ALK-6.6）的总体架构描述，回答"极境内核长什么样子、如何对 Linux 6.6 进行 seL4 思想借鉴的微内核化改造"\
> **文档版本**：v1.0\
> **最后更新**：2026-07-20\
> **核心约束**：OS-ARCH-001（不修改 Linux 6.6 主线源码，仅增量添加）
> **设计依据**：ADR-012（微内核化改造技术路线）+ ADR-014（seL4 唯一思想来源）+ ADR-016（版本基线锁定 Linux 6.6 LTS）

---

## §1 物理形态

ALK-6.6 = Linux 6.6 LTS 完整 fork（不修改主线源码）
       + 新增 [SC] 10 头文件（kernel/include/uapi/linux/airymax/）
       + 新增 security/airy/ 纯 C LSM 模块
       + 新增 kernel/kernel/superv/ Micro-Supervisor 模块
       + 新增 security/capability/ agent_caps[1024] 静态数组管理
       + 新增 4 个 syscall（编号 512-515）
       + 复用 io_uring IORING_OP_URING_CMD（不新增 opcode）
       + 复用 SCHED_DEADLINE/SCHED_FIFO/EEVDF（不新增调度类）
       + airy_defconfig fragment（不修改 Kconfig）


### 1.1 目录树（新增内容）

```
ALK-6.6（基于 Linux 6.6 LTS fork）
│
├── [SC] 共享契约头文件（新增）───────────────────── kernel/include/uapi/linux/airymax/
│   ├── error.h              # A-UEF 错误码/Fault 码契约
│   ├── log_types.h          # A-ULP 日志类型契约
│   ├── ipc.h                # A-IPC 128B 消息头 Layout C v4（11 字段，capability_badge offset 40）
│   ├── sched.h              # A-ULS 调度类型契约（sched_tac）
│   ├── memory_types.h       # MemoryRovol L1-L4 数据结构
│   ├── security_types.h     # Capability 41 ID + Badge 64-bit 布局
│   ├── cognition_types.h    # CoreLoopThree 阶段枚举 + Thinkdual 模式枚举
│   ├── syscalls.h           # 4 核心 syscall + 20 预留槽位
│   ├── uapi_compat.h        # UAPI 兼容层
│   └── lsm_types.h          # 纯 C LSM 类型契约
│
├── 纯 C LSM 模块（新增）────────────────────────── security/airy/
│   ├── Kbuild
│   ├── airy_lsm.c           # DEFINE_LSM(airy) 注册 + 250 个 LSM 钩子
│   ├── airy_cap_init.c      # agent_caps[1024] 静态数组初始化
│   ├── airy_cap_array.c     # agent_caps[1024] 静态数组管理（v1.1 替代 radix_tree）
│   ├── airy_cap_derive.c    # seL4 CNode 派生（Copy/Mint/Move/Mutate/Revoke/Delete/Rotate）
│   ├── airy_cap_check.c     # slowpath 校验（fastpath C-S9 Badge 内联在 kernel/kernel/superv/）
│   ├── airy_cap_revoke.c    # 撤销（atomic_inc(&airy_cap_global_epoch) O(1)）
│   ├── airy_cap_rotate.c    # 轮换（per-Agent Epoch 更新）
│   ├── airy_superv_lsm.c    # Micro-Supervisor LSM 钩子注册
│   ├── airy_ipc_freeze.c    # IPC Ring 冻结（ring->frozen = true）
│   ├── airy_die_notify.c    # die_notifier 内核崩溃通知链
│   ├── airy_eventfd.c       # eventfd 故障通知 Macro-Supervisor
│   └── airy_cap.h           # capability 内部头文件
│
├── Capability 系统（新增）───────────────────────── security/capability/
│   ├── Kbuild
│   ├── airy_cap_init.c      # 初始化（agent_caps[1024] 静态数组 + 全局 Epoch）
│   ├── airy_cap_array.c     # agent_caps[1024] 静态数组管理
│   ├── airy_cap_derive.c    # seL4 CNode 派生操作
│   ├── airy_cap_check.c     # slowpath 校验
│   ├── airy_cap_revoke.c    # 撤销
│   ├── airy_cap_rotate.c    # 轮换
│   └── airy_cap.h
│
├── Micro-Supervisor 内核模块（新增）──────────────── kernel/kernel/superv/
│   ├── Kbuild
│   ├── airy_cap_check.c     # fastpath C-S9 Badge 校验（~10ns）
│   ├── airy_die_notify.c    # die_notifier 注册
│   ├── airy_eventfd.c       # eventfd 通知 Macro-Supervisor
│   ├── airy_ipc_freeze.c    # IPC Ring 冻结
│   └── airy_superv_lsm.c    # Micro-Supervisor LSM 钩子
│
├── 新增 syscall（新增）─────────────────────────── kernel/kernel/syscalls/airy_syscalls.c
│   └── 4 核心 syscall（编号 512-515）
│
├── defconfig fragment（新增）─────────────────────── kernel/arch/{x86,arm64}/configs/airy_defconfig
│
├── 非 [SC] 内部类型头文件（新增）─────────────────── kernel/include/airymax/
│   ├── build_types.h        # 构建系统派生类型
│   └── kconfig_types.h      # Kconfig 派生类型定义
│
└── Linux 6.6 LTS 主线源码（不修改）────────────────── kernel/{arch,block,crypto,drivers,fs,include,init,ipc,kernel,lib,mm,net,security,sound,...}
```

### 1.2 与 vanilla Linux 6.6 的差异

```
vanilla Linux 6.6 LTS
├── kernel/           ← 不修改
├── security/         ← 新增 airy/ 子目录 + capability/ 子目录
├── include/uapi/     ← 新增 linux/airymax/ 子目录
├── io_uring/         ← 不修改（复用 IORING_OP_URING_CMD）
├── kernel/sched/     ← 不修改（复用 SCHED_DEADLINE/SCHED_FIFO/EEVDF）
├── mm/               ← 不修改（复用 MGLRU/userfaultfd/alloc_pages）
└── drivers/          ← 不修改

ALK-6.6 新增内容（增量，不修改主线）
├── include/uapi/linux/airymax/     ← [SC] 10 头文件
├── include/airymax/                ← 非 [SC] 内部类型（build_types.h / kconfig_types.h）
├── security/airy/                  ← 纯 C LSM 模块（airy_lsm + capability）
├── security/capability/            ← agent_caps[1024] 静态数组管理
├── kernel/kernel/superv/           ← Micro-Supervisor（冷酷执法）
├── kernel/kernel/syscalls/airy_syscalls.c ← 4 新增 syscall（512-515）
├── arch/x86/configs/airy_defconfig ← defconfig fragment
└── arch/arm64/configs/airy_defconfig
```

## §2 改造方式

### 2.1 核心原则：不修改主线源码 + 增量添加

ALK-6.6 不是"魔改 Linux 内核"，而是"在 Linux 6.6 LTS 之上增量添加 agentrt-linux 所需的内核能力"。遵循 OS-ARCH-001 硬约束：**不修改任何 Linux 6.6 主线源码**。

### 2.2 6 大改造点

| # | 改造点 | 改造方式 | 物理位置 | 修改主线？ |
|---|--------|---------|---------|:---:|
| 1 | [SC] 共享契约头文件 | 新增 10 个 UAPI 头文件 | `include/uapi/linux/airymax/` | 否 |
| 2 | 纯 C LSM 模块 | 新增 `airy_lsm` 模块（编译为 .ko 或内置） | `security/airy/` | 否 |
| 3 | Micro-Supervisor | 新增内核模块（冷酷执法） | `kernel/kernel/superv/` | 否 |
| 4 | Capability 系统 | 新增 `agent_caps[1024]` 静态数组管理 | `security/capability/` | 否 |
| 5 | 4 个新增 syscall | 新增 syscall 入口（512-515） | `kernel/syscalls/airy_syscalls.c` + `include/uapi/asm-generic/unistd.h` 扩展 | 否（扩展 unistd.h） |
| 6 | airy_defconfig | 新增 defconfig fragment | `arch/{x86,arm64}/configs/airy_defconfig` | 否 |

### 2.3 技术选型四大支柱

| 支柱 | 选型 | 复用/新增 | 理由 |
|------|------|----------|------|
| 调度 | sched_tac（SCHED_DEADLINE/SCHED_FIFO/EEVDF） | 复用 | 不新增调度类，不依赖 BPF |
| IPC | io_uring IORING_OP_URING_CMD | 复用 | 不新增 opcode，零拷贝 |
| 安全 | 纯 C LSM（airy_lsm） | 新增 | 对齐 openEuler 纯 C 模式 |
| 内存 | alloc_pages + mmap | 复用 | x86_64 默认缓存一致 |

## §3 微内核化体现（seL4 思想借鉴）

### 3.1 seL4 机制映射

| seL4 机制 | ALK-6.6 实现 | 物理载体 | 借鉴程度 |
|-----------|-------------|---------|---------|
| CNode Capability | agent_caps[1024] 静态数组 + Badge 64-bit | `security/capability/` + `security/airy/` | 机制借鉴 |
| handleFault() | Micro-Supervisor 冷酷执法 | `kernel/kernel/superv/` | 模式借鉴 |
| Endpoint IPC | io_uring + 128B 消息头 Layout C v4 | 复用 `io_uring/` + [SC] ipc.h | 语义借鉴 |
| Notification | eventfd + 位图聚合 | `kernel/kernel/superv/airy_eventfd.c` | 语义借鉴 |
| MCS 调度上下文 | sched_tac（SCHED_DEADLINE 映射） | 复用 `kernel/sched/deadline.c` | 映射借鉴 |
| 服务用户态化 | 12 daemon 用户态运行 | `services/` 子仓 | 架构借鉴 |
| 机制/策略分离 | 内核冷酷执法 + 用户态温情裁决 | `kernel/kernel/superv/` + `services/macro_d` | 哲学借鉴 |
| fastpath | C-S0~C-S12 校验链 + C-S9 Badge 内联 | fastpath C-S9 `airy_cap_badge_ok()` | 模式借鉴 |

### 3.2 微内核设计原则落地

| 原则 | ALK-6.6 落地 | 证据 |
|------|-------------|------|
| Liedtke minimality | 仅新增 4 个 syscall + 1 个 LSM 模块，复用 Linux 已有能力 | syscall 12→4，LSM 250 钩子 |
| Capability-based security | agent_caps[1024] + Badge 64-bit + fastpath C-S9 内联 | `security/capability/` + `security/airy/` |
| 消息传递 IPC | io_uring 128B 消息头 + kfifo SPSC 无锁 | `io_uring/` + [SC] ipc.h |
| 服务用户态化 | 12 daemon 全部用户态运行 | `services/` 子仓 |
| 机制/策略分离 | 内核冷酷执法 + 用户态温情裁决 | Micro-Supervisor + Macro-Supervisor |

## §4 关键设计决策

### 4.1 为什么使用 sched_tac 而非 sched_ext？

OLK 6.6 已 backport sched_ext（`kernel/sched/ext.c`，215KB），但选择不使用 sched_ext 的理由：

1. **BPF 依赖冲突**：sched_ext 依赖 `BPF_SYSCALL && BPF_JIT && DEBUG_INFO_BTF`（`kernel/Kconfig.preempt:138`），与 H5 纯 C LSM 原则冲突
2. **KABI 考量**：x86 默认禁用 sched_ext（`include/linux/sched.h:1663-1667` 注释明确）
3. **形式化验证**：BPF verifier 在调度路径的语义不确定性影响形式化验证可行性
4. **体系一致性**：sched_ext 的 BPF struct_ops 模型与 agentrt-linux 纯 C 体系不一致

注：vanilla Linux 6.6 主线不含 sched_ext（6.12 才合入），但 OLK 6.6 已 backport 此特性。

### 4.2 为什么使用纯 C LSM 而非 BPF LSM？

见 [07-airy-lsm-design.md](../110-security/07-airy-lsm-design.md) §1.1。

### 4.3 为什么使用 io_uring 而非新增 IPC syscall？

见 [02-ipc-protocol.md](../30-interfaces/02-ipc-protocol.md) §4.4。

## §5 编译与配置

### 5.1 airy_defconfig
```
# 纯 C LSM 模块
CONFIG_SECURITY_AIRY=y
# CONFIG_BPF_LSM is not set

# io_uring
CONFIG_IO_URING=y

# 调度
CONFIG_SCHED_DEADLINE=y
# CONFIG_SCHED_CLASS_EXT is not set

# 内存
CONFIG_LRU_GEN=y
CONFIG_LRU_GEN_ENABLED=y
CONFIG_USERFAULTFD=y
CONFIG_TRANSPARENT_HUGEPAGE=y

# Airymax syscall
CONFIG_AIRY_SYSCALL=y
```

### 5.2 Kconfig
- `security/airy/Kconfig`：`CONFIG_SECURITY_AIRY` 纯 C LSM 配置
- `security/Kconfig`：`CONFIG_LSM` 默认值添加 `airy`（位于 `capability` 之后）

## §6 相关文档

- [01-kernel.md](../20-modules/01-kernel.md) — 内核模块详细设计
- [06-iron9-shared-model.md](06-iron9-shared-model.md) — IRON-9 v3 四层模型
- [07-directory-structure.md](07-directory-structure.md) — 目录结构 v2.0
- [10-unify-design.md](10-unify-design.md) — Unify Design v1.1
- [09-kernel-agent-supervisor.md](../20-modules/09-kernel-agent-supervisor.md) — Micro-Supervisor 详细设计
- [03-capability-model.md](../110-security/03-capability-model.md) — Capability 模型
- [07-airy-lsm-design.md](../110-security/07-airy-lsm-design.md) — 纯 C LSM 设计

---

© 2025-2026 SPHARX Ltd. All Rights Reserved. | 极境内核 ALK-6.6 总体架构 | v1.0 | 2026-07-20