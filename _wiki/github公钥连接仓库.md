---
layout: wiki
title: GitHub公钥连接仓库
categories: Anything
description: GitHub公钥连接仓库
keywords: GitHub公钥连接仓库
---

# GitHub公钥连接仓库

```
系统环境：Mac OS
Git版本：v2.39.5
```

### 🔒 GitHub 已弃用密码认证？解决方案

GitHub 自 **2021 年 8 月 13 日起**停止支持通过 HTTPS 使用账号密码进行 Git 操作（如克隆、推送代码）。您需要使用以下两种替代方式完成认证：

---

## ✅ 方式一：个人访问令牌（Personal Access Token，简称 PAT）

### 步骤 1：生成个人访问令牌
1. 登录 GitHub，点击右上角头像 → **Settings**（设置）。
2. 左侧菜单选择 **Developer settings**（开发者设置）→ **Personal access tokens**。
3. 点击 **Generate new token**（生成新令牌）：
   - 设置有效期（建议 30 天或 90 天）
   - 勾选权限范围：
     - `repo`：用于仓库读写
     - `read:org`（可选）：用于组织权限
4. 点击 **Generate token**（生成令牌）。
5. ⚠️ **立即保存令牌**（关闭页面后无法再次查看）。

### 步骤 2：使用令牌代替密码
当执行以下操作时，输入您的 **GitHub 用户名 + 生成的令牌**：
```bash
# 克隆仓库示例
git clone https://github.com/username/repo.git
Username: <你的GitHub用户名>
Password: <粘贴生成的个人访问令牌>
```

### 步骤 3：清除旧缓存密码
- **Windows**：
  1. 打开「控制面板」→「凭据管理器」
  2. 找到 `git:https://github.com`，删除旧记录
- **macOS**：
  1. 打开「钥匙串访问」
  2. 删除 `github.com` 相关记录
- **Linux**：
  ```bash
  git credential-cache exit
  ```

### 步骤 4：推荐安装 Git 凭证管理器（GCM）
支持自动存储和更新令牌（无需频繁输入）：
```bash
# Windows/macOS
git config --global credential.helper manager

# Linux
git config --global credential.helper cache
```

---

## ✅ 方式二：SSH 密钥认证（推荐高级用户）

### 步骤 1：生成 SSH 密钥
```bash
# 推荐使用 Ed25519 算法（更安全）
ssh-keygen -t ed25519 -C "你的邮箱@example.com"

# 旧系统可用 RSA 算法
ssh-keygen -t rsa -b 4096 -C "你的邮箱@example.com"
```
- 按提示设置保存路径（默认 `~/.ssh/id_ed25519`）和密钥密码（可选）

### 步骤 2：添加密钥到 GitHub
1. 将公钥文件内容（`~/.ssh/id_ed25519.pub` 或 `~/.ssh/id_rsa.pub`）复制到剪贴板：
   ```bash
   pbcopy < ~/.ssh/id_ed25519.pub   # macOS
   clip < ~/.ssh/id_ed25519.pub     # Windows
   ```
2. GitHub 设置页面 → **SSH and GPG keys** → **New SSH Key**
3. 标题自定义，粘贴公钥内容 → 点击 **Add SSH key**

### 步骤 3：测试 SSH 连接
```bash
ssh -T git@github.com
```
若显示 `Hi username! You've successfully authenticated...` 表示成功

### 步骤 4：切换仓库 URL 为 SSH 模式
```bash
git remote set-url origin git@github.com:username/repo.git
```

---

## 🛠️ 常见问题排查
| 问题           | 解决方案                                                     |
| -------------- | ------------------------------------------------------------ |
| **Token 无效** | 重新生成令牌，确保勾选 `repo` 权限                           |
| **权限错误**   | 清除旧缓存密码（见上文步骤3），重试                          |
| **SSH 不工作** | 确认 `ssh-agent` 运行并添加了密钥：<br>`eval "$(ssh-agent -s)"`<br>`ssh-add ~/.ssh/id_ed25519` |

---

