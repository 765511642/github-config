# GitHub 通过 HTTPS 配置与使用（小白版）

本文说明：不用 SSH，用 **HTTPS 链接** 克隆、推送时怎么登录。适合不想折腾密钥、或公司网络只允许 HTTPS 的情况。

---

## 一、先搞懂几个概念

| 名词 | 简单理解 |
|------|----------|
| **Git** | 在你电脑上管理代码版本的工具。 |
| **GitHub** | 网上存代码、协作的网站。 |
| **HTTPS** | 用 `https://` 开头的仓库地址，通过用户名 + **令牌（密码替代品）** 验证身份。 |
| **个人访问令牌（PAT）** | 一长串密码式字符串，代替 GitHub 登录密码（Git 命令行已不再支持用账号密码推送）。 |

HTTPS 地址长这样：  
`https://github.com/用户名/仓库名.git`

---

## 二、准备工作

1. **注册并登录 GitHub**：[github.com](https://github.com)

2. **安装 Git**  
   - Mac：`git --version` 或从 [git-scm.com](https://git-scm.com) 安装。  
   - Windows：官网安装，可选 Git Bash。

3. **配置提交时显示的名字和邮箱**（与 SSH 文档相同）：

   ```bash
   git config --global user.name "你的名字"
   git config --global user.email "你的邮箱"
   ```

---

## 三、创建个人访问令牌（PAT）— 推送必备

Git 用 HTTPS 推送时，**不能再用 GitHub 账号密码**，必须用 **Personal Access Token**。

### 步骤（以经典方式为例，界面若更新以官网为准）

1. 登录 GitHub → 头像 → **Settings**。  
2. 左侧最底部 **Developer settings** → **Personal access tokens**。  
3. 选 **Tokens (classic)** → **Generate new token** → **Generate new token (classic)**。  
4. **Note**：写备注，如「家里电脑 Git」。  
5. **Expiration**：过期时间，可按需选择。  
6. **勾选权限**：至少要勾选 **`repo`**（完整仓库权限），否则无法推送私有仓库或正常推送。  
7. 点 **Generate token**。  
8. **立刻复制并保存**那一串字符（只显示一次），丢失只能重新生成。

> 以后在终端里 `git push` / `git pull` 若提示输入密码，**密码处粘贴这串令牌**，用户名填你的 **GitHub 用户名**。

### 凭据助手（少输几次令牌）

- **Mac**：可启用钥匙串保存：

  ```bash
  git config --global credential.helper osxkeychain
  ```

- **Windows**：安装 Git 时若选了「Git Credential Manager」，一般会记住；也可：

  ```bash
  git config --global credential.helper manager
  ```

首次输入用户名和 PAT 后，之后一段时间可能不用再输。

---

## 四、用 HTTPS 克隆仓库

1. 打开 GitHub 上任意仓库 → 绿色 **Code**。  
2. 选 **HTTPS**，复制地址，例如：  
   `https://github.com/zhangsan/hello-world.git`

3. 在终端进入你想放项目的目录，执行：

   ```bash
   git clone https://github.com/zhangsan/hello-world.git
   ```

4. 若是**私有仓库**，会提示输入用户名和密码：  
   - 用户名：GitHub 用户名  
   - 密码：**粘贴 PAT，不是登录密码**

5. 进入项目目录：

   ```bash
   cd hello-world
   ```

---

## 五、已有仓库从 SSH 改成 HTTPS（可选）

若本地之前用 SSH 克隆，想改成 HTTPS：

```bash
git remote -v
git remote set-url origin https://github.com/用户名/仓库名.git
```

之后 `git push` / `git pull` 就会走 HTTPS + PAT。

---

## 六、日常提交与推送（和 SSH 一样，只是远程地址不同）

在仓库文件夹里：

### 1. 查看状态

```bash
git status
```

### 2. 暂存修改

```bash
git add .
```

### 3. 提交到本地

```bash
git commit -m "说明这次修改"
```

### 4. 推送到 GitHub

```bash
git push
```

提示输入密码时，仍填 **PAT**。令牌过期或撤销后需重新生成并再输一次。

---

## 七、流程小结

```
克隆：git clone https://github.com/用户/仓库.git（私有库要用户名+PAT）
日常：改文件 → git add → git commit → git push（密码处用 PAT）
```

---

## 八、SSH 和 HTTPS 怎么选？

| 方式 | 优点 | 缺点 |
|------|------|------|
| **HTTPS** | 不用配 SSH；防火墙友好 | 要管理 PAT，过期要更新 |
| **SSH** | 配好后一般不用反复输密码 | 要生成密钥、添加公钥 |

SSH 详细步骤见同目录 **`github-ssh.md`**。

---

## 九、常见问题

| 现象 | 处理 |
|------|------|
| `Authentication failed` | 确认用的是 **PAT** 不是账号密码；令牌是否勾选 `repo`、是否过期。 |
| 每次都要输令牌 | 配置 `credential.helper`（见上文）。 |
| 克隆公开仓库仍失败 | 检查网络；或仓库是否为私有（私有必须登录）。 |

按本文：**装好 Git → 生成并保存 PAT → HTTPS 克隆 → 推送时用用户名+PAT**，即可完成 HTTPS 方式的完整使用。
