---
name: keil-embedded
description: >-
  Keil MDK/UVision 嵌入式开发技能，适用于 8051、STM32、ARM Cortex-M 项目，涵盖工程配置、命令行编译、
  pyocd 烧录、GDB 调试、链接脚本与启动文件。当用户提到 Keil、MDK、uVision、UV4、armcc、armclang、
  编译、烧录、下载固件、调试、.uvprojx、.sct 时自动触发，也兼容 /keil-embedded 显式调用。
metadata:
  allowed-tools:
    - Bash(pyocd, gdb, UV4 命令行, pip): true
    - Read: true
    - Edit: true
    - Write: true
---

# Keil 嵌入式开发

## 工程文件
| 文件 | 说明 |
|---|---|
| `*.uvprojx` | Keil 项目文件（XML）|
| `startup_xxx.s` | 启动文件，按芯片选（STM32F103 中容量=xb、大容量=xe、F407=407xx）|
| `*.sct` | 分散加载链接脚本 |

## 命令行编译
```bash
"/d/Keil5/UV4/UV4.exe" -b <工程.uvprojx> -j0 -o /tmp/keil.log   # 退出码 0=成功
```

## pyocd 烧录（CMSIS-DAP/J-Link/ST-Link 通用）
```bash
pyocd list                                 # 查已连调试器
pyocd list --targets | grep -i <芯片系列>    # 查 target，如 stm32f103 → stm32f103c8
pyocd flash -t <target> <xx.hex>           # 烧录（内容一致自动跳过）
pyocd flash -t <target> -e chip <xx.hex>   # 强制整片擦除重烧
```

## GDB 调试
```bash
pyocd gdbserver -t <target> -p 3333 &
arm-none-eabi-gdb <xx.axf> -ex "target remote localhost:3333" -ex "monitor reset halt" -ex "break main"
# 交互：run / stepi / continue / print 变量
```

## CubeMX 边界（重要）
`Core/` 除 `USER CODE` 区域外、`*.ioc` 会被 CubeMX 覆盖，只能改 `Drivers/` 或 USER CODE 区；改硬件配置走 CubeMX。

## 常见编译错误
- L6218E Undefined symbol → 缺 `.c` 文件未加入工程
- L6406E No space in execution regions → ROM/RAM 不足，查 `.sct` 与芯片选型
- L6200E multiply defined → 全局符号重复定义
- #68-D 符号性转换 → 有/无符号类型不匹配

## Define 宏（Options→C/C++→Define，按工程类型）
- HAL/CubeMX：`USE_HAL_DRIVER,STM32F103xE`（芯片宏按型号：F103C8→F103xE、F407→F407xx）
- 标准外设库：`USE_STDPERIPH_DRIVER,STM32F10X_HD`

## .sct 默认布局示例
```
LR_IROM1 0x08000000 0x00080000  {
  ER_IROM1 0x08000000 0x00080000  {
   *.o (RESET, +First)
   *(InRoot$$Sections)
   .ANY (+RO)
  }
  RW_IRAM1 0x20000000 0x00010000  {
   .ANY (+RW +ZI)
  }
}
```

## 生成 bin / hex
Options→Output 勾 "Create HEX File"。bin：User 选项卡 After Build 加
`fromelf --bin -o "$L@L.bin" "#L"`

## Keil/armcc 扩展语法
| 语法 | 用途 |
|---|---|
| `__attribute__((at(0x20001000)))` | 变量绝对定位 |
| `__align(4)` | 对齐 |
| `__packed` | 紧凑结构体 |
| `__weak` | 弱定义 |
| `__inline` | 内联 |