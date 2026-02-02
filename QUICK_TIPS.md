# Tmux 快速技巧

## 处理长输出的多种方法

### 场景：命令输出超过一屏（如 `ls -l ~/Downloads`）

#### 方法 1: Tmux 复制模式 ⭐ 推荐

```bash
# 执行命令后
<prefix> [              # 进入复制模式

# 浏览快捷键：
g                       # 跳到最顶部
G                       # 跳到最底部
Ctrl-u                  # 向上滚动半页
Ctrl-d                  # 向下滚动半页
Ctrl-b                  # 向上滚动整页
Ctrl-f                  # 向下滚动整页
/pattern                # 向下搜索
?pattern                # 向上搜索
n                       # 下一个搜索结果
N                       # 上一个搜索结果
q 或 Escape             # 退出复制模式
```

#### 方法 2: 启用鼠标模式

```bash
<prefix> m              # 切换鼠标模式
# 然后用鼠标滚轮上下滚动
```

#### 方法 3: 使用 less/more

```bash
# 最简单的方式
ls -l ~/Downloads | less

# less 快捷键：
空格                    # 向下翻页
b                       # 向上翻页
/pattern                # 搜索
q                       # 退出
h                       # 帮助
```

#### 方法 4: 保存到文件

```bash
ls -l ~/Downloads > /tmp/output.txt
vim /tmp/output.txt
```

#### 方法 5: 捕获 tmux 窗格内容

```bash
# 在 tmux 命令模式 (<prefix> :)
capture-pane -S -50000 -p > /tmp/pane.txt

# 或用别名简化
<prefix> :
capture-pane -S - -p > /tmp/full-history.txt
```

---

## 其他实用技巧

### 快速清屏但保留历史

```bash
# 方法 1: Oh My Tmux! 内置
Ctrl-l                  # 清屏并清除 tmux 历史

# 方法 2: 只清屏不清历史
clear
# 或
<prefix> : clear-history   # 清除 tmux 历史缓冲区
```

### 搜索窗格历史内容

```bash
<prefix> [              # 进入复制模式
/搜索词                  # 向下搜索
?搜索词                  # 向上搜索
n                       # 下一个匹配
N                       # 上一个匹配
```

### 复制整个命令输出

```bash
# 1. 执行命令
ls -l ~/Downloads

# 2. 进入复制模式
<prefix> [

# 3. 移动到输出开始位置
# 使用 /命令名 搜索到命令开头

# 4. 开始选择
v                       # 开始 visual 选择

# 5. 移动到结尾
G                       # 跳到底部

# 6. 复制
y                       # 复制选中内容

# 7. 粘贴到文件
<prefix> p              # 粘贴
```

### 实时查看日志文件

```bash
# 方式 1: 使用 tail -f
tail -f /var/log/app.log

# 在 tmux 中可以：
# - 用鼠标滚动查看历史
# - 用 <prefix> [ 进入复制模式暂停输出
# - 按 q 返回继续跟踪

# 方式 2: 使用 less +F
less +F /var/log/app.log
# 按 Ctrl-c 暂停
# 按 F 继续跟踪
```

### 比较两个窗格的输出

```bash
# 创建两个窗格
<prefix> -              # 水平分割

# 在两个窗格中执行命令
# 上方窗格：ls -l ~/Downloads
# 下方窗格：ls -l ~/Documents

# 同步滚动（需要手动实现）
# 可以在两个窗格中都进入复制模式
```

### 保存会话所有输出

```bash
# 方法 1: 使用 script 命令
script -f /tmp/session-log.txt
# 所有输出都会记录到文件
# Ctrl-d 结束记录

# 方法 2: 使用 tmux logging 插件
# 在 .tmux.conf.local 添加：
# set -g @plugin 'tmux-plugins/tmux-logging'
# <prefix> I 安装
# <prefix> Shift-p : 开始/停止日志记录
```

### 在窗格中运行长任务并分离

```bash
# 1. 运行长任务
find / -name "*.log" 2>/dev/null

# 2. 分离会话
<prefix> d

# 3. 稍后重新连接查看结果
tmux attach
<prefix> [              # 查看历史输出
```

---

## 实用命令组合

### 查看大目录的优雅方式

```bash
# 组合 1: 分页 + 颜色
ls -lh --color=always ~/Downloads | less -R

# 组合 2: 使用 tree 限制深度
tree -L 2 ~/Downloads | less

# 组合 3: 只看最近修改的文件
ls -lt ~/Downloads | head -20

# 组合 4: 按大小排序
ls -lhS ~/Downloads | less
```

### 搜索命令输出

```bash
# 直接使用 grep
ls -l ~/Downloads | grep "pdf"

# 或先输出，再在 tmux 复制模式中搜索
ls -l ~/Downloads
<prefix> [
/pdf
```

### 复制部分输出

```bash
# 1. 执行命令
ls -l ~/Downloads

# 2. 进入复制模式
<prefix> [

# 3. 搜索定位
/filename

# 4. 选择
v                       # 开始选择
5j                      # 向下选择 5 行
y                       # 复制

# 5. 使用
<prefix> p              # 粘贴到当前窗格
# 或粘贴到其他应用（如果启用了剪贴板集成）
```

---

## 配置优化建议

### 增加历史记录（已完成）

```bash
# 在 ~/.tmux.conf.local
set -g history-limit 50000 #!important
```

### 启用鼠标模式（可选）

```bash
# 在 ~/.tmux.conf.local
set -g mouse on #!important
```

### 复制到系统剪贴板

```bash
# 在 ~/.tmux.conf.local
tmux_conf_copy_to_os_clipboard=true
```

### 添加自定义键绑定

```bash
# 在 ~/.tmux.conf.local 底部添加

# 快速捕获窗格内容到文件
bind-key S capture-pane -S - \; save-buffer /tmp/tmux-buffer.txt \; display "Buffer saved to /tmp/tmux-buffer.txt"

# 快速进入复制模式并跳到顶部
bind-key C copy-mode \; send-keys -X history-top

# 快速清除历史
bind-key X confirm-before -p "Clear history? (y/n)" "send-keys -R \; clear-history"
```

---

## 故障排查

### 鼠标滚轮不工作

```bash
# 检查鼠标模式
tmux show-options -g mouse

# 如果显示 off，启用它
<prefix> m
# 或
tmux set -g mouse on
```

### 历史记录滚动不够远

```bash
# 检查当前限制
tmux show-options -g history-limit

# 增加限制
tmux set -g history-limit 100000
```

### 复制模式下无法搜索

```bash
# 确保使用正确的搜索键
# Vi 模式（默认）:
/           # 向下搜索
?           # 向上搜索

# Emacs 模式:
Ctrl-s      # 向下搜索
Ctrl-r      # 向上搜索
```

---

## 最佳实践

1. **处理大量输出**
   - 优先使用 `| less` 而不是直接输出
   - 使用 `head`/`tail` 限制输出行数
   - 启用 tmux 鼠标模式方便滚动

2. **保存重要输出**
   - 重要命令输出重定向到文件
   - 使用 `tee` 同时查看和保存: `command | tee output.txt`

3. **搜索历史输出**
   - 小量数据：直接在复制模式中搜索
   - 大量数据：导出到文件用 vim/grep 搜索

4. **长时间运行的任务**
   - 使用 tmux 会话确保任务持续运行
   - 分离会话后可以安全关闭终端

---

## 速查卡

| 需求 | 方法 | 快捷键/命令 |
|------|------|-------------|
| 查看历史输出 | 复制模式 | `<prefix> [` |
| 翻页浏览 | 复制模式 | `Ctrl-u/d` `Ctrl-b/f` |
| 搜索内容 | 复制模式 | `/` 或 `?` |
| 跳到顶部/底部 | 复制模式 | `g` / `G` |
| 使用鼠标滚动 | 启用鼠标 | `<prefix> m` |
| 长输出分页 | 管道 | `| less` |
| 保存输出 | 重定向 | `> file.txt` |
| 捕获窗格 | tmux 命令 | `capture-pane -S -` |
| 清除历史 | tmux 命令 | `<prefix> : clear-history` |

---

**提示**: 将此文件收藏，遇到问题时快速查阅！

生成时间: 2026-02-02
配置位置: /Users/lty/Tools/git_core/github/.tmux
