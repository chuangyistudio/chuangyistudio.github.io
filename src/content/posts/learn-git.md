---
title: Git 零基础入门指南（终端版）
published: 2026-09-07
description: "专为零基础初学者准备的 Git 实操手册：从安装、配置到提交、分支、撤销与解决冲突，跟着步骤走一遍就能上手。"
tags: [Git, 教程, 入门]
category: 教程
draft: false
---

> 这是一份专为完全没接触过 Git 的开发者准备的实操手册。  
> 你不需要背命令，只要跟着步骤走一遍，就能理解 Git 的核心逻辑。

---

## 📌 一、Git 是什么？

Git 是一个**版本控制系统**，简单说就是一款能帮你记录文件修改历史、方便多人协作的“超级备份工具”。

- **记录每一次修改**：你可以随时回到任意历史版本。
- **多人并行工作**：每个人在自己的分支上开发，互不干扰，最后再合并。

---

## 🛠️ 二、安装 Git

### Windows
1. 访问 [Git for Windows](https://git-scm.com/download/win) 下载安装包。
2. 一路默认安装（建议保留默认选项）。
3. 安装完成后，在开始菜单中找到 **Git Bash**，打开它，这就是你的 Git 终端。

### macOS
- 打开终端，输入 `git --version`，如果未安装，系统会自动提示安装 Command Line Tools，按提示操作即可。

### Ubuntu / Debian
```bash
sudo apt update
sudo apt install git
```

安装完成后，在终端输入 `git --version` 看到版本号即成功。

---

## ⚙️ 三、初次配置（必须）

告诉 Git 你是谁，这样每次提交都会带上你的身份信息。

```bash
git config --global user.name "你的昵称"
git config --global user.email "你的邮箱@example.com"
git config --global init.defaultBranch main   # 让新仓库默认使用 main 作为主分支名
```

> 💡 邮箱请使用你 GitHub 注册的邮箱，否则贡献不会被计入。
>
> 💡 本教程的命令都以主分支 `main` 为例；如果 `git branch` 显示的是 `master`（老版本默认），把命令里的 `main` 换成 `master` 即可。

---

## 📁 四、创建第一个仓库（本地）

### 4.1 新建一个项目文件夹，并初始化仓库
```bash
mkdir my-first-project
cd my-first-project
git init   # 在当前目录初始化一个 Git 仓库
```
此时会生成一个隐藏的 `.git` 文件夹，这就是版本库的核心，**千万不要删除**。

### 4.2 添加一个文件并提交
```bash
echo "Hello Git" > README.md   # 创建一个文件
git status                     # 查看当前状态（红色 = 有改动/未跟踪，绿色 = 已暂存）
git add README.md              # 将文件添加到暂存区
git commit -m "首次提交：添加 README"  # 提交到仓库
```

现在你已经完成了第一次提交！🎉

### 4.3 让 Git 忽略某些文件（.gitignore）

有些文件**不应该被提交**：编译产物、日志、密钥文件、下载的依赖等。
在项目根目录新建一个名为 `.gitignore` 的文件（可以用记事本/VSCode 创建），写上要忽略的东西：

```text
# 忽略单个文件
secret.txt

# 忽略整个文件夹
node_modules/

# 忽略所有 .log 文件
*.log
```

保存后再 `git status`，这些文件就不会出现在列表里了。把 `.gitignore` 本身也提交一次：

```bash
git add .gitignore
git commit -m "添加 .gitignore"
```

> ⚠️ `.gitignore` 只对**还没提交过**的文件生效。如果某个文件已经被提交了才想起来要忽略，执行 `git rm --cached 文件名`（把文件移出 Git 跟踪、但保留在电脑上），再提交一次即可。

---

## 🔄 五、理解三个关键区域

| 区域 | 作用 | 对应命令 |
|------|------|----------|
| **工作区 (Working Directory)** | 你电脑上的实际文件目录 | - |
| **暂存区 (Staging Area)** | 准备提交的文件清单 | `git add` |
| **本地仓库 (Local Repository)** | 已经安全保存的历史记录 | `git commit` |

### 常用状态查看命令
```bash
git status        # 查看工作区和暂存区的状态
git diff          # 查看工作区与暂存区的差异
git diff --staged # 查看暂存区与上次提交的差异
```

---

## 🌐 六、与远程仓库（GitHub）关联

### 6.1 创建远程仓库（GitHub）
1. 登录 GitHub，点击右上角 **+** → **New repository**。
2. 填写仓库名，**不要**勾选 “Initialize this repository with a README”（因为本地已有）。
3. 创建后，你会看到一段类似 `git remote add origin ...` 的命令，复制下来。

### 6.2 关联本地与远程
```bash
git remote add origin https://github.com/你的用户名/仓库名.git
```
> 这里 `origin` 是远程仓库的默认别名，可以改成别的，但习惯用 origin。

### 6.3 推送本地代码
```bash
git push -u origin main   # 如果你的主分支叫 master，把 main 换成 master
```
`-u` 表示将本地的 `main` 分支与远程 `main` 建立关联，以后只需 `git push` 即可。

---

## 🔑 七、解决推送时的认证问题

从 2021 年起，GitHub 不再支持账户密码推送，必须使用以下两种方式之一：

### 方案 A：使用 SSH 密钥（推荐）
1. **生成密钥对**：
   ```bash
   ssh-keygen -t ed25519 -C "你的邮箱"
   ```
   一路回车，默认保存位置即可。

2. **复制公钥**：
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   复制输出的全部内容。

3. **添加到 GitHub**：
   - 登录 GitHub → 右上角头像 → **Settings** → **SSH and GPG keys** → **New SSH key**。
   - 粘贴公钥，保存。

4. **修改远程地址为 SSH 格式**：
   ```bash
   git remote set-url origin git@github.com:你的用户名/仓库名.git
   ```

### 方案 B：使用个人访问令牌（PAT）
1. 在 GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token。
2. 勾选 `repo` 权限，生成后复制令牌（只显示一次）。
3. 推送时，用户名输入你的 GitHub 用户名，密码输入 **令牌**（不是登录密码）。

---

## 🌿 八、分支操作（必学）

分支让你可以同时进行多个功能开发，互不影响。

### 8.1 查看分支
```bash
git branch          # 本地分支列表，* 表示当前所在分支
git branch -r       # 远程分支
```

### 8.2 创建并切换分支
```bash
git checkout -b 新分支名
# 或者分开执行：
git branch 新分支名
git checkout 新分支名
```

### 8.3 切换回主分支
```bash
git checkout main   # 或 master
```

### 8.4 合并分支
```bash
git checkout main          # 先切换到目标分支
git merge 新分支名          # 将新分支的修改合并进来
```

### 8.5 删除分支
```bash
git branch -d 新分支名      # 删除本地分支（只允许删已经合并的）
git branch -D 新分支名      # 强制删除（没合并也能删，确认不要了再用）
git push origin --delete 新分支名  # 删除远程分支
```

---

## 📥 九、克隆别人的仓库（参与开源的第一步）

```bash
git clone https://github.com/用户名/仓库名.git
```
克隆会自动将远程仓库完整下载到本地，并自动创建远程别名 `origin`。

### 小知识：fetch 和 pull 的区别

```bash
git fetch        # 把远程的新提交下载到本地（先不改动你的文件）
git pull         # = fetch + 自动合并，直接把远程更新合进你当前分支
```

先记住结论：**日常用 `git pull` 就行**；`fetch` 以后需要“先看看远程有什么、再决定怎么合并”时再学。

---

## 🤝 十、给开源项目贡献代码（Fork + PR 工作流）

这是 GitHub 标准协作流程：

1. **Fork 项目**：在 GitHub 上点击要贡献的仓库右上角的 **Fork** 按钮，将仓库复制到你的账号下。
2. **克隆你自己的副本**：
   ```bash
   git clone https://github.com/你的用户名/仓库名.git
   ```
3. **添加上游仓库**（便于同步原项目更新）：
   ```bash
   git remote add upstream https://github.com/原作者/仓库名.git
   ```
4. **创建新分支**（不要直接在 main 上改）：
   ```bash
   git checkout -b fix-xxx
   ```
5. **修改、提交、推送**到你的远程分支：
   ```bash
   git add .
   git commit -m "描述修改"
   git push origin fix-xxx
   ```
6. **发起 Pull Request**：
   - 去你的 GitHub 仓库页面，点击 **Compare & pull request**，填写说明，提交。

7. **后续同步上游**：
   ```bash
   git checkout main
   git pull upstream main   # 拉取原项目的最新更改
   git push origin main     # 同步到你的 GitHub 仓库
   ```

> 💡 如果过几天 PR 提示“冲突”，说明原项目又更新了，而你的 `fix-xxx` 分支还是旧代码。此时切回 `fix-xxx` 分支，把上游最新代码合并进来，再重新推送即可（怎么解决冲突见下一节）：
> ```bash
> git checkout fix-xxx
> git merge upstream/main
> git push origin fix-xxx
> ```

---

## 🧹 十一、撤销与回退（后悔药）

先背一句话：**还没 push 的随便改；已经 push 的别用 reset 抹历史，改用 revert。**

什么时候用哪条命令（先记住这个）：

| 你的情况 | 用这个 |
|---------|-------|
| 文件改乱了，还没 `add` | `git restore 文件`（11.1） |
| `add` 错了，还没 `commit` | `git restore --staged 文件`（11.2） |
| 提交信息写错了 / 提交错了 | `git commit --amend`（11.3）或 `git reset`（11.4） |
| 提交已经 push 出去了 | `git revert`（11.5） |

### 11.1 撤销工作区的修改（未 add）
```bash
git restore 文件名          # 丢弃对某个文件的修改
git restore .               # 丢弃所有已跟踪文件的修改
```
> ⚠️ 只对“已被 Git 跟踪”的文件生效，新建的文件不会被删除。改动一旦丢弃就找不回来了，动手前确认一下。
> 想同时删掉新建的未跟踪文件：`git clean -fd`（会真的删除文件，慎用）。

### 11.2 撤销暂存区的修改（已 add 未 commit）
```bash
git restore --staged 文件名  # 将文件移出暂存区，但保留工作区修改
git reset HEAD 文件名        # 同样效果（旧命令）
```

### 11.3 修改最后一次 commit 信息
```bash
git commit --amend -m "新的提交信息"
```
> ⚠️ 只适合**还没 push** 的提交；已经 push 的就别再 amend 了（会改写历史，别人同步时会很痛苦），重新提交一条新的即可。

### 11.4 回退到某个历史版本（只限本地、未推送时）
```bash
git log --oneline           # 查看简短的提交 ID
git reset --hard 提交ID     # 回到该版本，丢弃它之后的所有提交和未保存的修改
```
> ⚠️ `--hard` 很危险，执行前确认没有需要保留的修改。
> 💡 真后悔了也别慌：被丢掉的提交几天内还能找回来——先 `git reflog` 找到它，再 `git reset --hard 那个提交ID` 即可。

### 11.5 已推送的提交：用 revert 撤销
```bash
git revert 提交ID
```
`revert` 会**新生成一个“把这次修改撤销掉”的提交**，不动原来的历史。这样已经克隆了仓库的同事直接 `git pull` 就能同步，不会出错。

### 11.6 临时收起改动：git stash
想“先把手头的半成品收起来，去干别的”，就用 stash：

```bash
git stash          # 把未提交的改动先收起来（工作区变干净）
# …… 切到别的分支干完活，再切回来 ……
git stash pop      # 把刚才收起来的改动拿回来
git stash list     # 查看收起来过哪些
```
> 💡 默认只收起“已跟踪文件”的修改；新建的文件想一起收起，用 `git stash -u`。

---

## 🚧 十二、解决合并冲突

当两个分支修改了同一文件的同一区域，合并时就会冲突。

1. Git 会暂停合并，并提示哪些文件冲突。
2. 打开冲突文件，你会看到类似（`HEAD` = 你当前所在的分支）：
   ```
   <<<<<<< HEAD
   你当前分支的内容
   =======
   被合并分支的内容
   >>>>>>> branch-name
   ```
3. 手动保留需要的部分，删除 `<<<<<<<`、`=======`、`>>>>>>>` 这些标记行。
4. 保存文件，然后：
   ```bash
   git add .
   git commit -m "解决冲突"
   ```

> 💡 如果冲突太多、改乱了，想放弃这次合并：`git merge --abort`（回到合并前的状态）。

---

## 🔍 十三、查看历史与统计

```bash
git log            # 详细提交记录
git log --oneline  # 简洁一行显示
git log --graph    # 显示分支图形
git show 提交ID     # 查看某次提交的详细信息
git diff 提交A 提交B  # 比较两个提交的差异
```

---

## 📦 十四、导出某次提交的源码（不包含 .git）

```bash
git archive --format=zip --output=../代码.zip 提交ID
```

---

## ⚠️ 十五、常见错误及解决办法

| 错误提示 | 可能原因 | 解决方案 |
|---------|---------|---------|
| `Permission denied (publickey)` | SSH 公钥未添加 | 按第七节配置 SSH |
| `fatal: refusing to merge unrelated histories` | 远程和本地没有共同祖先（常见：你先本地 `init`，又在 GitHub 勾了 README） | 最省事：把文件复制走 → 删除本地 `.git` → 重新 `git clone` → 把文件放回去再提交。确实想硬合并：`git pull origin main --allow-unrelated-histories` |
| `Your local changes would be overwritten...` | 切换分支前有未提交修改 | 先 `git stash` 收起（干完活回来 `git stash pop`），或先提交/放弃修改 |
| `error: failed to push some refs` | 远程有新提交，本地落后 | 先 `git pull` 再推送 |
| `HTTP/2 framing layer error` | 网络问题 | `git config --global http.version HTTP/1.1` |

---

## 🧠 十六、记住这些核心命令（每日必备）

```bash
git status          # 查看状态
git add .           # 添加所有修改
git commit -m "msg" # 提交
git pull            # 拉取远程更新并自动合并
git push            # 推送
git checkout -b 分支 # 创建并切换新分支
git branch          # 查看分支
git merge 分支       # 合并
git restore 文件     # 丢弃某个文件的改动（危险，先确认）
git log --oneline   # 查看历史
```

---

## 📚 十七、推荐学习路径

1. 先按照本文，在本地建一个测试仓库，反复练习 `add`、`commit`、`branch`、`merge`。
2. 然后去 GitHub 建一个仓库，练习 `push` 和 `pull`。
3. 找一个专门给新手练手的项目（如 [firstcontributions/first-contributions](https://github.com/firstcontributions/first-contributions)），按上面的 Fork 流程走一遍。
4. 遇到问题，用英文搜索错误信息，Stack Overflow 和 GitHub Issues 上都有答案。
5. 想更系统地理解概念，可以看免费的《Pro Git》中文版前几章（[git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)），或用 [Learn Git Branching](https://learngitbranching.js.org/?locale=zh_CN) 小游戏练分支，都很适合入门。

---

## 🎯 最后的话

Git 是开发者必备的基础工具，刚开始可能会觉得命令有点多，但只要你每天用、常常用，最多两周就能形成肌肉记忆。**不要害怕犯错**，因为每一次 `git reset` 和 `git commit --amend` 都是最真实的学习机会。
