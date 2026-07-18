Copyright (c) 2025-2026 SPHARX Ltd. All Rights Reserved.

# agentrt-linux（AirymaxOS）测试体系设计
> **文档定位**：agentrt-linux（AirymaxOS）测试工程体系主索引（KUnit + kselftest + 集成测试 + CI 流水线）\
> **文档版本**：v1.0\
> **最后更新**：2026-07-17\
> **上级文档**：[AirymaxOS 总览](../README.md)\
> **同源映射**：agentrt 7 层自动化验证 + Linux 6.6 测试框架（KUnit/kselftest/动态分析）\
> **理论根基**：Linux 内核测试体系 + Airymax E-8 可测试性 + A-4 完美主义 + SSoT v2 CI 强制校验

---

## 1. 模块概述

agentrt-linux 测试体系是工程标准可执行性的核心保障。它继承 Linux 内核 30+ 年沉淀的多层测试哲学（KUnit 白盒 + kselftest 系统级 + 动态分析 + 静态分析 + 覆盖度量），并在其上扩展智能体操作系统专属的sched_tac 调度测试、IPC 零拷贝测试、[SC] 头文件逐字节校验、Agent 行为契约测试、模糊测试、形式化验证等。本目录覆盖四方面职责：

1. **KUnit 单元测试**：白盒单元测试，毫秒级执行，验证单函数行为契约（含 A-UEF 错误码、A-ULP 128B 记录格式、A-UCS 配置加载）。
2. **kselftest 系统级测试**：从用户态测试内核特性，重点覆盖sched_tac 调度类（`SCHED_DEADLINE` / `SCHED_FIFO` / `EEVDF`）、io_uring IORING_OP_URING_CMD 零拷贝、纯 C LSM 钩子。
3. **集成测试**：跨子仓跨模块的端到端测试，验证 IRON-9 v3 四层模型的 [SC] 头文件逐字节一致、agentrt ↔ agentrt-linux 同源 API 互操作。
4. **CI 流水线**：`sc-dual-ci.yml`（[SC] 逐字节校验）+ `ssot-validate.yml`（四层模型归属校验）+ 覆盖率门槛（kernel 90% / security 95%，关键路径 100%）。

### 1.1 测试体系分层

| 层级 | 类型 | 工具 | 时间尺度 | 范围 |
|------|------|------|---------|------|
| L1 | 白盒单元测试 | KUnit | 毫秒级 | 单函数 |
| L2 | 系统级测试 | kselftest | 秒级 | 子系统 |
| L3 | 内核自检 | `lib/test_*` | 秒级 | 内核特性 |
| L4 | 动态分析 | KASAN/KFENCE/UBSAN/KCSAN/lockdep/kmemleak | 运行时 | 内存/并发 |
| L5 | 静态分析 | Sparse/Smatch/Coccinelle/clang-analyzer | 编译时 | 代码模式 |
| L6 | 覆盖度量 | KCOV/gcov | 运行时 | 代码覆盖 |
| L7 | ftrace 启动自检 | ftrace + ring buffer | 启动时 | 跟踪 |
| **L8** | **Agent 契约测试** | **agentrt-linux 专属** | **秒级** | **Agent 行为** |
| **L9** | **模糊测试** | **syzkaller + agentrt-linux 扩展** | **小时级** | **输入边界** |
| **L10** | **形式化验证** | **seL4 风格 + TLA+** | **天级** | **关键路径** |

### 1.2 agentrt-linux 专属测试

- **sched_tac 调度测试**：验证 `SCHED_DEADLINE` / `SCHED_FIFO` / `EEVDF` 三层调度类组合的 Agent 8 态生命周期映射（INACTIVE→SPAWNING→READY→RUNNING→BLOCKED→STOPPING→STOPPED→DEAD）
- **IPC 零拷贝测试**：验证 `IORING_OP_URING_CMD` 的 fastpath 性能（~160ns）与 page flipping 路径未启用
- **[SC] 头文件逐字节校验**：`sc-dual-ci.yml` 双端 diff 10 个 `include/airymax/*.h` 头文件
- **纯 C LSM 验证**：验证 `airy_lsm` 钩子注册、capability 缓存命中、BPF LSM 未启用
- **alloc_pages + mmap 验证**：验证共享内存未使用 `dma_alloc_coherent`

---

## 2. 技术选型声明

agentrt-linux v1.0 测试体系在内核调度、IPC 传输、安全钩子、内存分配与同源代码共享五个维度遵循 [AirymaxOS 总览](../README.md) §2 的不可妥协基线。测试体系是五大选型的**验证守护者**——CI 流水线在每次提交时强制校验五大选型未被偏离，任何回归均阻塞合并。五个维度的选型在本目录的具体落地如下：

| # | 技术维度 | 选定方案 | 明确不采用的方案 | 在本目录的落地 |
|---|---------|---------|----------------|--------------|
| 1 | **内核调度** | **sched_tac**：复用 Linux 6.6 原生 `SCHED_DEADLINE` / `SCHED_FIFO` / `EEVDF` 调度类 | **不使用 sched_ext**（不引入 eBPF 调度器、不使用 `SCHED_EXT=7` 调度类） | kselftest `sched/` 子目录验证sched_tac 三层调度类组合；CI 断言 `CONFIG_SCHED_EXT` 未启用 |
| 2 | **IPC 零拷贝** | **IORING_OP_URING_CMD**：通过 io_uring 命令操作码实现内核↔用户态零拷贝传输 | **不使用 page flipping**（不交换物理页、不破坏内存布局稳定性） | IPC fastpath 性能基准测试（~160ns SLA）；CI 断言 page flipping 代码路径未编译 |
| 3 | **安全钩子** | **纯 C LSM**：以纯 C 实现的 `airy_lsm` 通过 `security_hook_list` 注册 | **不使用 BPF LSM**（不依赖 BPF LSM 框架、不通过 eBPF 程序挂载安全钩子） | KUnit 测试纯 C LSM 钩子注册与 capability 缓存；CI 断言 `CONFIG_BPF_LSM` 未启用 |
| 4 | **内存分配** | **alloc_pages + mmap**：通过 `alloc_pages` 分配物理页后 `vm_map_pages` / `remap_pfn_range` 映射 | **不使用 DMA 一致性内存**（不调用 `dma_alloc_coherent`、不依赖硬件一致性缓存） | KUnit 测试 `alloc_pages + mmap` 内存路径；CI 断言 `dma_alloc_coherent` 未在共享内存路径调用 |
| 5 | **同源代码共享** | **IRON-9 v3 四层模型**：[SC] 共享契约层 + [SS] 语义同源层 + [IND] 独立实现层 + [DSL] 降级生存层 | （v2 三层模型升级为 v3 四层模型，新增 [DSL] 降级生存层） | `sc-dual-ci.yml` 双端逐字节校验 10 个 [SC] 头文件；`ssot-validate.yml` 校验四层归属一致性 |

### 2.1 IRON-9 v3 四层模型在测试体系的归属

| 技术点 | [SC] | [SS] | [IND] | [DSL] | 落地文档 |
|--------|:----:|:----:|:-----:|:-----:|---------|
| [SC] 逐字节校验测试 | ● | — | — | — | [01-kunit-framework.md](01-kunit-framework.md) |
| sched_tac 调度测试 | — | — | ● | — | [02-kselftest.md](02-kselftest.md) |
| IPC 零拷贝性能测试 | — | — | ● | — | [02-kselftest.md](02-kselftest.md) |
| [DSL] 降级块自检 | — | — | — | ● | [01-kunit-framework.md](01-kunit-framework.md) |
| 同源 API 互操作测试 | — | ● | — | — | [01-kunit-framework.md](01-kunit-framework.md) |

---

## 3. 文档索引

本目录现有文档（v1.0 范围）：

```
80-testing/
├── README.md                       # 本文件（v1.0）
├── 01-kunit-framework.md           # KUnit 单元测试框架 + [SC] 契约测试
└── 02-kselftest.md                 # kselftest 系统级测试 + sched_tac 调度测试 + IPC 零拷贝测试
```

| # | 文档 | 版本 | 内容概要 |
|---|------|------|---------|
| — | [README.md](README.md) | v1.0 | 测试体系主索引（本文件） |
| 1 | [01-kunit-framework.md](01-kunit-framework.md) | v1.0 | KUnit 白盒单元测试框架、A-UEF 错误码契约测试、A-ULP 128B 记录格式测试、[SC] 头文件一致性测试、[DSL] 降级块自检 |
| 2 | [02-kselftest.md](02-kselftest.md) | v1.0 | kselftest 系统级测试、sched_tac 调度类测试（`SCHED_DEADLINE` / `SCHED_FIFO` / `EEVDF`）、IPC 零拷贝性能基准（~160ns）、纯 C LSM 钩子验证 |

### 3.1 后续规划文档（1.0.1 版本）

以下文档在 1.0.1 版本完成，不在 v1.0 范围内：

- `03-kernel-selftests.md`：`lib/test_*` 内核自检
- `04-dynamic-analysis.md`：动态分析（KASAN/KFENCE/UBSAN/KCSAN/lockdep）
- `05-static-analysis.md`：静态分析（Sparse/Smatch/Coccinelle）
- `06-coverage-metrics.md`：覆盖度量（KCOV/gcov）+ 覆盖率门槛
- `07-ftrace-selftest.md`：ftrace 启动自检
- `08-agent-contract-testing.md`：agentrt-linux 专属 Agent 行为契约测试
- `09-fuzz-testing.md`：模糊测试（syzkaller + 扩展）
- `10-formal-verification.md`：形式化验证（seL4 风格 + TLA+）

---

## 4. Airymax Unify Design 映射

本目录与 Airymax Unify Design 五模块（A-UEF/A-ULP/A-UCS/A-ULS/A-IPC）的关系详见 [AirymaxOS 总览](../README.md) §5。测试体系主要承载 **sched_tac 调度测试**（A-ULS 调度）、**IPC 零拷贝测试**（A-IPC）、**[SC] 头文件逐字节校验**（IRON-9 v3 [SC] 层），其余模块为辅助关系。

| Unify 模块 | 关系 | 在本目录的体现 |
|-----------|------|--------------|
| **A-UEF** | **核心** | A-UEF 错误码 / Fault 码空间的 KUnit 契约测试；[SC] `error.h` 双端逐字节校验 |
| **A-ULP** | 辅助 | A-ULP 128B 日志记录格式的 KUnit 测试；[SC] `log_types.h` 双端逐字节校验 |
| **A-UCS** | 辅助 | A-UCS 配置加载与 RCU 热重载的 KUnit 测试；`airy_defconfig` 锁定五大选型的回归测试 |
| **A-ULS** | **核心** | sched_tac 调度测试——验证 Agent 8 态生命周期与 Linux 进程状态的映射；纯 C LSM 钩子注册与 capability 缓存测试；Micro-Supervisor 冷酷执法 + Macro-Supervisor 温情裁决的双层测试 |
| **A-IPC** | **核心** | IPC 零拷贝测试——验证 `IORING_OP_URING_CMD` fastpath 性能（~160ns SLA）、Ring 生命周期解耦、离线缓存校验、Reconciliation 三原则 |

### 4.1 Unify Design 权威源引用

- A-UEF 总纲：[../10-architecture/10-unify-design.md](../10-architecture/10-unify-design.md) §4
- A-ULP 总纲：[../10-architecture/10-unify-design.md](../10-architecture/10-unify-design.md) §5
- A-ULS 总纲：[../10-architecture/10-unify-design.md](../10-architecture/10-unify-design.md) §7
- A-IPC 总纲：[../10-architecture/10-unify-design.md](../10-architecture/10-unify-design.md) §8
- [SC] 契约校验：[../30-interfaces/08-sc-error-contract.md](../30-interfaces/08-sc-error-contract.md) + [../30-interfaces/09-sc-log-types-contract.md](../30-interfaces/09-sc-log-types-contract.md)

---

## 5. 相关文档

- [AirymaxOS 总览](../README.md)：v1.0 技术选型声明 + 20 子目录索引
- [架构设计层](../10-architecture/README.md)：Unify Design 总纲 + IRON-9 v3 四层模型
- [10-architecture/10-unify-design.md](../10-architecture/10-unify-design.md)：Airymax Unify Design 总纲（SSoT）
- [10-architecture/06-iron9-shared-model.md](../10-architecture/06-iron9-shared-model.md)：IRON-9 v3 四层模型（SSoT）
- [20-modules/08-tests-linux.md](../20-modules/08-tests-linux.md)：tests-linux 子仓设计
- [30-interfaces/08-sc-error-contract.md](../30-interfaces/08-sc-error-contract.md)：A-UEF [SC] error.h 契约
- [30-interfaces/09-sc-log-types-contract.md](../30-interfaces/09-sc-log-types-contract.md)：A-ULP [SC] log_types.h 契约
- [30-interfaces/10-sc-sched-extension.md](../30-interfaces/10-sc-sched-extension.md)：sched_tac [SC] sched.h 契约
- [50-engineering-standards/09-ssot-registry.md](../50-engineering-standards/09-ssot-registry.md)：SSoT v2 单一权威源注册表
- [70-build-system/03-ci-cd-pipeline.md](../70-build-system/03-ci-cd-pipeline.md)：CI/CD 流水线（`sc-dual-ci.yml` + `ssot-validate.yml`）
- [110-security/README.md](../110-security/README.md)：安全测试（纯 C LSM + capability）
- [170-performance/README.md](../170-performance/README.md)：性能工程（IPC fastpath SLA）

---

## 6. 参考材料

- Linux 6.6 `lib/kunit/`（KUnit 框架）
- Linux 6.6 `tools/testing/selftests/`（kselftest，含 `sched/` 子目录）
- Linux 6.6 `Documentation/dev-tools/testing-overview.rst`（测试工具全景）
- Linux 6.6 `lib/test_*.c`（内核自检样本）
- seL4 项目（形式化验证参考，capDL / l4v 工具链）

---

## 7. 版本历史

| 版本 | 日期 | 变更 |
|------|------|------|
| 0.1.1 | 2026-07-13 | 初始版本，README + 01 + 02 文档奠基，确立 KUnit/kselftest 核心机制 |
| v1.0 | 2026-07-17 | 升级为 v1.0：新增sched_tac / IORING_OP_URING_CMD / 纯 C LSM / alloc_pages + mmap / IRON-9 v3 四层模型五大技术选型声明（测试体系作为验证守护者）；新增 Airymax Unify Design 映射（sched_tac 调度测试 + IPC 零拷贝测试 + [SC] 逐字节校验为核心）；文档索引对齐实际目录文件 |

---

> **文档结束** | agentrt-linux 测试体系设计 v1.0 | 维护者：开源极境工程与规范委员会 | "From data intelligence emerges."
