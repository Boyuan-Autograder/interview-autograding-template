# Glossary

本仓的领域术语表。工程技能在输出中提及领域概念时，使用此处定义的术语，不要漂移到同义词。

---

## 仓库角色

- **模板仓（Template Repository）** — `Boyuan-Autograder/interview-autograding-template`。公开的 GitHub Template Repository。候选人通过 "Use this template" 在自己账号下创建派生仓。承载 task4 工作区（starter_makefile、input_images、tool/）、README 指引、`.gitignore`、`package.json`（列工具仓为依赖）、`.github/workflows/grade.yml`（push 触发解密展示）。
- **工具仓（Tool Repository）** — `Boyuan-Autograder/interview-autograder`。纯 TypeScript npm 包，包名 `@boyuan-autograder/interview-autograder`，CLI 命令 `autograding`。单包双角色：`grade`（本地加密评测）和 `decrypt`（云端解密展示）。不发布到 npmjs.com，通过 `github:` ref 安装。本仓不持有其源码，仅作为依赖消费其 CLI 外部行为。
- **派生仓（Fork Repository）** — 候选人通过 "Use this template" 在自己 GitHub 账号下创建的仓库。候选人是派生仓的 owner。

## 评测流程

- **grade** — `npx autograding grade`。在候选人本地（派生仓 clone 目录）运行，执行四个 task 的环境检查，生成加密报告单。评分全部发生在本地。
- **decrypt** — `npx autograding decrypt <report-path>`。在 GitHub Actions 内运行，解密报告单并输出 Markdown 得分面板。只做解密和展示，不跑任何评测。
- **报告单（Report）** — `autograding_report.json`。grade 产出的加密文件，AES-256-GCM 加密。候选人 commit 并 push 此文件触发展示。含 author、timestamp、四 task 得分与明细、total_score。

## task 结构

- **task1** — 配置运行环境。检查 OS/WSL 版本（50 分）、Git 已装（25 分）、Python3 已装（25 分）。
- **task2** — 安装 Docker。检查 Docker 已装（25 分）、服务运行（25 分）、用户在 docker 组（25 分，macOS 跳过）、容器执行 hello-world（25 分）。
- **task3** — 基础 Linux 操作。在 Docker 容器内做文件操作（目录创建、文件创建、文件内容、目录复制，共 50 分）+ 15 道选择题（共 50 分）。
- **task4** — Makefile。检查候选人写的 Makefile：`make_all_creates_zip`（30 分）、`zip_contains_all_files`（40 分）、`make_clean_works`（30 分）。工作区在仓库根。

## task4 工作区资产

- **starter_makefile** — 候选人参考的 Makefile 模板，含 TODO 标记。候选人复制为 `Makefile` 并完成。模板仓提交此文件。
- **input_images/** — 4 张输入 jpg（cat、dog、panda、pig）。Makefile 处理的输入。
- **tool/** — AI 扣图工具源码（Dockerfile、main.py、requirements.txt、models/u2netp.onnx）。候选人无需修改，Makefile 的 `docker build` 目标构建此目录为 `ai-remover` 镜像。
- **Makefile** — 候选人完成的文件。工具仓 task4 在 `process.cwd()` 找字面 `Makefile`，无 starter_makefile 回退。

## 展示

- **Job Summary** — GitHub Actions 的 `$GITHUB_STEP_SUMMARY`。decrypt 输出 Markdown 到此文件，候选人在 Actions 运行记录里看得分面板。私有仓可用、零设置。
