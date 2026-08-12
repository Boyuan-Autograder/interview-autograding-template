# ADR-0003: task4 工具镜像运行时采用数据挂载，保持镜像自包含

**Date:** 2026-08-13
**Status:** Accepted

## Context

task4 的 `ai-remover` 工具镜像按"自包含"方式构建：`COPY . .` 把脚本与模型一并拷入 `/app`（`/app/main.py`、`/app/models/`），默认入口 `ENTRYPOINT ["python", "main.py"]`。但 Makefile 模板的运行配方把整个 `task4/` 工作区（含 `tool/` 源码目录）以 `-v "$(shell pwd):/app"` 挂载到容器的 `/app`——bind mount 会遮住镜像内 `/app` 的内容：镜像自带的脚本与模型在运行时全部不可见，默认入口必然失败。

PR #10 曾用文档补丁记录这一陷阱（"挂载会遮住镜像内文件，需 `--entrypoint python` 覆盖入口、改用 `/app/tool/...` 挂载后路径"）。这要求候选人理解三个与 Makefile 考察目标无关的 docker 内幕概念，且文档写死了 `--entrypoint` 与挂载布局的耦合——将来任何人改动 Dockerfile（去掉 `COPY . .` 或改入口），这些指引会静默失效。文档补丁是止损，不是根治。

## Decision

- **工具镜像保持自包含，Dockerfile 不做任何改动。**
- 运行时采用**数据挂载**：只把 `input_images/`、`output_images/` 两个数据目录挂入容器的 `/app` 对应路径；`tool/` 源码目录不参与运行时挂载（它唯一的合法入口是构建上下文）。
- docker run 配方（模板提示与任务文档记载的形态）：

  ```makefile
  docker run --rm \
    -v "$(shell pwd)/input_images:/app/input_images" \
    -v "$(shell pwd)/output_images:/app/output_images" \
    -e U2NET_HOME=/app/models \
    $(TOOL_IMAGE_NAME) --input "/app/$<" --output "/app/$@"
  ```

  默认入口 `python main.py` 原样生效；`U2NET_HOME=/app/models` 指向镜像内模型目录；输入输出走挂载路径。
- `make shell` 调试入口同步改为数据挂载，保证调试布局与真实配方一致。
- 容器首次运行前配方在宿主侧 `mkdir -p` 输出目录，避免"源目录不存在"的根属目录歧义。

## Rationale

1. **挂载只承载数据。** 容器里"工具（镜像自带）"与"数据（挂载注入）"职责分离、两不相扰——不存在"遮住"问题，也就不需要任何绕过手段。
2. **镜像按构建原样使用。** 自包含镜像可独立运行、可独立调试（`make shell` 里 `python main.py --input /app/input_images/...` 直接可跑），配方不依赖镜像布局的意外。
3. **教学密度下降。** 配方每个成分语义唯一：`-v` 只做数据搬运，镜像名即工具，参数即"原料路径 → 成品路径"；无需理解 entrypoint 覆盖、挂载遮蔽等概念。
4. **改动面最小。** Dockerfile 与 grader 契约（make all → zip 精确集合 → clean）零改动。

## Considered Options

- **A1｜整目录挂载，挂载根移到 `/workspace`**：镜像保持自包含，挂载改为 `-v "$(shell pwd):/workspace"`。缺点：`tool/` 源码搭便车挂进容器成为空气挂载；引入第二套挂载根，与既有 `/app` 表述全面分叉。
- **A2｜镜像瘦身为纯运行环境**：Dockerfile 去掉 `COPY . .`、入口改为 `["python", "tool/main.py"]`，代码与模型全部走挂载。缺点：镜像离开挂载不能独立运行，`ai-remover` 名不副实；入口编码了挂载布局，与运行时耦合更紧。
- **A3（采纳）｜数据挂载**：见 Decision。

## Consequences

- **模板仓（本仓）：** `task4/starter_makefile` 配方提示块重写（删除"遮住镜像文件/覆盖入口"提示），`shell:` 目标同步数据挂载；`tasks/task4.md` 的"容器路径"章节重写；`CONTEXT.md` 新增"数据挂载"术语。
- **PR #10 的文档补丁被取代：** 其"遮蔽/覆盖入口"叙事简化删除，文档从解释陷阱降级为直述用法；README 的构建等待提示不受影响（构建行为未变）。
- **验证：** 本改动不新增自动化测试；人工按更新文档端到端运行 grader 的 task4 三项检查（`make_all_creates_zip` / `zip_contains_all_files` / `make_clean_works`）验证（Spec #11）。