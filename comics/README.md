# Git 漫画剧场 · 霓虹墨 Neon Ink

把 Git 的三区模型、分支合并、冲突解决与企业协作流程，画成四格漫画。每话对应教程的一个主题，配剧情回顾、知识点拆解、坑点清单与自测题。

打开 [漫画阅读器](./index.html) 获得最佳体验：左侧选集、右侧大图、下方可展开讲解文档。

## 导航

| 话 | 主题 | 对应正文 | 漫画 | 讲解 |
| ---- | ---- | ---- | ---- | ---- |
| EP.01 | 三区模型与分支指针 | [03-three-areas.md](../guide/basics/03-three-areas.md) | [SVG](./EP.01-three-areas-and-refs.svg) | [MD](./EP.01-three-areas-and-refs.md) |
| EP.02 | merge 还是 rebase | [03-merge-vs-rebase.md](../guide/collaboration/03-merge-vs-rebase.md) | [SVG](./EP.02-merge-vs-rebase.svg) | [MD](./EP.02-merge-vs-rebase.md) |
| EP.03 | 冲突的三方合并 | [02-conflict-essence.md](../guide/collaboration/02-conflict-essence.md) | [SVG](./EP.03-conflict-three-way.svg) | [MD](./EP.03-conflict-three-way.md) |
| EP.04 | 交互式整理提交 | [04-interactive-rebase.md](../guide/collaboration/04-interactive-rebase.md) | [SVG](./EP.04-interactive-rebase.svg) | [MD](./EP.04-interactive-rebase.md) |
| EP.05 | 精准搬运提交 | [05-cherry-pick.md](../guide/collaboration/05-cherry-pick.md) | [SVG](./EP.05-cherry-pick.svg) | [MD](./EP.05-cherry-pick.md) |
| EP.06 | 线上回滚怎么选 | [06-revert-vs-reset.md](../guide/collaboration/06-revert-vs-reset.md) | [SVG](./EP.06-revert-vs-reset.svg) | [MD](./EP.06-revert-vs-reset.md) |
| EP.07 | reflog 后悔药 | [01-reflog.md](../guide/mastery/01-reflog.md) | [SVG](./EP.07-reflog-rescue.svg) | [MD](./EP.07-reflog-rescue.md) |
| EP.08 | 企业级分支模型 | [09-branching-models.md](../guide/collaboration/09-branching-models.md) | [SVG](./EP.08-enterprise-branching.svg) | [MD](./EP.08-enterprise-branching.md) |

## 目录结构

```text
comics/
├── index.html                 # 零依赖阅读器
├── manifest.json              # 话数与分篇映射
├── style-guide.md             # 视觉与叙事规范
├── check-comics.mjs           # 一致性校验脚本
├── samples/character-sheet.svg
├── EP.NN-*.svg                # 四格漫画
└── EP.NN-*.md                 # 配套讲解
```

## 校验

```bash
node comics/check-comics.mjs
```

校验内容：manifest 与文件是否一一对应、SVG 标签是否良构、文字是否显式声明字号、是否存在溢出风险、讲解 MD 结构是否完整。

## 新增一话

1. 按 `style-guide.md` 的坐标与色板绘制 SVG；
2. 按固定结构写讲解 MD，并在顶部链接对应正文；
3. 更新 `manifest.json` 与本页导航表；
4. 运行校验脚本，再执行 `pnpm docs:build`。
