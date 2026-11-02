# EP.05 精准搬运提交

> 对应正文：[guide/collaboration/05-cherry-pick.md](../guide/collaboration/05-cherry-pick.md)

## 剧情回顾

- **第 1 格**：标签君在 main 修好线上 hotfix，release 分支也要，但整支合并会带上未发布功能。
- **第 2 格**：码叔解释 cherry-pick 只计算目标提交的 diff，在目标分支重放成新提交。
- **第 3 格**：像素酱学会单提交、多提交、区间与 `-x`、`-n`、`-e` 的用法。
- **第 4 格**：结案——少量搬运可以，长期双向同步要用 merge；区间搬运注意边界。

## 知识点拆解

### 第 1 格 · 只取一个修复

```bash
$ git log --oneline -3 main
a1b2c3d fix: 支付回调超时
d4e5f6a feat: 新会员体系
9c8b7a6 chore: 升级依赖
```

release 分支只需要 `a1b2c3d`，不需要另外两个提交。整支 merge 会把未发布功能带上去，因此选择 cherry-pick。

### 第 2 格 · 只搬 diff

cherry-pick 取出目标提交相对其父提交的改动，在当前 HEAD 上重新应用，并生成一个新的提交对象。因为父提交不同，哈希也会不同。

### 第 3 格 · 常用参数

```bash
$ git cherry-pick a1b2c3d                 # 单个提交
$ git cherry-pick a1b2c3d e4f5g6h         # 多个提交，按顺序
$ git cherry-pick A..B                    # 不含 A，含 B
$ git cherry-pick A^..B                   # 想包含 A 时用这个
$ git cherry-pick -x a1b2c3d              # 在信息里记录来源提交
$ git cherry-pick -n a1b2c3d              # 只应用改动，不自动提交
$ git cherry-pick -e a1b2c3d              # 应用前编辑提交信息
```

冲突处理与 merge 相同：改文件、`git add`、`git cherry-pick --continue`。

### 第 4 格 · 使用边界

- 适合：少量 hotfix 回灌、把误提交搬到正确分支；
- 不适合：长期双向同步、整条特性链搬运；
- 长期同步应使用 `merge` 或 `rebase`，否则会产生大量重复提交与隐藏冲突。

## 坑点清单

1. 误以为 `A..B` 包含 A，漏掉关键提交；
2. 同一个提交被反复 pick，产生重复内容；
3. 搬运后不核对来源，线上修复在 release 分支悄悄丢失。

## 自测 3 题（含答案）

1. cherry-pick 后哈希为什么变？——**提交内容相同，但父提交不同，提交对象的哈希由内容与父提交共同决定。**
2. `A..B` 包含 A 吗？——**不包含，区间语义是「A 之后到 B」；要包含 A 用 `A^..B`。**
3. `-x` 有什么用？——**在提交信息里追加来源提交哈希，便于日后追溯这次搬运。**
