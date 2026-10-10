# Spec Kit UI Context Extension

把图片、HTML 和用户说明，结合现有代码库的真实约束，整理成可追溯的
`ui-context.md`，供 Spec Kit 后续工作流显式读取。

第一版只有一个命令：`/speckit.ui.context`。它负责建立前端设计上下文，不生成
页面、不修改应用源码，也不替代需求定义、技术规划或实施后的视觉验收。

## 扩展结构

```text
spec-kit-extension-ui-context/
├── extension.yml
├── commands/context.md
├── templates/ui-context-template.md
├── README.md
└── LICENSE
```

仓库/包的展示名称可以叫 `speckit-ui`，但 Manifest 的 `extension.id` 是 `ui`，
这样注册的命令才是 `speckit.ui.context`。安装目录为 `.specify/extensions/ui/`。

| 文件 | 职责 |
|------|------|
| `extension.yml` | 注册扩展、命令、模板和兼容要求；默认不贡献自动 hooks |
| `commands/context.md` | 前后 hooks、工具能力发现、七阶段分析、生成和质量校验、完成报告 |
| `templates/ui-context-template.md` | contract version `1.0` 的九节输出结构，承载证据、能力与校验结果 |
| `README.md` | 安装、使用、hook 配置和下游消费说明 |

命令正文组织为：`User Input → Scope Guard → Pre-Execution Checks → Outline → Mandatory
Post-Execution Hooks → Completion Report → Context Quality Rules → Key rules
→ Done When`。七步 Outline 保留输入建立、参考分析、仓库发现、证据对齐、
歧义处理、产物生成和质量校验；实际报告在后置 hooks 检查之后发出。
Scope Guard 在 hooks 前限定职责；能力发现放在第一阶段，按实际输入和目标
选择所需操作。分析中的参考清单、仓库发现与对齐映射保留在内存，不增加产物。
Outline 只组织操作与输入输出；Context Quality Rules 集中定义证据门槛和就绪
判定；Key rules 集中定义写入、访问与信任边界，避免三处重复同一组约束。

## 安装

在**目标项目**中安装，而不是把目标项目初始化到这个扩展源码目录：

```bash
cd /absolute/path/to/your-project
specify extension add /absolute/path/to/spec-kit-extension-ui-context --dev
specify extension list
```

先确保目标项目已通过 `specify init` 初始化并配置所用 agent。
当前 CLI 的 `--dev` 是开关，扩展目录是位置参数。
Manifest 要求 `specify-cli >=1.0.13`；这是本扩展采用的兼容下限，不声称是
Spec Kit 首次支持扩展的版本。已验证版本与具体安装结果见
[验证记录](docs/validation.md)。

不同 agent 的调用形式不同：

| 集成形式 | 调用 |
|----------|------|
| 常见 slash command 集成 | `/speckit.ui.context` |
| 使用连字符的 slash 集成 | `/speckit-ui-context` |
| Codex skills 模式 | `$speckit-ui-context` |

验证所用版本中，Claude 和 Copilot 也默认生成 skills；它们的调用方式应以
各自集成和生成技能说明为准，不能假设一定安装了 slash command 文件。

以安装后实际生成的命令/技能为准。下方示例使用 canonical slash 写法；
Codex skills 模式下将命令名替换为 `$speckit-ui-context`，原生命令同理。

卸载扩展：

```bash
specify extension remove ui
```

生成的 `ui-context.md` 是项目产物，不属于扩展安装目录。

## 使用

命令接受自然语言，不要求固定参数格式：

```text
/speckit.ui.context
参考 design/dashboard.png 和 design/dashboard.html，为 apps/web 的工作台生成 UI 上下文。
保留现有设计系统。HTML 只做静态分析。暂存到 .specify/ui-context/dashboard/ui-context.md。
```

```text
/speckit.ui.context
分析 https://example.com/reference 的订单列表页面，结合 apps/admin 的组件和 tokens。
记录实际检查的视口；无法安全渲染时说明缺口，不执行未知脚本。
```

```text
/speckit.ui.context
为已有 feature specs/012-dashboard 刷新 UI 上下文，参考 design/dashboard-v2.png。
保留 ui-context.md 中已记录的用户决策；指出新参考与旧决策的冲突。
```

图片必须实际查看像素，HTML 静态检查与浏览器渲染必须分别记载。
仅 OCR 或读取 HTML 源码，不代表已完成视觉检查。截图中的尺寸和颜色可以是
估计，不能直接变成 CSS、项目 tokens 或已确认的交互规则。

未知 HTML 默认静态分析；执行参考脚本需要明确授权和隔离环境。
普通浏览器并不自动构成隔离。渲染能力不可用时记录限制，继续完成可支持的分析。

## 输出位置与刷新

| 场景 | 默认输出 |
|------|----------|
| 已存在、已确认且与目标匹配的 feature | `<实际 feature 目录>/ui-context.md` |
| 没有明确的现有 feature | `.specify/ui-context/ui-context.md` |
| 暂存文件属于另一目标 | `.specify/ui-context/<唯一目标名>/ui-context.md` |

不按“最新编号”猜 feature，不创建分支或 feature，不运行会改变状态的初始化
脚本。支持明确指定的项目内路径，并检查真实路径及符号链接边界。

刷新会读取旧内容，保留用户注释、决策、证据编号和历史来源，对变化的结论
标注更新或失效。当 Specify 已创建 feature 后，可再次运行命令明确关联该
feature；新产物记录 `Derived from`，保留暂存文件和原始引用版本。
所有源码/本地设计引用相对于项目根目录，因此关联后不需要改变引用基准。
参考版本、结论、模板契约和决策均无实质变化时，文件保持逐字节不变，复查时间
只出现在完成报告。先在内存生成并校验草稿，再发布；校验或写入失败保留原文件。

匹配 feature 的 `spec.md`、`plan.md` 存在时，按需读取其意图、决策及状态。
源码说明当前实现，参考说明设计证据，feature 文档记录已有需求与规划；不能
自动推定文档已获批准，也不能以当前实现悄悄覆盖既有决策。差异进入冲突表。
整个命令最多提出三个澄清问题，优先目标范围、核心意图和重要冲突；回答更新
相关字段并保留来源，未回答的问题继续可见。实施请求列入 Next Actions。

模板通过目标项目官方只读解析脚本加载，尊重 override、preset 与扩展提供者的
组合和优先级。命令 frontmatter 的 `scripts.sh / ps / py` 分别绑定三种运行时；
安装时将正文 `{SCRIPT}` 替换为目标项目所选脚本调用。Setup 从项目根目录执行
一次，解析 `TEMPLATE_NAME` 和 `TEMPLATE_CONTENT`，后者作为分析和生成使用的
活动模板。解析器不要求 feature 已存在，也不创建 feature。

脚本不可运行、返回失败或 JSON 字段不完整时停止，不再手工读取某个模板兜底。
JSON 没有返回 feature/output 路径，不能假装这些变量也由该脚本确定。
目标匹配和输出路径仍由 Establish context 的后续步骤负责。

这里复用 `.specify/scripts/...` 中的核心脚本，不新增扩展脚本。
command frontmatter 的 `scripts` 声明运行时绑定；Manifest 的 `provides.scripts`
用于声明扩展提供的脚本，两者职责不同。已经规范化的 `.specify/scripts/...`
路径可避免扩展安装器将其改写为 `.specify/extensions/ui/scripts/...`。

## 九节上下文契约

1. Reference Overview：来源、实际检查范围、页面目的与用户意图。
2. UI Structure：区域 ID、层级、布局关系与证据。
3. Visual Characteristics：视觉特征及观察/测量/声明/推断分类。
4. Behavior and UI States：有证据的行为，以及未确认的产品意图。
5. Codebase Alignment：真实技术栈、组件、样式、tokens 和资源映射。
6. Conflicts and Decisions Needed：参考与仓库约束的冲突及决策来源。
7. Assumptions and Unknowns：可逆假设、问题、影响阶段和状态。
8. Downstream Guidance：Specify / Plan / Tasks 的消费边界。
9. Evidence and Coverage：证据登记、检查范围、局限和就绪门槛。

主要编号：`R-*` 参考、`K-*` 源码、`UI-*` 区域、`C-*` 冲突、`A-*` 假设、
`Q-*` 问题。重要事实必须关联可定位的来源。源码需记录版本及未提交修改；
URL 需记录检查时间和实际访问状态；渲染需记录视口与脚本模式。

组件映射 `Verified` 表示检查了相关 API、样式或使用方式，证明其适配价值；
仍需 Plan 决策和实施后渲染验证。`Candidate` 表示证据不充分。
未发现组件只表述为“在已检查范围内未发现”。

## 如何让原生命令消费

安装扩展和模板**不会自动注入**生成的上下文。第一版默认不贡献原生命令 hooks，
也不修改官方命令；上下文命令自身支持前后 hook 槽。调用原生命令时明确提供
路径及消费边界：

```text
/speckit.specify
为工作台定义需求。先读取 .specify/ui-context/dashboard/ui-context.md。
使用其中的页面目的、用户可见行为和功能问题；视觉观察不自动成为产品需求。
保持 WHAT/WHY，不把组件路径或 CSS 写成需求。先处理影响核心行为的未决问题。
```

Specify 创建 feature 后继续：

```text
/speckit.plan
先读取 .specify/ui-context/dashboard/ui-context.md，并结合当前 feature 的 spec.md。
核验仓库约束和组件映射是否仍然有效，解决 C-* / Q-* 中影响规划的冲突。
不得把未证实的演示行为或静态图片推断当作已确认业务规则。
```

Tasks 和实施只消费已在 spec/plan 中接受的决策：

```text
/speckit.tasks
以当前 feature 的 spec.md 和 plan.md 为依据。
补充读取 .specify/ui-context/dashboard/ui-context.md，保留 UI-* 与证据引用。
仅将已接受的 UI 工作和验证要求转成任务；开放冲突或假设不得变成既定需求。
```

如果 Plan 中已有充分的上下文引用，Tasks 可直接从 spec/plan 的接受决策继续。
这里的显式路径示例是 agent 指令，不是 CLI 额外支持的参数。

## 就绪判定

每次输出检查 G1 目标、G2 参考、G3 仓库、G4 证据完整性、G5 决策。

| 状态 | 可检查的条件 |
|------|--------------|
| Blocked | 目标/安全输出无法确定、唯一有效参考不可检查、必要仓库范围不可访问、证据契约失败，或核心意图存在阻塞决策 |
| Conditional | 无阻塞条件，但存在相关的部分参考、缺失资源、渲染/响应式/状态证据缺口、候选映射或非阻塞问题 |
| Ready | 在声明的检查范围内，所有适用门槛通过且没有已知的重要缺口 |

同时分别记录 Specify、Plan、Tasks / Implement 的前提。按钮色彩冲突可能
不影响需求定义，却会影响 Plan 最终设计决策。即使上下文 Ready，实施仍需
已批准的 spec/plan；上下文状态不等于成品视觉或生产验收。
已清楚记录的冲突通常是 Gap；仅因颜色决策未定，不应把可供规划使用的上下文
整体判为 Blocked。`Status: Validated` 只表示输出契约通过，和证据就绪状态分开。

完成报告另列 Created / Updated / Associated / Unchanged / Failed。草稿最多
校验修正三轮；真实缺失的证据作为缺口保存，不能通过编造“修复”。没有安全输出
路径或模板时不写文件。失败不触发成功生成的后置 hooks。

门槛由命令的 Context Quality Rules 定义，执行命令的 agent 检查，模板保存
检查结果。当前没有宣称存在能够
自动证明视觉语义或证据真实性的程序验证器。

## 工具能力发现

命令在参考分析与代码导航之前，通过当前环境提供的发现机制检查可调用工具和
适用技能，并核验访问范围。只配置了 MCP 名称不等于当前会话具备可用能力。
不为了探测而安装工具、登录、执行未知脚本或修改项目配置。

| 所需能力 | 可能的提供者 | 缺失时处理 |
|----------|--------------|------------|
| 图像像素检查 | 原生视觉、图片查看工具 | OCR 只能提供部分文本证据；唯一图片无法实际检查时 Blocked |
| HTML/CSS 源码检查 | 本地文件读取、允许的 URL 获取 | 缺失源码或资源时记录覆盖缺口 |
| 渲染截图、DOM、computed styles | 可用且有权限的浏览器工具，例如 Chrome DevTools MCP | 可退回静态分析，但不声称已观察渲染或测量布局 |
| 仓库结构导航 | 可用代码图谱，例如 codebase-memory-mcp | 退回文件搜索和当前源码读取，保持证据标准 |
| 模板解析、产物写入 | 已有只读解析器、文件工具 | 无可用模板或安全输出能力时报告无法生成 |

能力表写入产物第 9 节：实际提供者、可用/受限/未验证状态、访问范围、实际采用
的方法和证据限制。工具调用失败后更新表格，不继续声称全覆盖。

## Hooks 生命周期

区分两个方向：Manifest 的 `hooks` 声明将某个命令挂到事件上；命令正文读取
项目 `.specify/extensions.yml` 并执行该事件的配置。原生 `before_plan` 仍由
Plan 处理，UI Context 不代替它执行。

UI Context 自己定义 `before_ui_context` / `after_ui_context` 两个扩展事件，
不是官方预置核心事件。官方注册实现接受事件名称，实际触发由本命令负责；
仅在 Manifest 中写下一个事件并不会使任何命令自动派发它。

可以在项目 `.specify/extensions.yml` 中为这些槽配置已安装的命令。例如，
下方 `review` 只是示例扩展名，需要替换为真实已安装命令，不能直接照抄启用：

```yaml
hooks:
  before_ui_context:
    - extension: review
      command: speckit.review.preflight
      enabled: true
      optional: true
      prompt: "先检查设计参考的可访问性？"
      description: "检查输入范围，不改变 feature 状态"
  after_ui_context:
    - extension: review
      command: speckit.review.context
      enabled: true
      optional: false
      description: "检查已生成上下文的证据完整性"
```

处理规则对齐当前官方 command 模板：跳过 `enabled: false` 和非空 condition；
可选 hooks 展示后由用户选择；强制 hooks 发出 `EXECUTE_COMMAND`，并实际调用、
等待结束。单纯输出标记不算执行。无配置时静默跳过；解析失败明确报告未检查
hooks 的限制。无法调用或失败的强制 hook 不会被报告为工作流成功。

当前官方 command 模板直接按 YAML 顺序处理，不按 priority 排序，也不自行
求值 condition；Python `HookExecutor` 可以排序和求值，两者行为应分别描述。
本命令采用模板的派发行为。后置 hook 若改变上下文，会重新校验再报告；不会
因重新校验再次触发后置 hook。上下文就绪状态与 hooks 执行成功状态分别报告。

hook 配置不能扩张输入范围或绕过命令的非实施边界，也不自动传递产物给
Specify/Plan。默认 Manifest 不注册任何具体 hook，避免引入未经选择的副作用。

## 演进边界

后续可评估 `before_specify` / `before_plan` hooks 或独立 preset 的组合注入，
但 hook 的存在不构成上下文传递证明；需要另外设计命令、feature 关联及
不同 agent 的消费测试。第一版不引入第二个视觉验证命令、多文件研究产物、
CSS 生成或页面修复。视觉验收留在实施后执行。

Manifest 已填写本项目 GitHub 仓库地址与维护者 `philo-x`。当前尚未发布到
Spec Kit 扩展目录；提交到目录前仍需核对所声明兼容版本。

## 官方依据

设计核对日期：2026-10-10。在线 `main` 会继续变化；本地实现与在线文档版本
分别记录在验证报告，不把开发版测试当作所有版本兼容性证明。

- [Extensions 使用文档](https://github.github.com/spec-kit/reference/extensions.html)：安装与集成形式。
- [Extension API Reference](https://github.com/github/spec-kit/blob/main/extensions/EXTENSION-API-REFERENCE.md)：Manifest、命名空间及模板声明。
- [Specify](https://github.com/github/spec-kit/blob/main/templates/commands/specify.md)、[Plan](https://github.com/github/spec-kit/blob/main/templates/commands/plan.md)、[Tasks](https://github.com/github/spec-kit/blob/main/templates/commands/tasks.md)、[Analyze](https://github.com/github/spec-kit/blob/main/templates/commands/analyze.md)：原生命令边界、上下文加载及证据约束。
- [Artifacts](https://github.github.com/spec-kit/reference/artifacts.html)：安装后模板与命令来源的只读检查。
