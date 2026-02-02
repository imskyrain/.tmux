# iTerm2 + Tmux 配置指南

## 解决 Command+C 复制问题

### 问题根源

在终端中：
- `Command+C` 默认 = 发送 `Ctrl+C`（中断信号）
- 不是复制快捷键！

当 tmux 启用鼠标模式时，iTerm2 检测到你可能想复制但无法选择，所以提示：
```
Disable mouse reporting?
```

---

## 最佳解决方案

### 方案 A: 使用 Option+拖动（推荐）⭐⭐⭐⭐⭐

```bash
# 1. 按住 Option 键
# 2. 鼠标拖动选择文本
# 3. 释放 → 自动复制到剪贴板
# 4. 在任何地方 Cmd+V 粘贴
```

**优点**：
- ✅ 不需要任何配置
- ✅ 绕过 tmux 鼠标模式
- ✅ 与系统行为一致
- ✅ 最简单快捷

### 方案 B: 配置 iTerm2 快捷键

让 `Cmd+C` 在有选择时复制，无选择时中断：

#### 配置步骤

1. **打开 iTerm2 偏好设置**
   ```
   iTerm2 → Settings (或 Preferences)
   ```

2. **进入 Keys 设置**
   ```
   Keys → Key Bindings
   ```

3. **添加自定义键绑定**
   ```
   点击 "+" 添加新绑定

   Keyboard Shortcut: Cmd+C
   Action: Send Text with "vim" Special Chars
   Text: \x03

   或者选择:
   Action: Send ^C
   ```

   但这样会失去原有的中断功能！

#### 更好的方案：条件复制

iTerm2 不直接支持条件快捷键，但可以：

1. **保持 Cmd+C 为中断**
2. **使用 Cmd+Shift+C 复制**

配置：
```
Keys → Key Bindings → 添加

Keyboard Shortcut: Cmd+Shift+C
Action: Copy Selection
```

---

## 推荐的键盘快捷键方案

### 标准方案（无需配置）

| 操作 | 快捷键 | 说明 |
|------|--------|------|
| 复制 | `Option + 拖动` | 选择并自动复制 |
| 粘贴 | `Cmd+V` | 粘贴剪贴板内容 |
| 中断 | `Cmd+C` 或 `Ctrl+C` | 终止当前进程 |

### 进阶方案（自定义）

| 操作 | 快捷键 | 配置 |
|------|--------|------|
| 复制 | `Cmd+Shift+C` | 需在 iTerm2 配置 |
| 粘贴 | `Cmd+V` | 默认 |
| 中断 | `Cmd+C` | 默认 |

---

## iTerm2 完整优化配置

### 1. 启用自然文本选择

```
Settings → Profiles → Terminal
勾选: "Report mouse clicks & drags"
```

这样可以让 Option+拖动更顺畅。

### 2. 配置 Option 键行为

```
Settings → Profiles → Keys → General

Left Option key: Esc+
Right Option key: Normal
```

推荐配置：
- **Left Option**: `Esc+` (用于 meta 键)
- **Right Option**: `Normal` (用于绕过 tmux)

这样：
- 左 Option: 可以用作 Alt/Meta (如 Option+B 后退单词)
- 右 Option: 用于绕过 tmux 选择复制

### 3. 鼠标相关设置

```
Settings → Profiles → Terminal

勾选:
☑ Report mouse clicks & drags
☑ Report mouse wheel
☐ Disable mouse reporting when command key is down
```

### 4. 复制模式设置

```
Settings → General → Selection

勾选:
☑ Copy to pasteboard on selection
☐ Copied text includes trailing newline
☑ Applications in terminal may access clipboard
```

**重要**：
- `Copy to pasteboard on selection`: 选择后自动复制
- `Applications in terminal may access clipboard`: 允许 tmux 访问剪贴板

---

## 完整的复制工作流

### 场景 1: 快速复制（日常使用）

```bash
# 方法 1: Right Option + 拖动（最快）
按住右 Option + 鼠标拖动
→ 自动复制

# 方法 2: 直接拖动（tmux 模式）
鼠标拖动选择
→ 自动复制（因为启用了剪贴板集成）

# 方法 3: 双击/三击
双击选择单词
三击选择整行
→ 自动复制
```

### 场景 2: 精确复制（复杂内容）

```bash
# 进入 tmux 复制模式
<prefix> [

# 使用 vim 键位精确选择
v                   # visual 模式
hjkl                # 移动
y                   # 复制

# 或使用搜索定位
/关键词              # 搜索
n                   # 下一个
v                   # 选择
y                   # 复制
```

### 场景 3: 复制长输出

```bash
# 方法 1: 捕获窗格内容
<prefix> :
capture-pane -S -1000 -p | pbcopy

# 方法 2: 使用管道
command | pbcopy

# 方法 3: 复制模式
<prefix> [
gg                  # 到开头
VG                  # 选择到结尾
y                   # 复制
```

---

## 避免 "Disable mouse reporting?" 提示

### 原因

当你在 tmux 鼠标模式下尝试使用终端原生选择时，iTerm2 会提示。

### 解决方法

#### 方法 1: 使用修饰键绕过

```bash
# 使用 Right Option（或 Left Option）
Right Option + 鼠标拖动
→ 绕过 tmux，不会提示
```

#### 方法 2: 已经配置了剪贴板集成

现在直接拖动选择就会复制到系统剪贴板，不需要 `Cmd+C`：

```bash
鼠标拖动选择
→ 自动复制到剪贴板
→ Cmd+V 粘贴即可
```

#### 方法 3: 禁用 iTerm2 的提示

```
Settings → Profiles → Terminal
取消勾选: "Disable session-initiated printing"
```

或者在提示出现时选择 "Don't show again"。

#### 方法 4: 配置快捷键禁用鼠标报告

在 iTerm2 中添加快捷键临时禁用鼠标报告：

```
Settings → Keys → Key Bindings → 添加

Keyboard Shortcut: Cmd+Shift+M
Action: Toggle Mouse Reporting
```

这样可以快速切换鼠标报告。

---

## 最佳实践建议

### ✅ 推荐做法

1. **日常使用**
   ```
   Right Option + 拖动选择
   → 最快速，不需要记忆其他操作
   ```

2. **长内容复制**
   ```
   <prefix> [          # 进入复制模式
   gg → VG → y         # 选择全部复制
   ```

3. **命令输出**
   ```
   command | pbcopy    # 直接复制到剪贴板
   ```

4. **忘记 Cmd+C 用于复制**
   ```
   终端中 Cmd+C 是中断，不是复制！
   ```

### ❌ 避免做法

1. ❌ 不要尝试 `Cmd+C` 复制
   - 这是中断信号，不是复制

2. ❌ 不要频繁切换鼠标模式
   - 保持开启，使用 Option+拖动绕过

3. ❌ 不要在实时输出时选择
   - 先进入复制模式或向上滚动

---

## 快速参考

### 复制文本到剪贴板

| 方法 | 操作 | 场景 |
|------|------|------|
| **Option+拖动** | `Right Opt + 拖` | 最快，任何情况 ⭐⭐⭐⭐⭐ |
| **直接拖动** | `鼠标拖动` | tmux 内，已集成剪贴板 ⭐⭐⭐⭐ |
| **双击** | `双击` | 快速选择单词 ⭐⭐⭐⭐ |
| **三击** | `三击` | 快速选择整行 ⭐⭐⭐⭐ |
| **复制模式** | `<prefix> [ → v → y` | 精确复杂选择 ⭐⭐⭐⭐⭐ |
| **管道** | `cmd \| pbcopy` | 命令输出 ⭐⭐⭐⭐⭐ |

### 常见误区

| 误区 | 正确方式 |
|------|----------|
| ❌ `Cmd+C` 复制 | ✅ `Option+拖动` 或 `<prefix>[` → `y` |
| ❌ 选择后按 `Cmd+C` | ✅ 释放鼠标即自动复制 |
| ❌ 看到提示点 "Yes" | ✅ 使用 `Option+拖动` 避免提示 |

---

## 测试配置

### 测试 1: 基本复制

```bash
# 1. 输出测试文本
echo "测试复制功能 - $(date)"

# 2. Right Option + 拖动选择

# 3. 打开浏览器或其他应用

# 4. Cmd+V 粘贴

# ✅ 成功：看到刚才的文本
# ❌ 失败：检查 Option 键配置
```

### 测试 2: tmux 复制模式

```bash
# 1. 进入复制模式
<prefix> [

# 2. 选择并复制
v → hjkl 移动 → y

# 3. 到其他应用 Cmd+V

# ✅ 成功：剪贴板集成工作正常
# ❌ 失败：检查 tmux_conf_copy_to_os_clipboard
```

### 测试 3: 管道复制

```bash
# 1. 执行
echo "管道测试" | pbcopy

# 2. 到其他应用 Cmd+V

# ✅ 成功：看到 "管道测试"
# ❌ 失败：检查 pbcopy 是否可用
```

---

## 故障排查

### 问题：Option+拖动不工作

**检查 iTerm2 配置**:
```
Settings → Profiles → Keys → General

确保 Right Option key 设为: "Normal"
不要设为: "Esc+" 或 "Meta"
```

### 问题：仍然看到 "Disable mouse reporting?"

**方案 A**: 点击 "Don't show again"

**方案 B**: 总是使用 Option+拖动

**方案 C**: 禁用 tmux 鼠标模式
```bash
<prefix> m          # 切换关闭
```

### 问题：复制后粘贴是空的

**检查**:
```bash
# 测试剪贴板
echo "test" | pbcopy
pbpaste
# 应该显示 "test"

# 检查 tmux 配置
grep copy_to_os_clipboard ~/.tmux.conf.local
# 应该是 true
```

---

## 总结

### 记住这个口诀

```
复制文本不用 C
Option 拖动才是王道
Cmd+C 只能中断进程
Cmd+V 粘贴没问题
```

### 最简单的方式

```bash
Right Option + 鼠标拖动 = 复制
Cmd+V = 粘贴

就这么简单！
```

---

**提示**: 习惯了 Option+拖动后，你会发现比 Cmd+C 还要快捷！

生成时间: 2026-02-02
