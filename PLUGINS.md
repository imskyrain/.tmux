# Tmux Plugins 使用指南

> 所有插件的安装状态、快捷键和常见用法

---

## 插件列表概览

| # | 插件 | 功能 | 状态 |
|---|------|------|------|
| 1 | tmux-sensible | 合理的默认配置 | ✅ |
| 2 | tmux-themepack | 主题 (powerline/block/blue) | ✅ |
| 3 | tmux-cpu | 状态栏显示 CPU 占用 | ✅ |
| 4 | tmux-battery | 状态栏显示电池信息 | ✅ |
| 5 | tmux-resurrect | 手动保存/恢复会话 | ✅ |
| 6 | tmux-continuum | 自动保存会话 | ✅ |
| 7 | tmux-yank | 复制模式选中内容直接写入系统剪贴板 | ✅ |
| 8 | vim-tmux-navigator | Vim 和 tmux 之间 Ctrl-hjkl 无缝切换窗格 | ✅ |
| 9 | tmux-fzf | 在 tmux 内启动 fzf 模糊查找器 | ✅ |
| 10 | tmux-thumbs | 屏幕上可见 token 快速复制 | ✅ |
| 11 | tmux-sidebar | 当前目录树状图侧栏 | ✅ |

---

## 插件管理快捷键 (TPM)

| 快捷键 | 作用 |
|--------|------|
| `<prefix> I` | 安装新插件 |
| `<prefix> u` | 更新所有插件 |
| `<prefix> Alt-u` | 卸载已删除的插件 |

---

## 1. tmux-sensible

**作用**：自动应用一组公认合理的 tmux 默认配置，省得手动写。

主要效果（静默生效，无需手动操作）:

- `escape-time` 降至 50ms，按 Esc 响应更快
- `history-limit` 至少 10000（你已设为 50000，优先级更高）
- 自动开启 `focus-events`
- 终端标题自动刷新

无快捷键，开启即可。

---

## 2. tmux-themepack (powerline/block/blue)

**作用**：提供美观的 Powerline 风格状态栏主题。

### 当前主题色调

```
状态栏左侧:
  [会话名] [用户名] [窗口:窗格]   ← 深蓝色块状渐变

状态栏右侧:
  [时间] [CPU / 电池] [主机名]   ← 灰色块状渐变
```

### 切换主题

编辑 `.tmux.conf.local`，修改一行：

```bash
set -g @theme-pack 'powerline/block/blue'
# 可选主题举例:
#   powerline/block/green
#   powerline/block/red
#   powerline/double/blue
#   powerline/simple/blue
#   basic/dark
```

完整列表参考: https://github.com/jimeh/tmux-themepack

### 自定义状态栏内容

```bash
# 左侧三块
set -goq @themepack-status-left-area-left-format "#S"          # 会话名
set -goq @themepack-status-left-area-middle-format "#(whoami)" # 用户名
set -goq @themepack-status-left-area-right-format "#I:#P"      # 窗口:窗格

# 右侧三块
set -goq @themepack-status-right-area-left-format "%H:%M:%S"   # 时间
set -goq @themepack-status-right-area-middle-format "CPU:#{cpu_percentage} BAT:#{battery_percentage}#{battery_status}"
set -goq @themepack-status-right-area-right-format "#H"        # 主机名
```

> 注意: 因为 themepack 接管了 status-left / status-right，Oh My Tmux 的 `tmux_conf_theme` 已设为 `disabled`。

---

## 3. tmux-cpu

**作用**：在状态栏显示当前 CPU 占用百分比。

### 状态栏变量

| 变量 | 显示内容 |
|------|----------|
| `#{cpu_percentage}` | CPU 占用百分比 (如 `42%`) |
| `#{cpu_icon}` | 根据占用高低自动选图标 |

当前配置已将 `#{cpu_percentage}` 放入右侧中间段。

---

## 4. tmux-battery

**作用**：在状态栏显示电池信息。

### 状态栏变量

| 变量 | 显示内容 |
|------|----------|
| `#{battery_percentage}` | 电池百分比 (如 `87%`) |
| `#{battery_status}` | 充电/放电符号 (`↑` / `↓`) |
| `#{battery_icon}` | 电池图标 |
| `#{battery_bar}` | 电池条形图 |

当前配置显示 `#{battery_percentage}#{battery_status}`。

---

## 5. tmux-resurrect

**作用**：手动保存和恢复 tmux 会话（所有窗口、窗格布局、命令历史、Even 正在运行的程序）。

### 快捷键

| 快捷键 | 作用 |
|--------|------|
| `<prefix> Ctrl-s` | 保存当前所有会话 |
| `<prefix> Ctrl-r` | 恢复上次保存的会话 |

### 恢复的内容

- 会话名和窗口布局
- 窗格的当前路径
- 窗格内容 (因为开启了 `@resurrect-capture-pane-contents`)
- vim / neovim 的打开文件（插件内置支持）
- 常见程序: ssh, man, less, more, vi, vim, nvim

### 常见场景

```bash
# 重启电脑前
<prefix> Ctrl-s         # 保存

# 重启后启动 tmux
tmux
<prefix> Ctrl-r         # 恢复 → 回到重启前的状态
```

---

## 6. tmux-continuum

**作用**：在 tmux-resurrect 基础上，**自动**每隔一段时间保存会话，并在 tmux 启动时自动恢复。

### 当前配置

```bash
set -g @continuum-restore 'on'   # 启动 tmux 时自动恢复
```

### 效果

- 默认每 15 分钟自动保存一次
- 启动 tmux 时自动恢复上次的会话
- 无需手动操作

### 修改保存间隔

```bash
set -g @continuum-save 'on'
set -g @continuum-save-interval '5'   # 每 5 分钟保存一次
```

---

## 7. tmux-yank

**作用**：在复制模式中选中文本后，自动写入系统剪贴板（macOS 用 pbcopy）。

### 快捷键

| 快捷键 | 场景 |
|--------|------|
| `y` (复制模式中) | 复制选中内容到系统剪贴板 |
| `Y` (复制模式中) | 复制当前行到系统剪贴板 |
| `<prefix> y` | 复制上一条命令到剪贴板 |
| `<prefix> shift-y` | 复制上一条命令的输出到剪贴板 |

### 典型使用流程

```
<prefix> [          → 进入复制模式
/pattern            → 搜索定位
v                   → 开始选择
...移动...
y                   → 复制到系统剪贴板，直接可以 Cmd+V
```

> 这个插件让之前需要 Option+拖动 才能复制到系统剪贴板的痛点彻底消失了。

---

## 8. vim-tmux-navigator

**作用**：在 Vim 窗口内部和 tmux 窗格之间，用统一的 `Ctrl-h/j/k/l` 无缝切换，不需要先离开 Vim 再按 `<prefix>`。

### 快捷键

| 快捷键 | 作用 |
|--------|------|
| `Ctrl-h` | 切换到左边 (Vim split 或 tmux 窗格) |
| `Ctrl-j` | 切换到下边 |
| `Ctrl-k` | 切换到上边 |
| `Ctrl-l` | 切换到右边 |
| `Ctrl-\` | 切换到上一个 (Vim split 或 tmux 窗格) |

### Vim 侧配置（如果用 Vim）

如果你用 Vim，需要安装对应插件:

```vim
" 使用 vim-plug:
Plug 'christoomey/vim-tmux-navigator'

" 使用 Lazy.nvim:
{ "christoomey/vim-tmux-navigator", keys = {
    { "n", "<C-h>", "<cmd>TmuxNavigateLeft<cr>" },
    { "n", "<C-j>", "<cmd>TmuxNavigateDown<cr>" },
    { "n", "<C-k>", "<cmd>TmuxNavigateUp<cr>" },
    { "n", "<C-l>", "<cmd>TmuxNavigateRight<cr>" },
    { "n", "<C-\\>", "<cmd>TmuxNavigatePrevious<cr>" },
}}
```

### 典型场景

```
┌─────────────────┬──────────────┐
│  vim            │  terminal    │
│  (编辑代码)      │  (运行测试)   │
└─────────────────┴──────────────┘

在 vim 中按 Ctrl-l → 光标直接跳到右边的 terminal 窗格
在 terminal 中按 Ctrl-h → 光标跳回 vim

无需碰 <prefix> 键
```

---

## 9. tmux-fzf

**作用**：在 tmux 内部启动 fzf 模糊查找器，用于快速切换会话、窗口、窗格或运行 fzf 子命令。

### 快捷键

| 快捷键 | 作用 |
|--------|------|
| `<prefix> F` | 启动 fzf 菜单（交互式选择） |

### fzf 菜单内的选项

启动后屏幕会出现一个交互式列表，默认包含:

```
> Session          ← 切换会话
  Window           ← 切换窗口
  Pane             ← 切换窗格
  Command History  ← 搜索历史命令
```

用上下箭头或输入关键词过滤，回车确认。

### 前提条件

需要系统安装 `fzf`：

```bash
brew install fzf
```

---

## 10. tmux-thumbs

**作用**：激活后屏幕上所有可识别的 token（路径、URL、SHA 等）会显示一个字母提示，按字母即复制。

### 快捷键

| 快捷键 | 作用 |
|--------|------|
| `<prefix> Space` | 激活 thumbs 模式 |
| 对应字母 | 复制该 token |
| `Esc` | 取消退出 |

### 自动识别的模式

路径、URL、IP 地址、Git SHA、UUID、数字串、自定义正则 (`[a-z][a-z0-9-]{30,}` 用于长 Pod 名称等)。

### 与复制模式的选择

- **thumbs**: 屏幕上看到的短 token，一键复制
- **复制模式 + 搜索**: 需要精确控制、大段文本、正则定位

两者互补，不替代。

---

## 11. tmux-sidebar

**作用**：在当前窗口左侧（或右侧）打开一个窗格，显示当前路径的目录树。同一个键绑定可以切换开关，光标留在原窗格不动，不打断工作流。

### 快捷键

| 快捷键 | 作用 |
|--------|------|
| `<prefix> Tab` | 切换侧栏（开/关），光标**留在原窗格** |
| `<prefix> Backspace` | 切换侧栏，并将光标**移到侧栏** |

### 当前配置

```bash
# 显示树状图，彩色输出，忽略常见无需关注的目录
set -g @sidebar-tree-command 'tree -C -I "node_modules|.git|target|dist"'

# 侧栏出现在左侧
set -g @sidebar-tree-position 'left'

# 默认宽度 40 列
set -g @sidebar-tree-width '40'
```

### 所有可用选项

| 选项 | 默认值 | 说明 |
|------|--------|------|
| `@sidebar-tree` | `Tab` | 切换侧栏的键绑定后缀 |
| `@sidebar-tree-focus` | `Backspace` | 切换侧栏并聚焦的键绑定后缀 |
| `@sidebar-tree-command` | `tree` | 侧栏内执行的命令 |
| `@sidebar-tree-position` | `left` | `left` 或 `right` |
| `@sidebar-tree-width` | `40` | 侧栏宽度（列数） |
| `@sidebar-tree-pager` | 无 | 指定分页器（如 `view -`） |

### 典型场景

```
# 场景 1: 开发时浏览项目结构
cd ~/project
<prefix> Tab

# 屏幕变为：
┌────────────────────┬─────────────────────────┐
│ .                  │                         │
│ ├── src            │  (你的工作窗格)          │
│ │   ├── main.rs    │                         │
│ │   └── lib.rs     │                         │
│ ├── Cargo.toml     │                         │
│ └── README.md      │                         │
│                    │                         │
└────────────────────┴─────────────────────────┘
  侧栏 (tree)          原窗格 (光标在这里)

# 场景 2: 查看完毕，关闭侧栏
<prefix> Tab          # 侧栏消失，布局恢复原样

# 场景 3: 想在侧栏里操作（翻页、搜索等）
<prefix> Backspace    # 打开侧栏并把光标移到侧栏
```

### 常见自定义

```bash
# 只列文件名，不画树线
set -g @sidebar-tree-command 'ls -1'

# 加深度限制，大仓库不会刷屏
set -g @sidebar-tree-command 'tree -C -L 3 -I "node_modules|.git|target|dist"'

# 放到右边
set -g @sidebar-tree-position 'right'

# 加宽
set -g @sidebar-tree-width '50'
```

> 注意: 侧栏记住每个目录的宽度。换一个目录打开侧栏，宽度会独立记忆。

---

## 快速参考卡

```
会话保存/恢复
  <prefix> Ctrl-s        保存
  <prefix> Ctrl-r        恢复

复制（复制模式内）
  y                      复制选中 → 系统剪贴板
  Y                      复制当前行 → 系统剪贴板
  <prefix> y             复制上一条命令
  <prefix> Shift-y       复制上一条命令的输出

Vim ↔ Tmux 窗格切换
  Ctrl-h/j/k/l           左/下/上/右

模糊查找
  <prefix> F             启动 fzf 菜单

快速复制屏幕 token
  <prefix> Space         激活 thumbs

目录树侧栏
  <prefix> Tab           切换侧栏（光标留原窗格）
  <prefix> Backspace     切换侧栏并聚焦侧栏

插件管理
  <prefix> I             安装插件
  <prefix> u             更新插件
  <prefix> Alt-u         卸载插件
```

---

## 状态栏布局说明

```
┌─────────────────────────────────────────────────────────────────┐
│ [会话名] [用户名] [窗口:窗格]  1:bash  2:vim  3:k8s  [HH:MM:SS] [CPU BAT] [host] │
│  ←── 左侧 (蓝色块) ──→       ←─ 窗口列表 ─→        ←──── 右侧 (灰色块) ────→    │
└─────────────────────────────────────────────────────────────────┘
```

- 左侧第一块(深蓝): 会话名
- 左侧第二块: 用户名
- 左侧第三块: 当前窗口:窗格编号
- 窗口列表: 中间，当前窗口高亮为深蓝色块
- 右侧第一块: 时间
- 右侧第二块: CPU% + 电池%
- 右侧第三块: 主机名
