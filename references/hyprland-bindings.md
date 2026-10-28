# Hyprland 快捷键与窗口规则指南 (Omarchy)

Omarchy 桌面环境使用 Lua 作为 Hyprland 的配置语言。用户级快捷键位于 `~/.config/hypr/bindings.lua`，窗口行为规则位于 `~/.config/hypr/hyprland.lua`。

## 1. 核心快捷键架构

Horae 与 Omarchy 系统结合时，推荐配置三组核心交互方式：

| 快捷键 | 动作 | 原理与调用目标 | 体验特色 |
|---|---|---|---|
| `SUPER + I` | 闪念录入模式 (Flash) | `{ tui = "horae-flash", focus = true }` | 居中浮动小窗，快速输入标题/标签/周期，回车后瞬间保存入库并秒级关闭，零上下文干扰 |
| `SUPER + SHIFT + I` | 应用一般模式 (Normal) | `{ tui = "horae-normal", focus = true }` | 呼出或聚焦（已打开时自动切到前台）完整的 GTD 交互视图 |
| `CTRL + 1` | 剪贴板快速捕获 (Clip) | `~/.local/bin/horae-clip` | 自动复制鼠标划选内容（或剪贴板文本）至收件箱，并弹出 `notify-send` 确认提示 |
| `SUPER + ALT + V` | （可选）语音录入待办 | `~/.local/bin/horae-voice` | 调用 Omarchy 内置的 Voxtype 离线 Whisper 模型，自然语言抽取时间与优先级 |

> 上表为首选键位。在原生 Omarchy 上 `SUPER + I` 与 `SUPER + SHIFT + I` 默认被输入法切换占用，实际落地键位以脚本运行结束时的报告为准（见下方「快捷键冲突排查」）。

## 2. bindings.lua 配置示例

在 `~/.config/hypr/bindings.lua` 中追加：

```lua
-- ============================================================================
-- Horae GTD 快捷键
-- ============================================================================

-- 闪念录入模式：按下立即弹出小型录入框
o.bind("SUPER + I", "Horae (Flash)", { tui = "horae-flash", focus = true })

-- 一般模式：按下平滑呼出或聚焦完整交互视图
o.bind("SUPER + SHIFT + I", "Horae (Normal)", { tui = "horae-normal", focus = true })

-- 剪贴板录入：将系统剪切板内容瞬间录入为任务
o.bind("CTRL + 1", "Horae clip capture", os.getenv("HOME") .. "/.local/bin/horae-clip")
```

### 快捷键冲突排查

在添加快捷键前，可运行以下命令检查是否有按键冲突：
```bash
omarchy menu keybindings --print | grep "SUPER + I"
```

> [!IMPORTANT]
> `omarchy menu keybindings --print` 需要运行中的 Hyprland 会话。`setup-omarchy.sh` 不依赖它：会同时扫描 `~/.config/hypr/*.lua` 与 `$OMARCHY_PATH/default/hypr/**/*.lua`（剥离 `--` 注释、排除 `*.bak*`），把 `"SUPER + SHIFT + I"` 与 `"SHIFT SUPER + I"` 这类不同写法归一化后比对。因此即使在没有图形会话的终端里执行，也能发现 Omarchy 默认绑定。
>
> 扫描覆盖 `o.bind` / `o.rebind` / `o.bind_toggle` / `o.bind_hold` / `hl.bind` 五种写法 —— 只认前两种会漏掉 `SUPER + SHIFT + SPACE`（开关顶栏）、`F9`（按住说话）这类绑定。
>
> 若索引为空（既读不到运行时绑定表，也读不到任何 lua 配置），脚本**不会**当作"没有冲突"继续绑定，而是跳过键位配置只写窗口规则 —— 检测不到不等于不存在。

若发现冲突，脚本会按候选链自动降级，无需手工处理：

| 动作 | 候选链 |
|---|---|
| Flash | `SUPER + I` → `SUPER + ALT + I` → `SUPER + ALT + C` |
| Normal | `SUPER + SHIFT + I` → `SUPER + ALT + SHIFT + I` → `SUPER + ALT + N` |
| Clip | `CTRL + 1` → `SUPER + ALT + C` → `SUPER + ALT + SHIFT + C` |

想固定某个键位，用环境变量覆盖（冲突时脚本会报错并跳过，不会强行占用）：
```bash
HORAE_FLASH_KEY="SUPER + ALT + I" ./scripts/setup-omarchy.sh --shortcuts
```

## 3. hyprland.lua 浮动与尺寸规则

为了确保弹窗不会以平铺（Tile）方式破坏当前桌面的窗口排布，在 `~/.config/hypr/hyprland.lua` 中配置窗口规则：

```lua
-- Horae Flash Capture: 紧凑居中浮动小窗 (800x600)
o.window("^org\\.omarchy\\.horae-flash$", {
	float = true,
	center = true,
	size = { 800, 600 },
})

-- Horae TUI: 居中浮动视窗 (占屏幕 75% 宽高，兼顾沉浸感与空间感)
o.window("^(org\\.omarchy\\.horae|org\\.omarchy\\.horae-normal)$", {
	float = true,
	center = true,
	size = { "(monitor_w*0.75)", "(monitor_h*0.75)" },
})
```

## 4. 辅助启动脚本说明

`~/.local/bin/` 目录下的辅助脚本封装了 `HORAE_BIN` 探测与终端隔离启动：
- `horae-flash`：`exec "$HORAE_BIN" -f "$@"`
- `horae-normal`：`exec "$HORAE_BIN" -n "$@"`
- `horae-clip`：优先将鼠标划选的文本（Wayland Primary Selection）复制至主剪贴板，随后执行 `horae capture --clip "$@"` 并通过 `notify-send` 桌面通知显示结果。
