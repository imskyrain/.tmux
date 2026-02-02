# Tmux-Thumbs 插件使用指南

> 快速复制屏幕上的任何内容 - 路径、URL、Git SHA、IP 地址等

---

## 什么是 Tmux-Thumbs？

tmux-thumbs 是一个强大的插件，可以：
- 🎯 自动识别屏幕上的文本模式（路径、URL、Git SHA、IP 等）
- ⌨️ 用键盘快速选择并复制
- ⚡️ 比鼠标选择快 10 倍
- 🎨 视觉化提示，一目了然

### 演示场景

```bash
# 执行命令后
$ git log --oneline
a1b2c3d Fix bug in authentication
e4f5g6h Add new feature
i7j8k9l Update documentation

# 按 <prefix> + Space
# 屏幕上会显示：
a[a]1b2c3d Fix bug in authentication
e[s]4f5g6h Add new feature
i[d]7j8k9l Update documentation

# 按 'a' → 复制 a1b2c3d
# 按 's' → 复制 e4f5g6h
# 按 'd' → 复制 i7j8k9l
```

---

## 安装步骤

### 1. 检查依赖

tmux-thumbs 需要 Rust 环境：

```bash
# 检查是否已安装 Rust
rustc --version

# 如果没有安装，安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

### 2. 配置已添加

配置已经添加到 `~/.tmux.conf.local`：

```bash
set -g @plugin 'fcsonline/tmux-thumbs'

# 配置选项
set -g @thumbs-key Space              # 激活快捷键
set -g @thumbs-alphabet qwerty        # 键盘布局
set -g @thumbs-reverse enabled        # 反向显示
set -g @thumbs-unique enabled         # 只显示唯一项
set -g @thumbs-position left          # 提示位置
set -g @thumbs-command 'echo -n {} | pbcopy' # 复制命令
```

### 3. 安装插件

在 tmux 会话中：

```bash
# 方法 1: 使用 TPM 快捷键（推荐）
<prefix> + I        # 大写的 I（安装插件）

# 方法 2: 手动安装
# 在 tmux 外执行
~/.tmux/plugins/tpm/scripts/install_plugins.sh
```

### 4. 验证安装

```bash
# 检查插件是否安装
ls ~/.tmux/plugins/tmux-thumbs/

# 应该看到：
# - tmux-thumbs（可执行文件）
# - tmux-thumbs.tmux
# - 其他文件
```

---

## 基本使用

### 激活 Thumbs 模式

```bash
<prefix> + Space    # 默认快捷键
```

### 屏幕上会显示什么？

```bash
# 示例：查看日志
$ tail /var/log/app.log
[ERROR] Failed to connect to https://api.example.com
[WARN] Retrying in 5 seconds
[INFO] Connected to database at 192.168.1.100:5432
[INFO] Processing file /home/user/data/file.csv

# 按 <prefix> + Space 后：
[ERROR] Failed to connect to [a]https://api.example.com
[WARN] Retrying in 5 seconds
[INFO] Connected to database at [s]192.168.1.100[d]:5432
[INFO] Processing file [f]/home/user/data/file.csv

# 提示说明：
[a] → https://api.example.com
[s] → 192.168.1.100
[d] → 5432
[f] → /home/user/data/file.csv
```

### 复制内容

```bash
# 按 <prefix> + Space 激活
# 按对应的字母（如 'a'、's'、'f'）
# → 自动复制到系统剪贴板
# → 退出 thumbs 模式
```

---

## 高级使用

### 识别的模式类型

tmux-thumbs 自动识别：

1. **文件路径**
   ```
   /home/user/file.txt
   ~/Documents/report.pdf
   ./src/main.rs
   ```

2. **URL**
   ```
   https://github.com/fcsonline/tmux-thumbs
   http://localhost:8080
   ftp://example.com
   ```

3. **IP 地址**
   ```
   192.168.1.1
   10.0.0.1
   127.0.0.1
   ```

4. **Git SHA**
   ```
   a1b2c3d4e5f6
   commit abc123
   ```

5. **UUID**
   ```
   550e8400-e29b-41d4-a716-446655440000
   ```

6. **数字（端口、ID 等）**
   ```
   :8080
   #12345
   ```

7. **自定义正则（已配置）**
   ```
   邮箱地址: user@example.com
   ```

### 配置选项说明

```bash
# 激活快捷键
set -g @thumbs-key Space
# 可改为: F, f, C-f 等

# 键盘布局
set -g @thumbs-alphabet qwerty
# 可选: qwerty, qwertz, azerty, dvorak, colemak

# 显示方向
set -g @thumbs-reverse enabled
# enabled: 从下往上（推荐）
# disabled: 从上往下

# 只显示唯一项
set -g @thumbs-unique enabled
# 相同内容只显示一次

# 提示位置
set -g @thumbs-position left
# left: 在匹配文本左侧显示提示
# right: 在右侧显示

# 复制命令
set -g @thumbs-command 'echo -n {} | pbcopy'
# macOS: pbcopy
# Linux: xclip -selection clipboard
# 或: wl-copy (Wayland)

# 自定义正则表达式
set -g @thumbs-regexp-1 '[a-z]+@[a-z]+.com'  # 邮箱
set -g @thumbs-regexp-2 'JIRA-[0-9]+'        # JIRA ticket
set -g @thumbs-regexp-3 'TODO:.*'            # TODO 注释
```

---

## 实用场景

### 场景 1: 复制文件路径

```bash
# 查找文件
$ find . -name "*.log"
./logs/app.log
./logs/error.log
./cache/debug.log

# <prefix> + Space
# 按对应字母复制路径
# → 可以直接在其他命令中使用
$ vim <粘贴路径>
```

### 场景 2: 复制 Git 提交 SHA

```bash
# 查看提交历史
$ git log --oneline
abc123d Fix authentication bug
def456e Add new API endpoint
ghi789f Update dependencies

# <prefix> + Space
# 按字母复制 SHA
$ git show <粘贴 SHA>
```

### 场景 3: 复制 URL 在浏览器打开

```bash
# 日志中出现 URL
[INFO] API documentation: https://docs.example.com/api/v1

# <prefix> + Space
# 复制 URL
# → 在浏览器地址栏粘贴
```

### 场景 4: 复制错误信息中的路径

```bash
# 编译错误
error: cannot find file `/home/user/src/main.rs`
error: failed to open `/home/user/config.toml`

# <prefix> + Space
# 快速复制错误路径
# → 检查或编辑文件
```

### 场景 5: 复制 IP 地址或端口

```bash
# 网络配置
Server listening on 192.168.1.100:8080
Database at 10.0.0.50:5432

# <prefix> + Space
# 复制 IP 或端口
# → 用于 SSH、配置等
```

### 场景 6: 复制 Docker 容器 ID

```bash
$ docker ps
CONTAINER ID   IMAGE     COMMAND
abc123def456   nginx     "nginx -g 'daemon of…"
ghi789jkl012   redis     "docker-entrypoint.s…"

# <prefix> + Space
# 快速复制容器 ID
$ docker logs <粘贴 ID>
```

---

## 键盘布局选择

### QWERTY（默认，推荐）

```
最常用的字母优先：
a s d f g h j k l
```

### DVORAK

```
适合 Dvorak 键盘用户：
a o e u i d h t n s
```

### COLEMAK

```
适合 Colemak 键盘用户：
a r s t d h n e i o
```

---

## 与其他复制方式对比

| 方式 | 速度 | 精确度 | 场景 |
|------|------|--------|------|
| **tmux-thumbs** | ⚡️⚡️⚡️ | ⭐⭐⭐⭐⭐ | 复制屏幕上的路径/URL/SHA |
| **Option+拖动** | ⚡️⚡️ | ⭐⭐⭐ | 复制任意文本 |
| **复制模式** | ⚡️ | ⭐⭐⭐⭐⭐ | 精确复制大段文本 |
| **鼠标拖动** | ⚡️⚡️ | ⭐⭐⭐ | 快速复制可见文本 |

### 何时使用 tmux-thumbs？

✅ **推荐使用**：
- 复制文件路径
- 复制 URL
- 复制 Git SHA
- 复制 IP 地址
- 复制容器/进程 ID
- 屏幕上有多个目标需要快速选择

❌ **不推荐使用**：
- 复制大段文本（用复制模式）
- 复制代码块（用复制模式或 Option+拖动）
- 屏幕上只有一个目标（直接拖动更快）

---

## 工作流示例

### 工作流 1: 开发调试

```bash
# 1. 运行测试，发现错误
$ cargo test
error: could not compile `project` due to previous error
  --> src/main.rs:42:5

# 2. 使用 thumbs 复制路径
<prefix> + Space
按字母复制 src/main.rs:42

# 3. 在编辑器中打开
$ vim src/main.rs +42
```

### 工作流 2: 日志分析

```bash
# 1. 查看日志
$ tail -f /var/log/nginx/access.log
192.168.1.50 - GET /api/users HTTP/1.1 200
192.168.1.75 - POST /api/login HTTP/1.1 401
192.168.1.100 - GET /static/app.js HTTP/1.1 304

# 2. 发现异常 IP
<prefix> + Space
复制可疑 IP: 192.168.1.75

# 3. 查询 IP 信息
$ whois 192.168.1.75
```

### 工作流 3: Git 操作

```bash
# 1. 查看历史
$ git log --oneline --graph
* abc123d (HEAD) Merge pull request #42
* def456e Fix critical bug
* ghi789f Add feature X

# 2. 复制 SHA 查看详情
<prefix> + Space
复制 SHA: def456e

# 3. 查看提交
$ git show def456e

# 4. Cherry-pick
$ git cherry-pick def456e
```

---

## 故障排查

### 问题 1: 按 <prefix> + Space 没反应

**检查**:
```bash
# 1. 确认插件已安装
ls ~/.tmux/plugins/tmux-thumbs/

# 2. 检查 Rust 是否安装
rustc --version

# 3. 重新编译插件
cd ~/.tmux/plugins/tmux-thumbs/
cargo build --release

# 4. 重新加载配置
<prefix> + r
```

### 问题 2: 插件安装失败

**原因**: 可能缺少 Rust 或编译工具

**解决**:
```bash
# 安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

# 重新安装插件
<prefix> + I
```

### 问题 3: 复制后无法粘贴到其他应用

**检查复制命令**:
```bash
# macOS（应该用 pbcopy）
set -g @thumbs-command 'echo -n {} | pbcopy'

# Linux X11
set -g @thumbs-command 'echo -n {} | xclip -selection clipboard'

# Linux Wayland
set -g @thumbs-command 'echo -n {} | wl-copy'
```

### 问题 4: 没有识别到我想要的模式

**添加自定义正则**:
```bash
# 在 ~/.tmux.conf.local 添加
set -g @thumbs-regexp-1 'your-pattern-here'

# 示例：匹配 JIRA ticket
set -g @thumbs-regexp-2 'PROJ-[0-9]+'

# 重新加载
<prefix> + r
```

---

## 快速参考

### 基本操作

| 操作 | 快捷键 |
|------|--------|
| 激活 thumbs | `<prefix> + Space` |
| 选择并复制 | `按提示的字母` |
| 退出 | `Esc` 或 `q` |
| 安装插件 | `<prefix> + I` |
| 更新插件 | `<prefix> + u` |

### 配置快速参考

```bash
# 修改激活键
set -g @thumbs-key F

# 只在当前窗格搜索（不是整个窗口）
set -g @thumbs-select-pane enabled

# 自定义颜色
set -g @thumbs-fg-color green
set -g @thumbs-bg-color black
set -g @thumbs-hint-fg-color yellow
set -g @thumbs-hint-bg-color black

# 选择后执行自定义操作
set -g @thumbs-command 'echo {} | pbcopy && tmux display "Copied: {}"'

# 多行模式（跨行匹配）
set -g @thumbs-multi-line enabled
```

---

## 与其他工具集成

### 与 fzf 结合

```bash
# 复制路径后用 fzf 搜索
<prefix> + Space
# 复制路径
$ cat <粘贴路径> | fzf
```

### 与 vim 结合

```bash
# 复制文件路径后在 vim 中打开
<prefix> + Space
# 复制路径
$ vim <粘贴路径>
```

### 与 tldr 结合

```bash
# 复制命令名后查看帮助
<prefix> + Space
# 复制命令
$ tldr <粘贴命令>
```

---

## 最佳实践

### ✅ 推荐

1. **用 thumbs 复制结构化内容**
   - 路径、URL、SHA 等

2. **保持默认键盘布局（QWERTY）**
   - 最符合直觉

3. **启用 unique 模式**
   - 避免重复项干扰

4. **添加项目特定的正则**
   - 如 ticket ID、构建号等

### ❌ 避免

1. ❌ 用 thumbs 复制长文本
   - 应该用复制模式

2. ❌ 修改过多配置
   - 默认配置已经很好

3. ❌ 在文本很少时使用
   - 直接拖动更快

---

## 进阶技巧

### 技巧 1: 自定义多个正则

```bash
# 匹配不同类型的内容
set -g @thumbs-regexp-1 '[A-Z]+-[0-9]+'        # JIRA
set -g @thumbs-regexp-2 '#[0-9]+'              # PR 编号
set -g @thumbs-regexp-3 'v[0-9]+\.[0-9]+\.[0-9]+' # 版本号
set -g @thumbs-regexp-4 '[0-9a-f]{40}'         # Git full SHA
```

### 技巧 2: 复制并执行操作

```bash
# 复制后自动在浏览器打开（URL）
set -g @thumbs-command 'echo -n {} | pbcopy && open {}'

# 复制后显示通知
set -g @thumbs-command 'echo -n {} | pbcopy && tmux display "已复制: {}"'
```

### 技巧 3: 不同类型使用不同快捷键

```bash
# 默认 thumbs
set -g @thumbs-key Space

# 可以配置多个实例用于不同场景（需要额外配置）
bind-key F run-tmux-thumbs # 只匹配文件路径
bind-key U run-tmux-thumbs # 只匹配 URL
```

---

## 总结

### tmux-thumbs 的价值

- 🚀 **效率提升**: 比鼠标选择快 5-10 倍
- 🎯 **精确**: 自动识别有用的内容
- ⌨️ **纯键盘**: 无需鼠标
- 🔧 **可定制**: 支持自定义模式

### 记住这个

```
有结构化内容（路径、URL、SHA）→ 用 thumbs
有大段文本 → 用复制模式
有任意文本 → 用 Option+拖动
```

---

**提示**: tmux-thumbs 是提升效率的神器，特别是在处理日志、Git 历史、文件路径时！

生成时间: 2026-02-02
