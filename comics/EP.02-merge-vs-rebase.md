# EP.02 merge 还是 rebase

> 对应正文：[guide/collaboration/03-merge-vs-rebase.md](../guide/collaboration/03-merge-vs-rebase.md)

## 剧情回顾

- **第 1 格**：像素酱的功能分支落后 main 二十个提交，同事问她选 merge 还是 rebase。
- **第 2 格**：码叔画出两种历史：merge 保留分叉并生成双父提交，rebase 把提交逐个搬到新基底。
- **第 3 格**：标签君对照两行命令，确认公共分支不能变基，私有分支可以 rebase 后强推。
- **第 4 格**：结案——公共分支只合并不变基，功能分支可以 rebase；强推要用 `--force-with-lease`。

## 知识点拆解

### 第 1 格 · 先问分支归属

选 merge 还是 rebase，不取决于个人喜好，而取决于这条分支有没有被别人拉取、基于它开发。

```bash
$ git log --oneline --graph --all
* 9a8b7c6 (main) feat: 新增导出
| * 4c5d6e7 (HEAD -> feature) wip
|/
* 2a9c7f1 chore: 初始化项目
```

只要有人基于你的分支工作，改写它的哈希就会让别人历史对不上。

### 第 2 格 · 两种历史的差别

- **merge**：把两条历史汇合，生成一个有两个父提交的合并提交，真实分叉被保留；
- **rebase**：以新基底为起点，把原提交的 diff 逐个重放，生成哈希全新的提交副本；
- 原提交不会立刻消失，只是没有分支指向它，仍可通过 reflog 找回。

### 第 3 格 · 命令与边界

```bash
# 功能分支跟上主干，保持线性
$ git switch feature
$ git rebase main

# 改写后只能强推，但要带安全阀
$ git push --force-with-lease
```

`--force-with-lease` 会先核对远端分支位置；如果同事在此期间推了新提交，它会拒绝覆盖。

### 第 4 格 · 黄金法则

- 公共分支（main、develop、release）只 merge，不 rebase；
- 个人功能分支可以 rebase 整理，再强推；
- 用 `git pull --rebase` 代替默认 merge，减少无意义的合并提交。

## 坑点清单

1. rebase 后直接 `git push`，被远端拒绝；
2. 用 `--force` 覆盖了同事刚推的提交；
3. 把已经合并进主干的 feature 分支再次 rebase，制造重复提交。

## 自测 3 题（含答案）

1. rebase 后为什么必须强推？——**提交被重放成新哈希，远端旧提交与新历史没有共同祖先路径，普通 push 会被拒绝。**
2. `--force-with-lease` 比 `--force` 安全在哪？——**它要求远端位置与本地记录一致，别人推过新提交时就拒绝覆盖。**
3. 什么时候绝对不要 rebase？——**分支已被他人拉取或作为协作基线时，改写历史会破坏别人的本地仓库。**
