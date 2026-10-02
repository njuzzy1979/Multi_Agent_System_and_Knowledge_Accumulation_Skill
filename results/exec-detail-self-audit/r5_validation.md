# r5 交叉验证报告：self-audit-v1 修复清单逐条实测

- 验证时间：2026-10-01
- 验证方法：全部结论基于 Read/Grep 实测磁盘内容，不采信任何自述
- 验证范围：修复清单 A~J 共 43 条 + 全库一致性专项 2 条（合计 45 条）
- 基线说明：本仓库非 git，无历史版本可 diff，越界（OVERSTEP）检测采用内部一致性代查（占位符分布、引用实存、登记表对账）；results/ 下 r1/r2/r3 为本战役审查工作产物，属 EXTERNAL，不计入资产一致性口径

## 一、逐条对照表

### A. skills/knowledge-capture/references/六件套管线手册.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 1 | §1 末尾新增「测试承接」段（测试由编排器承担，不设独立测试 agent） | 六件套管线手册.md:32 | ✅一致 |
| 2 | §2 铁律 4 不再自报「重试 1 次」，改引用集成段硬性规则 7 | 六件套管线手册.md:39（「按集成段硬性规则 7 的重试上限重试（管线运行类数值唯一权威位在集成段，本手册不自报）」） | ✅一致 |
| 3 | §3 新增「名实注」+「判定生成规则」段并标注唯一权威 | 六件套管线手册.md:55（名实注：回执实为 4 行）、:57-61（FAIL⟺High/ERROR/CONFIRMED；仅非阻塞级→PASS 带计数；不可完成→DEGRADED；:57 标注「唯一权威——门禁类 agent 定义内嵌副本须与本节同步」） | ✅一致 |
| 4 | §4 审查任务书要求含「输出落盘路径（expect 产物）」 | 六件套管线手册.md:86 | ✅一致 |
| 5 | §6 脚本三件：resolve() 绝对路径化 / mtime 回炉防护 + STALE-EXPECT / 坏行容错 | 六件套管线手册.md:136（`str(Path(expect_file).resolve())`）、:152-158（mtime<dispatch ts → stale）、:161（STALE-EXPECT 输出）、:144（JSONDecodeError 容错） | ✅一致 |
| 6 | §6 末尾「问题登记表」定义（problems.md 四列） | 六件套管线手册.md:178（级别 P0-P3 / 描述 / 处置 / 状态） | ✅一致 |
| 7 | §8 清单 3 改排除 knowledge-capture/ + 恒跑；新增第 5 条 drafts 日期机械锚；原第 5 条改编号 6 并引用 §6 | 六件套管线手册.md:190、:192、:193 | ✅一致 |

### B. skills/knowledge-capture/references/CLAUDE.md集成段.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 8 | 头部数值权威分界 + 四节 ≤70 行预算 | CLAUDE.md集成段.md:3 | ✅一致 |
| 9 | 指回行改全路径 .claude/skills/knowledge-capture/references/索引与路由模板.md | CLAUDE.md集成段.md:34 | ✅一致 |
| 10 | 路由表尾行「\| {{...按权威上限裁剪}} \| \|」已删除 | CLAUDE.md集成段.md:36-41（表体经逐行核对无该行；全库 Grep「按权威上限裁剪」零命中） | ✅一致 |
| 11 | 收官判定节：排除 knowledge-capture/ + documenter 每次收官恒跑（无「零命中时」） | CLAUDE.md集成段.md:47 | ✅一致 |
| 12 | 项目上下文声明引导行留空语义说明 | CLAUDE.md集成段.md:60（「留空=保留 {{占位符}} 原样或写『无』，agent 均按未声明处理」） | ✅一致 |

### C. skills/knowledge-capture/references/索引与路由模板.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 13 | §〇 新增 knowledge-capture/ 自指排除例外 | 索引与路由模板.md:10（「唯一例外：knowledge-capture/ 自身属机制元文档，收官症状词扫描不计其命中……其被 Grep 兜底检索的可达性不受影响」） | ✅一致 |
| 14 | §二 格式说明改引用 SKILL.md 写作规范节 | 索引与路由模板.md:37 | ✅一致 |
| 15 | 维护规则第 5 条补行数计量口径 | 索引与路由模板.md:55（「表体行数，含表头，不含标题与空行」） | ✅一致 |
| 16 | §三 示意尾行移出表外改注释行；实测口径 8 行公式 | 索引与路由模板.md:86（表后注释「不设示意尾行」）、:91（固定开销=引导4+表头2+锚2=8，表行预算=上限−8） | ✅一致 |
| 17 | §五 标题去「17 行」 | 索引与路由模板.md:100（「最小规则集，复制即启动」） | ✅一致 |
| 18 | §七 冷启动豁免去「≥1 个月」改定性 | 索引与路由模板.md:123（「可观数量史料」） | ✅一致 |
| 19 | §七 执行主体改「轻档子 agent（平台通用 agent 即可）」 | 索引与路由模板.md:125 | ✅一致 |

### D. skills/knowledge-capture/SKILL.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 20 | 头部新增转化引用块 + 版本 v1.1 | SKILL.md:6 | ✅一致 |
| 21 | sop_hint 机械锚扫描范围（含 drafts/ 与 methodology/、排除 knowledge-capture/） | SKILL.md:49 | ✅一致 |
| 22 | 呈报协议季度清理改收官机械锚 | SKILL.md:54（「收官机械锚：每次收官列 drafts/ 文件名日期，超期草稿逐条呈报请裁决」） | ✅一致 |
| 23 | drafts 门禁流转①去重范围 / ③ origin 头部行保留 | SKILL.md:61（①「总索引既有条目与 drafts/ 现存草稿」；③「origin 头部行保留……零改写承诺」） | ✅一致 |
| 24 | 索引与路由节三类分治（含诞生门槛权威在本文 Phase B 节） | SKILL.md:84 | ✅一致 |
| 25 | 六渠道表 drafts/ 行括注 | SKILL.md:22 | ✅一致 |
| 26 | 尾部元信息块四字段 | SKILL.md:102（状态/最后验证/适用版本/源材料齐备） | ✅一致 |

### E. skills/knowledge-capture/references/四模板.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 27 | 四模板元信息行四字段+三档枚举，Grep '^元信息：' 4 行全命中格式一致 | 四模板.md:46、:76、:103、:130（Grep 实测 4 行，逐字符一致） | ✅一致 |
| 28 | 共通规则 2 补 draft/stale 首句内置警告 | 四模板.md:138 | ✅一致 |

### F. agents/（eng-recon 不在改动范围，未动，符合清单边界）

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 29 | 五份输入节均含「输出落盘路径（expect 产物）」 | Grep agents/ 命中 6 处：eng-arch:12、eng-implementer:12、eng-validator:12、eng-documenter:12、adversarial-reviewer:13，+domain-guard:13（第 6 处为条目 33 要求，精确吻合无多无少） | ✅一致 |
| 30 | eng-validator 与 adversarial-reviewer 新增「判定生成与并发纪律」节，位置在证据纪律/专项必查之后、边界约束之前 | eng-validator.md:53-56（证据纪律:47 后、边界约束:58 前）；adversarial-reviewer.md:36-39（专项必查:30-34 后、边界约束:41 前）；均含副本+独占运行声明 | ✅一致 |
| 31 | eng-documenter 草稿产出明示「恒为 wrapup」 | eng-documenter.md:33 | ✅一致 |
| 32 | adversarial-reviewer 同名双轨改「优先既有版+补丁节合并」；五类缺陷模式引用改全路径 | adversarial-reviewer.md:7（判例 10 补丁节合并）、:33（.claude/skills/methodology/review-patterns） | ✅一致 |
| 33 | domain-guard 新增输入节 / 第 0 步模板填充自检 / DETERMINISTIC_LAYER「无」出口 / 立场纪律判定生成行 | domain-guard.md:11-13（输入四件套）、:47-49（第 0 步 Grep {{ 与「例：」→DEGRADED）、:42（「项目暂无第一道防线时填『无』」）、:75（CONFIRMED 阻塞级） | ✅一致 |

### G. README.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 34 | 第一步占位符枚举 5 处 + 保护态对齐第 0 步 + 同名 agent 补丁节合并指引（引判例 10） | README.md:32（5 处枚举：红线域名/失败后果/检查项/影响单位/确定性防线；「DEGRADED 保护态（工作流程第 0 步机械自检：残留 {{ 字面量即降级交人工）」；判例 10 引用齐备） | ✅一致 |
| 35 | 第三步改「按该模板 §五 最小规则集复制即启动」 | README.md:42 | ✅一致 |

### H. skills/knowledge-capture/references/实战判例精选.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 36 | 判例 2 来源行改「集成段管线表 ⑤ 行」，G1 全库清零 | 实战判例精选.md:21（「集成段管线表 ⑤ 行『首轮 FAIL 回炉是设计内防线』」）；Grep 'G1' 体系资产零命中（残留仅在 results/r3 审查报告，属缺陷记录原文） | ✅一致 |
| 37 | 判例 7 新增 v1.1 转化注记（3:1~6:1 未达等长取舍） | 实战判例精选.md:53 | ✅一致 |

### I. skills/methodology/ 三份 SKILL.md

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 38 | review-patterns 断言附则去「替身计数=N」 | review-patterns/SKILL.md:38（已为「目标记录数=N 且持续 M 个周期」通用表述）；Grep '替身' 资产零命中 | ✅一致 |
| 39 | 三份头部转化引用块 | review-patterns:6 / design-lessons:6 / chain-audit:6 | ✅一致 |
| 40 | 三份先例引用占位改自说明写法，「登记处」清零 | review-patterns:54 / design-lessons:53 / chain-audit:62（「格式=<日期｜战役/报告路径｜一句话结论>」）；Grep '登记处' 资产零命中 | ✅一致 |
| 41 | 三份尾部元信息四字段 | review-patterns:63 / design-lessons:64 / chain-audit:73 | ✅一致 |

### J. CLAUDE.md（仓库根）

| # | 清单要求 | 实现位置 | 判定 |
|---|---|---|---|
| 42 | 修改纪律 1 改三类分治表述 | CLAUDE.md:43 | ✅一致 |
| 43 | 修改纪律 4 改「references/（五文件 + README 登记表）」 | CLAUDE.md:46；对账 references/README.md:3-9（登记恰五文件，meta 豁免注记:11） | ✅一致 |

### 全库一致性专项

| # | 清单要求 | 实测结果 | 判定 |
|---|---|---|---|
| 44 | 「17 行」「机械档」「零命中时 documenter」「G1 」应零命中 | 体系资产（skills/、agents/、README.md、CLAUDE.md）四项全部零命中；残留命中仅位于 results/exec-detail-self-audit/ r2/r3 审查报告（记录缺陷原文的工作产物，非资产，属 EXTERNAL 合理存在） | ✅一致 |
| 45 | 判定生成规则四方一致性 | 手册 §3:57-61（唯一权威+三阻塞级枚举）↔ eng-validator:55（ERROR）↔ adversarial-reviewer:38（High）↔ domain-guard:75（CONFIRMED）；Grep「唯一权威=六件套管线手册 §3」三副本全部命中，四方互相吻合 | ✅一致 |

## 二、三分法发现

### 遗漏项（MISSING）—— 无

45 条清单要求全部在磁盘实测命中，无一条缺失。

### 越界项（OVERSTEP）—— 无

非 git 仓库无历史基线，改用三项内部一致性代查，均未发现清单外可疑改动：

1. `{{` 占位符分布：Grep `\{\{` 全库，资产内命中仅 domain-guard.md（模板占位，8 处）、CLAUDE.md集成段.md（模板占位）、README.md:32/35（接入说明）、根 CLAUDE.md:44/54（规则文本自身引用 `{{` 符号）——与根 CLAUDE.md:54「占位符清点」声明的允许范围完全一致，无清单外文件引入占位符。
2. 引用实存：清单涉及的交叉引用（判例 10、集成段硬性规则 7、手册 §3/§6、模板 §〇/§五/§七、review-patterns 全路径）逐条 Grep 实存，无断链。
3. 登记表对账：references/README.md 登记五文件与「五文件 + README 登记表」表述吻合，无未登记的新增/删除文件。

results/exec-detail-self-audit/ 下 r1/r2/r3 报告为本战役审查工作产物，EXTERNAL，不计越界。

### 偏差项（DEVIATION）—— 无

数值/方向/边界/枚举类要求逐项核过：阻塞级枚举（High/ERROR/CONFIRMED）与三方 agent 副本逐字吻合；重试权威指向（集成段硬性规则 7=「重试 1 次」，手册只引用不自报）单向无环；§三 8 行公式与模板实物逐行清点吻合（引导 4 + 表头 2 + 锚 2）；四模板元信息四行逐字符一致。未发现落地方式与清单意图不符之处。

## 三、轻微观察项（不构成门禁不通过，供 orchestrator 裁决）

1. **domain-guard 第 0 步「例：」Grep 的子串误命中边角**（agents/domain-guard.md:49 ↔ :3）：frontmatter description 含「（示例：真实资金……）」，「例：」是「示例：」的子串。第 0 步原文为「Grep 本定义**正文**的 `{{` 字面量与『例：』引导语」，而 frontmatter:3 自身使用「正文中全部 {{占位符}}」的「正文=body」口径，善意解读下逻辑自洽（填充者整体替换 {{...}} 占位符后 body 内「例：」随占位符消失）；但若执行者把 Grep 范围理解为含 frontmatter，已正确填充的模板也会恒报 DEGRADED。建议后续版本把 Grep 模式收紧为「—— 例：」或「，例：」并显式声明扫描范围为 frontmatter 之后的正文。属实现边角的模糊点，非清单条目 33 的偏差（清单要求本身已如实落地）。
2. **索引类权威位的双指向措辞**（CLAUDE.md集成段.md:3「以知识库总索引 README 维护规则为唯一权威」↔ 根 CLAUDE.md:43「唯一权威=索引与路由模板.md 维护规则节」）：前者指目标项目实例化后的总索引 README，后者指本仓库模板源节；模板 §二 维护规则节正是被复制为目标 README 维护规则的那一节，语义同源、不构成数字冲突（两处均未自报任何具体数字）。此措辞差异系既有设计语义，非本次修复引入，列为后续统一候选。

## 四、结论

- **门禁：通过（PASS）**
- 45/45 条清单要求全部实测命中：MISSING=0，OVERSTEP=0，DEVIATION=0
- 全库一致性专项四组 Grep 资产口径全部零命中；判定生成规则四方吻合
- 2 条轻微观察项均为边角模糊点/既有措辞，不影响体系资产的功能正确性，无需阻塞本次修复收官
