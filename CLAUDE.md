# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 场景判定（先读这个——读本文件的两个场景）

本资产集的运作模型：**分发源仓库 → 拷贝到目标工作区 → 按项目特点初始化定制**。你（Claude）可能在其中任一场景读到本文件：

- **场景 A〔源〕——你在分发源仓库维护资产集本身**（本仓库：资产不被直接使用，每次使用都是拷贝出去）：适用全部纪律，含下文标注〔源〕的条目
- **场景 B〔拷贝后〕——本目录被整体拷贝到了某个目标工作区**：你的首要任务是**执行初始化定制**（清单见 [README.md](README.md)「整目录拷贝与初始化」节），其中下文标注〔源〕的纪律**对你不适用**——它们约束的是分发源的维护，不是你的项目；填充 domain-guard、替换占位符、按项目特点改写配置与案例，都是设计内的正当操作
- 不确定在哪个场景：看工作区是否有真实的业务代码/数据——有即场景 B

## 项目性质（场景 A 语境）

这不是可执行代码项目，而是一套**可移植的 Claude Code 配置资产集**（v1.1）：7 个多 agent 工程管线定义 + 知识沉淀 skill 体系。**本仓库同时是这套知识体系的运行态知识库**（实战沉淀持续转正中）。场景 B 中此描述不适用——目标工作区以自己的项目性质为准。

- **没有 build / lint / test 命令**〔源〕——产物是 markdown 资产本身
- 唯一代码：[六件套管线手册.md](skills/knowledge-capture/references/六件套管线手册.md) §6 内嵌的 `agent_reconcile.py`（v1.1 加固版对账脚本：绝对路径化 + mtime 回炉防护 +1s fail-closed 容差 + 同键 done 失效 + 坏行容错 + utf-8 输出；16 场景回归实测全过；**拷贝后可直接提取使用**）
- git 仓库（main）〔源〕；战役报告等工作产物落盘 `results/`（已 gitignore，不入库）——此约定两场景通用
- 版本标记 v1.1〔源〕：七份 agent 头部单行 + 各 SKILL.md 头部引用块与尾部元信息「适用版本」+ references 各文件头部版本行；目标项目对条目的实质修改应更新自己的「最后验证」而非跟随源版本号

## 架构大图景（两场景通用）

### 分发模型与知识库结构

```text
方式 A（选择性复制）：agents/*.md → .claude/agents/；skills/ 整体 → .claude/skills/
方式 B（整目录拷贝）：整个目录拷贝到目标工作区 → 按 README「整目录拷贝与初始化」完成定制
```

知识库（skills/）现含 **7 个 skill**：knowledge-capture（机制总纲）+ methodology 三件（review-patterns / design-lessons / chain-audit）+ **三份实战转正 skill**（asset-maintenance-defects / multi-agent-campaign-sop / blind-retry-rework）。总索引=[skills/README.md](skills/README.md)（唯一权威全量表，维护规则 10 条——**目标项目按自身情况定制数值，只改该节**）；drafts/=[skills/drafts/](skills/drafts/)（未采纳草稿区，零污染门禁）。库边界规则见[索引与路由模板.md](skills/knowledge-capture/references/索引与路由模板.md) §〇。

### 六件套管线（agents/ 七个定义间的关系）

①侦查（eng-recon ×N 并行，tech/data/research 三子模式）→ ②设计（eng-arch，复杂战役才走）→ ③对抗审查（adversarial-reviewer，先立后破）→ ④实施（eng-implementer，模仿铁律）→ ⑤交叉验证（eng-validator，MISSING/OVERSTEP/DEVIATION 三分法）→ ⑥归档（eng-documenter，锚点写入 + 提炼机械路由）。任何阶段发现触碰红线域 → domain-guard 强制插入（不因改动量小降档）；**测试执行由编排器（主对话）承担**（测试命令读集成段 TEST_COMMAND 键），不设独立测试 agent。

**v1.1 关键增强**：选轨四档（直通/机械小改/轻量/重型——由集成段**启动锚**触发）；七 agent 已声明 `tools` 白名单（门禁类=Read,Glob,Grep,Write——平台强制最小权限，行为约定兜底）；**战役计划先落盘** `<战役目录>/campaign.md`（含状态行区=上下文压缩后的恢复锚）；回炉任务书必带阻塞级发现（Reflexion 实证：盲重试不优于基线）；DEGRADED 人工介入四动作（批准重试/修正重派/否决归因/直接答复）。

**编排器是主对话的职能（depth-0），不是可分派的子 agent**——编排器剧本 agent 形态已被实战退役（嵌套委托困境）。

### 三大跨切面机制（全部 agent 定义共享的契约）

1. **输出契约（落盘即遗忘）**：子 agent 详情 Write 到战役目录，final report 只允许极简三行（`[AGENT-OUTPUT-START]` 包裹；名实注：回执实为 4 行含首尾标记，解析以标记配对为准）。**判定生成规则唯一权威=手册 §3**：FAIL⟺存在阻塞级发现（High/ERROR/CONFIRMED）；仅非阻塞级→PASS 但判定行必带各级计数；过程不可完成→DEGRADED。编排器按判定机械路由：PASS/DONE→下一环节、FAIL→回炉、DEGRADED→人工
2. **分派对账**：dispatch/done/expect/check 四事件 JSONL 协议（登记命令模板见手册 §6）；完成判定**锚产物文件存在性，不锚自述**；产物 mtime 早于 dispatch ts（+1s 容差，fail-closed）= 旧产物残留报 STALE-EXPECT；**dispatch 同键覆盖使旧 done 回执失效**；**每战役独立 ledger**；done 事件可选记 tokens/duration_ms/tool_calls；问题登记表=战役目录 `problems.md`（P0-P3 四列，编排者维护）
3. **知识沉淀闭环**：四模板（修复/设计/审查/认知）按来源特征机械路由；双时机提炼（即时通道 origin=review-round|reversal + 收官通道 sop_hint）；drafts/ 人工采纳门禁（未采纳零污染，**转正走索引模板 §八操作卡**——含结构必调一处：origin 头行移至 frontmatter 后，否则 skill 无法识别）；机械锚（收到 X 必做动作 Y，不依赖自觉）；收官症状词扫描范围为 skills/ 全域但**排除 knowledge-capture/**，且**命中判定以 frontmatter 与路由表行为先例**；轻扫兜底每次收官恒跑；drafts/ 超期草稿按收官日期机械锚呈报（`呈报=<日期>#<次数>`，≥2 次未裁决降频）

### 档位体系

agent frontmatter 的 `model: sonnet|opus` 是轻/重档建议：判断密集环节（设计/审查/验证/红线守卫）重档且独占运行，执行密集环节（侦查/实施/归档）轻档可并行。

## 本仓库的标准工作法（三轮战役实证，两场景通用）

1. **战役计划先落盘**：首个 dispatch 前 Write `results/exec-detail-<战役名>/campaign.md`（目标/选轨/agent 清单/expect 表/预算+状态行区；中断恢复首步=Read 它；每环节完成追加状态行）
2. 审查/调研/改写任务：**并行派发多实例各独占一域**（文件域互不重叠防编辑冲突），任务书含背景/独占域/重点/工具纪律/落盘路径/极简三行格式；对账用 expect 产物存在性（可布防文件监控）
3. 编排器汇总分级路由：P0（阻塞）/P1（直接修）/P2（修后记录）/P3（呈报用户决策，不擅动）——裁决理由呈报，尤其二选一处
4. **大型方案过对抗审查**：多文件改动方案先派 adversarial-reviewer 审（FAIL→按阻塞级发现回炉修订→快速复审放行）
5. 修复后**必须派 code-validator 终验**（逐条编号清单 vs 磁盘实测，不采信自述；FAIL→带阻塞级发现回炉→**targeted 复核**只核修复处）
6. 会话中文件可能被外部修改——编辑前重读目标区域；old_string 不匹配时以磁盘现文重新锚定，不回滚外部变更
7. **知识沉淀联动**：战役中的可命名普适模式按四模板落草稿至 skills/drafts/（即时通道不等收官），收官呈报列草稿清单待用户采纳；用户说"采纳"→按 §八操作卡转正（建 skill 目录+总索引加行+删草稿）
8. 完工提交 git：中文提交信息 + `Co-Authored-By: Claude Code <noreply@anthropic.com>`

## 修改纪律（〔源〕=仅分发源仓库；〔通用〕=两场景皆适用）

1. **单一权威源（三类分治）**〔通用〕：索引/行数/滚动类数值唯一权威=知识库总索引维护规则节（**目标项目只维护 skills/README.md 一处**；〔源〕附注：分发源另有模板源[索引与路由模板.md](skills/knowledge-capture/references/索引与路由模板.md) §二，两者须同步）；知识诞生门槛唯一权威=knowledge-capture SKILL.md Phase B 节；管线运行类数值唯一权威=[CLAUDE.md集成段.md](skills/knowledge-capture/references/CLAUDE.md集成段.md) 硬性规则——其余位置单向引用**不自报数字**。历史判例：曾有体系因两处数字不一致（18 vs 10）实测翻车
2. **全新开发定位**〔源〕：分发源禁止引入外部系统指涉、案例保持通用化（保教训结构、保量化数字）。**目标项目不受此限**——沉淀自己的 SOP/判例带自己的项目语境是设计内行为（provenance 标注来源即可）
3. **domain-guard 模板态**〔源〕：分发源内 5 类占位符不可填充、第 0 步机械自检不可破坏、触发点清单唯一权威位在目标项目 CLAUDE.md。**目标项目初始化的第一件事就是填充它**（无红线域填「无」休眠）——填充后第 0 步自检自然通过
4. **元信息块四字段同构**〔通用〕：每个 SKILL.md 尾部 `状态 | 最后验证 | 适用版本 | 源材料` 不可缺（「最后验证」语义=最近一次触发场景实测日期）；实质性修改后更新
5. **交叉引用一致性**〔通用〕：改一处必须 Grep 全库核对其余引用点同步；**判定生成规则四方**（手册 §3 ↔ validator ↔ adversarial-reviewer ↔ domain-guard）与**机械锚模式串三处**（SKILL.md/索引模板/集成段的 `缺陷模式|普适|反模式|高危|Critical|High`）必须逐字同步
6. **adversarial-reviewer 随包分发是设计决策**〔通用〕：管线③直接引用它；目标项目或用户全局已有同名 agent 时优先既有版，按判例 10「补丁节合并」接入（不整文件覆盖）
7. **克制清单（不可违反）**〔通用〕：不引入外部框架、不建向量知识库（markdown + grep + 索引优于 RAG）、不做全自动采纳（人工门禁是安全机制）、新增 agent 角色必须有调用痕迹预期
8. **skill 写作规范**〔通用〕：新增/修改 skill 遵循 knowledge-capture [SKILL.md](skills/knowledge-capture/SKILL.md) 三级渐进披露规范（L1 窄触发 / L2 行数上限 / L3 references 带 README 登记表）+ 维护规则 10 的合规自检（name ≤64 小写连字符 / description ≤1024 字符 / 引用正斜杠）
9. **战役报告不入库**〔通用〕：工作产物落盘 `results/` 且 gitignore；关键结论须在收官呈报中提炼进资产或按工作法 7 落草稿后才算沉淀完成

## 改完资产后的自查

**两场景通用**：
- **引用实存**：改动涉及 agent/skill/文件名时，Grep 全库确认无断链（重构合并角色后必跑退役实体名扫描）
- **skill 识别**：SKILL.md 的 frontmatter 必须在文件首（origin 等头行只能放 frontmatter 之后）
- **数值一致性**：全库 Grep 具体数字，确认只在权威源出现（目标项目的权威源=总索引维护规则节+集成段硬性规则）

**〔源〕专属**（目标项目跳过）：
- **占位符清点**：`{{` 只应出现在 domain-guard.md、CLAUDE.md集成段.md 与 README.md 的接入说明中（数值占位用 `<...>` 尖括号）
- **双承载同步**：索引模板 §二 与 skills/README.md 维护规则逐字对齐
- **残留扫描**：`FAS|源体系|转化自` 于全仓库零命中
- **版本一致**：全库版本标记统一
