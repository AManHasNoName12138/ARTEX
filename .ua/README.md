# ARTEX 学习图谱

使用 [Understand-Anything](https://github.com/Egonex-AI/Understand-Anything) 的 `understand --language zh` 流程生成：官方文件扫描、Tree-sitter 结构与 import 提取、语义分批分析、图谱合并、架构分层、中文学习导览及结构校验。

本次源码基线为 [`ee413324c868ec09d5ccbae31d04da7a6e24076d`](https://github.com/AManHasNoName12138/ARTEX/commit/ee413324c868ec09d5ccbae31d04da7a6e24076d)。生成工具使用上游 [`4e6d3299017a7432da15957874992837a315c5b4`](https://github.com/Egonex-AI/Understand-Anything/commit/4e6d3299017a7432da15957874992837a315c5b4)，插件版本 `2.9.8`。图谱元数据中的 commit 指向被分析的源码，而非随后保存图谱的提交。

图谱覆盖 **599 个文件、4,627 个节点、25,026 条关系、10 个架构层和 15 步中文导览**。27 个分析批次的文件与显著符号检查、官方 schema 校验、全部 15,819 条内部 import 核对及官方 Dashboard 数据读取检查均通过。19 个未发现明确关系的文件节点保留在对应架构层中，详见 [分析报告](analysis-report.json)。

## 打开交互图谱

在 ARTEX 仓库根目录运行，需 Node.js 18 或更新版本：

```bash
git switch study-notes
npx --yes https://github.com/Egonex-AI/Understand-Anything/releases/latest/download/understand-anything-viewer.tgz .
```

打开终端打印的完整地址（保留 `?token=…`）。可以按架构层筛选、搜索文件与符号、查看中文摘要和源码位置，并通过 Guided Tour 按顺序学习。

也可以使用生成时的同一份工具源码启动 Dashboard。以下命令从 ARTEX 仓库根目录执行，要求 Node.js 22+ 和 pnpm 10+：

```bash
ARTEX_ROOT="$PWD"
git clone https://github.com/Egonex-AI/Understand-Anything.git ../Understand-Anything
cd ../Understand-Anything
git checkout 4e6d3299017a7432da15957874992837a315c5b4
pnpm install --frozen-lockfile
pnpm --filter @understand-anything/core build
GRAPH_DIR="$ARTEX_ROOT" pnpm dev:dashboard
```

## 文件说明

| 文件 | 用途 |
| --- | --- |
| `knowledge-graph.json` | 文件、显著函数与类型、依赖边、架构层和中文学习导览 |
| `config.json` | 中文输出偏好及图谱更新配置 |
| `meta.json` | 分析时间、源码提交和文件数 |
| `fingerprints.json` | 官方结构指纹，供后续增量分析使用 |
| `analysis-report.json` | 生成来源、覆盖统计、校验结果与已知限制 |
| `.understandignore` | 保留默认排除规则及测试文件，排除 `.ua/` 自身产物 |

`intermediate/`、`tmp/`、`.trash-*/` 和 `diff-overlay.json` 是本地中间结果，由 Git 忽略。

## 分析范围与更新

覆盖默认规则保留的 599 个文件，包括 Go 后端、React/Next.js 前端、测试、SQL、配置、部署脚本与文档。静态 import 关系由官方解析器生成；Go 的包导入会展开为包内文件关系。函数调用边仅记录能从源码确认的关系，动态调用和外部依赖不构成完整运行时调用图。

已安装 Understand-Anything 技能时，在 ARTEX 根目录执行 `$understand --language zh`（Codex），或 `/understand --language zh`（Claude Code）。首次在新克隆中运行若缺少本地扫描缓存，工具会重新扫描；图谱与指纹已保存在仓库中。需要完整重建时追加 `--full`。
