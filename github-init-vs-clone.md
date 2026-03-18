# 先 `git init` 再连 GitHub：和 `clone` 有什么不一样？

## 一句话

- **`git clone`**：从 GitHub **拉下已有仓库**，远程地址、分支关系都帮你配好了。  
- **`git init`**：只在本地建空仓库，**和 GitHub 还没关系**，要自己 **添加远程 + 第一次推送**。

所以：**日常改代码 → add → commit → push** 这部分一样；**前面多几步「关联远程」**。

---

## 对比表

| 步骤 | 用 `clone` | 用 `init` |
|------|------------|-----------|
| 本地先有代码 | 克隆后才有文件夹 | 你自己先有文件夹，在里面 `git init` |
| 远程 `origin` | 自动存在 | **没有**，要自己 `git remote add` |
| 第一次上 GitHub | 直接 `push`（一般即可） | 要先在网页建空仓库，再 `push -u origin main` |

---

## `init` 后的完整流程（SSH / HTTPS 都一样，只差远程地址）

### 1. 本地（你已经在项目目录里）

```bash
git init
git add .
git commit -m "初始提交"
```

若还没有任何提交，部分 Git 版本不允许推空分支，所以**至少做一次 commit** 再推。

### 2. 在 GitHub 网页新建仓库

- 登录 GitHub → **New repository**  
- 起名，**可先不勾选** README / .gitignore（避免和本地第一次推送冲突）。  
- 若勾了 README，见下文「已有 README 的情况」。

### 3. 关联远程（二选一地址）

**SSH：**

```bash
git remote add origin git@github.com:你的用户名/仓库名.git
```

**HTTPS：**

```bash
git remote add origin https://github.com/你的用户名/仓库名.git
```

查看是否加对：

```bash
git remote -v
```

### 4. 分支名与第一次推送

现在常用主分支名是 `main`，若你本地是 `master` 可改名：

```bash
git branch -M main
```

推上去并建立跟踪关系（以后直接 `git push`）：

```bash
git push -u origin main
```

- **SSH**：靠密钥，不提示密码。  
- **HTTPS**：提示时用 **用户名 + PAT**（见 `github-https.md`）。

---

## 若 GitHub 上建库时勾了 README

远程已有提交，你本地也有第一次提交，**历史不一致**，直接 `push` 可能被拒绝。任选一种：

**方式 A（常见）：先拉再推**

```bash
git pull origin main --rebase
git push -u origin main
```

**方式 B：允许合并不相关历史**

```bash
git pull origin main --allow-unrelated-histories
# 如有冲突，解决后 commit
git push -u origin main
```

---

## 小结

| 场景 | 你要多做的事 |
|------|----------------|
| 一直用 `clone` | 不用 `remote add`，按文档做 add/commit/push 即可。 |
| 先 `init` 再上网 | 网页建库 → `remote add origin` → `push -u origin main`。 |

**会变动的只有「怎么和 GitHub 接上」这一段；接上之后，提交、推送习惯和 clone 下来的一样。**
