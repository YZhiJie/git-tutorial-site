# EP.04 交互式整理提交

> 对应正文：[guide/collaboration/04-interactive-rebase.md](../guide/collaboration/04-interactive-rebase.md)

## 剧情回顾

- **第 1 格**：像素酱的 PR 里塞了七个 wip 提交，reviewer 要求先整理成两个再提。
- **第 2 格**：码叔把 `git rebase -i` 比作一张待办清单，列出 pick、reword、squash、fixup、drop、exec。
- **第 3 格**：标签君发现整理后哈希全变了，码叔解释：本质是逐个 cherry-pick 到新基底。
- **第 4 格**：结案——只对没推送的分支整理历史，冲突时用 continue / skip / abort。

## 知识点拆解

### 第 1 格 · 评审看的是叙事

```bash
$ git log --oneline -7
c7 wip
b6 wip
a5 fix typo
94 feat: 支付回调（半成品）
83 wip
```

一个功能拆成七段「wip」，评审无法判断每一步的意图；整理成「实现 + 测试」两个提交，阅读成本立刻下降。

### 第 2 格 · 清单里的动作

| 动作 | 作用 |
| ---- | ---- |
| `pick` | 保留该提交 |
| `reword` | 保留改动，改提交信息 |
| `squash` | 并入上一个提交，可编辑合并后的信息 |
| `fixup` | 并入上一个提交，丢弃本条信息 |
| `drop` | 删除该提交 |
| `exec` | 在该位置插入一条命令（如跑测试） |

清单从上往下执行，保存退出后开始重放。

### 第 3 格 · 哈希为什么变

```bash
$ git rebase -i HEAD~5
# 编辑器里改成：
pick   a5 fix typo
squash b6 wip
squash c7 wip
```

rebase 会把每个提交的 diff 在新基底上重新应用，父提交变了，哈希必然变化。旧提交仍可通过 reflog 找回。

### 第 4 格 · 冲突与收尾

```bash
$ git add .
$ git rebase --continue   # 解决完继续
$ git rebase --skip       # 跳过当前提交
$ git rebase --abort      # 回到开始前
```

整理完如果分支已推送，需要 `git push --force-with-lease`；强推前先 fetch，确认远端没有新提交。

## 坑点清单

1. 对公共分支做交互式 rebase 后强推，同事历史断裂；
2. squash 时把提交信息也合并成一句「wip」，整理失去意义；
3. 冲突中 `--skip` 掉关键提交，功能悄悄缺失。

## 自测 3 题（含答案）

1. `squash` 和 `fixup` 的区别？——**squash 会打开编辑器让你编辑合并后的提交信息；fixup 直接丢弃本条信息，沿用上一条。**
2. rebase 后原提交还在吗？——**对象仍在仓库中，只是没有分支指向，reflog 过期前都能找回。**
3. 什么时候不该用交互式 rebase？——**分支已被他人拉取或已合并进公共分支时，改写历史会影响协作者。**
