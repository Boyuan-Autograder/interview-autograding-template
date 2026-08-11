# Task 5: Git 版本控制

在前面的任务中，你已经掌握了环境配置、Docker、Linux 基础操作和 Makefile。本任务考察 Git 版本控制的实战——**分支创建、提交、合并**，这是日常工程协作中最核心的能力之一。请通过一个"修复 bug 再合并"的真实工作流完成它，而不是为了提交而提交。

> 你会在派生仓里修改一个预置的带 bug 脚本 `task5/greet.sh`，用 Git 工作流把它修复并最终合回 main。

## 任务目标

仓库根目录有一个预置脚本 [task5/greet.sh](../task5/greet.sh)，它想打印 `Hello, 博远!`，但因为一个变量名拼写错误（typo）只能输出 `Hello, !`（空名字）。

你的任务分三步：

1. **创建分支**：新建一个名为 `task5-git` 的分支。
2. **修复并提交**：在 `task5-git` 分支上把 `task5/greet.sh` 里的 typo 修好（把 `$nam` 改成 `$name`），并做一次有意义的提交——commit 说明文字里要包含 `task5` 子串，且这次提交必须改动 `task5/greet.sh`。
3. **合并**：把修复合回 main（fast-forward 或 `--no-ff` 创建合并提交均可），合回后运行 `bash task5/greet.sh` 应输出 `Hello, 博远!`。

## 操作示例

```bash
# 1. 前往仓库目录，确认在 main 分支
git status
git branch

# 2. 创建并切换到 task5-git 分支
git checkout -b task5-git

# 3. 修复 greet.sh 的 typo：把 $nam 改成 $name（可用编辑器，或 sed）
sed -i 's/\$nam/\$name/' task5/greet.sh

# 4. 本地验证修复效果
bash task5/greet.sh          # 应输出 Hello, 博远!

# 5. 提交（message 需包含 task5，改动需触及 task5/greet.sh）
git add task5/greet.sh
git commit -m "task5: 修复 greet.sh 变量名拼写错误"

# 6. 切回 main 并合并（ff 或 --no-ff 均可）
git checkout main
git merge task5-git          # 默认 ff；也可 git merge --no-ff task5-git

# 7. 合并后再次验证 main 上的修复生效
bash task5/greet.sh          # 应输出 Hello, 博远!
```

## 评分规则（满分 100）

| 评分点 | 分值 | 判定方式 |
| --- | --- | --- |
| 分支创建 | 30 | 存在名为 `task5-git` 的分支 |
| 提交 | 30 | `task5-git` 分支上有一个提交，其 message 含 `task5` 子串，且该提交改动触及 `task5/greet.sh` |
| 合并 / 验证 | 40 | 在 main 上运行 `bash task5/greet.sh` 输出恰为 `Hello, 博远!`（即修复已合回 main 生效） |

> **提示**：评测是本地 `git`/`bash` 确定性判定，不连接网络。合并允许 fast-forward；`--no-ff` 会保留分支的历史轨迹，便于展示你的合并操作，推荐使用。

## 评测

在仓库根目录运行评测命令，五个 task 会一起执行：

```bash
npx autograding grade
```

评测结束会生成加密报告单 `autograding_report.json`，commit 并 push 后可在 Actions 运行记录里查看你的各 task 得分与明细。