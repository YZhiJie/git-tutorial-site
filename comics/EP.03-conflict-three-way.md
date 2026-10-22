# EP.03 冲突的三方合并

> 对应正文：[guide/collaboration/02-conflict-essence.md](../guide/collaboration/02-conflict-essence.md)

## 剧情回顾

- **第 1 格**：标签君面对满屏 `<<<<<<<` 不敢动，想全删又怕代码跑不起来。
- **第 2 格**：码叔揭底：冲突不是 bug，是 base、ours、theirs 三方信息不足，Git 无法替你决策。
- **第 3 格**：像素酱学会标准动作——看 status、改文件、`git add`、`git commit`。
- **第 4 格**：结案——删标记只是第一步，写出最终逻辑才算解决。

## 知识点拆解

### 第 1 格 · 冲突标记怎么读

```text
<<<<<<< HEAD
timeout: 3000
=======
timeout: 5000
>>>>>>> feature/pay
```

上半段是当前分支（ours），下半段是被合入分支（theirs）。标记只表示「同一处两边都改了」，不代表哪边一定对。

### 第 2 格 · 为什么是三方

- **base**：两个分支的共同祖先，提供「原来是什么」；
- **ours**：当前分支的版本；
- **theirs**：被合入分支的版本；
- 当两边对同一区域的改动无法自动合并时，Git 只能把选择权交还给人。

这就是为什么冲突不是 Git 的缺陷，而是信息论上的必然。

### 第 3 格 · 标准解决流程

```bash
$ git status                 # 1. 看哪些文件 both modified
# 2. 编辑文件，删掉冲突标记，写出最终内容
$ git add src/config.ts      # 3. 暂存即表示「这个文件处理完了」
$ git commit                 # 4. 完成合并
```

如果整份文件只想采用某一方：

```bash
$ git checkout --ours   src/config.ts
$ git checkout --theirs src/config.ts
```

### 第 4 格 · 解决的标准

- 不是「删掉尖括号」，而是「写出同时满足两边意图的最终逻辑」；
- 改完先跑测试，再 `git add`；
- 想推倒重来：`git merge --abort` 回到合并前。

## 坑点清单

1. 只删冲突标记，不核对业务语义，代码能跑但逻辑错了；
2. 漏掉某个冲突文件就 commit，Git 会拒绝继续；
3. 用 `--ours` / `--theirs` 整份覆盖，丢掉了另一边的必要改动。

## 自测 3 题（含答案）

1. 冲突标记里的 HEAD 指哪一边？——**当前分支（ours），也就是你正在其上执行 merge 的分支。**
2. 什么时候 Git 认为冲突已解决？——**你把文件修改后 `git add` 进暂存区，Git 才把它标记为 resolved。**
3. 解冲突时最容易被忽略的一步是什么？——**写出最终业务逻辑并跑测试，而不是机械删除标记。**
