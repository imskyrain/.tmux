# Tmux 完整工作流指南

> 基于 Oh My Tmux! 配置的详细使用文档
>
> 配置位置: `/Users/lty/Tools/git_core/github/.tmux`

---

## 目录

1. [Tmux 原生功能](#tmux-原生功能)
2. [Oh My Tmux! 增强功能](#oh-my-tmux-增强功能)
3. [完整工作流](#完整工作流)
4. [键位绑定速查表](#键位绑定速查表)
5. [配置自定义](#配置自定义)

---

## Tmux 原生功能

### 什么是 Tmux?

Tmux (Terminal Multiplexer) 是一个终端复用器,允许你在一个终端窗口中运行多个终端会话。主要功能包括:

- **会话管理**: 创建、分离、重新连接会话
- **窗口管理**: 在一个会话中创建多个窗口(类似浏览器标签页)
- **窗格管理**: 在一个窗口中分割多个窗格(split panes)
- **持久化**: 即使断开连接,会话仍在后台运行

### 核心概念

```
会话 (Session)
  └── 窗口 (Window) 1
       ├── 窗格 (Pane) 1
       └── 窗格 (Pane) 2
  └── 窗口 (Window) 2
       └── 窗格 (Pane) 1
```

### 基本操作

#### 1. 会话管理

```bash
# 创建新会话
tmux new -s session_name

# 列出所有会话
tmux ls

# 附加到现有会话
tmux attach -t session_name
# 或简写
tmux a -t session_name

# 分离当前会话(在 tmux 内部)
<prefix> d

# 杀死会话
tmux kill-session -t session_name
```

#### 2. 前缀键 (Prefix Key)

Tmux 的所有命令都以"前缀键"开始。默认是 `Ctrl-b`,但本配置同时支持:

- **主前缀**: `Ctrl-b` (tmux 默认)
- **次前缀**: `Ctrl-a` (GNU Screen 风格,Oh My Tmux! 添加)

后续文档中 `<prefix>` 表示按下 `Ctrl-b` 或 `Ctrl-a`

#### 3. 窗口操作(原生)

```
<prefix> c        # 创建新窗口
<prefix> ,        # 重命名当前窗口
<prefix> &        # 关闭当前窗口
<prefix> n        # 切换到下一个窗口
<prefix> p        # 切换到上一个窗口
<prefix> 0-9      # 切换到指定编号的窗口
<prefix> w        # 列出所有窗口
```

#### 4. 窗格操作(原生)

```
<prefix> %        # 垂直分割窗格
<prefix> "        # 水平分割窗格
<prefix> x        # 关闭当前窗格
<prefix> o        # 切换到下一个窗格
<prefix> ;        # 切换到上一个活动窗格
<prefix> 方向键    # 切换窗格
<prefix> z        # 最大化/还原当前窗格
```

#### 5. 复制模式(原生)

```
<prefix> [        # 进入复制模式
空格键             # 开始选择
Enter            # 复制选中内容并退出
<prefix> ]        # 粘贴
```

---

## Oh My Tmux! 增强功能

Oh My Tmux! 是由 Gregory Pakosz 开发的精美 tmux 配置,提供了许多改进和增强。

### 1. 双前缀支持

- 默认 `Ctrl-b` 和 `Ctrl-a` 都可以作为前缀
- 方便从 GNU Screen 迁移的用户

### 2. 美化的状态栏

#### 左侧状态栏显示:
- `❐ #S`: 会话名称
- `↑ Xd Xh Xm`: 系统运行时间

#### 右侧状态栏显示:
- 前缀指示器: `⌨` (当按下 prefix 时显示)
- 鼠标模式指示器: `↗` (启用鼠标时显示)
- 配对指示器: `⚇`
- 同步指示器: `⚏` (窗格同步时显示)
- 电池状态: 电量百分比和充电/放电状态 `↑/↓`
- 时间: `%R` (24小时制)
- 日期: `%d %b`
- 用户名和主机名
- Root 警告: `!` (以 root 运行时显示)

### 3. 改进的窗格分割

```
<prefix> -        # 水平分割(上下分割) ←更直观
<prefix> _        # 垂直分割(左右分割) ←更直观
```

注意: 与 tmux 原生相反,这里的命名更符合直觉

### 4. Vim 风格的窗格导航

```
<prefix> h        # 移动到左侧窗格
<prefix> j        # 移动到下方窗格
<prefix> k        # 移动到上方窗格
<prefix> l        # 移动到右侧窗格
```

这些按键可重复(repeat),按下 prefix 后可连续按 hjkl

### 5. 窗格大小调整(Vim 风格)

```
<prefix> H        # 向左扩展窗格
<prefix> J        # 向下扩展窗格
<prefix> K        # 向上扩展窗格
<prefix> L        # 向右扩展窗格
```

### 6. 窗格交换

```
<prefix> >        # 与下一个窗格交换
<prefix> <        # 与上一个窗格交换
```

### 7. 增强的窗口导航

```
<prefix> Tab      # 切换到上一个活动窗口
<prefix> C-h      # 上一个窗口(可重复)
<prefix> C-l      # 下一个窗口(可重复)
```

### 8. 窗口交换

```
<prefix> C-S-H    # 将当前窗口向左移动
<prefix> C-S-L    # 将当前窗口向右移动
```

注意: C-S-H 表示 Ctrl-Shift-H

### 9. 会话管理增强

```
<prefix> C-c      # 创建新会话
<prefix> C-f      # 查找并切换会话
<prefix> BTab     # 切换到上一个会话
```

### 10. 窗格最大化

```
<prefix> +        # 最大化当前窗格到新窗口/还原
```

这比原生的 `<prefix> z` 更强大:
- 可以在最大化的窗格中继续分割
- 可以在多个窗口间切换而保持最大化状态
- 从源窗口或最大化窗口都可以还原

### 11. 鼠标模式切换

```
<prefix> m        # 开启/关闭鼠标模式
```

启用鼠标模式后可以:
- 点击选择窗格
- 点击选择窗口
- 拖动调整窗格大小
- 滚动浏览历史
- 鼠标选择文本自动进入复制模式

### 12. 配置文件快速编辑

```
<prefix> e        # 编辑 .tmux.conf.local 配置文件
<prefix> r        # 重新加载配置
```

### 13. 增强的复制模式(Vi 风格)

```
<prefix> Enter    # 进入复制模式

# 在复制模式中:
v                # 开始选择
C-v              # 切换块选择模式
H                # 跳到行首
L                # 跳到行尾
y                # 复制选中内容
Escape           # 取消
```

### 14. 剪贴板集成

- 复制的内容自动同步到系统剪贴板
- macOS: 使用 `pbcopy`
- Linux X11: 使用 `xsel` 或 `xclip`
- Linux Wayland: 使用 `wl-copy`
- Windows: 使用 `clip.exe`

### 15. 屏幕清除增强

```
C-l               # 清除屏幕和 tmux 历史记录
```

不需要前缀键,直接 Ctrl-l

### 16. 工具集成

```
<prefix> F        # 启动 Facebook PathPicker (如果安装)
<prefix> U        # 启动 Urlscan/Urlview 扫描 URL
```

### 17. TPM 插件管理器集成

```
<prefix> I        # 安装插件
<prefix> U        # 更新插件
<prefix> Alt-u    # 卸载插件
```

### 18. 配置选项(在 .tmux.conf.local 中)

当前配置的关键设置:

```bash
# 新窗格保持当前路径
tmux_conf_new_pane_retain_current_path=true

# 不保留 tmux 原生绑定(使用 Oh My Tmux! 绑定)
tmux_conf_preserve_stock_bindings=false

# 主题已启用
tmux_conf_theme=enabled

# 24 位真彩色支持(自动检测)
tmux_conf_24b_colour=auto

# 不自动复制到系统剪贴板
tmux_conf_copy_to_os_clipboard=false

# TPM 插件自动更新
tmux_conf_update_plugins_on_launch=true
tmux_conf_update_plugins_on_reload=true
```

### 19. 其他增强

- 窗口编号从 1 开始(不是 0)
- 窗格编号从 1 开始
- 自动重命名窗口
- 删除窗口后自动重新编号
- 历史记录 5000 行
- 快速响应时间(escape-time 10ms)
- 256 色支持
- 支持 SSH 连接状态显示
- 电池状态显示(笔记本)

---

## 完整工作流

### 场景 1: 日常开发工作流

```bash
# 1. 启动/恢复工作会话
tmux new -s dev
# 或恢复现有会话
tmux a -t dev

# 2. 创建项目窗口布局
<prefix> c              # 创建新窗口(编辑器)
<prefix> ,              # 命名为 "editor"

<prefix> c              # 创建新窗口(服务器)
<prefix> ,              # 命名为 "server"

<prefix> c              # 创建新窗口(测试)
<prefix> ,              # 命名为 "test"

# 3. 在编辑器窗口中设置布局
<prefix> -              # 水平分割(创建下方窗格)
<prefix> k              # 回到上方窗格
<prefix> _              # 垂直分割(创建右侧窗格)

# 现在有三个窗格:
# +-----------+-----------+
# |           |           |
# |   编辑器   |   文件树   |
# |           |           |
# +-----------+-----------+
# |      终端/Git         |
# +-----------------------+

# 4. 在服务器窗口
<prefix> C-l            # 切换到 server 窗口
<prefix> -              # 水平分割
# 上方运行开发服务器,下方查看日志

# 5. 窗格间导航
<prefix> h/j/k/l        # Vim 风格导航
<prefix> ;              # 回到上次的窗格

# 6. 调整窗格大小
<prefix> H              # 向左扩展
<prefix> J              # 向下扩展
<prefix> K              # 向上扩展
<prefix> L              # 向右扩展

# 7. 临时最大化某个窗格
<prefix> +              # 最大化当前窗格
# 完成后
<prefix> +              # 还原

# 8. 完成工作,分离会话
<prefix> d              # 会话在后台继续运行
```

### 场景 2: 远程服务器运维

```bash
# 1. SSH 到服务器并启动 tmux
ssh user@server
tmux new -s ops

# 2. 监控布局
<prefix> -              # 创建上下两个窗格
# 上方: htop (系统监控)
# 下方: tail -f /var/log/app.log (日志监控)

<prefix> c              # 新窗口用于操作

# 3. 需要执行长时间任务
# 启动任务后可以安全地分离
<prefix> d
# 断开 SSH

# 4. 稍后重新连接
ssh user@server
tmux a -t ops           # 任务仍在运行
```

### 场景 3: 多项目并行工作

```bash
# 为每个项目创建独立会话
tmux new -s project-A
<prefix> d

tmux new -s project-B
<prefix> d

tmux new -s project-C
<prefix> d

# 在会话间切换
<prefix> C-f            # 交互式选择会话
# 或
tmux a -t project-A

# 在 tmux 内切换会话
<prefix> C-f
# 输入会话名称

# 列出所有会话
tmux ls
```

### 场景 4: 结对编程

```bash
# 创建共享会话
tmux new -s pairing

# 另一个用户连接到同一台机器
tmux a -t pairing

# 两个用户看到相同的屏幕并可以同时控制

# 使用窗格同步(在所有窗格中输入相同命令)
# 需要在 .tmux.conf.local 中配置或手动执行:
<prefix> :
setw synchronize-panes on
```

### 场景 5: 使用鼠标进行快速操作

```bash
# 启动 tmux
tmux

# 启用鼠标模式
<prefix> m

# 现在可以:
# - 点击窗格切换焦点
# - 点击状态栏窗口切换
# - 拖动窗格边界调整大小
# - 用鼠标滚轮滚动历史
# - 拖动选择文本(自动进入复制模式)

# 完成后禁用鼠标模式
<prefix> m
```

### 场景 6: 复制粘贴工作流

```bash
# 方式 1: 使用 Vi 模式(推荐)
<prefix> Enter          # 进入复制模式
# 使用 hjkl 移动光标
v                      # 开始选择
# 使用 hjkl 扩展选择
y                      # 复制并退出
<prefix> p             # 粘贴

# 方式 2: 使用鼠标
<prefix> m             # 启用鼠标模式
# 拖动鼠标选择文本
# 自动复制到剪贴板(如果配置了)

# 方式 3: 使用缓冲区
<prefix> b             # 列出所有复制缓冲区
<prefix> P             # 选择要粘贴的缓冲区
```

---

## 键位绑定速查表

### 会话管理
| 快捷键 | 功能 |
|--------|------|
| `<prefix> C-c` | 创建新会话 |
| `<prefix> C-f` | 查找并切换会话 |
| `<prefix> d` | 分离当前会话 |
| `<prefix> BTab` | 切换到上一个会话 |

### 窗口管理
| 快捷键 | 功能 |
|--------|------|
| `<prefix> c` | 创建新窗口 |
| `<prefix> ,` | 重命名当前窗口 |
| `<prefix> &` | 关闭当前窗口 |
| `<prefix> C-h` | 上一个窗口 |
| `<prefix> C-l` | 下一个窗口 |
| `<prefix> Tab` | 最后活动窗口 |
| `<prefix> C-S-H` | 向左移动窗口 |
| `<prefix> C-S-L` | 向右移动窗口 |
| `<prefix> 0-9` | 切换到指定窗口 |
| `<prefix> w` | 窗口列表 |

### 窗格管理
| 快捷键 | 功能 |
|--------|------|
| `<prefix> -` | 水平分割(上下) |
| `<prefix> _` | 垂直分割(左右) |
| `<prefix> h` | 移动到左侧窗格 |
| `<prefix> j` | 移动到下方窗格 |
| `<prefix> k` | 移动到上方窗格 |
| `<prefix> l` | 移动到右侧窗格 |
| `<prefix> H` | 向左调整大小 |
| `<prefix> J` | 向下调整大小 |
| `<prefix> K` | 向上调整大小 |
| `<prefix> L` | 向右调整大小 |
| `<prefix> >` | 与下一个窗格交换 |
| `<prefix> <` | 与上一个窗格交换 |
| `<prefix> +` | 最大化/还原窗格 |
| `<prefix> x` | 关闭当前窗格 |
| `<prefix> z` | 切换窗格缩放 |
| `<prefix> !` | 将窗格移到新窗口 |

### 复制模式
| 快捷键 | 功能 |
|--------|------|
| `<prefix> Enter` | 进入复制模式 |
| `v` | 开始选择 |
| `C-v` | 切换块选择 |
| `y` | 复制选择 |
| `H` | 跳到行首 |
| `L` | 跳到行尾 |
| `Escape` | 退出复制模式 |
| `<prefix> p` | 粘贴 |
| `<prefix> P` | 选择缓冲区粘贴 |
| `<prefix> b` | 列出粘贴缓冲区 |

### 其他
| 快捷键 | 功能 |
|--------|------|
| `<prefix> m` | 切换鼠标模式 |
| `<prefix> e` | 编辑配置文件 |
| `<prefix> r` | 重新加载配置 |
| `<prefix> ?` | 列出所有键绑定 |
| `<prefix> :` | 进入命令模式 |
| `<prefix> t` | 显示时钟 |
| `<prefix> q` | 显示窗格编号 |
| `C-l` | 清除屏幕和历史 |
| `<prefix> F` | 启动 PathPicker |
| `<prefix> U` | 启动 Urlscan |
| `<prefix> I` | 安装 TPM 插件 |

---

## 配置自定义

### 编辑配置文件

```bash
# 在 tmux 中
<prefix> e

# 或直接编辑
vim ~/.tmux.conf.local
```

### 常用自定义选项

#### 1. 更改前缀键为纯 Ctrl-a

```bash
# 在 .tmux.conf.local 中添加
set -gu prefix2
unbind C-a
unbind C-b
set -g prefix C-a
bind C-a send-prefix
```

#### 2. 启用鼠标模式(默认开启)

```bash
set -g mouse on #!important
```

#### 3. 新窗口保持当前路径

```bash
tmux_conf_new_window_retain_current_path=true
```

#### 4. 复制到系统剪贴板

```bash
tmux_conf_copy_to_os_clipboard=true
```

#### 5. 启用 Powerline 符号

在 .tmux.conf.local 中取消注释:

```bash
tmux_conf_theme_left_separator_main='\uE0B0'
tmux_conf_theme_left_separator_sub='\uE0B1'
tmux_conf_theme_right_separator_main='\uE0B2'
tmux_conf_theme_right_separator_sub='\uE0B3'
```

#### 6. 自定义状态栏

```bash
# 移动状态栏到顶部
set -g status-position top
```

#### 7. 增加历史记录

```bash
set -g history-limit 10000 #!important
```

#### 8. 强制 Vi 模式

```bash
set -g status-keys vi #!important
set -g mode-keys vi #!important
```

### 重要标记 #!important

在自定义配置时,如果 Oh My Tmux! 覆盖了你的设置,在行尾添加 `#!important`:

```bash
bind c new-window -c '#{pane_current_path}' #!important
set -g default-terminal "screen-256color" #!important
```

### 应用配置

```bash
# 方式 1: 在 tmux 中
<prefix> r

# 方式 2: 命令行
tmux source ~/.tmux.conf

# 方式 3: tmux 命令模式
<prefix> :
source ~/.tmux.conf
```

---

## 高级技巧

### 1. 命名窗口和会话的约定

```bash
# 会话命名
tmux new -s work      # 工作相关
tmux new -s personal  # 个人项目
tmux new -s server    # 服务器维护

# 窗口命名示例
0: editor
1: server
2: db
3: logs
4: git
```

### 2. 脚本化 Tmux 布局

创建 `~/.tmux/layouts/dev.sh`:

```bash
#!/bin/bash
SESSION="dev"

# 创建会话
tmux new-session -d -s $SESSION -n editor

# 编辑器窗口布局
tmux split-window -h -t $SESSION:editor
tmux split-window -v -t $SESSION:editor.1

# 创建其他窗口
tmux new-window -t $SESSION -n server
tmux new-window -t $SESSION -n git

# 选择默认窗口
tmux select-window -t $SESSION:editor

# 附加到会话
tmux attach -t $SESSION
```

使用:
```bash
chmod +x ~/.tmux/layouts/dev.sh
~/.tmux/layouts/dev.sh
```

### 3. Tmux 与 Vim 集成

安装 vim-tmux-navigator 插件,实现无缝窗格切换:

```vim
" 在 .vimrc 中
Plug 'christoomey/vim-tmux-navigator'
```

然后可以在 Vim 和 Tmux 窗格间用 Ctrl-hjkl 自由移动

### 4. 保存和恢复会话(使用 tmux-resurrect)

在 `.tmux.conf.local` 中启用:

```bash
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @continuum-restore 'on'
```

然后:
```
<prefix> I        # 安装插件
<prefix> Ctrl-s   # 保存会话
<prefix> Ctrl-r   # 恢复会话
```

### 5. 使用 tmuxinator 管理复杂项目

安装 tmuxinator:
```bash
gem install tmuxinator
```

创建项目配置:
```bash
tmuxinator new myproject
```

配置示例 `~/.tmuxinator/myproject.yml`:
```yaml
name: myproject
root: ~/projects/myproject

windows:
  - editor:
      layout: main-vertical
      panes:
        - vim
        - guard
  - server: bundle exec rails s
  - logs: tail -f log/development.log
```

启动:
```bash
tmuxinator start myproject
```

---

## 常见问题

### Q: 如何查看所有键绑定?
A: `<prefix> ?` 或 `tmux list-keys`

### Q: 颜色显示不正确?
A: 确保终端支持 256 色,设置:
```bash
export TERM=xterm-256color
```

### Q: 鼠标滚轮不工作?
A: 启用鼠标模式: `<prefix> m`

### Q: 如何在多个窗格中同时输入命令?
A:
```
<prefix> :
setw synchronize-panes on
# 完成后关闭
setw synchronize-panes off
```

### Q: 如何复制 tmux 输出到系统剪贴板?
A: 设置 `tmux_conf_copy_to_os_clipboard=true` 并确保安装了:
- macOS: 内置 pbcopy
- Linux: xsel 或 xclip 或 wl-copy

### Q: SSH 断开后会话会丢失吗?
A: 不会,tmux 会话在服务器上持续运行,重新连接后可以恢复。

### Q: 如何发送字面的 Ctrl-a 到程序(如 shell)?
A: `<prefix> C-a` (按两次)

---

## 资源链接

- [Tmux 官方文档](https://github.com/tmux/tmux/wiki)
- [Oh My Tmux! GitHub](https://github.com/gpakosz/.tmux)
- [Tmux 速查表](https://tmuxcheatsheet.com/)
- [Tmux Book by Brian Hogan](https://pragprog.com/titles/bhtmux3/tmux-3/)

---

## 本地配置信息

```
配置文件位置: ~/.tmux.conf -> ~/.tmux/.tmux.conf
自定义配置文件: ~/.tmux.conf.local
Oh My Tmux 目录: /Users/lty/Tools/git_core/github/.tmux (通过符号链接)
```

### 快速命令

```bash
# 编辑自定义配置
vim ~/.tmux.conf.local

# 重新加载配置
tmux source ~/.tmux.conf
# 或在 tmux 中
<prefix> r

# 查看当前配置
tmux show-options -g

# 查看当前键绑定
tmux list-keys
```

---

## 总结

Tmux 是提高终端工作效率的强大工具,配合 Oh My Tmux! 的美化和增强功能:

1. **会话持久化**: 即使断开连接工作也不丢失
2. **多任务管理**: 一个终端管理多个项目
3. **高效布局**: 自由分割和组织工作空间
4. **远程友好**: 低带宽下也能流畅使用
5. **可定制性**: 完全可以按个人喜好配置

建议循序渐进:
1. 先掌握基本的会话、窗口、窗格操作
2. 熟悉 Vim 风格的导航
3. 学习复制粘贴工作流
4. 根据需求自定义配置
5. 探索高级功能和插件

Happy tmuxing! 🚀
