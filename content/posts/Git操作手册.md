---
title: "Git 完整操作手册"
date: 2024-06-21T17:00:00+08:00
draft: false
tags: ["Git", "工具", "教程"]
---

# Git 完整操作手册

本文档收录了所有常用的 Git 命令，按使用场景分类，方便查阅。

## 一、核心概念（小白必看）

Git 是一个**版本控制系统**，有三个区域：

```
工作区（你电脑上的文件）
    ↓ git add
暂存区（准备提交的文件）
    ↓ git commit
本地仓库（保存历史版本）
    ↓ git push
远程仓库（GitHub）
```

## 二、最常用的三件套（日常使用）

### 1. `git add` - 添加文件到暂存区

```bash
# 添加所有修改过的文件
git add .

# 添加指定文件
git add 文件名.md

# 添加指定类型的文件
git add *.md

# 添加指定文件夹
git add content/posts/
```

### 2. `git commit` - 提交到本地仓库

```bash
# 提交并写说明
git commit -m "修复评论系统问题"

# 说明文字要简洁明了
# 推荐格式：动词 + 内容
# 例如：
#   "添加新文章"
#   "修复样式问题"
#   "更新配置文件"
```

### 3. `git push` - 推送到 GitHub

```bash
# 推送到默认远程仓库
git push

# 第一次推送（需要关联分支）
git push -u origin main

# 强制推送（慎用！会覆盖远程历史）
git push -f
```

## 三、查看状态和历史

### 查看当前状态

```bash
# 查看哪些文件被修改了
git status

# 简洁模式
git status -s
```

### 查看修改内容

```bash
# 查看未暂存的所有修改
git diff

# 查看已暂存但未提交的修改
git diff --staged

# 查看指定文件的修改
git diff 文件名.md
```

### 查看提交历史

```bash
# 查看所有提交记录
git log

# 简洁模式（一行显示）
git log --oneline

# 带文件修改统计
git log --stat

# 查看最近 5 次提交
git log -5

# 图形化显示分支
git log --graph --oneline
```

## 四、撤销操作（重要！）

### 撤销工作区的修改

```bash
# 撤销指定文件的修改（危险！不可恢复）
git checkout -- 文件名.md

# 撤销所有未暂存的修改
git checkout -- .
```

### 撤销暂存区的文件

```bash
# 把文件从暂存区撤回到工作区
git reset HEAD 文件名.md

# 撤销所有暂存
git reset HEAD .
```

### 撤销提交

```bash
# 撤销最近一次提交，保留修改
git reset --soft HEAD~1

# 撤销最近一次提交，不保留修改（危险！）
git reset --hard HEAD~1

# 撤销指定提交，保留后续修改
git revert 提交ID
```

## 五、文件操作

### 删除文件

```bash
# 删除文件并暂存删除操作
git rm 文件名.md

# 删除文件夹
git rm -r 文件夹名/

# 从 Git 追踪中删除但保留本地文件
git rm --cached 文件名.md
```

### 移动/重命名文件

```bash
# 重命名文件
git mv 旧文件名.md 新文件名.md

# 等同于：
# mv 旧文件名.md 新文件名.md
# git rm 旧文件名.md
# git add 新文件名.md
```

## 六、分支操作

### 查看分支

```bash
# 查看本地分支
git branch

# 查看所有分支（包括远程）
git branch -a
```

### 创建和切换分支

```bash
# 创建新分支
git branch 新分支名

# 切换到指定分支
git checkout 分支名

# 创建并切换到新分支
git checkout -b 新分支名

# 切换并跟踪远程分支
git checkout -b 本地分支名 origin/远程分支名
```

### 合并和删除分支

```bash
# 合并指定分支到当前分支
git merge 分支名

# 删除已合并的分支
git branch -d 分支名

# 强制删除分支（即使未合并）
git branch -D 分支名
```

## 七、Git 子模块（Submodule）

### 什么是子模块

子模块允许你在一个 Git 仓库中嵌入另一个 Git 仓库。在 Hugo 项目中，`themes/PaperMod` 通常是一个子模块。

### 查看子模块状态

```bash
# 查看子模块状态
git submodule status

# 查看子模块修改
git diff --submodule
```

### 修改子模块

```bash
# 1. 进入子模块目录
cd themes/PaperMod

# 2. 查看状态
git status

# 3. 添加修改
git add .

# 4. 提交
git commit -m "修改主题样式"

# 5. 返回主项目
cd ../..

# 6. 提交子模块更新到主项目
git add themes/PaperMod
git commit -m "更新主题子模块"
```

### 克隆包含子模块的仓库

```bash
# 克隆时同时拉取子模块
git clone --recursive https://github.com/用户名/仓库名.git

# 如果已经克隆，手动初始化子模块
git submodule init
git submodule update
```

### 子模块更新

```bash
# 更新所有子模块到最新版本
git submodule update --remote

# 更新指定子模块
git submodule update --remote themes/PaperMod
```

### 常见问题

**Q: 子模块显示 `modified content` 但 `git add .` 没效果？**

A: 需要先在子模块内部提交，然后在主项目中提交子模块引用：
```bash
cd themes/PaperMod
git add .
git commit -m "修改"
cd ../..
git add themes/PaperMod
git commit -m "更新子模块"
```

## 八、远程仓库操作

### 查看远程仓库

```bash
# 查看所有远程仓库
git remote -v

# 查看指定远程仓库
git remote show origin
```

### 添加和删除远程仓库

```bash
# 添加远程仓库
git remote add origin https://github.com/用户名/仓库名.git

# 修改远程仓库地址
git remote set-url origin https://github.com/新地址.git

# 删除远程仓库
git remote remove origin
```

### 拉取和推送

```bash
# 拉取远程更新到本地
git pull

# 拉取并合并
git pull origin main

# 推送本地到远程
git push origin main
```

## 八、暂存工作（stash）

当你需要切换分支但当前修改还没完成时：

```bash
# 暂存当前修改
git stash

# 暂存并写说明
git stash save "修复中的功能"

# 查看所有暂存
git stash list

# 恢复最近一次暂存
git stash pop

# 恢复指定暂存
git stash apply stash@{0}

# 删除暂存
git stash drop stash@{0}
```

## 九、标签（版本号）

```bash
# 创建标签
git tag v1.0.0

# 创建带说明的标签
git tag -a v1.0.0 -m "正式发布"

# 推送标签到远程
git push origin v1.0.0

# 推送所有标签
git push origin --tags

# 删除本地标签
git tag -d v1.0.0

# 删除远程标签
git push origin --delete v1.0.0
```

## 十、配置文件

### .gitignore 文件

在项目根目录创建 `.gitignore`，告诉 Git 哪些文件**不**要追踪：

```gitignore
# Hugo 编译输出（最重要！）
public/
resources/
.hugo_build.lock

# 系统文件
.DS_Store
Thumbs.db

# 编辑器配置
.vscode/
.idea/
*.swp

# 依赖
node_modules/
```

### 创建 .gitignore 的时机

**最佳实践**：在项目初始化时就创建 `.gitignore`。

如果已经提交了不需要的文件（如 `public/`），需要先移除跟踪：

```bash
# 移除跟踪但保留本地文件
git rm -r --cached public/

# 然后添加 .gitignore
git add .gitignore

# 提交
git commit -m "添加.gitignore，忽略public目录"
```

### 常见的 Hugo 项目忽略规则

| 文件/目录 | 说明 |
|-----------|------|
| `public/` | Hugo 构建输出，每次 `hugo` 都会重新生成 |
| `resources/` | Hugo 缓存目录 |
| `.hugo_build.lock` | Hugo 构建锁文件 |
| `node_modules/` | 如果使用 npm 依赖 |

## 十一、首次使用配置

```bash
# 设置用户名
git config --global user.name "你的名字"

# 设置邮箱
git config --global user.email "your@email.com"

# 设置默认编辑器
git config --global core.editor "code --wait"

# 设置默认分支名
git config --global init.defaultBranch main

# 查看所有配置
git config --list
```

## 十二、SSH 配置（推荐）

### 生成 SSH 密钥

```bash
# 生成密钥（一路回车）
ssh-keygen -t rsa -b 4096 -C "your@email.com"
```

### 添加到 GitHub

1. 复制公钥内容：
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

2. 登录 GitHub → Settings → SSH and GPG keys → New SSH key

3. 粘贴公钥并保存

### 测试连接

```bash
ssh -T git@github.com
```

## 十三、博客部署专用流程

### 日常更新（最常用）

```bash
# 1. 构建 Hugo 网站
hugo --config config.yml

# 2. 添加所有修改
git add .

# 3. 提交
git commit -m "描述修改内容"

# 4. 推送到 GitHub
git push
```

### 首次部署

```bash
# 1. 初始化仓库（如果还没有）
git init

# 2. 添加远程仓库
git remote add origin https://github.com/用户名/仓库名.git

# 3. 添加所有文件
git add .

# 4. 首次提交
git commit -m "Initial commit"

# 5. 推送到 GitHub
git push -u origin main
```

### 克隆已有仓库

```bash
# 克隆到本地
git clone https://github.com/用户名/仓库名.git

# 进入项目目录
cd 仓库名

# 安装依赖（如果有）
# hugo 不需要 npm install
```

## 十四、常见问题

### Q: 推送时要求输入用户名密码？

A: 配置 SSH 密钥，或者使用 Personal Access Token。

### Q: 推送被拒绝（rejected）？

A: 远程有本地没有的提交，先拉取再推送：
```bash
git pull --rebase
git push
```

### Q: 合并冲突怎么办？

A: 打开冲突文件，手动修改后：
```bash
git add .
git commit -m "解决合并冲突"
```

### Q: 误删文件能恢复吗？

A: 如果提交过，可以从历史恢复：
```bash
git checkout HEAD -- 文件名
```

### Q: 如何修改上一次的 commit 说明？

A: 
```bash
git commit --amend -m "新的说明"
```

### Q: 如何完全删除某个文件的所有历史？

A: 使用 `git filter-branch`（复杂，建议先备份）。

## 十五、命令速查表

| 操作 | 命令 |
|------|------|
| 查看状态 | `git status` |
| 添加文件 | `git add .` |
| 提交 | `git commit -m "说明"` |
| 推送 | `git push` |
| 拉取 | `git pull` |
| 查看历史 | `git log --oneline` |
| 查看修改 | `git diff` |
| 创建分支 | `git checkout -b 分支名` |
| 切换分支 | `git checkout 分支名` |
| 合并分支 | `git merge 分支名` |
| 删除分支 | `git branch -d 分支名` |
| 暂存修改 | `git stash` |
| 恢复暂存 | `git stash pop` |
| 克隆仓库 | `git clone 仓库地址` |
| 撤销修改 | `git checkout -- 文件名` |

---

*最后更新：2024-06-21*