# EP.07 reflog 后悔药

> 对应正文：[guide/mastery/01-reflog.md](../guide/mastery/01-reflog.md)

## 剧情回顾

- **第 1 格**：像素酱先删分支又 `reset --hard`，两天的工作看起来全没了。
- **第 2 格**：码叔翻开 reflog：HEAD 每次移动都有记录，默认保留 90 天。
- **第 3 格**：标签君学会两种救法：建分支接住哈希，或用 `ORIG_HEAD` 整体回退。
- **第 4 格**：结案——先停手再翻日记；reflog 救不了从未 add 的工作区改动。

## 知识点拆解

### 第 1 格 · 删除只是删指针

`git branch -D` 删除的是分支引用，不是提交对象；`reset --hard` 移动的是分支指针，原提交只是暂时没有引用指向。对象仍保存在 `.git/objects`，直到垃圾回收才可能清除。

### 第 2 格 · reflog 记了什么

```bash
$ git reflog --date=iso -8
e5d4c3b HEAD@{0}: reset: to HEAD~2
9a8b7c6 HEAD@{1}: commit: 导出字段
1f2e3d4 HEAD@{2}: commit: 导出订单
7d3e2f1 HEAD@{3}: checkout: 回到 main
```

每条记录都带当时的提交哈希，`HEAD@{n}` 是相对时间索引。分支 reflog 位于 `.git/logs/refs/heads/<branch>`。

### 第 3 格 · 两种恢复路径

```bash
# 恢复误删的分支：先建临时分支接住
$ git branch feature/pay 7d3e2f1

# 撤销一次 reset --hard：回到操作前
$ git reset --hard ORIG_HEAD

# 只想捡回某个提交
$ git cherry-pick 9a8b7c6
```

建分支后先 `git log` 与 `git diff` 验证内容，再决定是否改回原名。

### 第 4 格 · 操作顺序

1. 立即停止提交、reset、checkout 等会移动 HEAD 的操作；
2. `git reflog` 找到目标哈希；
3. 用临时分支接住，验证无误后再整理；
4. reflog 默认 90 天过期，越早处理越安全。

## 坑点清单

1. 出事后继续提交，把现场覆盖得更难辨认；
2. 以为 reflog 能恢复从未 `git add` 的工作区改动（不能）；
3. 恢复后直接改回原名并强推，没先验证内容是否完整。

## 自测 3 题（含答案）

1. 删除分支后提交对象会立刻消失吗？——**不会，只是没有引用指向；GC 之前对象仍在仓库中。**
2. `HEAD@{1}` 是什么意思？——**HEAD 上一次移动的位置，可通过 reflog 查到对应提交哈希。**
3. reflog 的盲区是什么？——**它记录引用移动，不记录工作区文件内容；未 add 的改动无法靠它恢复。**
