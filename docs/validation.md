# 验证记录

日期：2026-10-10，Asia/Shanghai。

## 核对依据与版本

- 在线核对官方 `main` 的 Extension API Reference、Extensions、Artifacts、
  Specify、Plan、Tasks 和 Analyze 文档/模板。`main` 是变化的分支，在线资料
  未固定到 commit；不声称在线文档与本机版本完全一致。
- 使用 `codebase-memory` 技能检查本机 Spec Kit 的 Manifest 与模板解析入口，
  查询证据路径覆盖并读取实际源码。该源码 checkout 的 HEAD 为
  `89ce4104baf72c8f802afa7375d89e22d475f855`，版本 `1.0.13.dev0`。
  图谱 generation 为 `2026-09-26T04:11:56Z`，覆盖查询没有报告这两个证据
  路径的问题；最终判断仍以当前文件为准，不把覆盖信号当作完整性证明。
- 真正执行安装、模板解析和卸载验证的是本机已安装 CLI `1.1.3.dev0`。
  没有在 `1.0.13` 正式版上执行安装，也没有证明所有未来版本兼容。

关键实现路径：

- `src/specify_cli/extensions/__init__.py`：Manifest 校验、命令命名空间及安装。
- `scripts/python/common.py` 和 `scripts/python/resolve_template.py`：模板选择与内容解析。

官方文档把 `extension.repository` 列为必填；实际校验器允许本地 Manifest
不提供该字段。本扩展未编造仓库 URL，可本地安装，但发布前需补充真实地址。

## 已执行的安装验证

在 `/private/tmp` 创建三个独立项目，初始化使用 `--script py`，然后执行
`specify extension add <本扩展绝对路径> --dev`。

| 集成 | 生成路径 | 安装前后原有 Markdown 文件校验 | 扩展模板解析 | 项目 override |
|------|----------|-------------------------------|--------------|---------------|
| Codex，`--skills` | `.agents/skills/speckit-ui-context/SKILL.md` | 16 个文件哈希一致 | 通过 | 通过 |
| Claude，默认 | `.claude/skills/speckit-ui-context/SKILL.md` | 16 个文件哈希一致 | 通过 | 通过 |
| Copilot，默认 | `.github/skills/speckit-ui-context/SKILL.md` | 16 个文件哈希一致 | 通过 | 通过 |

另已确认：

- 官方 `ExtensionManifest` 接受 Manifest，无校验警告；所有声明文件存在。
- 安装后的模板文件与扩展源模板逐字节一致。
- 初稿的三个生成技能都保留 `$ARGUMENTS` 和当时的 Readiness Gates 内容。
  后续结构调整已将就绪判定细则放入模板，更新检查见下节。
- 通过目标项目的 `resolve_template.py ui-context-template --json`，解析出的
  `TEMPLATE_CONTENT` 与扩展模板一致；新增项目 override 后返回 override。
- `specify artifact info speckit.ui.context --kind command --json` 识别 `ui`
  扩展与 Codex 技能路径；模板查询识别项目 override 覆盖扩展模板。
- Codex 临时项目卸载 `ui` 成功，扩展目录和生成技能被删除，原有 16 个
  Markdown 文件哈希一致。

这些检查验证安装与模板消费基础设施，不证明 agent 能可靠执行每条自然语言
规则；没有把它们表述为真实页面分析或视觉验收测试。

## 命令结构调整后的验证

这是上一轮结构调整的历史记录，当前生命周期结构见下一节。
同日按用户反馈重新组织命令。对照本地官方 Specify、Plan、Tasks、Analyze
模板，保留七步逻辑职责，将命令正文调整为：
`User Input → Outline → Key rules → Done When`。
这不是声称官方所有命令使用统一标题，而是采用其输入、执行流程、专项规则
和完成条件的职责分离方式。

已检查：

- 顶层正文仅有上述四个章节；七步按顺序完整存在。
- 第一步建立共用上下文变量、确定输出位置、加载模板和已有产物；模板在参考
  分析之前加载，生成步骤不再重新发现执行环境。
- 就绪判定细则与九节契约位于模板；命令的第七步调用这些检查并报告。
- 使用 CLI `1.1.3.dev0` 在新的 Codex skills 临时项目重新安装成功；生成技能
  正文与更新后的命令一致（忽略文件末尾空白），安装模板与更新模板逐字节一致。
- 修改文件未发现尾随空白。

本轮临时目录为 `/private/tmp/speckit-ui-structure-08mk1tbr`。未重复没有受
此次正文调整影响的其他集成和卸载验证，也没有执行真实参考的语义验收。

## Hooks、职责和能力发现调整后的验证

再次核对在线官方 Plan、Extension API Reference、Extensions 文档，以及本地
`HookExecutor.register_hooks` / `get_hooks_for_event` 当前源码。
本轮继续使用 `codebase-memory` 技能查询入口和证据路径覆盖，以实际源码为准。
在线文档仍按 `main` 检查，未固定在线 commit。

该轮结构为 `User Input → Pre-Execution Checks → Outline → Mandatory
Post-Execution Hooks → Completion Report → Context Quality Rules → Key rules
→ Done When`。七阶段主流程保留，完成报告移到后置 hooks 之后。

职责已明确：命令发现能力、执行分析并判断质量；模板保存字段与结果；Manifest
负责注册；README 负责调用与配置说明。上一轮把详细就绪政策放入模板的设计
已撤回，政策现在由命令的 Context Quality Rules 维护。

本轮实际验证（CLI `1.1.3.dev0`）：

- Codex skills 临时项目安装成功，已有 16 个 Markdown 文件哈希保持一致。
- 生成技能正文与更新命令一致（忽略末尾空白）；模板解析返回更新模板全文。
- 本扩展默认不贡献具体 hooks；安装后项目没有新增自动执行条目。
- 使用独立测试 Manifest，通过官方 `HookExecutor` 注册
  `before_ui_context` / `after_ui_context` 成功。配置保留前置 optional=true、
  后置 optional=false 和空 condition；查询能返回对应命令。
- 测试事件不会产生 `before_plan` / `before_specify` 注册；移除测试扩展后，
  自定义事件条目也被移除。
- 命令中工具能力发现位于分析之前；后置 hooks 位于完成报告之前；模板仍保留
  九个必需章节，并新增 Inspection Capabilities 字段表。

`before_ui_context` / `after_ui_context` 是本扩展定义的事件，不是官方预置事件。
验证证明官方注册实现接受这些名称；具体派发由命令正文承担。当前核心 command
模板按 YAML 顺序派发并跳过非空 condition；Python HookExecutor 有排序和求值
能力。本扩展遵循模板派发行为，没有宣称两者完全等价。

本轮临时目录：`/private/tmp/speckit-ui-lifecycle-xeyozsqt`。可查运行日志及
`result.json`。最终补齐可选后置 hook 展示块后，又在该临时项目刷新安装，
复核了最终生成技能与命令正文的一致性。

该轮尚未执行 agent 级 hook 调用或真实设计参考分析。以下验收仍需真实运行：强制
hook 实际调用并等待、可选 hook 用户选择、无法调用的 hook 失败报告、非法 YAML
限制报告、条件跳过、post-hook 修改产物后的再次校验，以及工具失效后的降级。
这类语义要求不以静态结构检查或注册 API 验证代替。

临时验证目录：`/private/tmp/speckit-ui-validation-18kbded0`，其中有运行日志和
`result.json`。临时文件可能被系统清理，长期结果以本记录为准。

## 全量官方命令审查及最终调整

按用户要求完整读取 `templates/commands` 的全部十个文件，共 2,454 行。
这次固定到官方 main 的 commit
[`0443760542e7e5269724744a2ac55c4bd386d095`](https://github.com/github/spec-kit/tree/0443760542e7e5269724744a2ac55c4bd386d095/templates/commands)，
避免混合不同时间的命令版本。此快照用于设计审查；安装测试仍使用本机
CLI `1.1.3.dev0`，两种依据分别记录。

### 公共设计

- 每个命令都有 description、User Input 和前后 hook 处理；具体正文随职责
  变化，并不存在统一的 `Outline → Output → Rules → Done When` 标题协议。
- frontmatter 声明集成元数据；正文组织输入、准备、加载、执行、校验和报告。
  scripts、handoffs、tools 并非全部命令都必须具备；UI Context 没有需要运行的
  专属脚本，也不应为了形似而声明虚构脚本。但可以且应当复用核心模板解析
  脚本；该项在此轮遗漏，纠正见下文“脚本执行契约修正”。
- 必需输入与可选输入分开，先确认路径和已有产物，再做有界分析。内部模型
  可以很丰富，输出只保留本命令负责的产物。
- 原生命令在不同阶段各自负责 WHAT/WHY、HOW、任务与实施；已有文档、源码、
  用户说明具有不同角色，不能把观察结果直接提升为批准的需求。
- 质量规则不仅规定成功路径，还规定缺失输入、错误、停止、迭代上限和保存
  行为。后置 hooks 必须在完成报告前处理；输出标记不等于真正执行 hook。

### 每个命令的关键设计与可借鉴之处

| 命令 | 关键设计 | UI Context 的借鉴边界 |
|------|----------|-----------------------|
| constitution | Scope Guard 先筛职责；基于已有治理文本增量修订；版本与影响报告 | 在 hooks 前限制使命，保留决策；不修改 Constitution |
| specify | WHAT/WHY；feature 目录与分支分离；重要澄清最多三项；有界质量修正 | 采用澄清与修正预算；只读既有 feature 状态，不创建 feature |
| clarify | 内部覆盖分类；最多五个问题逐项处理；回答立即整合并消除矛盾 | 问题少而有影响，回答更新相关字段；不另造需求澄清流程 |
| plan | Setup / Load context / 分阶段工作；模板与治理门槛驱动产物 | 显式加载输入与模板；不生成 research、contracts 或技术计划 |
| tasks | 必需 spec/plan 与可选设计输入；用户故事、依赖和严格任务格式 | 声明消费前提；不把开放假设直接转成任务 |
| analyze | 严格只读；渐进加载、内部语义模型、有界且可定位的发现 | 内部参考/源码/对齐模型，证据支持；本命令仅写自己的上下文 |
| checklist | 检查需求质量；渐进取证；新条目保持未勾选，避免代替审查者验收 | 检查上下文质量，不声称实施或视觉验收已完成 |
| implement | 实际环境探测；既有 checklist 只读；依赖执行与状态跟踪 | 按实际输入发现可调用能力；不执行实施工作 |
| converge | 以意图文档界定范围；检查真实实现而非只看 diff/勾选；无发现不改文件 | 事实不等于批准意图；无实质变化保持字节不变 |
| taskstoissues | 废弃提示置前；显式工具要求；目标仓库约束；分页、去重与安全外部写入 | 能力与作用域必须可核验；不引入 GitHub 写入或专用工具依赖 |

### 已落实的调整

1. 增加 Scope Guard，将越界实施请求列为 Next Actions；前置 hook 仅获得
   范围内输入。
2. Setup 按需加载匹配 feature 的 spec/plan，记录其状态、决策与源码的差异。
   核对官方 `common.get_feature_paths` 源码：默认会持久化环境指定 feature，
   存在 `no_persist` 支持。命令要求先核验版本与只读模式，不能直接运行默认 helper。
3. 按输入和 app 发现工具能力，区分可用、受限和未验证；失败更新覆盖记录。
4. 参考模型、仓库模型和对齐映射留在内存，仍只输出一个九节上下文。
5. 全流程最多三个澄清问题；回答整合到受影响字段，保留未决问题和决策来源。
6. 创建、更新、关联、无变化、失败分别报告。无实质变化不重写；先校验草稿，
   再安全发布，失败保留旧产物。
7. 草稿最多修正三轮；缺失证据作为真实缺口，不能编造修复。
8. 契约 Status、证据 readiness、下游阶段前提、hook 结果分开。已记录的冲突
   一般为 Gap，避免仅因尚未选色便把有用上下文整体判为 Blocked。

### 本轮执行验证

在独立临时项目重新安装当前扩展，实际结果：

- CLI `1.1.3.dev0` 安装成功。
- Codex 生成技能正文与当前命令一致；只读模板解析返回当前模板全文。
- 项目已有 16 个 Markdown 文件哈希不变。
- 七阶段和九节契约完整，Scope Guard 在 hooks 前，能力发现先于参考分析，
  后置 hooks 先于完成报告；文件无尾随空白。

临时目录 `/private/tmp/speckit-ui-final-28z05czl` 保存安装日志及 `result.json`。
这些结果证明当前文件可安装、可解析以及改动范围，不能证明 agent 在真实输入
上正确执行每条自然语言规则。本轮未运行真实页面分析或 agent 级 hook 调用，
下方场景仍保留为语义验收清单。

## 脚本执行契约修正

用户指出，先前只把模板加载写成自然语言，遗漏官方 command 常用的
`scripts → {SCRIPT} → 执行脚本 → 解析 JSON → 使用变量` 契约。此项反馈成立；
“没有专属脚本”不能成为省略已有核心脚本绑定的理由。

重新核对官方 Constitution、Plan、Checklist、Tasks 及实际注册实现：

- Constitution 绑定 `resolve-template`，正文执行 `{SCRIPT}` 并消费
  `TEMPLATE_CONTENT`；Plan、Checklist、Tasks 使用各自准备脚本的 JSON 字段。
  并非所有脚本返回相同字段，不能从一个命令的契约推断另一个命令的变量。
- `CommandRegistrar._adjust_script_paths` 处理脚本路径，技能生成的
  `resolve_skill_placeholders` 根据项目 init options 选择运行时并替换
  `{SCRIPT}`；Python 调用还会补充解释器。
- 扩展的裸 `scripts/...` 路径会被定位到扩展目录；显式
  `.specify/scripts/...` 保持核心路径，因此这里复用核心解析器时必须明确路径。

当前命令 frontmatter 已添加 sh/ps/py 三种核心解析脚本绑定。Establish context
首先从项目根目录运行一次，校验 `TEMPLATE_NAME` 与 `TEMPLATE_CONTENT`，把返回
内容作为活动模板。脚本失败或 JSON 不符合契约则停止，撤回此前手工退回单一
模板文件的规则。解析器返回的不是 feature/output 路径，这些路径仍需后续
只读选择及目标匹配，不宣称来自脚本 JSON。

实际验证使用 CLI `1.1.3.dev0`，独立临时目录
`/private/tmp/speckit-ui-script-wrs2gdpp`：

| 检查 | 结果 |
|------|------|
| Codex 安装后 `{SCRIPT}` 已替换且核心路径未误改到扩展目录 | 通过 |
| 执行安装后正文中的 Python 调用，JSON 名称和内容正确 | 通过 |
| 无 feature 的项目解析成功，不创建 specs 或 feature.json | 通过 |
| 项目 override 返回覆盖内容 | 通过 |
| 模板缺失时非零退出、明确错误，不返回伪造内容 | 通过 |
| 安装前后原有 16 个 Markdown 文件 | 哈希不变 |
| Bash 运行时选择、替换与实际解析 | 通过 |
| PowerShell 运行时选择与路径替换 | 通过；未执行 PowerShell |

Bash/PowerShell 验证在该临时项目复制同一已安装 CLI 的核心脚本，并切换项目
script 选项来检查绑定。PowerShell 不以路径检查代替运行验证；真实页面分析与
agent 级 hook 验收的限制仍然成立。脚本绑定现在是真实执行契约，不只是静态
正文中出现一句 Run。

## 命令压缩与职责整理

按用户要求重点调整 Outline、Context Quality Rules 和 Key rules，对照官方
Plan 的简明执行步骤与 Key rules，以及 Analyze 的操作约束与证据分析分工。
命令从 367 行、2,893 个英文词压缩到 245 行、1,714 个英文词，词数减少约 41%。

- Outline 保留七阶段，集中描述操作、使用的变量和阶段结果，删除重复解释。
- Context Quality Rules 仅维护 G1–G5、Pass/Gap/Fail 与就绪状态判定。
- Key rules 仅维护操作路径、写入范围、信任边界、工具限制和消费边界。
- scripts / `{SCRIPT}` / JSON 契约及前后 hooks 生命周期保留；Done When
  合并为四项验收结果，不再逐项复述 Outline。

CLI `1.1.3.dev0` 在现有独立临时项目重新安装通过。替换 `{SCRIPT}` 后，生成
技能正文与压缩命令一致；实际执行模板解析调用成功；原有 16 个 Markdown
文件哈希保持不变。运行结果见临时项目的 `compact-command-result.json`。
本轮没有改变模板或 hook 派发规则，也没有重复未受影响的跨运行时检查。

## 后续真实输入验收场景

以下是已固化到命令的验收要求，**尚未通过实际参考输入执行**。使用扩展时可
按这些场景评估生成产物，不应把安装成功当作这些语义场景已通过。

| 场景 | 期望结果 |
|------|----------|
| 单张截图，没有 feature | 暂存；实际查看像素；尺寸/字体/响应式的不确定性可见；不创建 feature |
| 仅有 HTML 源码，渲染不可用 | 标注 declared 和 Partial；不声称已测量实际布局；相关缺口导致 Conditional |
| 唯一参考不可读取 | G2 Fail，Blocked；说明限制，不伪造视觉结果 |
| 必要目标源码不可访问 | G3 Fail，Blocked；不编造技术栈、组件或 tokens |
| 参考蓝色按钮，项目规定绿色 | C-ID 引用两侧证据；不默默选色；记录受影响的下游阶段 |
| 缺少次要图标 | 无其他阻塞时 Conditional，记录资源缺口 |
| 仓库没有前端 | 记录已检查范围与实际发现；不要求虚构前端组件才能完成分析 |
| 已有暂存属于另一页面 | 使用独立目标目录，旧文件不被覆盖 |
| feature 存在但目标不匹配/不明确 | 不猜最新 feature，先暂存或澄清 |
| 刷新含用户注释/决策的上下文 | 保留内容及 ID；变化证据可追溯；旧决策不会变成新批准 |
| 从暂存关联到 feature | 保留暂存、Derived from、原始版本和项目根相对引用 |
| HTML 含代理指令/恶意脚本 | 内容只作数据；未授权不执行；不传播指令到下游 guidance |
| 静态按钮看似可点击 | 不编造后端规则、导航、权限或已确认业务行为 |
| 页面演示弹出“成功” | 标为参考行为，产品意图保持 unknown 或 user-stated |

## 验证复现

仅在独立临时目录执行，避免改变真实项目：

```bash
specify init --here --integration codex --integration-options="--skills" --script py --ignore-agent-tools --non-interactive
specify extension add /absolute/path/to/spec-kit-extension-ui-context --dev
python .specify/scripts/python/resolve_template.py ui-context-template --json
specify artifact info speckit.ui.context --kind command --json
specify artifact info ui-context-template --kind template --json
specify extension remove ui --force
```

复现需要同版本 CLI 与 Python。若使用其他脚本模式、agent 或 preset，需重新
核对实际生成位置、模板解析及覆盖关系。
