# Tmux 复制粘贴完全指南

> 解决"Disable mouse reporting?"提示和各种复制场景

---

## 问题：为什么会出现"Disable mouse reporting?"提示？

当终端检测到 tmux 启用了鼠标模式时，终端和 tmux 都想处理鼠标事件，导致冲突。

```
终端原生选择        →  直接复制到系统剪贴板
     ⬇️
  冲突！
     ⬇️
Tmux 鼠标模式       →  复制到 tmux 缓冲区
```

---

## 解决方案概览

| 方案 | 优点 | 缺点 | 推荐度 |
|------|------|------|--------|
| **Option+拖动** | 简单快速 | 需要记住快捷键 | ⭐⭐⭐⭐⭐ |
| **启用剪贴板集成** | 自动同步 | tmux 旧版本不支持 | ⭐⭐⭐⭐⭐ |
| **复制模式** | 功能最强 | 步骤稍多 | ⭐⭐⭐⭐ |
| **临时禁用鼠标** | 灵活 | 需要切换 | ⭐⭐⭐ |

---

## 方案 1: 按住修饰键复制（最快）⭐⭐⭐⭐⭐

### macOS (iTerm2)
```bash
按住 Option (⌥) + 鼠标拖动选择
→ 绕过 tmux，直接复制到系统剪贴板
→ 可以在任何应用中 Cmd+V 粘贴
```

### macOS (Terminal.app)
```bash
按住 Option (⌥) + 鼠标拖动选择
→ 同上
```

### Linux (大多数终端)
```bash
按住 Shift + 鼠标拖动选择
→ 绕过 tmux，直接复制
```

### 使用场景
```bash
# 1. 查看日志，想快速复制某行到浏览器搜索
tail -f app.log
# 看到错误信息
# 按住 Option + 拖动选择错误信息
# 到浏览器 Cmd+V 粘贴搜索
```

---

## 方案 2: 启用剪贴板集成（已启用）⭐⭐⭐⭐⭐

### 当前配置

```bash
# 在 ~/.tmux.conf.local
tmux_conf_copy_to_os_clipboard=true
```

### 效果

现在在 tmux 中复制的任何内容都会自动同步到系统剪贴板！

```bash
# 方式 1: 鼠标拖动
鼠标拖动选择文本
→ 复制到 tmux 缓冲区
→ 同时复制到系统剪贴板
→ 可以在任何应用中粘贴

# 方式 2: 键盘复制
<prefix> [          # 进入复制模式
v                   # 开始选择
hjkl                # 移动扩展选择
y                   # 复制
→ 同时复制到系统剪贴板
```

### 验证是否生效

```bash
# 测试步骤
1. 在 tmux 中: echo "test clipboard sync"
2. 进入复制模式: <prefix> [
3. 选择并复制文本: v (选择) → y (复制)
4. 打开任何应用（如浏览器、Slack）
5. Cmd+V 粘贴
6. 如果能粘贴出 "test clipboard sync"，说明集成成功！
```

---

## 方案 3: 使用 Tmux 复制模式（最灵活）⭐⭐⭐⭐

### 基本流程

```bash
# 1. 进入复制模式
<prefix> [
# 或
<prefix> Enter

# 2. 移动光标到起始位置
hjkl                # Vim 风格移动
/关键词              # 搜索定位

# 3. 开始选择
v                   # 进入 visual 模式
V                   # 选择整行
Ctrl-v              # 块选择模式

# 4. 扩展选择
hjkl                # 移动扩展选择
w/b                 # 按单词移动
$                   # 到行尾
0                   # 到行首
G                   # 到文件尾
gg                  # 到文件头

# 5. 复制
y                   # 复制并退出
→ 已启用剪贴板集成，会同时复制到系统剪贴板

# 6. 粘贴
<prefix> p          # 在 tmux 中粘贴
Cmd+V              # 在其他应用中粘贴
```

### 高级技巧

```bash
# 选择整行
<prefix> [
V                   # 选择当前行
3j                  # 向下扩展 3 行
y                   # 复制

# 选择矩形区域
<prefix> [
Ctrl-v              # 块选择
3j5l                # 向下 3 行，向右 5 列
y                   # 复制

# 选择从当前到某个模式
<prefix> [
v                   # 开始选择
/ERROR              # 搜索到 ERROR
y                   # 复制从当前位置到 ERROR 的所有内容
```

---

## 方案 4: 临时禁用鼠标模式 ⭐⭐⭐

### 使用场景

当你需要连续使用终端原生复制时：

```bash
# 1. 临时禁用鼠标
<prefix> m          # 切换鼠标模式（关闭）

# 2. 现在可以直接用鼠标选择
# 像普通终端一样拖动选择
# 自动复制到系统剪贴板

# 3. 操作完成后重新启用
<prefix> m          # 切换鼠标模式（开启）
```

---

## 完整的复制方式对比

### 场景 1: 复制一行错误日志

**方式 A: Option+拖动（最快）**
```
按住 Option + 鼠标拖动选择
→ 完成！
```

**方式 B: 三击（快捷）**
```
三击该行
→ 自动选择整行并复制
```

**方式 C: 键盘（精确）**
```
<prefix> [
V                   # 选择整行
y                   # 复制
```

### 场景 2: 复制部分命令输出（跨多屏）

**推荐: 键盘复制模式**
```
<prefix> [
gg                  # 跳到开头
v                   # 开始选择
/结束标记            # 搜索到结束位置
y                   # 复制
```

### 场景 3: 复制配置文件内容到编辑器

**方式 A: Option+拖动**
```
cat config.yaml
按住 Option + 拖动选择全部
到编辑器 Cmd+V
```

**方式 B: 使用复制模式**
```
cat config.yaml
<prefix> [
gg                  # 到开头
V                   # 行选择
G                   # 到结尾
y                   # 复制
到编辑器 Cmd+V
```

**方式 C: 直接复制文件（最佳）**
```
cat config.yaml | pbcopy
到编辑器 Cmd+V
```

### 场景 4: 复制代码片段

**推荐: 鼠标拖动（已启用剪贴板集成）**
```
# 显示代码
cat script.sh

# 方式 1: 直接拖动
鼠标拖动选择代码
→ 自动复制到系统剪贴板
→ 到 IDE 中 Cmd+V

# 方式 2: Option+拖动
按住 Option + 拖动
→ 直接复制
```

---

## 粘贴方式

### 在 Tmux 内粘贴

```bash
# 方式 1: 从 tmux 缓冲区粘贴
<prefix> p          # 粘贴最近的缓冲区
<prefix> P          # 选择缓冲区粘贴

# 方式 2: 从系统剪贴板粘贴（如果启用了集成）
<prefix> p          # 也会包含系统剪贴板内容

# 方式 3: 直接粘贴（绕过 tmux）
Cmd+V              # 在 macOS
Ctrl+Shift+V       # 在 Linux（某些终端）
```

### 查看粘贴缓冲区

```bash
# 列出所有缓冲区
<prefix> b

# 显示类似：
[0] 125 bytes: "echo hello world..."
[1] 89 bytes: "ls -la..."
[2] 245 bytes: "cat config.yaml..."

# 选择要粘贴的缓冲区
<prefix> P
# 用上下键选择，Enter 粘贴
```

---

## 特殊场景处理

### 场景 1: 复制时保留格式（如代码缩进）

```bash
# 使用块选择模式
<prefix> [
Ctrl-v              # 块选择
# 精确选择需要的列
y
```

### 场景 2: 复制长命令到本地执行

```bash
# 在远程服务器的 tmux 中
ls -la | grep "log"

# 方式 1: 复制模式
<prefix> [
?ls                 # 反向搜索到命令
V                   # 选择整行
y
# 到本地终端 Cmd+V

# 方式 2: 直接选择
按住 Option + 拖动选择命令行
# 到本地终端 Cmd+V
```

### 场景 3: 复制 URL 并在浏览器打开

```bash
# tmux 中出现 URL
echo "https://github.com/gpakosz/.tmux"

# 方式 1: 双击 URL（如果终端支持）
双击 URL → 自动选择
Cmd+V 到浏览器

# 方式 2: 使用 Urlscan
<prefix> U          # 启动 Urlscan（如果安装）
# 选择 URL，自动复制或打开

# 方式 3: Option+拖动
按住 Option + 拖动选择 URL
Cmd+V 到浏览器
```

### 场景 4: 在 Vim 中编辑 tmux 缓冲区

```bash
# 1. 在 tmux 中复制内容
<prefix> [
v → y

# 2. 打开 vim
vim file.txt

# 3. 在 vim 中粘贴
# 如果启用了剪贴板集成:
"+p                 # 从系统剪贴板粘贴

# 或在插入模式:
Cmd+V              # 直接粘贴（macOS）
```

---

## 配置选项

### 当前配置（在 ~/.tmux.conf.local）

```bash
# 已启用的配置
set -g mouse on #!important
tmux_conf_copy_to_os_clipboard=true

# 鼠标行为优化
unbind -T copy-mode-vi MouseDragEnd1Pane #!important
bind -T copy-mode-vi MouseDragEnd1Pane send -X copy-selection-no-clear #!important
bind -T copy-mode-vi DoubleClick1Pane select-pane \; send -X select-word #!important
bind -T copy-mode-vi TripleClick1Pane select-pane \; send -X select-line #!important
```

### 可选配置

```bash
# 如果不想要剪贴板集成
tmux_conf_copy_to_os_clipboard=false

# 如果不想要鼠标模式
set -g mouse off #!important

# 自定义复制命令（macOS）
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "pbcopy"

# 自定义复制命令（Linux）
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "xclip -selection clipboard"
```

---

## 故障排查

### 问题 1: 复制后无法在其他应用粘贴

**检查清单**:
```bash
# 1. 确认剪贴板集成已启用
grep "copy_to_os_clipboard" ~/.tmux.conf.local
# 应该显示: tmux_conf_copy_to_os_clipboard=true

# 2. 重新加载配置
<prefix> r

# 3. 测试
<prefix> [
v → 选择文本 → y
# 到其他应用 Cmd+V

# 4. 如果还是不行，检查是否安装了剪贴板工具
# macOS: pbcopy/pbpaste（内置）
which pbcopy        # 应该有输出

# Linux: xsel 或 xclip
which xsel
# 或
which xclip
```

### 问题 2: 鼠标选择时仍然跳到底部

**解决**:
```bash
# 确认配置已加载
tmux show-options -g | grep -i mouse

# 重新加载配置
<prefix> r

# 或重启 tmux
tmux kill-server
tmux
```

### 问题 3: Option+拖动不工作

**iTerm2 配置**:
```
Preferences → Profiles → Keys
找到: "Left Option key"
确保选择: "Normal" 或 "Esc+"（不要选 "Meta"）
```

**Terminal.app**:
```
默认应该工作，无需配置
```

### 问题 4: 复制的文本有额外换行

**原因**: 窗格宽度导致自动换行

**解决**:
```bash
# 方式 1: 使用块选择避免换行符
<prefix> [
Ctrl-v              # 块选择
# 精确选择列范围

# 方式 2: 复制后手动处理
# 到编辑器中删除多余换行
```

---

## 最佳实践

### ✅ 推荐

1. **启用剪贴板集成**（已启用）
   - 无缝体验，自动同步

2. **学习 Option+拖动**
   - 最快的绕过方式

3. **熟悉复制模式**
   - 处理复杂选择场景

4. **保持鼠标模式开启**
   - 提升整体效率

### ❌ 避免

1. ❌ 频繁切换鼠标模式
   - 容易忘记当前状态

2. ❌ 在实时输出时直接复制
   - 应该先进入复制模式

3. ❌ 复制敏感信息到公共剪贴板
   - 注意安全

---

## 快速参考卡

| 需求 | 方法 | 快捷键 |
|------|------|--------|
| 快速复制到系统 | Option+拖动 | `Option + 鼠标` |
| 复制一行 | 三击 | `三击鼠标` |
| 复制单词 | 双击 | `双击鼠标` |
| 精确复制 | 复制模式 | `<prefix> [` → `v` → `y` |
| 复制整屏 | 复制模式 | `<prefix> [` → `V` → `G` → `y` |
| tmux 内粘贴 | 粘贴 | `<prefix> p` |
| 查看缓冲区 | 列表 | `<prefix> b` |
| 选择缓冲区粘贴 | 选择粘贴 | `<prefix> P` |
| 临时禁用鼠标 | 切换 | `<prefix> m` |

---

## 终极技巧

### 技巧 1: 链式复制

```bash
# 复制多个片段到一个缓冲区
<prefix> [
v → 选择第一段 → y
A                   # 追加模式
v → 选择第二段 → y
# 现在缓冲区包含两段内容
<prefix> p          # 一次粘贴所有
```

### 技巧 2: 管道复制

```bash
# 复制命令输出到剪贴板（macOS）
ls -la | pbcopy

# Linux
ls -la | xclip -selection clipboard
```

### 技巧 3: 复制历史命令

```bash
# 查看历史
history

# 复制特定命令
<prefix> [
/要复制的命令        # 搜索定位
V                   # 选择整行
y                   # 复制
```

---

**提示**: 配合启用的剪贴板集成，现在复制体验应该非常流畅了！

生成时间: 2026-02-02
相关配置: ~/.tmux.conf.local
