# 中大冯班计组实验一

这是面向初学者的 YatCPU 单周期 CPU 实验一开源学习资料。

本仓库把“要学什么”“代码为什么这样写”“如何运行测试”“如何用波形定位问题”放在一起，适合作为实验一的学习笔记、代码参照和后续实验的基础版本。

## 你将在这里学到什么

- Scala、sbt 和 Chisel 项目的基本结构；
- RV32I 指令的 opcode、寄存器和立即数；
- 单周期 CPU 的取指、译码、执行、访存、写回数据通路；
- Chisel 中的 `Module`、`IO`、`RegInit`、`when`、`Mux` 和 `MuxLookup`；
- PC 顺序执行和跳转更新；
- load/store 的地址计算、字节访问和写回；
- ScalaTest、ChiselTest、Verilator 和 VCD 波形调试；
- FIRRTL `RefNotInitializedException` 等常见错误的定位方法。

## 推荐学习顺序

1. 阅读 [`docs/00-学习路线.md`](docs/00-学习路线.md)，了解实验全貌。
2. 阅读 [`docs/实验一基础知识点与代码对应.md`](docs/实验一基础知识点与代码对应.md)，把知识点和文件对应起来。
3. 阅读 [`docs/实验一思路与分析.md`](docs/实验一思路与分析.md)，理解四个实验标记区是如何推导出来的。
4. 对照以下四个核心文件阅读代码：
   - `src/main/scala/riscv/core/InstructionFetch.scala`
   - `src/main/scala/riscv/core/InstructionDecode.scala`
   - `src/main/scala/riscv/core/Execute.scala`
   - `src/main/scala/riscv/core/CPU.scala`
5. 阅读 `src/test/scala/riscv/singlecycle/CPUTest.scala`，先运行 `ByteAccessTest`，再运行全部测试。
6. 测试通过后，用 GTKWave 打开生成的 `test_run_dir/.../TestTopModule.vcd`，观察 PC、内存写使能和寄存器结果。

## 实验一完成内容

实验一在已有 CPU 框架中补齐四类逻辑：

| 模块 | 完成内容 |
|---|---|
| InstructionFetch | 有效取指时，跳转取目标地址，否则 PC 加 4 |
| InstructionDecode | 生成 ALU 操作数、访存和写回来源控制信号 |
| Execute | 根据控制信号选择寄存器、立即数或 PC 送入 ALU |
| CPU | 连接取指、译码、寄存器堆、执行、访存和写回 |

验证重点是 `ByteAccessTest`、`FibonacciTest` 和 `QuicksortTest`。参考实现已在 Windows 环境使用 JDK 17、Scala 2.13.10、Chisel 3.6.0、ChiselTest 0.6.0 和 Verilator 4.202 验证通过。

## 环境准备

- JDK 17；
- sbt 1.9.x 或 IDEA 的 sbt 集成；
- Scala 2.13.10（由 `build.sbt` 指定）；
- Chisel 3.6.0、ChiselTest 0.6.0（由 `build.sbt` 下载）；
- 与 ChiselTest 0.6.0 兼容的 Verilator 4.x，参考环境使用 Verilator 4.202；
- GTKWave（只用于打开 `.vcd` 波形文件，不参与 Scala 编译）。

Windows 下建议把仓库放在不含中文的路径，例如 `D:\yatcpu-lab1-reference`，可以减少 Verilator、Make 和 FIRRTL 的路径编码问题。

## 在 IDEA 中运行

1. 用 IDEA 打开仓库根目录中的 `build.sbt`，选择 **Open as Project** 或以 sbt 项目导入。
2. 等待依赖下载和索引建立完成。
3. 运行 `src/test/scala/riscv/singlecycle/CPUTest.scala` 中的测试。
4. 如果只想快速验证实验一，先运行 `ByteAccessTest`。

也可以在仓库根目录使用 sbt：

```text
sbt test
```

测试通过后，VCD 会出现在 `test_run_dir` 对应测试目录中。用 GTKWave 打开 `TestTopModule.vcd`，再加入 `clock`、指令地址、内存接口和寄存器调试信号即可观察执行过程。

## 重要说明

本仓库的代码是针对 YatCPU 当前模块接口、参数和依赖版本完成的实验一参照实现。它可以帮助你迁移思路，但不能保证不经修改就适用于任意其他 CPU 项目；迁移时必须先比较数据宽度、Bundle 字段、内存接口、PC 初始地址和工具版本。

源码文件保留了原项目的版权与许可证说明。本仓库中的中文教学文字用于开源学习和交流，欢迎在保留出处的基础上补充勘误与改进示例。

