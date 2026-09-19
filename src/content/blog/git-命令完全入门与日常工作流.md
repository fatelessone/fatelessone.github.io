---
title: Git 命令完全入门：从第一次提交到安全推送
description: 先理解 Git 在保存什么，再用终端输出看懂每条常用命令产生的效果；附博客写作场景的安全工作流与速查表。
pubDate: 2025-09-19
tags: [Git, 教程, 开发工具, 博客]
---

Git 并不是“把代码上传到 GitHub 的工具”。它首先是一个运行在你电脑上的**版本控制系统**：它能把某一时刻的文件状态保存成一条可以回看的记录，并允许你建立分支、比较改动、撤销错误、与他人或另一台电脑同步。

对个人博客而言，Git 的价值尤其直接：写错的文章能找回；每次改版都有历史；把代码推送到 GitHub 后，部署平台可以自动重新构建网站。你不必把所有 Git 命令都背下来，但应理解日常命令的逻辑与高风险操作的边界。

> 说明：Git 的官方参考列出了大量底层、迁移和服务器维护命令。本篇完整覆盖个人写作、网站开发与协作中会用到的命令类别，并在最后给出官方“全部命令”入口；不建议初学者为了“全背”而直接运行底层维护命令。

## 目录

1. Git 是什么，和 GitHub 有什么区别
2. 四个区域：理解 `add`、`commit`、`push`
3. 第一次配置与创建仓库
4. 每天写博客的标准流程
5. 查看、比较与找回内容
6. 分支、合并、远程仓库与同步
7. `restore`、`reset`、`revert`：撤销前必须分清
8. 暂存、标签与其他实用命令
9. 常见命令速查表与安全准则

---

## 第一章：Git 到底是什么？

### Git、GitHub 与仓库

- **Git**：安装在本机的版本控制程序。没有网络也能提交、查看历史和创建分支。
- **仓库（repository / repo）**：被 Git 管理的一组文件。项目根目录中隐藏的 `.git/` 文件夹保存历史记录；不要手动删除它。
- **提交（commit）**：一次有名字、有时间、有作者的“存档点”。好的提交只做一件能说清的事，例如“新增 Git 教程文章”。
- **GitHub / GitLab / Gitee**：提供远程仓库托管和协作服务的网站，它们不是 Git 本身。
- **远程仓库（remote）**：在线的那一份仓库副本。常见默认名字是 `origin`。

你可以把 Git 想成文章的“版本时间机器”，而远程平台是保存备份、触发部署和分享项目的地方。

### Git 保存的是快照，不是“文件差异清单”

每次 `commit`，Git 会记录此刻被提交文件的完整状态，并用前后提交的关系组织历史。平时我们会看到“差异”，是 Git 为了方便比较而计算出来的，不代表 Git 只能保存差异。

这带来三个实际好处：

1. 可以明确知道某一段文字是哪一次改的。
2. 可以在实验新布局时开分支，不影响稳定版本。
3. 可以在上线后发现问题时，安全地回退到某一次提交。

---

## 第二章：先看懂 Git 的四个区域

![Git 的工作目录、暂存区、本地仓库和远程仓库关系图](/images/git-four-areas.svg)

你的文件会依次经过四个位置：

1. **工作目录（Working Directory）**：你正用 VS Code 编辑的真实文件。
2. **暂存区（Staging Area / Index）**：你挑出来、准备进入下一次提交的改动。
3. **本地仓库（Local Repository）**：你电脑 `.git/` 中已经提交的历史。
4. **远程仓库（Remote Repository）**：GitHub 等网站上的备份与共享副本。

最核心的路径是：编辑文件 → `git add` 选择改动 → `git commit` 写入本地历史 → `git push` 推到远程。

### 一次完整操作的效果图

假设你新建了 `src/content/blog/春天的笔记.md`：

```text
编辑前
工作目录：没有这个文件
暂存区：空
本地仓库：上一次提交

保存文件后
工作目录：新增 春天的笔记.md
暂存区：空
本地仓库：仍是上一次提交

执行 git add src/content/blog/春天的笔记.md 后
工作目录：新增 春天的笔记.md
暂存区：已选中 春天的笔记.md
本地仓库：仍是上一次提交

执行 git commit -m "新增春天笔记" 后
工作目录：干净
暂存区：干净
本地仓库：多出一条“新增春天笔记”记录

执行 git push 后
远程仓库：也获得这条记录，部署平台可以开始构建
```

### 最重要的检查命令：`git status`

任何不确定“我现在做到哪一步”的时候，先运行：

```bash
git status
```

**典型输出与含义：**

```text
On branch main
Changes not staged for commit:
  modified:   src/content/blog/春天的笔记.md

Untracked files:
  public/images/spring.jpg
```

- `modified`：Git 已经认识这个文件，但你的新改动还没有进暂存区。
- `Untracked`：新文件还没被 Git 跟踪。
- `not staged`：下一次提交不会带上这些改动，除非你执行 `git add`。

若一切都提交完了，常会看到：

```text
nothing to commit, working tree clean
```

这就是最理想的“工作目录干净”状态。

---

## 第三章：第一次配置与创建仓库

### 1. 安装与确认版本

macOS 可以通过 Xcode Command Line Tools、Homebrew 等方式安装 Git；Windows 可安装 Git for Windows。装好后在终端运行：

```bash
git --version
```

**效果示例：**

```text
git version 2.x.x
```

能看到版本号，就说明终端能找到 Git。

### 2. 设置提交署名

Git 会把这个名字和邮箱写进未来的提交记录。只需在一台电脑上设置一次：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

检查配置：

```bash
git config --global --list
```

**效果示例：**

```text
user.name=你的名字
user.email=你的邮箱@example.com
```

如果你用 GitHub，邮箱应使用你在 GitHub 账号中验证过的邮箱，或使用 GitHub 提供的隐私 noreply 邮箱。不要把密码、访问令牌写进 `user.email`、文章或提交信息。

### 3. 给已有项目初始化 Git：`git init`

进入项目根目录，例如本博客的根目录，再运行：

```bash
cd "/你的路径/我的博客"
git init -b main
```

**效果示例：**

```text
Initialized empty Git repository in /你的路径/我的博客/.git/
```

`-b main` 表示初始分支名使用 `main`。如果项目已经是 Git 仓库，不需要再运行 `git init`；先用 `git status` 确认即可。

### 4. 从远程仓库获取项目：`git clone`

若 GitHub 上已经有仓库，使用：

```bash
git clone https://github.com/你的用户名/仓库名.git
cd 仓库名
```

**效果示例：**

```text
Cloning into '仓库名'...
remote: Enumerating objects: ...
Receiving objects: 100% (...), done.
```

`clone` 会同时下载文件、历史记录、默认分支，并自动把远程仓库命名为 `origin`。因此 clone 后通常不必再 `git remote add origin ...`。

### 5. 写好 `.gitignore`，避免把不该提交的文件放进去

`.gitignore` 是项目根目录中的普通文本文件，用来告诉 Git 忽略某些本机生成文件。Astro 项目常见内容：

```text
node_modules/
dist/
.DS_Store
.env
.env.*
!.env.example
```

- `node_modules/` 可用 `npm install` 重新生成，通常不提交。
- `dist/` 是构建产物，部署平台会重新构建，通常不提交。
- `.env` 可能包含密钥，绝不能提交到公开仓库。

注意：文件若**已经被提交过**，后来才加进 `.gitignore` 不会自动停止跟踪；需要谨慎使用 `git rm --cached 文件名` 将它从暂存跟踪中移除，同时保留本地文件。

---

## 第四章：写博客时的标准 Git 工作流

这是最值得形成习惯的一组命令。假设你刚写完一篇 Markdown：

### 1. 先检查：`git status`

```bash
git status
```

确认只出现你预期修改的文章、图片或样式文件。若出现 `.env`、陌生文件或大量 `dist/` 文件，先停下来检查 `.gitignore`，不要盲目 `git add .`。

### 2. 看清具体改了什么：`git diff`

```bash
git diff
```

**效果示例：**

```diff
diff --git a/src/content/blog/春天的笔记.md b/src/content/blog/春天的笔记.md
+## 午后的风
+
+今天在窗边写完了这段记录。
```

`+` 是新增行，`-` 是删除行。这个命令只展示“工作目录”和“暂存区”的差异；如果已经 `git add`，则改用 `git diff --staged` 看待提交内容。

### 3. 选择本次要提交的内容：`git add`

最安全、最清晰的写法是指定文件：

```bash
git add "src/content/blog/春天的笔记.md"
git add public/images/spring.jpg
```

一次文章和它的图片确实都要提交时，也可以使用：

```bash
git add src/content/blog/ public/images/
```

**效果：** `git status` 会把文件从 `Changes not staged for commit` 移到 `Changes to be committed`，并显示为绿色（终端主题不同，颜色可能不同）。

```text
Changes to be committed:
  new file:   public/images/spring.jpg
  new file:   src/content/blog/春天的笔记.md
```

`git add .` 会把当前目录下所有未忽略的改动都加进去，方便但容易误加文件。刚开始时，优先写明确的文件路径。

### 4. 再确认暂存区：`git diff --staged`

```bash
git diff --staged
```

这是提交前的最后检查：它展示的正是下一次 `commit` 将保存的内容。发现不该提交的文件，可执行：

```bash
git restore --staged 文件路径
```

它只会把文件移出暂存区，**不会删除你工作目录中的修改**。

### 5. 创建一个有意义的存档点：`git commit`

```bash
git commit -m "新增春天笔记文章"
```

**效果示例：**

```text
[main a1b2c3d] 新增春天笔记文章
 2 files changed, 42 insertions(+)
 create mode 100644 src/content/blog/春天的笔记.md
```

提交信息建议用“动词 + 内容”写清这次做了什么，例如：

```text
新增 VS Code Markdown 教程
修正文章页标题宽度
调整首页侧栏间距
```

不要写 `update`、`修改`、`111` 这种无法从历史中辨认意图的消息。

### 6. 推送到远程：`git push`

第一次把 `main` 推送到远程时：

```bash
git push -u origin main
```

`-u` 会建立本地 `main` 与远程 `origin/main` 的跟踪关系。以后通常只需要：

```bash
git push
```

**成功效果示例：**

```text
Enumerating objects: ...
Writing objects: 100% (...), done.
To https://github.com/你的用户名/仓库名.git
   123abcd..a1b2c3d  main -> main
```

如果部署平台已连接这个仓库并监听 `main`，这次推送会自动触发构建与重新发布。`git push` 成功只代表代码已经上传；网站是否构建成功，还应到部署平台日志中确认。

---

## 第五章：查看历史、比较改动、定位版本

### `git log`：看历史时间线

```bash
git log --oneline --decorate --graph --all
```

**效果示例：**

```text
* a1b2c3d (HEAD -> main, origin/main) 新增春天笔记文章
* 98f7e6d 调整文章排版
* 45a1b20 初始化 Astro 博客
```

- `--oneline`：每个提交只显示一行。
- `--decorate`：显示分支、标签与远程指针。
- `--graph`：用字符画显示分支合并关系。
- `--all`：包含所有本地和远程分支。

最左边像 `a1b2c3d` 的短字符串是提交 ID。可用它查看某次提交的细节。

### `git show`：打开一次提交或一个文件的内容

```bash
git show a1b2c3d
git show a1b2c3d:"src/content/blog/春天的笔记.md"
```

第一条会显示该提交的说明和改动；第二条会直接显示那次提交中某个文件的完整内容。适合找回“上周那段文字到底写了什么”。

### `git diff` 的常用比较方式

```bash
git diff                         # 工作目录 vs 暂存区
git diff --staged                # 暂存区 vs 最近一次提交
git diff HEAD                    # 工作目录（含已暂存）vs 最近一次提交
git diff main..feature/layout    # 两个分支的差异
git diff HEAD~1 HEAD             # 上一条提交 vs 当前提交
```

不要被 `HEAD` 吓到：它只是“当前所在提交”的指针。`HEAD~1` 表示当前提交的前一个提交。

### `git blame`：查看每一行是谁在何时修改

```bash
git blame src/content/blog/春天的笔记.md
```

**效果示例：**

```text
a1b2c3d (Fatelessone 2026-09-19 14:30:00 +0800 12) ## 午后的风
a1b2c3d (Fatelessone 2026-09-19 14:30:00 +0800 13) 今天在窗边写完了这段记录。
```

它用于追溯背景，不是用来“责怪谁”。个人博客中，它也能告诉你一句话是在哪次修改中加入的。

---

## 第六章：分支、合并与远程同步

### 分支是什么？

分支是一条独立的修改线。你可以让 `main` 保持可发布状态，在 `feature/new-homepage` 分支大胆改首页；确认没问题后再合并回 `main`。

### 创建与切换分支：`git switch` / `git branch`

```bash
git branch                       # 列出本地分支
git switch -c feature/new-homepage  # 创建并切换到新分支
git switch main                  # 切回 main
git branch -d feature/new-homepage # 删除已经合并的分支
```

**效果示例：**

```text
* feature/new-homepage
  main
```

星号表示当前分支。`git checkout` 是旧但仍可用的复合命令；对初学者而言，新的 `git switch` 专门负责切换分支，更直观。

若分支还有未合并提交，`git branch -d` 会拒绝删除，这是保护机制。确认确实不要了才用强制删除 `git branch -D 分支名`。

### 合并分支：`git merge`

先切到**接收改动的分支**，再执行合并：

```bash
git switch main
git merge feature/new-homepage
```

**可能效果一：快速前进（Fast-forward）**

```text
Updating 45a1b20..a1b2c3d
Fast-forward
 src/pages/index.astro | 18 +++++++++---------
```

**可能效果二：出现冲突**

```text
CONFLICT (content): Merge conflict in src/pages/index.astro
Automatic merge failed; fix conflicts and then commit the result.
```

冲突不是错误，而是 Git 不知道两份修改该保留哪一份。打开冲突文件，处理 `<<<<<<<`、`=======`、`>>>>>>>` 标记之间的内容，保存后：

```bash
git add src/pages/index.astro
git commit
```

如果决定这次不合并，且还在合并过程中，可执行 `git merge --abort` 回到合并前状态。

### 远程仓库：`git remote`

```bash
git remote -v
```

**效果示例：**

```text
origin  https://github.com/你的用户名/仓库名.git (fetch)
origin  https://github.com/你的用户名/仓库名.git (push)
```

为已初始化但没有远程的本地项目添加远程：

```bash
git remote add origin https://github.com/你的用户名/仓库名.git
```

更换远程地址（例如从 HTTPS 改成 SSH）时：

```bash
git remote set-url origin git@github.com:你的用户名/仓库名.git
```

### `fetch`、`pull`、`push` 的区别

```text
git fetch  ：下载远程历史到本机的 origin/main，不改你当前文件
git pull   ：先 fetch，再把远程更新整合进当前分支
git push   ：把本地提交发送到远程仓库
```

远程有人（或另一台电脑上的你）修改后，推荐安全顺序：

```bash
git status
git fetch origin
git log --oneline HEAD..origin/main
git pull --rebase
```

`git fetch` 只下载，便于你先看远程发生了什么；`git pull --rebase` 会先获取，再把你尚未推送的本地提交接到远程最新历史之后，使个人分支的历史更直。若这不是你熟悉的协作规则，使用普通 `git pull` 也可以，但看到冲突要先处理再继续。

### 推送被拒绝时怎么办？

若看到类似：

```text
! [rejected] main -> main (fetch first)
error: failed to push some refs
```

表示远程比本地多了你没有的提交。**不要第一反应就使用 `git push --force`。** 先：

```bash
git pull --rebase
# 若有冲突：解决冲突 → git add 文件 → git rebase --continue
git push
```

如需放弃这次 rebase，使用 `git rebase --abort`。只有你完全理解自己正在覆盖什么、并且分支由你独自使用时，才考虑 `git push --force-with-lease`；它比 `--force` 多一层远程状态检查，但仍然有风险。

---

## 第七章：撤销操作前，先分清三兄弟

Git 中最容易误用的是 `restore`、`reset` 与 `revert`。它们名字相近，作用位置却不同。

| 命令 | 主要作用 | 是否改写历史 | 适合什么情况 |
| --- | --- | --- | --- |
| `git restore` | 恢复工作目录或暂存区中的文件 | 否 | 丢弃尚未提交的文件改动 / 取消暂存 |
| `git reset` | 移动当前分支指针，或重置暂存区 | 是（用于提交时） | 整理本地、尚未推送的提交 |
| `git revert` | 创建一条“反向修改”的新提交 | 否 | 已经推送、需要安全撤销的提交 |

### 1. 取消暂存，但保留文件内容

```bash
git restore --staged src/content/blog/春天的笔记.md
```

**效果：** 文件从暂存区移回“已修改但未暂存”；你在 VS Code 里的文字完全保留。提交前误加文件时，这是首选。

### 2. 丢弃未提交的文件修改（谨慎）

```bash
git restore src/content/blog/春天的笔记.md
```

**效果：** 该文件回到暂存区/最近提交所对应的版本，未提交的文字会消失。运行前先 `git diff`，并确认真的不需要这些修改。

### 3. 用新提交撤销已经发布的提交：`git revert`

```bash
git revert a1b2c3d
```

**效果示例：**

```text
[main e4f5a6b] Revert "新增春天笔记文章"
 1 file changed, 42 deletions(-)
```

历史不会被删掉，而是新增一条反向提交。这是共享分支和已推送内容最安全的撤销方式。确认后别忘了 `git push`。

### 4. 重写最近提交：`git reset`（只建议未推送时用）

```bash
git reset --soft HEAD~1   # 取消最近一次提交，改动仍在暂存区
git reset HEAD~1          # 取消最近一次提交，改动留在工作目录
git reset --hard HEAD~1   # 取消最近一次提交，并丢弃改动
```

**状态效果：**

```text
--soft  : 提交消失 → 暂存区仍保留内容 → 工作目录保留内容
默认/--mixed: 提交消失 → 暂存区清空 → 工作目录保留内容
--hard  : 提交消失 → 暂存区清空 → 工作目录也恢复到旧版本
```

`git reset --hard` 会丢弃未提交改动，且会改写历史；不要在共享分支或不确定的情况下执行它。你此前已经推送过的提交，优先选择 `git revert`。

### 5. 最后的安全网：`git reflog`

即便误操作过 `reset`，Git 常常仍保存近期 `HEAD` 移动记录：

```bash
git reflog
```

**效果示例：**

```text
a1b2c3d HEAD@{0}: reset: moving to HEAD~1
e4f5a6b HEAD@{1}: commit: 新增春天笔记文章
```

找到还原目标后，可以先创建保护分支：

```bash
git branch recovery e4f5a6b
```

这不是替代备份的理由，但在本地误操作时非常有用。

---

## 第八章：其他日常实用命令

### 临时收起未完成工作：`git stash`

正在改布局，却要立刻切换分支修一个小问题？可以：

```bash
git stash push -m "首页布局草稿"
git switch main
# 处理并提交紧急修改后
git switch feature/new-homepage
git stash pop
```

**效果：** `stash push` 将未提交改动临时保存并让工作目录恢复干净；`stash pop` 尝试恢复最近一份暂存并删除该临时记录。查看所有临时记录用 `git stash list`，只想恢复但保留记录则用 `git stash apply`。

### 删除或移动被跟踪文件：`git rm` / `git mv`

```bash
git rm "src/content/blog/旧文章.md"
git mv "src/content/blog/草稿.md" "src/content/blog/正式文章.md"
git commit -m "整理文章文件名"
```

它们等同于“在磁盘上删除/移动 + `git add` 相应改动”。若只想让 Git 停止跟踪、但保留本地文件（例如误提交的 `.env`），使用：

```bash
git rm --cached .env
```

并立刻把 `.env` 加入 `.gitignore`；若密钥已经推送到远程，还必须去对应服务商轮换该密钥，因为历史中可能仍可见。

### 给版本打名字：`git tag`

文章站点每完成一次大改版，可以创建带说明的标签：

```bash
git tag -a v1.0.0 -m "第一个可发布的博客版本"
git push origin v1.0.0
```

**效果：** `v1.0.0` 成为某次提交的固定名字。查看标签：`git tag`；查看其内容：`git show v1.0.0`。标签不是分支，通常不继续在其上提交。

### 清理未跟踪文件：`git clean`（高风险）

```bash
git clean -n   # 只预览会删除什么
git clean -fd  # 删除未跟踪文件和目录
```

不要跳过 `-n`。`git clean -fd` 对新建但未 `git add` 的文章和图片同样有效，可能造成不可恢复的损失。

### 帮助与版本信息

```bash
git help                     # 打开 Git 帮助总览
git help commit              # 打开 git commit 的完整手册
git commit --help            # 同上
git --version                # 当前 Git 版本
```

命令忘记参数时，先看 `git <命令> --help`，比照搬不明来源的命令更可靠。

---

## 第九章：常见命令速查表

### 每天最常用的 10 条

| 命令 | 一句话作用 | 执行后的主要效果 |
| --- | --- | --- |
| `git status` | 查看当前状态 | 告诉你有哪些未提交/未跟踪文件 |
| `git diff` | 查看未暂存改动 | 显示新增与删除的行 |
| `git add 文件` | 选择改动 | 放进下一次提交的暂存区 |
| `git diff --staged` | 检查暂存内容 | 确认下一次 commit 会保存什么 |
| `git commit -m "说明"` | 创建本地版本 | 新增一条历史记录 |
| `git log --oneline` | 查看历史 | 显示提交 ID 与说明 |
| `git pull --rebase` | 获取并整合远程更新 | 本地基于远程最新状态继续 |
| `git push` | 上传提交 | 更新远程仓库并常触发部署 |
| `git restore --staged 文件` | 取消暂存 | 保留文件内容，只移出暂存区 |
| `git revert 提交ID` | 安全撤销旧提交 | 新建一个反向提交，不删历史 |

### 按任务查命令

| 任务 | 优先命令 | 备注 |
| --- | --- | --- |
| 看我改了哪些文件 | `git status` | 任何流程的第一步 |
| 看一行行改动 | `git diff` | 提交前检查 |
| 提交一篇文章 | `git add 文件` → `git commit` | 显式指定文件最安全 |
| 上传博客 | `git push` | 先确认构建成功 |
| 下载远程但暂不改文件 | `git fetch origin` | 安全地先观察 |
| 新做一个实验 | `git switch -c feature/名称` | 不直接在 main 上试验 |
| 取消误 add | `git restore --staged 文件` | 不丢内容 |
| 丢弃未提交修改 | `git restore 文件` | 执行前确认不再需要 |
| 线上已发布内容要撤销 | `git revert 提交ID` | 优先于 reset |
| 收起未完成工作 | `git stash push -m "说明"` | 之后用 `git stash pop` 恢复 |

### 一个可复制的个人博客发布流程

```bash
# 1. 先检查与构建
git status
npm run build

# 2. 确认本次只提交指定文章与图片
git add "src/content/blog/文章名.md"
git add public/images/图片名.jpg
git diff --staged

# 3. 创建提交并推送
git commit -m "新增文章：文章标题"
git push
```

如果 `git status` 显示工作目录干净、`npm run build` 成功、`git push` 成功，这就是一次可追溯、可部署的发布。

---

## 最后：三条不会后悔的 Git 原则

1. **先 `git status`，再做任何事。** 它能避免 80% 的误操作。
2. **已推送的公共历史，优先 `git revert`，不要随意 `reset --hard` 或强推。**
3. **提交前看 `git diff --staged`，推送前跑 `npm run build`。** Git 管版本，构建保证网站真的能生成。

完整命令目录请查看 [Git 官方 Reference](https://git-scm.com/docs)，其按“配置、创建项目、快照、分支、远程、检查、补丁、内部命令”等类别列出了当前版本的全部命令；不认识的命令先阅读官方手册再运行。

## 参考资料

- [Git 官方：Reference（全部命令目录）](https://git-scm.com/docs)
- [Git 官方：git-init](https://git-scm.com/docs/git-init)
- [Git 官方：git-restore](https://git-scm.com/docs/git-restore)
- [Git 官方：git-pull](https://git-scm.com/docs/git-pull)
- [Git 官方：Git Cheat Sheet](https://git-scm.com/cheat-sheet.pdf)
