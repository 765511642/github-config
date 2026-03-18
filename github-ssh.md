# GitHub 通过 SSH 配置与使用（小白版）

本文从零说明：什么是 SSH、怎么配好、怎么克隆、怎么提交。按顺序做即可。

---

## 一、先搞懂几个概念

| 名词 | 简单理解 |
|------|----------|
| **Git** | 在你电脑上管理代码版本的工具（记录修改、回退等）。 |
| **GitHub** | 网站，用来把代码存到网上、和别人协作。 |
| **SSH** | 一种「钥匙开门」的方式：你电脑里有一把私钥，GitHub 上登记你的公钥，匹配成功就不用每次输密码。 |
| **克隆（clone）** | 把 GitHub 上的仓库完整下载到你电脑。 |
| **提交（commit）** | 把本地修改「打包」成一条记录；再 **推送（push）** 才会传到 GitHub。 |

SSH 地址长这样：`git@github.com:用户名/仓库名.git`

---

## 二、准备工作

1. **注册 GitHub 账号**  
   打开 [github.com](https://github.com) 注册并登录。

2. **安装 Git**  
   - Mac：终端执行 `git --version`，若没有会提示安装；或从 [git-scm.com](https://git-scm.com) 下载。  
   - Windows：从官网下载安装，安装时勾选「Git Bash」更方便。

3. **（可选）配置你的名字和邮箱**（会显示在提交记录里）  
   在终端执行（把内容改成你自己的）：

   ```bash
   git config --global user.name "你的名字或昵称"
   git config --global user.email "你在GitHub用的邮箱"
   ```

---

## 三、生成 SSH 密钥（只做一次）

1. 打开终端（Mac：「终端」；Windows：Git Bash）。

2. 生成密钥（一路回车即可，也可先设密码更安全）：

   ```bash
   ssh-keygen -t ed25519 -C "你的邮箱@example.com"
   ```

   若提示 `ed25519` 不支持，可改用：

   ```bash
   ssh-keygen -t rsa -b 4096 -C "你的邮箱@example.com"
   ```

3. 密钥默认位置：  
   - 私钥：`~/.ssh/id_ed25519`（不要发给任何人）  
   - 公钥：`~/.ssh/id_ed25519.pub`（要复制到 GitHub）

4. **查看并复制公钥**（整段复制）：

   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```

   Windows 上路径类似：`C:\Users\你的用户名\.ssh\id_ed25519.pub`，用记事本打开复制也行。

---

## 四、把公钥添加到 GitHub

1. 登录 GitHub → 右上角头像 → **Settings**。  
2. 左侧 **SSH and GPG keys** → **New SSH key**。  
3. **Title**：随便写，例如「我的 Mac」。  
4. **Key**：粘贴刚才复制的整段公钥 → **Add SSH key**。

---

## 五、测试 SSH 是否连通

终端执行：

```bash
ssh -T git@github.com
```

第一次会问 `Are you sure you want to continue connecting?`，输入 `yes` 回车。  
若看到类似 `Hi 用户名! You've successfully authenticated...` 就说明 **SSH 配置成功**。

若失败：检查公钥是否完整粘贴、是否保存；或看 GitHub 文档「Testing your SSH connection」。

---

## 六、用 SSH 克隆仓库

1. 在 GitHub 打开某个仓库页面。  
2. 点绿色 **Code** → 选 **SSH**，复制地址，例如：  
   `git@github.com:zhangsan/hello-world.git`

3. 在你想放项目的文件夹里打开终端，执行：

   ```bash
   git clone git@github.com:zhangsan/hello-world.git
   ```

4. 会多出一个 `hello-world` 文件夹，进去就是完整项目：

   ```bash
   cd hello-world
   ```

以后这个文件夹里的 Git 默认就会用 SSH 和 GitHub 通信。

---

## 七、日常修改与提交、推送到 GitHub（完整流程）

假设你已经在克隆下来的项目文件夹里。

### 1. 看当前状态

```bash
git status
```

会显示哪些文件改了、哪些还没提交。

### 2. 把要提交的文件加入「暂存区」

```bash
# 提交所有改动（常用）
git add .

# 或只添加某个文件
git add 文件名.txt
```

### 3. 做一次提交（本地记录）

```bash
git commit -m "说明这次改了什么，例如：修复登录按钮"
```

`-m` 后面是本次提交的说明，以后好查历史。

若是第一次在本机用 Git，若提示要设置用户名邮箱，按第二节再执行一次 `git config`。

### 4. 推送到 GitHub

```bash
git push
```

若当前分支是 `main` 而远程叫 `origin`，一般就是推到 GitHub 上的 `main`。  
若是新仓库第一次推送，Git 有时会提示你先设上游分支，按提示复制执行即可，例如：

```bash
git push -u origin main
```

### 5. 小结：本地 → 远程

```
修改文件 → git add → git commit → git push
         （暂存）    （本地快照）  （上传到GitHub）
```

---

## 八、常见问题（小白向）

| 现象 | 可能原因 |
|------|----------|
| `Permission denied (publickey)` | 公钥没加到 GitHub，或用了错误的密钥；再检查 `ssh -T git@github.com`。 |
| `git push` 被拒绝 | 没有该仓库写权限；或要先 `git pull` 拉别人更新再推。 |
| 克隆很慢 | 网络问题；可尝试代理或换时段，与 SSH/HTTPS 无关。 |

---

## 九、和 HTTPS 怎么选？

- **SSH**：配好一次后，克隆/推送通常不用反复输密码（靠密钥），适合长期在自己电脑上开发。  
- **HTTPS**：不用配 SSH，但要凭 **个人访问令牌（PAT）** 当密码，见同目录下的 `github-https.md`。

按本文做完 **生成密钥 → 添加公钥 → ssh -T 成功 → clone → add/commit/push**，你就完成了 SSH 方式的完整闭环。
