# 通用 Agent 体系与知识沉淀 Skill（v1.0）


## 这是什么

一套可直接复制到任何 Claude Code 项目的**多 agent 工程管线 + 组织知识沉淀机制**：

- **六件套管线**：侦查（并行 N 实例）→ 设计（可选）→ 对抗审查 → 实施 → 交叉验证 → 归档提炼
- **三大跨切面机制**（体系最强成分，整体可移植）：
  1. **输出契约**：落盘即遗忘 + 极简三行 final report（防子 agent 大输出回灌主对话上下文）
  2. **分派对账**：dispatch/done/expect/check 四事件协议（防 agent 静默死亡靠肉眼发现）
  3. **知识沉淀闭环**：四模板 + 双时机提炼 + 机械锚 + drafts 人工采纳门禁（防经验流失，更防错误知识污染）
- **红线域守卫模板**（domain-guard）：项目自声明的不可妥协域（资金/安全/合规/数据删除等）强制重档审查

## 解决什么问题

| 问题 | 体系的回答 |
|---|---|
| 长战役中主对话上下文被子 agent 输出灌爆 | 落盘即遗忘：详情 Write 到战役目录，final report 只允许三行 |
| agent 静默死亡 / 完成与否靠自述 | expect 产物存在性对账（文件存在=完成，比自觉回执强）+ 独立验证不采信自述 |
| 同一问题在不同 session 重复排查 | 知识沉淀闭环：episodic 记录提炼为 procedural skill，症状触发词路由两步定位 |
| 沉淀的错误知识污染所有未来 session | drafts 草稿区 + 人工采纳门禁：未采纳零污染，LLM 只做机械抽取 |
| 关键守卫规则「依赖自觉」必然失效 | 机械锚：收到 X 必做动作 Y（Grep/对照/落盘），触发器可机械执行 |
| 「成功」掩盖静默失败 | 验证区分正常成功/空转成功/降级成功三态；结论必须挂可复核证据 |

## 三步接入任何项目

**第一步：复制 agent 定义**
```
agents\*.md  →  <目标项目>\.claude\agents\
```
7 个文件直接复制。`domain-guard.md` 是模板——必须先填充正文全部 5 类占位符（红线域名/失败后果/检查项/影响单位/确定性防线）后才可用；复制进 `.claude\agents\` 即被注册，未填充时它处于 DEGRADED 保护态（工作流程第 0 步机械自检：残留 `{{` 字面量即降级交人工，不放水）。其余 6 个即复制即用（领域注入点运行时从项目 CLAUDE.md「项目上下文声明」节读取，未声明的键 agent 自行 Grep 代码推断）。若目标项目或用户全局 `~/.claude/agents/` 已有同名 agent（如 adversarial-reviewer），按实战判例精选判例 10「补丁节合并」接入——把本包通用纪律节追加为既有版补丁，不整文件覆盖。

**第二步：粘贴 CLAUDE.md 集成段**
打开 `skills\knowledge-capture\references\CLAUDE.md集成段.md`，把其中四节（Agent 调用规则 / 知识路由 / 知识沉淀提炼规则 / 项目上下文声明）粘贴到目标项目 CLAUDE.md 尾部，替换 `{{占位符}}`。

**第三步：搭知识库骨架**
```
skills\knowledge-capture\    →  <目标项目>\.claude\skills\knowledge-capture\
skills\methodology\          →  <目标项目>\.claude\skills\methodology\
```
**知识库根 = `.claude\skills\` 整体**（不设库中库）：按 `skills\knowledge-capture\references\索引与路由模板.md` 在 `.claude\skills\` 根下创建总索引 `README.md` 和草稿区 `drafts\`（drafts README 按该模板 §五 最小规则集复制即启动；库边界规则见该模板 §〇）。首批领域 SOP 按 Phase A 机械初筛 → Phase B 人工终审从项目自己的 CHANGELOG/工作日志挖掘（操作卡见该模板 §七；冷启动可先以 methodology 三 skill + 判例精选开张，Phase A 延后）。

**接入手册**：编排器语义、对账协议、档位分配 → 读 `skills\knowledge-capture\references\六件套管线手册.md`（随第三步复制到目标项目内，路径自洽）。

## 目录导航

| 路径 | 内容 | 用途 |
|---|---|---|
| `agents\eng-recon.md` | 侦查（技术可行性/数据事实/外部调研三子模式） | 战役前置事实基线，可并行派发 N 实例 |
| `agents\eng-arch.md` | 方案架构（五步流+侵入点清单） | 复杂设计战役 |
| `agents\eng-implementer.md` | 编码执行（模仿铁律+窄职责） | 按单任务规格落地 diff |
| `agents\eng-validator.md` | 交叉验证（三分法+聚合复核+数据验证档） | 编码后质量门禁 |
| `agents\eng-documenter.md` | 归档（锚点式写入+提炼机械路由） | 收尾文档同步 |
| `agents\adversarial-reviewer.md` | 对抗审查（先立后破+五类缺陷模式核查） | 管线③审查环节，即复制即用 |
| `agents\domain-guard.md` | 红线域守卫**模板**（占位符化） | 项目自声明红线域，须填充后可用 |
| `skills\knowledge-capture\` | 知识沉淀 meta-skill（机制总纲+四模板+索引模板+管线手册+判例精选+CLAUDE.md 集成段） | 知识库搭建与运营的唯一机制权威 |
| `skills\methodology\review-patterns\` | 审查缺陷模式库（五类反模式+审查五法） | 审查/预判/断言设计 |
| `skills\methodology\design-lessons\` | 设计教训库（八教训+原子写红线） | 方案选型与非目标论证 |
| `skills\methodology\chain-audit\` | 链路测绘 SOP（七步+登记簿+预设修正） | 跨文件链路理解与复用面评估 |
| `skills\knowledge-capture\references\六件套管线手册.md` | 编排器语义/互查纪律/对账机制/战役预算 | 编排者（主对话）必读 |
| `skills\knowledge-capture\references\实战判例精选.md` | 12 条跨领域判例（含实证出处） | 理解每条规则背后的实证 |

## 核心设计决策（为什么长这样）

1. **侦查合并为单文件三子模式**：早期版本三个侦查 agent（技术/数据/调研）核心纪律高度重叠（查证不猜测/精确数字/引用必验证）。合并后编排器仍可并行派 2-3 个 eng-recon 实例各带不同子模式任务书——「并行的是实例，不是定义」。依据：调用痕迹治理判例（agent 定义是负载不是资产）。
2. **对抗审查随包分发**：管线表③直接引用 `adversarial-reviewer`——若不随包分发，三步接入完成后首次派发即断链（agent not found）。故作为第 7 个 agent 文件随包（默认纪律由定义承载；重点域与红线域标注仍由任务书给定）；项目已有同名 agent 时优先项目版（同名双轨）。
3. **单一权威源**：行数上限、阈值等数值只在知识库 README 维护规则处定义一次，CLAUDE.md 路由节单向引用不自报数字——曾有体系实战中出现两处数字不一致（18 行 vs 10 行）的契约冲突，本设计根治。
4. **知识提炼机制不内嵌在 documenter 定义里**：四模板/drafts/呈报协议属体系级机制，统一由 knowledge-capture skill 承载；documenter 只做机械路由调用。理由：机制放 agent 定义会导致双份定义漂移。
5. **数据质量验证并入 validator 配置档**：早期版本中独立数据质量 agent 三周出场 ≤2 次（常规验证已由 validator 承担），故降为 validator 的「数据验证档」而非独立角色。

## 使用原则（克制清单）

- 不引入外部框架（不装 LangChain/LangMem 等依赖，只借概念）
- 不建向量知识库（百条级 markdown + grep + 索引三方交叉验证优于 RAG）
- 不做全自动采纳（人工门禁是安全机制不是性能瓶颈）
- 新增 agent 角色必须有调用痕迹预期，周期复盘按实际痕迹清退（死定义清理判例）
