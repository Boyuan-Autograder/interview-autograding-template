# Glossary

本仓的领域术语表。工程技能在输出中提及领域概念时，使用此处定义的术语，不要漂移到同义词。

---

## 仓库角色

- **模板仓（Template Repository）** — `Boyuan-Autograder/interview-autograding-template`。公开的 GitHub Template Repository。候选人通过 "Use this template" 在自己账号下创建派生仓。承载 task 工作区资产（task2/compose.yml、task4/ 子目录含 starter_makefile/input_images/tool/、task5/greet.sh）、README 指引、`.gitignore`、`package.json`（列工具仓为依赖）、`.github/workflows/grade.yml`（push 触发解密展示）。
- **工具仓（Tool Repository）** — `Boyuan-Autograder/interview-autograder`。纯 TypeScript npm 包，包名 `@boyuan-autograder/interview-autograder`，CLI 命令 `autograding`。单包双角色：`grade`（本地加密评测）和 `decrypt`（云端解密展示）。不发布到 npmjs.com，通过 `github:` ref 安装。本仓不持有其源码，仅作为依赖消费其 CLI 外部行为。
- **派生仓（Fork Repository）** — 候选人通过 "Use this template" 在自己 GitHub 账号下创建的仓库。候选人是派生仓的 owner。

## 评测流程

- **grade** — `npx autograding grade`。在候选人本地（派生仓 clone 目录）运行，执行五个 task 的环境检查，生成加密报告单。评分全部发生在本地，不触碰网络（`gh`、`ssh -T` 等远端验证不进 grader）。
- **decrypt** — `npx autograding decrypt <report-path>`。在 GitHub Actions 内运行，解密报告单并输出 Markdown 得分面板。只做解密和展示，不跑任何评测。
- **报告单（Report）** — `autograding_report.json`。grade 产出的加密文件，AES-256-GCM 加密。候选人 commit 并 push 此文件触发展示。含 author、timestamp、五个 task 得分与明细、total_score（满分 500）。

## task 结构

- **task1** — 配置运行环境（满分 100）。OS/WSL 环境（30）、Git 已装且版本 ≥ 2.30（15）、Python3 已装且版本 ≥ 3.10（15）、Node.js 已装且版本 ≥ 20（20）、SSH 本地配置（20，三项全过：`~/.ssh/id_*.pub` 存在 + `ssh-add -l` 非空 + `ssh-keygen -l -f` 成功）。版本检查"已装且达标"才给分。
- **task2** — 安装 Docker（满分 100）。Docker 已装（15）、服务运行（15）、用户权限（15，macOS 跳过）、hello-world 执行（15）、Docker Compose 已装（15）、**Nginx Service Reachable Check**（25，候选人手动 `docker compose -f task2/compose.yml up -d` 拉起 nginx 后，grader 跑 `ps` 确认容器 running + `curl http://localhost:8080` 验证含 `Welcome to nginx!`；grader 不调 up/down，nginx 不可达直接判该项 0 分并提示候选人先 up）。详见 ADR-0002。
- **task3** — 基础 Linux 操作（满分 100）。在 Docker 容器内做文件操作（目录创建 10、文件创建 10、文件内容 10、目录复制 10、tar 打包 10，共 50 分）+ 15 道选择题（共 50 分）。
- **task4** — Makefile（满分 100）。检查候选人写的 Makefile：`make_all_creates_zip`（30）、`zip_contains_all_files`（40）、`make_clean_works`（30）。工作区在 `task4/` 子目录。
- **task5** — Git 版本控制（满分 100）。在候选人派生仓内本地评测 git 状态：分支创建（30，存在 `task5-git` 分支）、提交（30，该分支相对 main 有 ≥1 commit 且 message 含 `task5` 且改动触及 `task5/greet.sh`）、合并/验证（40，main 上跑 `bash task5/greet.sh` 输出 `"Hello, 博远!"`）。弱约束，允许 fast-forward。纯本地 `git`/`bash` 检查，不进网络。

## task 工作区资产

按 `taskN/` 子目录约定组织，分门别类，避免仓库根目录混乱。

- **task2/compose.yml** — 预置的 Docker Compose 文件，起一个 nginx 服务（`container_name: autograding-task2-nginx`，`8080:80`）。候选人手动跑 `docker compose -f task2/compose.yml up -d` 拉起、grade 结束后跑 `down` 清理；grader 只跑 `ps` + `curl` 验证，不调 up/down（见 ADR-0002）。候选人不修改此文件。
- **task4/starter_makefile** — 候选人参考的 Makefile 模板，含 TODO 标记。候选人复制为 `task4/Makefile` 并完成。模板仓提交此文件。
- **task4/input_images/** — 4 张输入 jpg（cat、dog、panda、pig）。Makefile 处理的输入。
- **task4/tool/** — AI 扣图工具源码（Dockerfile、main.py、requirements.txt、models/u2netp.onnx）。候选人无需修改，Makefile 的 `docker build` 目标构建此目录为 `ai-remover` 镜像。
- **task4/Makefile** — 候选人完成的文件。工具仓 task4 在 `task4/` 子目录找字面 `Makefile`，无 starter_makefile 回退。grader 用 `make -C task4` 调用。
- **task5/greet.sh** — 预置的带 bug 脚本（变量名 typo：`$nam` 应为 `$name`）。候选人在 `task5-git` 分支修复，合回 main 后 grader 跑 `bash task5/greet.sh` 验证输出 `"Hello, 博远!"`。
- **数据挂载（data mounts）** — 运行 task4 工具镜像时的挂载约定：只把 `input_images/`、`output_images/` 两个数据目录挂入容器的 `/app` 对应路径，工具代码与模型随镜像自带、不参与挂载。避免说整目录挂载或工作区挂载：把整个 `task4/` 盖到 `/app` 的旧约定会遮住镜像内文件、废掉默认入口（见 ADR-0003）。

## 展示

- **Job Summary** — GitHub Actions 的 `$GITHUB_STEP_SUMMARY`。decrypt 输出 Markdown 到此文件，候选人在 Actions 运行记录里看得分面板。私有仓可用、零设置。

## 报告提交

- **自主提交（Opt-in Submission）** — 候选人把加密报告单主动提交到社团官网的行为。push 不会自动触发：候选人在派生仓 Actions 页面对 `Upload report to official site` 工作流点一次 **Run workflow** 才提交；不点 = 报告只停留在自己的 Job Summary，不会进入官网（见 ADR-0004）。
- **提交身份（Submission Identity）** — GitHub 登录名（`github.actor`），候选人无法修改；官网接口的 `github_username` 字段即此身份，榜单按它归属。
- **报告作者（Report Author）** — 报告单明文 `author` 字段，取 `git config user.name`，候选人可自填（真名/昵称）。与提交身份是两个概念，两者都会入库，不得混用。
- **官网后端（Official Site Backend）** — 社团官网招新系统后端（Spring Boot / Java 17）。提供公开 intake 端点接收加密报告单，用与工具仓一致的密钥在自己一侧解密（不调用 npm 包）、按 JSON Schema 校验后入库，形成评测总览/榜单。解密契约见工具仓 `interview-autograder` 的 #7（docs/report-format.md + 冻结测试向量）。端点零认证、密钥公开：任何持密钥者可伪造任意报告直 POST，榜单为自愿采样 + 招新参考，非权威判定（荣誉系统，ADR-0004）。
