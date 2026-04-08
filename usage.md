# Oh my tmux! 使用说明

个人备忘速查手册。基于 [gpakosz/.tmux](https://github.com/gpakosz/.tmux) 配置。

---

## 目录

- [tmux 基础概念](#tmux-基础概念)
- [安装与配置](#安装与配置)
- [前缀键](#前缀键)
- [快捷键速查](#快捷键速查)
- [复制模式](#复制模式)
- [状态栏配置](#状态栏配置)
- [主题定制](#主题定制)
- [TPM 插件管理](#tpm-插件管理)
- [自定义变量](#自定义变量)
- [常用场景与技巧](#常用场景与技巧)

---

## tmux 基础概念

tmux 的三层结构：

```
Server
 └── Session（会话）
      └── Window（窗口）
           └── Pane（面板）
```

| 概念 | 说明 |
|------|------|
| **Session** | 一组窗口的集合，可以断开后重新连接（核心价值） |
| **Window** | 一个 Session 里的标签页，每个 Window 占满整个终端 |
| **Pane** | 一个 Window 内的分屏区域 |

基本命令：

```bash
tmux                          # 启动新 session
tmux new -s name              # 启动并命名 session
tmux ls                       # 列出所有 session
tmux attach -t name           # 重新连接到 session
tmux kill-session -t name     # 关闭 session
```

---

## 安装与配置

**自动安装：**

```bash
curl -fsSL "https://github.com/gpakosz/.tmux/raw/refs/heads/master/install.sh#$(date +%s)" | bash
```

**手动安装（安装到 ~）：**

```bash
cd
git clone --single-branch https://github.com/gpakosz/.tmux.git
ln -s -f .tmux/.tmux.conf
cp .tmux/.tmux.conf.local .
```

**重要规则：**
- 永远不要修改 `.tmux.conf` 主文件
- 所有自定义都在 `.tmux.conf.local` 中进行
- 如果需要覆盖 Oh my tmux! 的设置，在行尾加 `#!important`

```bash
bind c new-window -c '#{pane_current_path}' #!important
set -g default-terminal "screen-256color" #!important
```

**依赖：**
- tmux >= 2.6
- awk, perl (含 Time::HiRes), grep, sed
- 终端外需设置 `TERM=xterm-256color`
- 复制到系统剪贴板需要 `xsel`、`xclip` 或 `wl-copy` (Linux)

---

## 前缀键

Oh my tmux! 支持两个前缀键：

| 前缀 | 按键 |
|------|------|
| 默认前缀 | `Ctrl + b` |
| 第二前缀 | `Ctrl + a` |

下文中 `<prefix>` 表示先按任一前缀键，再按后续按键。

如果想只用 `C-a` 替代 `C-b`，在 `.tmux.conf.local` 中取消注释：

```bash
set -gu prefix2
unbind C-a
unbind C-b
set -g prefix C-a
bind C-a send-prefix
```

---

## 快捷键速查

### 通用

| 快捷键 | 功能 |
|--------|------|
| `<prefix> e` | 编辑 `.tmux.conf.local` 配置文件（用 `$VISUAL` 或 `$EDITOR` 打开） |
| `<prefix> r` | 重新加载配置 |
| `C-l` | 清屏 + 清除 tmux 历史 |

### Session 管理

| 快捷键 | 功能 |
|--------|------|
| `<prefix> C-c` | 创建新 session |
| `<prefix> C-f` | 按名称切换 session |
| `<prefix> d` | 断开（detach）当前 session |
| `<prefix> $` | 重命名当前 session |

### Window 管理

| 快捷键 | 功能 |
|--------|------|
| `<prefix> c` | 新建 window |
| `<prefix> C-h` | 切换到上一个 window |
| `<prefix> C-l` | 切换到下一个 window |
| `<prefix> Tab` | 切换到上一个活动 window |
| `<prefix> 0-9` | 切换到指定编号的 window |
| `<prefix> ,` | 重命名当前 window |

### Pane 管理

| 快捷键 | 功能 |
|--------|------|
| `<prefix> -` | 垂直分屏（上下） |
| `<prefix> _` | 水平分屏（左右） |
| `<prefix> h` | 移动到左边 pane |
| `<prefix> j` | 移动到下面 pane |
| `<prefix> k` | 移动到上面 pane |
| `<prefix> l` | 移动到右边 pane |
| `<prefix> H` | 向左调整 pane 大小 |
| `<prefix> J` | 向下调整 pane 大小 |
| `<prefix> K` | 向上调整 pane 大小 |
| `<prefix> L` | 向右调整 pane 大小 |
| `<prefix> <` | 与前一个 pane 交换位置 |
| `<prefix> >` | 与后一个 pane 交换位置 |
| `<prefix> +` | 将当前 pane 最大化到新 window（再按一次恢复） |
| `<prefix> q` | 显示 pane 编号 |
| `<prefix> x` | 关闭当前 pane |
| `<prefix> z` | 缩放/还原当前 pane（内置功能） |

> **`<prefix> +` vs `<prefix> z`**：`+` 是 Oh my tmux! 特有功能，将 pane 提取到独立 window，可以继续分屏；`z` 是 tmux 内置缩放，只是临时放大。

### 鼠标与工具

| 快捷键 | 功能 |
|--------|------|
| `<prefix> m` | 开关鼠标模式（鼠标模式下可用鼠标选择 pane/window、调整大小、选择文本） |
| `<prefix> U` | 启动 Urlscan/Urlview（提取当前 pane 中的 URL） |
| `<prefix> F` | 启动 Facebook PathPicker |
| `<prefix> t` | 显示时钟 |

---

## 复制模式

| 快捷键 | 功能 |
|--------|------|
| `<prefix> Enter` | 进入复制模式 |
| `<prefix> b` | 列出粘贴缓冲区 |
| `<prefix> p` | 从顶部缓冲区粘贴 |
| `<prefix> P` | 选择缓冲区粘贴 |

### 复制模式下的按键（vi 风格）

| 按键 | 功能 |
|------|------|
| `v` | 开始选择 |
| `C-v` | 切换块选择 / 普通选择 |
| `H` | 跳到行首 |
| `L` | 跳到行尾 |
| `y` | 复制选中内容 |
| `Escape` | 取消 |

**复制到系统剪贴板**：在 `.tmux.conf.local` 中设置：

```bash
tmux_conf_copy_to_os_clipboard=true
```

Linux 下需安装 `xsel`、`xclip` 或 `wl-copy`。

---

## 状态栏配置

编辑 `.tmux.conf.local` 中的以下变量来定制状态栏：

```bash
tmux_conf_theme_status_left=" ❐ #S | ↑... "
tmux_conf_theme_status_right=" #{prefix}#{mouse}... | #{username}#{root} | #{hostname} "
```

### 内置变量

| 变量 | 说明 |
|------|------|
| `#{battery_bar}` | 电池横条 |
| `#{battery_hbar}` | 电池横条（单字符宽） |
| `#{battery_vbar}` | 电池竖条（单字符宽） |
| `#{battery_percentage}` | 电池百分比 |
| `#{battery_status}` | 充电/放电状态 |
| `#{circled_session_name}` | 圈数字 session 名 (⓪-⑳) |
| `#{hostname}` | 主机名（SSH/Mosh 感知） |
| `#{hostname_ssh}` | 主机名（仅 SSH/Mosh 连接时显示） |
| `#{loadavg}` | 系统负载 |
| `#{pairing}` | 是否多客户端共享 |
| `#{prefix}` | 是否按下了前缀键 |
| `#{root}` | 是否 root 用户 |
| `#{synchronized}` | pane 是否同步 |
| `#{uptime_y}` / `#{uptime_d}` / `#{uptime_h}` / `#{uptime_m}` | 系统运行时间 |
| `#{username}` | 用户名（SSH/Mosh 感知） |
| `#{username_ssh}` | 用户名（仅 SSH/Mosh 连接时显示） |
| `#{pretty_pane_current_path}` | 美化的当前路径 |

### 加入天气信息示例

```bash
tmux_conf_theme_status_right='#{prefix}#{pairing}#{synchronized} #(curl -m 1 wttr.in?format=3 2>/dev/null; sleep 900) , %R , %d %b | #{username}#{root} | #{hostname} '
```

> `%` 是 strftime 的特殊字符，需要用 `%%` 转义。

---

## 主题定制

### 颜色

`.tmux.conf.local` 中有 17 个颜色变量 (`tmux_conf_theme_colour_1` ~ `_17`)，用 hex 值或 tmux colour 名定义。默认是深色主题。

### Powerline 风格

需要安装 Powerline 字体或 `PowerlineSymbols.otf`，然后在 `.tmux.conf.local` 中设置：

```bash
tmux_conf_theme_left_separator_main='\uE0B0'
tmux_conf_theme_left_separator_sub='\uE0B1'
tmux_conf_theme_right_separator_main='\uE0B2'
tmux_conf_theme_right_separator_sub='\uE0B3'
```

推荐字体：[Source Code Pro](https://github.com/adobe-fonts/source-code-pro) 或 [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts)。

### 常用主题选项

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `tmux_conf_theme_highlight_focused_pane` | 高亮当前 pane | `false` |
| `tmux_conf_theme_pane_border_style` | 边框样式 | `thin` |
| `tmux_conf_theme_clock_style` | 时钟格式 (12/24) | `24` |
| `tmux_conf_24b_colour` | 24-bit 颜色支持 | `auto` |

---

## TPM 插件管理

Oh my tmux! 内置 TPM 支持，不需要手动安装 tpm。

**启用插件**：在 `.tmux.conf.local` 中添加：

```bash
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
```

**注意事项：**
- 不要添加 `set -g @plugin 'tmux-plugins/tpm'`
- 不要添加 `run '~/.tmux/plugins/tpm/tpm'`
- 不支持 `set -g @tpm_plugins` 语法

**TPM 快捷键（与上游略有不同）：**

| 快捷键 | 功能 |
|--------|------|
| `<prefix> + I` | 安装插件 |
| `<prefix> + u` | 更新插件 |
| `<prefix> + Alt + u` | 卸载插件 |

**自动行为控制：**

```bash
tmux_conf_update_plugins_on_launch=true    # 启动时自动更新
tmux_conf_update_plugins_on_reload=true    # 重载配置时自动更新
tmux_conf_uninstall_plugins_on_reload=true # 无插件时自动卸载 tpm
```

---

## 自定义变量

可以在 `.tmux.conf.local` 底部的特定区域定义 POSIX shell 函数，创建自定义状态栏变量。

函数必须定义在 `# EOF` 和 `# "$@"` 之间，且左花括号 `{` 必须和函数名在同一行：

```bash
# EOF

# usage: #{weather}
weather() {
  curl -f -s -m 2 'wttr.in?format=3' || printf '\n'
  sleep 900
}

# usage: #{online}
online() {
  ping -c 1 1.1.1.1 >/dev/null 2>&1 && printf '✔' || printf '✘'
}

# usage: #{wan_ip_v4}
wan_ip_v4() {
  curl -f -s -m 2 -4 ifconfig.me
  sleep 300
}

"$@"
```

> 注意：函数前面的 `# ` 注释前缀是必需的，不要去掉。

然后在状态栏变量中使用 `#{weather}`、`#{online}` 等。

---

## 常用场景与技巧

### 1. 远程开发

Oh my tmux! 自动识别 SSH/Mosh 连接，状态栏会显示远程的用户名和主机名。

**SSH 自动重连**：在 `.tmux.conf.local` 中启用：

```bash
tmux_conf_new_pane_reconnect_ssh=true   # 新 pane 自动重连 SSH
tmux_conf_new_window_reconnect_ssh=true # 新 window 自动重连 SSH
```

**远程工作流**：

```bash
ssh remote-server                  # 连到远程
tmux new -s work                   # 新建 session
# ... 工作中 ...
# 网络断开也没关系，重新连接后：
ssh remote-server
tmux attach -t work                # 恢复工作现场
```

### 2. 多窗口工作流

```
<prefix> c          # 创建新 window 给不同任务
<prefix> ,          # 给 window 起个有意义的名字
<prefix> C-h / C-l  # 在 window 间快速切换
<prefix> Tab        # 跳回上一个 window
```

### 3. 分屏开发

```
<prefix> -          # 上下分屏：上面代码，下面终端
<prefix> _          # 左右分屏：左边代码，右边日志
<prefix> h/j/k/l    # vim 风格在 pane 间移动
<prefix> +          # 临时把一个 pane 放大到独立窗口
<prefix> +          # 再按一次恢复
```

### 4. 保留工作路径

```bash
tmux_conf_new_pane_retain_current_path=true    # 新 pane 保留当前路径（默认开启）
tmux_conf_new_window_retain_current_path=true  # 新 window 也保留当前路径
```

### 5. 同步多个 Pane

用于同时在多台机器上执行相同命令：

```
<prefix> :setw synchronize-panes on    # 开启同步
# 所有 pane 会同时接收键盘输入
<prefix> :setw synchronize-panes off   # 关闭同步
```

同步状态会在 pane 边框颜色和状态栏指示器上体现。

### 6. 移动状态栏到顶部

在 `.tmux.conf.local` 中取消注释：

```bash
set -g status-position top
```

### 7. 启用鼠标模式

```bash
set -g mouse on   # 在 .tmux.conf.local 中添加
```

或者在运行时用 `<prefix> m` 切换。鼠标模式下可以：
- 点击选择 pane 和 window
- 拖拽调整 pane 大小
- 滚轮进入复制模式并滚动

### 8. Windows (WSL) 下使用

推荐使用 WSL + Windows Terminal，不推荐 Cygwin（fork 性能太差）。

---

## 配置选项速查表

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `tmux_conf_preserve_stock_bindings` | 保留 tmux 原生快捷键 | `false` |
| `tmux_conf_new_session_prompt` | 新建 session 时提示输入名称 | `false` |
| `tmux_conf_new_session_retain_current_path` | 新 session 保留路径 | `false` |
| `tmux_conf_new_window_retain_current_path` | 新 window 保留路径 | `false` |
| `tmux_conf_new_window_reconnect_ssh` | 新 window 重连 SSH | `false` |
| `tmux_conf_new_pane_retain_current_path` | 新 pane 保留路径 | `true` |
| `tmux_conf_new_pane_reconnect_ssh` | 新 pane 重连 SSH | `false` |
| `tmux_conf_copy_to_os_clipboard` | 复制到系统剪贴板 | `false` |
| `tmux_conf_theme` | 启用/禁用主题 | `enabled` |
| `tmux_conf_24b_colour` | 24-bit 颜色 | `auto` |
| `tmux_conf_urlscan_options` | Urlscan 参数 | `--compact --dedupe` |
