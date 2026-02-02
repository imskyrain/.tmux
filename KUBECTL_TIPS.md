# Kubectl + Tmux 高效技巧

## 快速提取 Pod 名称的方法

### 场景
```bash
kubectl get pods
# 输出：
sxb-v2-front-test-4046941-77bd66bdcf-6z6zf            1/1     Running   0          54d
xiaocheng-v2-front-test-4048002-79dd9dd5c4-ht9gb      1/1     Running   0          4d5h
xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d         1/1     Running   13         192d

# 想要提取: xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d
```

---

## 方法对比

| 方法 | 速度 | 精确度 | 推荐度 |
|------|------|--------|--------|
| **tmux-thumbs** | ⚡️⚡️⚡️ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **grep + awk** | ⚡️⚡️⚡️ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **kubectl 补全** | ⚡️⚡️⚡️ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **复制模式** | ⚡️⚡️ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **鼠标双击** | ⚡️⚡️ | ⭐⭐⭐ | ⭐⭐⭐ |

---

## 方法 1: tmux-thumbs（最快捷）

### 操作步骤
```bash
# 1. 执行命令
kubectl get pods

# 2. 激活 thumbs
<prefix> + Space

# 3. 屏幕显示：
[a]sxb-v2-front-test-4046941-77bd66bdcf-6z6zf
[s]xiaocheng-v2-front-test-4048002-79dd9dd5c4-ht9gb
[d]xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d

# 4. 按 'd' → 复制完整 Pod 名称

# 5. 使用
kubectl logs xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d
# 或
kubectl exec -it <Cmd+V> -- /bin/bash
```

---

## 方法 2: grep + awk（编程式）

### 基本用法
```bash
# 提取并复制
kubectl get pods | grep xingcai | awk '{print $1}' | pbcopy

# 或直接使用在命令中
POD=$(kubectl get pods | grep xingcai | awk '{print $1}')
kubectl logs $POD
```

### 进阶：函数封装
```bash
# 添加到 ~/.bashrc 或 ~/.zshrc
kpod() {
    kubectl get pods | grep "$1" | awk '{print $1}'
}

# 使用
kubectl logs $(kpod xingcai)
kubectl exec -it $(kpod xingcai) -- /bin/bash
```

### 更强大的版本
```bash
# 提取并选择（如果有多个匹配）
kpod() {
    kubectl get pods | grep "$1" | awk '{print $1}' | fzf
}

# 使用 fzf 交互式选择
kubectl logs $(kpod xingcai)
```

---

## 方法 3: kubectl 自动补全（最专业）

### 安装配置

#### Bash
```bash
# 安装补全
kubectl completion bash > ~/.kubectl-completion
echo 'source ~/.kubectl-completion' >> ~/.bashrc

# 或使用 homebrew bash-completion
brew install bash-completion@2
kubectl completion bash > $(brew --prefix)/etc/bash_completion.d/kubectl
```

#### Zsh
```bash
# 启用补全
echo 'source <(kubectl completion zsh)' >> ~/.zshrc
# 或
echo 'autoload -Uz compinit; compinit' >> ~/.zshrc
echo 'source <(kubectl completion zsh)' >> ~/.zshrc

# 重新加载
source ~/.zshrc
```

### 使用自动补全
```bash
# 输入命令后按 Tab
kubectl logs xing<Tab>
# 自动补全为：
kubectl logs xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d

# 多个匹配时显示列表
kubectl logs x<Tab><Tab>
```

### 配合 alias
```bash
# 添加到 ~/.bashrc 或 ~/.zshrc
alias k='kubectl'
alias kl='kubectl logs'
alias kx='kubectl exec -it'
alias kg='kubectl get'

# 使用
kl xing<Tab>  # 自动补全
kx xing<Tab> -- /bin/bash
```

---

## 方法 4: 使用复制模式

### 操作步骤
```bash
# 1. 执行命令
kubectl get pods

# 2. 进入复制模式
<prefix> [

# 3. 搜索定位
/xingcai

# 4. 选择 Pod 名称
# 方式 A: 单词选择
w                   # 移到下一个单词开始
v                   # 开始选择
e                   # 选到单词末尾
y                   # 复制

# 方式 B: 行选择
V                   # 选择整行
y                   # 复制

# 5. 提取 Pod 名称（如果复制了整行）
# 需要手动编辑去掉其他列
```

---

## 方法 5: 鼠标操作

### 使用双击
```bash
# 1. 执行命令
kubectl get pods

# 2. 双击 Pod 名称
# 注意：可能只选中一部分（因为有连字符）

# 3. 使用 Option + 拖动
按住 Option + 精确拖动选择整个 Pod 名称

# 4. 自动复制到剪贴板
```

### 使用三击
```bash
# 三击选择整行
# 然后需要手动提取 Pod 名称部分
```

---

## 方法 6: 使用 kubectx/kubens（工具增强）

### 安装
```bash
# macOS
brew install kubectx

# 包含工具：
# - kubectx: 切换 context
# - kubens: 切换 namespace
```

### 配合 fzf
```bash
# 安装 fzf
brew install fzf

# kubectx 和 kubens 会自动使用 fzf 进行交互式选择
```

---

## 实用场景

### 场景 1: 查看日志
```bash
# 方式 A: thumbs
kubectl get pods
<prefix> + Space
# 按字母选择
kubectl logs <Cmd+V>

# 方式 B: grep
kubectl logs $(kubectl get pods | grep xingcai | awk '{print $1}')

# 方式 C: 自动补全
kubectl logs xing<Tab>

# 方式 D: 函数
kl() { kubectl logs $(kubectl get pods | grep "$1" | awk '{print $1}') "$@"; }
kl xingcai -f  # 跟踪日志
```

### 场景 2: 进入容器
```bash
# 方式 A: thumbs
kubectl get pods
<prefix> + Space
kubectl exec -it <Cmd+V> -- /bin/bash

# 方式 B: 函数
ksh() { kubectl exec -it $(kubectl get pods | grep "$1" | awk '{print $1}') -- /bin/bash; }
ksh xingcai

# 方式 C: 自动补全
kubectl exec -it xing<Tab> -- /bin/bash
```

### 场景 3: 描述 Pod
```bash
# 使用 thumbs
kubectl get pods
<prefix> + Space
kubectl describe pod <Cmd+V>

# 或
kdesc() { kubectl describe pod $(kubectl get pods | grep "$1" | awk '{print $1}'); }
kdesc xingcai
```

### 场景 4: 删除 Pod
```bash
# 安全删除（先确认）
kubectl get pods | grep xingcai
# 确认是正确的 Pod
<prefix> + Space
kubectl delete pod <Cmd+V>

# 或
kdel() {
    POD=$(kubectl get pods | grep "$1" | awk '{print $1}')
    echo "将删除: $POD"
    read -p "确认删除? (y/N) " -n 1 -r
    echo
    if [[ $REPLY =~ ^[Yy]$ ]]; then
        kubectl delete pod $POD
    fi
}
kdel xingcai
```

---

## 推荐的工作流配置

### 1. 在 ~/.bashrc 或 ~/.zshrc 中添加

```bash
# kubectl 别名
alias k='kubectl'
alias kg='kubectl get'
alias kd='kubectl describe'
alias kl='kubectl logs'
alias kx='kubectl exec -it'

# 快捷函数
# 通过名称片段获取 Pod 名称
kpod() {
    kubectl get pods | grep "$1" | awk '{print $1}'
}

# 查看日志
klogs() {
    local pod=$(kubectl get pods | grep "$1" | awk '{print $1}')
    shift
    kubectl logs $pod "$@"
}

# 进入容器
kexec() {
    local pod=$(kubectl get pods | grep "$1" | awk '{print $1}')
    shift
    kubectl exec -it $pod -- "${@:-/bin/bash}"
}

# 描述 Pod
kdesc() {
    kubectl describe pod $(kubectl get pods | grep "$1" | awk '{print $1}')
}

# 获取 Pod 的 YAML
kyaml() {
    kubectl get pod $(kubectl get pods | grep "$1" | awk '{print $1}') -o yaml
}

# 端口转发
kpf() {
    local pod=$(kubectl get pods | grep "$1" | awk '{print $1}')
    local port=${2:-8080}
    kubectl port-forward $pod $port:$port
}
```

### 2. 启用 kubectl 补全

```bash
# Zsh
echo 'source <(kubectl completion zsh)' >> ~/.zshrc

# Bash
kubectl completion bash > ~/.kubectl-completion
echo 'source ~/.kubectl-completion' >> ~/.bashrc
```

### 3. 使用示例

```bash
# 重新加载配置
source ~/.zshrc  # 或 source ~/.bashrc

# 使用新函数
klogs xingcai -f              # 跟踪日志
kexec xingcai                 # 进入容器
kdesc xingcai                 # 描述 Pod
kyaml xingcai                 # 获取 YAML
kpf xingcai 8080              # 端口转发
```

---

## 配合 fzf 的高级用法

### 安装 fzf
```bash
brew install fzf
# 安装 key bindings 和 fuzzy completion
$(brew --prefix)/opt/fzf/install
```

### 交互式选择 Pod

```bash
# 函数：交互式选择 Pod
kpod-select() {
    kubectl get pods | grep -v NAME | fzf | awk '{print $1}'
}

# 使用
kubectl logs $(kpod-select)
kubectl exec -it $(kpod-select) -- /bin/bash

# 或封装成函数
kl-fzf() {
    local pod=$(kubectl get pods | grep -v NAME | fzf | awk '{print $1}')
    kubectl logs $pod "$@"
}

kx-fzf() {
    local pod=$(kubectl get pods | grep -v NAME | fzf | awk '{print $1}')
    kubectl exec -it $pod -- "${@:-/bin/bash}"
}
```

### 使用效果
```bash
kl-fzf
# 会显示交互式列表：
> sxb-v2-front-test-4046941-77bd66bdcf-6z6zf
  xiaocheng-v2-front-test-4048002-79dd9dd5c4-ht9gb
  xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d

# 输入过滤：
> xing
  xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d

# 回车选择
```

---

## tmux 专用技巧

### 技巧 1: 在新窗格中显示日志

```bash
# 分割窗格并查看日志
<prefix> -                    # 水平分割
# 在新窗格中
kubectl get pods
<prefix> + Space
kubectl logs -f <Cmd+V>

# 现在可以：
# - 上方窗格：执行命令
# - 下方窗格：实时查看日志
```

### 技巧 2: 创建专用窗口

```bash
# 创建 kubectl 专用窗口
<prefix> c                    # 新窗口
<prefix> ,                    # 重命名
# 输入: k8s

# 在此窗口中：
# - 一个窗格运行 kubectl get pods --watch
# - 另一个窗格执行操作
```

### 技巧 3: 使用 tmux 环境变量

```bash
# 设置环境变量存储常用 Pod
<prefix> :
set-environment POD_NAME xingcai-v2-front-test-4046967-c6bbbdc99-p5t4d

# 使用
kubectl logs $POD_NAME
kubectl exec -it $POD_NAME -- /bin/bash
```

---

## 快速参考卡

### 提取 Pod 名称

| 方法 | 命令 | 速度 |
|------|------|------|
| **thumbs** | `<prefix> + Space` | ⚡️⚡️⚡️ |
| **grep** | `grep xingcai \| awk '{print $1}'` | ⚡️⚡️⚡️ |
| **补全** | `kubectl logs xing<Tab>` | ⚡️⚡️⚡️ |
| **fzf** | `kubectl get pods \| fzf` | ⚡️⚡️ |
| **复制模式** | `<prefix> [ → /xingcai → v → y` | ⚡️⚡️ |

### 常用操作

| 操作 | 快捷方式 |
|------|----------|
| 查看日志 | `klogs xingcai -f` |
| 进入容器 | `kexec xingcai` |
| 描述 Pod | `kdesc xingcai` |
| 获取 YAML | `kyaml xingcai` |
| 端口转发 | `kpf xingcai 8080` |

---

## 最佳实践建议

### ✅ 推荐

1. **使用 tmux-thumbs**
   - 最快速，可视化

2. **启用 kubectl 自动补全**
   - 减少输入，防止错误

3. **创建常用函数**
   - klogs, kexec 等

4. **使用别名**
   - k, kg, kl 等

5. **配合 fzf**
   - 交互式选择更安全

### ❌ 避免

1. ❌ 手动输入长 Pod 名称
   - 容易出错

2. ❌ 直接复制粘贴不验证
   - 可能操作错误的 Pod

3. ❌ 不使用工具辅助
   - 效率低

---

## 总结

### 最快的方法（按场景）

1. **临时操作**: tmux-thumbs
   ```
   <prefix> + Space → 按字母
   ```

2. **脚本/自动化**: grep + awk
   ```
   kubectl get pods | grep pattern | awk '{print $1}'
   ```

3. **日常使用**: kubectl 补全 + alias
   ```
   kl xing<Tab>
   ```

4. **复杂筛选**: fzf
   ```
   kubectl logs $(kubectl get pods | fzf | awk '{print $1}')
   ```

---

**建议**: 组合使用多种方法，根据场景选择最合适的！

生成时间: 2026-02-02
