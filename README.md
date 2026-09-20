# keil-embedded

> Keil MDK/uVision 嵌入式开发技能（Claude Code Skill）

适用于 **8051、STM32、ARM Cortex-M** 项目，涵盖工程配置、命令行编译、pyocd 烧录、GDB 调试、链接脚本与启动文件。

## 功能一览

| 能力 | 说明 |
|---|---|
| 🏗️ **工程配置** | `*.uvprojx` 解析、启动文件选型（F103 中/大/小容量、F407）、`*.sct` 链接脚本 |
| ⚡ **命令行编译** | `UV4.exe -b` 无界面编译，退出码判断成败 |
| 🔥 **pyocd 烧录** | CMSIS-DAP / J-Link / ST-Link 通用，自动跳过内容一致固件 |
| 🐛 **GDB 调试** | pyocd gdbserver + arm-none-eabi-gdb，断点/单步/变量查看 |
| ⚠️ **CubeMX 边界** | 明确哪些文件会被 CubeMX 覆盖，规避踩坑 |
| 🔍 **常见错误速查** | L6218E / L6406E / L6200E 等典型编译错误对照 |

## 触发方式

- **自动触发**：用户提到 Keil、MDK、uVision、UV4、armcc、armclang、编译、烧录、下载固件、调试、`.uvprojx`、`.sct` 时
- **显式调用**：`/keil-embedded`

## 快速参考

```bash
# 命令行编译
"/d/Keil5/UV4/UV4.exe" -b <工程.uvprojx> -j0 -o /tmp/keil.log   # 退出码 0=成功

# pyocd 烧录
pyocd list                                    # 查已连调试器
pyocd flash -t <target> <xx.hex>              # 烧录

# GDB 调试
pyocd gdbserver -t <target> -p 3333 &
arm-none-eabi-gdb <xx.axf> -ex "target remote localhost:3333" -ex "monitor reset halt"
```

## 目录

- `SKILL.md` — 技能定义与开发文档

## 转载说明

本项目为个人开发环境的 Claude Code 技能，也欢迎作为参考模板使用。