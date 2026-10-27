# horae-omarchy

> 专为 [Omarchy](https://omarchy.org/) 桌面环境（Arch Linux + Hyprland + Quickshell）打造的 [Horae](https://github.com/zhaohang1205/horae) GTD 极致无缝集成 AI 技能包（Skill）与自动化配置套件。

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)
[![Platform: Omarchy / Arch Linux](https://img.shields.io/badge/Platform-Omarchy%20%7C%20Arch%20Linux-brightgreen.svg)](https://omarchy.org/)
[![Horae GTD](https://img.shields.io/badge/GTD-Horae-orange.svg)](https://github.com/zhaohang1205/horae)

---

## 🌟 核心特性与丝滑体验

- ⚡ **闪念录入（Flash Capture）浮窗**：按下 `SUPER + I` 瞬间呼出居中紧凑终端小窗（800×600），支持一句话快速语法补全，回车后毫秒级保存入库并秒级退出，零上下文打扰。
- 🖥️ **一般模式（Normal TUI）呼出与聚焦**：按下 `SUPER + SHIFT + I` 平滑聚焦或启动全功能 GTD 交互视图（占屏幕 75% 居中浮窗），随时进行周回顾、看板或任务整理。
- 📋 **划选/剪贴板一键录入（Clip Capture）**：用鼠标划选目标文本后按下 `CTRL + 1`，自动完成复制并录入收件箱，伴随系统桌面气泡通知（`notify-send`）。
- 🍅 **Quattro 状态栏深度联动**：一键激活 [`horae_for_omarchy_shell`](https://github.com/zhaohang1205/horae_for_omarchy_shell) 顶部状态栏组件，内嵌动态番茄钟圆环（`PomoRing`）、到期任务卡片、隐私防窥模式（`󰈈`/`󰈉`）与悬停跑马灯。
- ⌨️ **零冲突 Shell 别名与补全**：自动配置 `hc`（快速捕获）与 `hcc`（剪贴板捕获），并注入 Bash / Zsh / Fish 全套自动补全。
- 🎨 **主题自动感应**：自动匹配 Omarchy 23 款官方主题（Catppuccin, Tokyo Night, Gruvbox 等），开箱即契合全局视觉。

---

## 📦 目录结构

```text
.
├── SKILL.md                          # 核心 AI Skill 决策树与执行规范
├── README.md                         # 项目介绍与使用指南
├── LICENSE                           # GPL-3.0 许可证
├── scripts/
│   ├── preflight.sh                  # 只读系统体检脚本（OS、工具链、快捷键、状态栏状态）
│   ├── setup-omarchy.sh              # 幂等一键配置脚本（支持分段参数）
│   ├── horae-flash                   # 闪念录入启动器
│   ├── horae-normal                  # 主应用 TUI 启动器
│   └── horae-clip                    # 剪贴板快速录入 + 桌面通知
└── references/
    ├── hyprland-bindings.md          # Hyprland Lua 快捷键规范与改键指南
    ├── bar-widget.md                 # Quickshell 状态栏组件配置参数指南
    └── troubleshooting.md            # 常见故障排查（字体乱码、通知不弹、PATH 等）
```

---

## 🚀 安装与使用

### 方式 1：作为 AI Skill 使用（推荐）

本仓库遵循标准 Agent Skill 规范，可供 **Google Antigravity**、**Gemini CLI**、**Claude Code** 等 AI 助手直接调用。

1. **克隆本仓库**：
   ```bash
   git clone https://github.com/zhaohang1205/horae-omarchy.git ~/Projects/horae-omarchy
   ```

2. **全局链接 Skill**：
   - **Antigravity / Gemini CLI**：
     ```bash
     mkdir -p ~/.gemini/config/skills
     ln -sfn ~/Projects/horae-omarchy ~/.gemini/config/skills/horae-omarchy
     ```
   - **Claude Code**：
     ```bash
     mkdir -p ~/.claude/skills
     ln -sfn ~/Projects/horae-omarchy ~/.claude/skills/horae-omarchy
     ```

3. **对 AI 助手说**：
   > “帮我在 Omarchy 系统上配置 Horae” 或 “检测我的 Horae 和 Omarchy 快捷键状态”

---

### 方式 2：纯脚本手动一键配置

无需 AI 助手，您也可以直接在终端中一键完成全套配置：

1. **运行系统体检**（只读无副作用）：
   ```bash
   ./scripts/preflight.sh
   ```

2. **执行全套配置**：
   ```bash
   # 若尚未安装 horae，可追加 --install-cargo 或 --install-npm
   ./scripts/setup-omarchy.sh --install-cargo --all
   # 或使用 npm
   ./scripts/setup-omarchy.sh --install-npm --all
   ```

---

## ⚙️ 快捷键速查

| 快捷键 | 动作 | 效果 |
|---|---|---|
| `SUPER + I` | 闪念录入 (Flash) | 居中浮窗，输入回车即保存收件箱并秒级退出 |
| `SUPER + SHIFT + I` | 应用交互 (Normal) | 居中 75% 浮窗，呼出或聚焦 Horae TUI |
| `CTRL + 1` | 划选/剪贴板录入 (Clip) | 划选文本自动复制入库 + 桌面通知提示 |
| `SUPER + ALT + V` | （可选）语音录入 | 联动 Omarchy 自带 Voxtype 离线语音转写 |

> 快捷键定义于 `~/.config/hypr/bindings.lua`，窗口规则定义于 `~/.config/hypr/hyprland.lua`。详情参见 [references/hyprland-bindings.md](references/hyprland-bindings.md)。

---

## 📄 开源许可

本项目基于 [GPL-3.0 License](LICENSE) 开源。
