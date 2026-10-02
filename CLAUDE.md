# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目性质（先读这个）

这不是可执行代码项目，而是一套**可移植的 Claude Code 配置资产集**（v1.1，全新开发定位）：7 个多 agent 工程管线定义 + 知识沉淀 skill 体系，设计为复制进任何目标项目的 `.claude/` 目录使用（三步接入 + 第 0 步冒烟自检 + 升级/卸载/用户操作卡见 [README.md](README.md)）。**本仓库同时是这套知识体系的运行态知识库**（实战沉淀持续转正中，见下文）。

- **没有 build / lint / test 命令**——产物是 markdown 资产本身
- 唯一代码：[六件套管线手册.md](skills/knowledge-capture/references/六件套管线手册.md) §6 内嵌的 `agent_reconcile.py`（v1.1 加固版对账脚本：绝对路径化 + mtime 回炉防护 +1s fail-closed 容差 + 同键 done 失效 + 坏行容错 + utf-8 输出；16 场景回归实测全过；供目标项目提取使用）
- git 仓库（main）；战役报告等工作产物落盘 `results/`（已 gitignore，不入库——仓库保持纯资产集）
- 版本标记 v1.1：七份 agent 头部单行 + 各 SKILL.md 头部引用块与尾部元信息「适用版本」+ references 各文件头部版本行

## 架构大图景（需跨文件理解的部分）

### 分发模型与知识库结构

```text
agents/*.md   → 目标项目 .claude/agents/   （即复制即用；domain-guard 须先填 5 类占位符）
skills/ 整体  → 目标项目 .claude/skills/   （复制即得完整知识库：预建总索引 + drafts 骨架 + 全部 skill）
```

知识库（skills/）现含 **7 个 skill**：knowledge-capture（机制总纲）+ methodology 三件（review-patterns / design-lessons / chain-audit）+ **三份实战转正 skill**（asset-maintenance-defects / multi-agent-campaign-sop / blind-retry-rework——三次战役沉淀、经用户人工采纳转正）。总索引=[skills/README.md](skills/README.md)（唯一权威全量表，维护规则 10 条）；drafts/=[skills/drafts/](skills/drafts/)（未采纳草稿区，零污染门禁）。库边界规则见[索引与路由模板.md](skills/knowledge-capture/references/索引与路由模板.md) §〇。

### 六件套管线（agents/ 七个定义间的关系）

①侦查（eng-recon ×N 并行，tech/data/research 三子模式）→ ②设计（eng-arch，复杂战役才走）→ ③对抗审查（adversarial-reviewer，先立后破）→ ④实施（eng-implementer，模仿铁律）→ ⑤交叉验证（eng-validator，MISSING/OVERSTEP/DEVIATION 三分法）→ ⑥归档（eng-documenter，锚点写入 + 提炼机械路由）。任何阶段发现触碰红线域 → domain-guard 强制插入（不因改动量小降档）；**测试执行由编排器（主对话）承担**（测试命令读集成段 TEST_COMMAND 键），不设独立测试 agent。

**v1.1 关键增强**：选轨四档（直通/机械小改/轻量/重型——由集成段**启动锚**触发，治"主入口依赖自觉"）；七 agent 已声明 `tools` 白名单（门禁类=Read,Glob,Grep,Write——平台强制最小权限，行为约定兜底）；**战役计划先落盘** `<战役目录>/campaign.md`（首个 dispatch 前 Write，含状态行区=上下文压缩后的恢复锚）；回炉任务书必带阻塞级发现（Reflexion 实证：盲重试不优于基线）；DEGRADED 人工介入四动作（批准重试/修正重派/否决归因/直接答复）。

**编排器是主对话的职能（depth-0），不是可分派的子 agent**——编排器剧本 agent 形态已被实战退役（嵌套委托困境）。

### 三大跨切面机制（全部 agent 定义共享的契约）

1. **输出契约（落盘即遗忘）**：子 agent 详情 Write 到战役目录，final report 只允许极简三行（`[AGENT-OUTPUT-START]` 包裹；名实注：回执实为 4 行含首尾标记，解析以标记配对为准）。**判定生成规则唯一权威=手册 §3**：FAIL⟺存在阻塞级发现（High/ERROR/CONFIRMED）；仅非阻塞级→PASS 但判定行必带各级计数；过程不可完成→DEGRADED。编排器按判定机械路由：PASS/DONE→下一环节、FAIL→回炉、DEGRADED→人工
2. **分派对账**：dispatch/done/expect/check 四事件 JSONL 协议（登记命令模板见手册 §6）；完成判定**锚产物文件存在性，不锚自述**；产物 mtime 早于 dispatch ts（+1s 容差，fail-closed）= 旧产物残留报 STALE-EXPECT；**dispatch 同键覆盖使旧 done 回执失效**；**每战役独立 ledger**；done 事件可选记 tokens/duration_ms/tool_calls；问题登记表=战役目录 `problems.md`（P0-P3 四列，编排者维护）
3. **知识沉淀闭环**：四模板（修复/设计/审查/认知）按来源特征机械路由；双时机提炼（即时通道 origin=review-round|reversal + 收官通道 sop_hint）；drafts/ 人工采纳门禁（未采纳零污染，**转正走索引模板 §八操作卡**——含结构必调一处：origin 头行移至 frontmatter 后，否则 skill 无法识别）；机械锚（收到 X 必做动作 Y，不依赖自觉）；收官症状词扫描范围为 skills/ 全域但**排除 knowledge-capture/**，且**命中判定以 frontmatter 与路由表行为先例**（正文普通词命中不计）；轻扫兜底每次收官恒跑；drafts/ 超期草稿按收官日期机械锚呈报（`呈报=<日期>#<次数>`，≥2 次未裁决降频）

### 档位体系

agent frontmatter 的 `model: sonnet|opus` 是轻/重档建议：判断密集环节（设计/审查/验证/红线守卫）重档且独占运行，执行密集环节（侦查/实施/归档）轻档可并行。

## 本仓库的标准工作法（三轮战役实证）

1. **战役计划先落盘**：首个 dispatch 前 Write `results/exec-detail-<战役名>/campaign.md`（目标/选轨/agent 清单/expect 表/预算+状态行区；中断恢复首步=Read 它；每环节完成追加状态行）
2. 审查/调研/改写任务：**并行派发多实例各独占一域**（文件域互不重叠防编辑冲突），任务书含背景/独占域/重点/工具纪律/落盘路径/极简三行格式；对账用 expect 产物存在性（可布防文件监控）；外部调研走 web-search-skill 脚本（内置 WebSearch 本环境不可用）
3. 编排器汇总分级路由：P0（阻塞）/P1（直接修）/P2（修后记录）/P3（呈报用户决策，不擅动）——裁决理由呈报，尤其二选一处
4. **大型方案过对抗审查**：多文件改动方案先派 adversarial-reviewer 审（ FAIL→按阻塞级发现回炉修订→快速复审放行——第三轮实战抓获 C1 级方案错误）
5. 修复后**必须派 code-validator 终验**（逐条编号清单 vs 磁盘实测，不采信自述；FAIL→带阻塞级发现回炉→**targeted 复核**只核修复处，不重跑全量）
6. 会话中文件可能被外部修改——编辑前重读目标区域；old_string 不匹配时以磁盘现文重新锚定，不回滚外部变更
7. **知识沉淀联动**：战役中的可命名普适模式按四模板落草稿至 skills/drafts/（即时通道不等收官），收官呈报列草稿清单待用户采纳；用户说"采纳"→按 §八操作卡转正（建 skill 目录+总索引加行+删草稿）
8. 完工提交 git：中文提交信息 + `Co-Authored-By: Claude Code <noreply@anthropic.com>`

## 修改本仓库的纪律（体系自身规则约束编辑行为）

1. **单一权威源（三类分治）**：索引/行数/滚动类数值唯一权威=知识库总索引维护规则节（分发态双承载：[索引与路由模板.md](skills/knowledge-capture/references/索引与路由模板.md) §二 模板源 ↔ [skills/README.md](skills/README.md) 预建实例，两者必须同步）；知识诞生门槛唯一权威=knowledge-capture SKILL.md Phase B 节；管线运行类数值（并行上限/重试次数/侦查预算）唯一权威=[CLAUDE.md集成段.md](skills/knowledge-capture/references/CLAUDE.md集成段.md) 硬性规则——其余任何位置单向引用**不自报数字**。历史判例：曾有体系因两处数字不一致（18 vs 10）实测翻车
2. **全新开发定位**：禁止引入外部系统指涉（其他项目名/产品名/溯源表述）；案例保持通用化（不与具体项目结合），改写时**保教训结构、保量化数字**（空洞化比残留更糟）
3. **[domain-guard.md](agents/domain-guard.md) 保持模板态**：5 类占位符不可填充；第 0 步机械自检（Grep 残留 `{{` 或 `—— 例：`→DEGRADED）不可破坏；触发点清单唯一权威位在目标项目 CLAUDE.md，本模板不维护副本
4. **元信息块四字段同构**：每个 SKILL.md 尾部 `状态 | 最后验证 | 适用版本 | 源材料` 不可缺（「最后验证」语义=最近一次触发场景实测日期）；实质性修改后更新
5. **交叉引用一致性**：机制规则散布于 knowledge-capture SKILL.md ↔ references/（五文件 + README 登记表） ↔ skills/README.md 总索引 ↔ README ↔ 集成段——改一处必须 Grep 全库核对其余引用点同步；**判定生成规则四方**（手册 §3 ↔ validator ↔ adversarial-reviewer ↔ domain-guard）与**机械锚模式串三处**（SKILL.md/索引模板/集成段的 `缺陷模式|普适|反模式|高危|Critical|High`）必须逐字同步
6. **adversarial-reviewer 随包分发是设计决策**：管线③直接引用它，不随包则接入后首次派发即断链；目标项目或用户全局已有同名 agent 时优先既有版，按判例 10「补丁节合并」接入（不整文件覆盖）
7. **克制清单（不可违反）**：不引入外部框架、不建向量知识库（markdown + grep + 索引优于 RAG）、不做全自动采纳（人工门禁是安全机制）、新增 agent 角色必须有调用痕迹预期
8. 新增/修改 skill 必须遵循 knowledge-capture [SKILL.md](skills/knowledge-capture/SKILL.md) 的三级渐进披露规范（L1 description 窄触发、L2 正文行数上限、L3 references/ 带 README 登记表）+ 维护规则 10 的合规自检（name ≤64 小写连字符 / description ≤1024 字符 / 引用正斜杠）
9. **战役报告不入库**：工作产物落盘 `results/` 且被 gitignore；关键结论须在收官呈报中提炼进资产或按工作法 7 落草稿后才算沉淀完成（不留"报告落盘即结束"的断点）

## 改完资产后的自查

- **引用实存**：改动涉及 agent/skill/文件名时，Grep 全库确认无断链（review-patterns 的知识漂移核查——重构合并角色后必须跑一轮退役实体名扫描）
- **占位符清点**：`{{` 只应出现在 [domain-guard.md](agents/domain-guard.md)、[CLAUDE.md集成段.md](skills/knowledge-capture/references/CLAUDE.md集成段.md) 与 README.md 的接入说明中（索引与路由模板的数值占位用 `<...>` 尖括号）
- **数值一致性**：全库 Grep 具体数字（行数上限/阈值），确认只在三类权威源出现；总索引与索引模板 §二的维护规则必须逐字对齐
- **残留扫描**：`FAS|源体系|转化自` 于全仓库应零命中（历史版本号不在扫描项——随版本演进）
- **skill 识别**：SKILL.md 的 frontmatter 必须在文件首（origin 等头行只能放 frontmatter 之后）
