# ADR-0002: task2 的 Compose up/down 改由候选人手动执行，grader 只验证

**Date:** 2026-08-11
**Status:** Accepted

## Context

task2（安装 Docker）的第六项检查 "Compose 拉起验证" 原本由 grader 编排完整闭环：`docker compose -f task2/compose.yml up -d` → `ps` 检查容器 running → `curl http://localhost:8080` 验证 nginx 默认页 → `down` 清理。候选人在此步是被动的——grader 替候选人完成拉起与清理，候选人只需保证 Docker 与 Compose 可用、镜像能拉取。

task2 的考察目标是"会用 Compose 编排服务"。grader 全权代劳 up/down，意味着候选人实际上没有亲手执行过编排命令——这与考察目标错位：候选人通过了检查，却未必亲手拉起过一次服务。

## Decision

把 Compose 生命周期的 up 与 down 交回候选人手动执行，grader 只验证"已 up 的 nginx"是否真的可达。

具体分工：

- **候选人**在运行 `npx autograding grade` **之前**手动执行 `docker compose -f task2/compose.yml up -d` 拉起 nginx；在 grade 结束**之后**手动执行 `docker compose -f task2/compose.yml down` 清理。
- **grader** 跑到 task2 时只做两件事：`docker compose -f task2/compose.yml ps` 确认 `autograding-task2-nginx` 容器处于 running，再 `curl http://localhost:8080` 验证返回内容含 `Welcome to nginx!`。grader **不**调用 `up` 或 `down`。
- 若 ps 或 curl 失败，grader 在该项判 0 分并输出提示"请先 `docker compose -f task2/compose.yml up -d`"，不替候选人兜底拉起。
- 第六项检查名从 `Compose Up Nginx Check` 改为 `Nginx Service Reachable Check`，满分仍为 25，六项总分仍为 100。

## Rationale

1. **考察对齐。** task2 考"会用 Compose"，那么候选人就该亲手 up 过一次。把 up 交还候选人，检查名实相符——通过即代表候选人真的拉起过服务。

2. **grader 只验证、不编排。** grader 的职责是"评测已存在的状态"，不是"替候选人执行操作再自我验证"。原闭环里 grader 既是执行者又是验证者，up 失败时它自己判自己失败，语义上拧巴。改后 grader 只做只读验证，职责清晰。

3. **不兜底，才有牙。** 既然把 up 交给候选人，up 没做就该扣分。grader 不在失败时自己 `up` 一次兜底，否则这个分工是名义上的——候选人跑不跑 up 都能过，等于没改。

4. **保留 `ps` 检查防冒充。** grader 仍跑 `ps` 确认容器 running，而非只 `curl`，是为了挡"候选人在 8080 端口起一个别的静态服务冒充 nginx"的取巧。`ps` 检查的是 `task2/compose.yml` 定义的 `autograding-task2-nginx` 容器，冒充成本高于诚实拉起。

5. **down 归候选人是可接受的残留。** 候选人不跑 down 会留下 nginx 容器——但这是候选人的本地环境，不影响 grader；且已确认 task3 用的是独立的 `challenge` 容器、不碰 8080 端口，task2 的 nginx 留着不干扰后续 task。

## Consequences

- **工具仓（`interview-autograder`）：** `src/task2.ts` 的 `composeUpVerifiesNginx()` 改为只跑 `ps` + `curl`，去掉 `up`/`down` 调用；检查名改为 `Nginx Service Reachable Check`。`test/cli.test.js` 删掉 `FAKE_DOCKER_COMPOSE_UP_OK`/`DOWN_OK` mock 分支与对应开关，删掉"Compose Up Nginx fails when compose up fails"测试，新增"nginx 不可达（`FAKE_CURL_OK=false`）→ Nginx Service Reachable Check 失败"测试。
- **模板仓（本仓）：** `tasks/task2.md` 改写"关于 Compose 拉起验证"一节，明确新契约——候选人负责 up/down，grader 只验证。`CONTEXT.md` 的 task2 词条与 `task2/compose.yml` 注释同步更新。
- **失败处理偏硬。** 候选人忘了先 up，task2 第六项直接 0 分。grader 会输出提示，但不兜底。这是"分工有牙"的代价，需在 task2.md 里把"先 up 再 grade"讲清楚。
