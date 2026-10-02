# r1 审查报告：agents/ 七定义（战役 self-audit-v1）

审查对象：`agents/` 下七份定义（eng-recon / eng-arch / eng-implementer / eng-validator / eng-documenter / adversarial-reviewer / domain-guard）
对照契约：项目 CLAUDE.md、`skills/knowledge-capture/references/六件套管线手册.md`（§3/§5/§7）、`skills/knowledge-capture/references/CLAUDE.md集成段.md`、SKILL.md 总纲、实战判例精选.md、README.md（仅作衔接核对，不审其机制设计）
审查方法：七份定义全文通读 + 三份契约逐字段比对 + 引用实存 Grep/Glob 核实

## 执行摘要

- Critical: 0 | High: 0 | Medium: 6 | Low: 7
- 整体判断：**需修正后重审（FAIL，M6/L7）**。主体结构经得起对抗（frontmatter 合法同构、档位三处一致、输出契约同构、降级路径齐备、职责互斥清晰、关键引用实存），无「复制即断」的阻塞级缺陷；但 6 项 Medium 中两项（F1 门禁判定无生成规则、F4 红线保护态缺机械锚）直接触及体系两大核心卖点（机械路由、红线域防线），一项（F6）是体系被自己的随包判例（判例 7）击中的自反一致性缺口。v1.1 以「转化自实战验证」名义分发，应修正上述缺陷后再宣称定稿。

## 一、先立后破核实表（实证「做对了的部分」）

以下 13 项逐条经 Grep/Read 行号复核成立，是后续发现不推翻的基线：

| # | 核实项 | 结论 | 证据锚 |
|---|---|---|---|
| L1 | frontmatter 七份同构：均 `name / description / model` 三字段、顺序一致、YAML 合法（description 双引号字符串内无未转义引号） | 成立 | 七文件第 1-5 行 |
| L2 | name 唯一且符合 Claude Code 子 agent 命名规范（小写+连字符，≤64 字符） | 成立 | Glob `agents/*` 恰 7 文件无重名 |
| L3 | model 值三处一致：frontmatter（sonnet×3: recon/implementer/documenter；opus×4: arch/validator/adversarial/domain-guard）↔ 手册 §5 档位表七行 ↔ 集成段管线表+硬性规则 1（domain-guard「重档」） | 成立，三处零偏差 | 各文件第 4 行；手册 §5:81-89；集成段:13-18、22 |
| L4 | 输出契约同构：七份包裹标记（`[AGENT-OUTPUT-START/END] <agent名>`）、字段行（首字段\|判定\|结论）、报告行与手册 §3 模板逐字段一致；documenter 的 `SOP草稿=<文件名>` 扩展与手册 §3「可按需扩展字段、不增行」及所举示例完全吻合 | 成立 | recon:75-79 / arch:80-84 / impl:69-73 / val:70-74 / doc:59-63 / adv:53-57 / guard:79-83 ↔ 手册 §3:42-47 |
| L5 | 判定枚举角色分化事实一致：产出类四份全部 `DONE\|DEGRADED`，门禁类三份全部 `PASS\|FAIL\|DEGRADED`，无一份错位 | 成立（但缺明文规则，见 F1 关联） | 同上行号 |
| L6 | 降级路径节七份齐备，fail_to_human 姿态与「行为约定，非平台配置项」措辞统一 | 成立 | recon:64-68 / arch:69-73 / impl:58-62 / val:59-63 / doc:48-52 / adv:42-46 / guard:68-72 |
| L7 | domain-guard 触发点权威位指向「项目 CLAUDE.md『Agent 调用规则·硬性规则 1』」——集成段该结构存在且编号吻合（唯一权威位+模板不维护副本+「不因改动量小降档」） | 成立 | domain-guard:7、15 ↔ 集成段:22（硬性规则 1） |
| L8 | 引用的上下文声明键全实存且带兜底：DEPLOYMENT_PROFILE（recon:41）、ARCHITECTURE_MODEL/TECH_STACK/RESOURCE_SERIALIZATION/HARD_CONSTRAINTS（arch:12、impl:45）↔ 集成段「项目上下文声明」五键全在；均配「未声明 → Grep 推断并标注依据」，第 2 步粘贴遗漏时不断链 | 成立 | 集成段:61-67 |
| L9 | 判例/知识引用实存：domain-guard:32 → 判例 6「确定性防线优先于概率防线」（判例精选:41-45，内容即双层防御分工，吻合）；adversarial:33 → `methodology/review-patterns` 实存（Glob 确认 `skills/methodology/review-patterns/SKILL.md`）；documenter:32 四模板路由表与 SKILL.md:27-34 逐行语义一致；drafts 命名 `draft-<模式名>-<YYMMDD>.md`、origin 枚举、CONFLICT、不仲裁与 SKILL.md drafts 门禁（SKILL.md:55-60）一致 | 成立（路径简称问题另列 F8） | 见左列行号 |
| L10 | 职责互斥、交接指认明确：recon:61「不做方案设计是 eng-arch 的事」→ arch:66「diff 由 implementer 产出」→ impl:41「是否符合方案意图是 eng-validator 职责」→ val:55-56「不评判方案好坏（审查环节）、修复回炉给 implementer」→ adv:40 红线域改动送 domain-guard；六环节+守卫无环内重叠 | 成立 | 各定义边界约束节 |
| L11 | 「红线域不因改动量小降档」三处一致：domain-guard:3、14 ↔ 手册 §1:28 ↔ 集成段规则 1 | 成立 | 见左列 |
| L12 | 占位符分布合规：agents 域 `{{` 仅出现在 domain-guard.md（Grep 确认 8 处），符合 CLAUDE.md 修改纪律 2 与自查「占位符清点」 | 成立 | Grep `\{\{` agents/ 仅命中 domain-guard.md |
| L13 | README 设计决策与定义互证自洽：决策 1「并行的是实例不是定义」↔ recon description「可并行派发多个实例各承担一种子模式」+ 手册 §7 侦查=轻档可并行；决策 4「机制不内嵌 documenter」↔ documenter 第三步仅做机械路由；决策 5「数据验证并入 validator」↔ validator 模式三 | 成立 | README:66-70 |

## 二、审查发现（四件套）

### [Medium] F1 门禁判定无生成规则：FAIL 的充分条件在三份门禁定义中全部缺失

- **触发场景**：同一份方案审查产出「2 Medium、0 High」发现时，实例 A 判 PASS（结论「Medium 不阻塞」）、回炉重审后实例 B 判 FAIL——判定随实例波动。编排器「按判定机械路由」（手册 §3:49）的路由键实际锚在 agent 主观裁量上，可重复性失效。validator 模式三同理：报告三级 ERROR/WARNING/INFO（val:45）与最终判定 PASS|FAIL|DEGRADED 的映射未定义（1 ERROR 判 FAIL？3 WARNING 判 PASS 还是 FAIL？）。
- **证据锚**：eng-validator.md:71-76（判定枚举 + 路由说明，无任何 FAIL 条件）；adversarial-reviewer.md:54（「FAIL 时含 H/M/L 计数」——但何种 H/M/L 组合 → FAIL 无规则）；domain-guard.md:63+80（「宁可误报」约束的是发现罗列，非判定映射；C/P/O 何组合 → FAIL 无规则；OBSERVATION 存在时判什么无定义）；手册 §3:49 只定义四值的路由语义，不定义判定语义。
- **修复方向**：三份门禁定义各补判定生成规则（例：任一 High/CONFIRMED → FAIL；仅 Medium/PLAUSIBLE → FAIL 或附条件放行；零发现 → PASS），或收敛到手册 §3 定义全体系统一判定语义、定义内单向引用——与体系「单一权威源」纪律一致。

### [Medium] F2 管线无测试承接：对代码类目标项目存在覆盖真空

- **触发场景**：目标项目为可执行代码项目（资产集宣称「复制到任何 Claude Code 项目」，README:6）。implementer 完成改动后自查仅覆盖「语法/import/类型/明显逻辑错误」（impl:29），validator 三模式均为静态对照（模式一 diff 对照、模式二 Grep 确认、模式三查数据，val:14-45），无任何环节执行测试；编排器侧契约（手册 §1 六环节、集成段管线表）亦无测试环节或「测试由主对话承担」的显式路由。改动后的回归验证无处发生——validator:51 自己要求「真断言（检查实质行为），不是冒烟」，但管线未给执行路径。
- **证据锚**：Grep agents/ 全域「测试|test」仅命中 eng-implementer.md:54（负面约束「不修改测试基线」）与 eng-documenter.md:3（description 中归档测试结果）；源体系实战管线有 test-designer（G4）/integration-verifier（G7）两角色（本机 available agents 清单实证），资产集砍除后 README:64-70 五条设计决策无一说明测试环节去向（对比：数据质量 agent 的砍除有决策 5 说明）。
- **修复方向**：二选一并成文——在手册 §1/集成段管线表显式声明「测试执行由编排器（主对话）直接承担，不入 agent 管线」；或恢复轻量承接（validator 增模式四「测试执行档」，按验收标准跑测试并映射判定）。

### [Medium] F3 domain-guard 无「## 输入」节：七份结构不同构，任务书契约缺失

- **触发场景**：编排器按各定义「## 输入」节构造任务书（任务书契约的既定用法，六份均为此结构）：domain-guard 无输入节可依——改动清单/diff 来源/验收标准/输出落盘路径均未声明，而其输出契约却要求回执「改动ID=<ID>」（guard:80），改动ID 从何传入无定义；工作流程第一步「Read 改动涉及的所有文件」（guard:44）隐含需要文件清单输入但未成契约。红线域守卫是七份中输入要求最高的角色（需改动上下文+检查项对照材料），恰是唯一无输入契约的一份。
- **证据锚**：对照 recon:10-12 / arch:10-12 / impl:10-12 / val:10-12 / doc:10-12 / adv:11-13 均有「## 输入」节；domain-guard.md 全文（1-84 行）无该节。
- **修复方向**：补「## 输入」节（改动ID、改动文件清单或 diff 路径、验收标准、输出落盘路径），与其余六份同构。

### [Medium] F4 README 承诺的「DEGRADED 保护态」缺机械触发器：未填充模板可伪装为已填充态运行

- **触发场景**：用户执行三步接入第 1 步（README:28-32「7 个文件直接复制……必须先填充」）但漏填 domain-guard 即开战；改动触碰红线域触发点，编排器按集成段规则 1 强制派发 guard。此时 prompt 中 `{{CHECK_ITEMS}}` 位置残留「按以下格式逐项声明，例：」+ 六类检查项示例骨架（guard:23-30）——示例骨架的形态（「### 检查项 N：名称/检查什么/怎么验证/边界值集」）与合法填充产物完全同构，agent 可直接按六类通用检查项开审并输出带判定的三行回执，全程不触发降级。README:32 承诺「降级路径可识别『模板未填充』显式交人工，不放水」实际依赖两个非机械机制：顶部 blockquote 警示（guard:7，依赖 agent 自觉阅读遵守）与触发条件「领域检查项与实际代码结构根本不匹配（模板未填充或已过期）」（guard:71，语义比对而非字面检测）。体系自身判例 4「机械锚防自觉退化」（判例精选:29-33）正是要杜绝防线依赖自觉。
- **后果**：红线域守卫以通用空壳运行、审查看似完成——防线静默架空，与 guard:72「红线域审查宁可停止不可放水」的自我要求直接矛盾。
- **修复方向**：工作流程增设第 0 步机械自检：「开始审查前 Grep 本定义正文，残留 `{{` 字面量或『例：』引导语 → 立即输出 DEGRADED 交人工」——字面可判定，不依赖语义识别；同时在 README 保护态表述处对齐该机制。

### [Medium] F5 「同名双轨」与「复制即项目版」冲突：用户全局调优版 adversarial-reviewer 被静默遮蔽

- **触发场景**：用户机器全局 `~/.claude/agents/` 已有同名 adversarial-reviewer 实战调优版（本会话环境实证：available agents 中的 adversarial-reviewer 描述含「综合多方输入定稿最终文档 / 新方案评审(G0) / Review-Integrate 阶段」语义，与资产版不同源、更丰富）。用户按 README 第 1 步把 7 文件「直接复制」进目标项目 `.claude/agents/`（无同名检测/合并步骤）——Claude Code 项目级 agents 优先于用户级，此后该项目所有 adversarial-reviewer 派发解析到通用骨架版，全局调优版静默失效且无任何提示。adversarial-reviewer.md:7「项目已有同名 agent 时优先项目版；本定义为通用骨架」描述的解析规则，被复制动作本身击穿：复制后「项目已有」恒成立，该规则恒保护较贫瘠的资产版。判例 10（判例精选:65-69）的设计意图是「项目版 = 通用骨架 + 领域补丁节追加而非整文件复制」，但三步接入未提供任何合并路径。
- **证据锚**：adversarial-reviewer.md:7；README:67（设计决策 2 重申同一规则）；README:28-32（第 1 步无同名处理）；判例精选:65-69。
- **修复方向**：接入第 1 步增补同名检测指令（复制前查目标 `.claude/agents/` 与用户全局 `~/.claude/agents/` 的同名文件，命中则按判例 10「补丁节合并」而非整文件覆盖），并将 adversarial-reviewer.md:7 的双轨声明从「优先项目版」改为指向该合并动作。

### [Medium] F6 判例 7「负面约束至少与正面职责等长」在七份定义上普遍不成立：体系被自己的随包判例击中

- **触发场景**：任何外部审查者按资产集随包分发的判例集核对资产本身（adversarial-reviewer 专项必查第 1 条「自反一致性」的标准应用）：判例精选:47-51 明文「负面约束比正面职责更防角色漂移；一个 agent 的『不做什么』清单**至少与『做什么』等长**」，来源标注「全部 9 个 agent 定义」——即源体系 9 定义当时满足。实测七份（按指令条目计）：eng-recon 正面约 19 条（三子模式+通用纪律）vs 边界约束 3 条（其一为通用「遵循 CLAUDE.md」）；eng-arch 约 15 vs 3；eng-implementer 约 12 vs 4；eng-validator 约 12 vs 3；eng-documenter 约 10 vs 4；adversarial-reviewer 约 8 vs 3；domain-guard 约 10 vs 2-3。比例 3:1 至 6:1，无一满足「至少等长」。
- **后果**：v1.1 宣称「转化自实战体系（2026-09~10 多战役验证）」（CLAUDE.md:7），但六件套重构过程中负面约束被稀释；按判例 7 自身逻辑，角色漂移防护弱于源体系；该缺口可被任何持判例集的第三方一键复现，构成体系信誉风险——恰是体系最忌的「知识漂移」形态发生在自己的定义层。
- **修复方向**：各定义边界约束节扩充至与正面职责数量级相当——把散落在通用纪律中的隐性负面约束（recon 的「不猜测/不凑数/不就地修复」、documenter 的「不仲裁」等）归并进边界约束节并补齐角色特异的「不做什么」清单；或在判例 7 处显式记录重构取舍（说明合并后为何放宽），二选一，不可两者皆无。

### [Low] F7 六份输入节未列「输出落盘路径」，与输出约定的依赖不匹配

- **触发场景**：编排器严格按「## 输入」节构造任务书：arch:12（设计简报+架构上下文）、impl:12（改动ID+规格+方案路径）、val:12（五项）、doc:12（四项）、adv:13（四项）均无「输出落盘路径」，而七份输出约定均要求「Write 到任务书指定路径」（如 arch:77）；手册 §4:76 审查任务书要求四项同样不含输出路径。按契约清单构造则 agent 缺落盘路径。
- **缓解**：手册 §6 对账协议要求 dispatch 时登记 expect_file（手册:99、103），实践中路径必随任务书下发；eng-recon:12 是唯一把输出落盘路径列入输入节的范式。
- **修复方向**：六份输入节补「输出落盘路径」项；手册 §4 任务书要求清单同步补。

### [Low] F8 知识引用为非落盘简称，依赖 agent 自行搜索

- **触发场景**：子 agent cwd = 目标项目根，按 adversarial-reviewer.md:33 指引读「methodology/review-patterns」——复制后实际位置 `.claude/skills/methodology/review-patterns/SKILL.md`，直接 Read 该相对路径失败，需 Glob 兜底；domain-guard.md:32「knowledge-capture references/实战判例精选.md」同样缺 `.claude/skills/` 前缀。README:44 声称「随第三步复制到目标项目内，路径自洽」——自洽的是集成段中的全路径引用（如集成段:25），agent 定义内的简称不自洽。
- **修复方向**：定义内引用统一改为复制后全路径，或统一加一句「知识引用均相对 `.claude/skills/` 解析」。

### [Low] F9 重档门禁「独占运行」声明不对称

- **触发场景**：目标项目用户完成第 1 步（复制 agents）但遗漏第 2 步（未粘集成段）、也未读手册，仅凭 agent 定义构造并发策略：domain-guard:66 自带「独占运行不并行」，而同属重档门禁的 validator 与 adversarial-reviewer 定义内无此声明——两门禁可能被并行派发，审查质量被并发稀释（手册 §7:161 自己指出的风险）。
- **修复方向**：三份门禁定义统一内嵌独占声明（与 guard:66 同句式），或统一移除内嵌、只留手册单一权威——消除不对称即消除歧义。

### [Low] F10 「极简三行」名实不符：代码块实际 4 行

- **触发场景**：按「三行」字面实现 final report 解析器（截取 3 行）会截掉 `[AGENT-OUTPUT-END]` 标记导致配对失败。手册 §3:42-47 与七份定义的回执代码块均为 4 行（START/字段/报告/END），而文案一律说「只允许以下三行」（recon:72 等）。
- **缓解**：全体系所有位置一致为 4 行，按标记截取的解析不受影响，仅文案与模板不符。
- **修复方向**：措辞改为「极简回执（包裹标记 + 字段行 + 报告行）」，或模板对齐三行说法，任选其一使名实一致。

### [Low] F11 降级触发条件大量使用全称量词，不可机械判定

- **触发场景**：术语表定义「机械锚 = 收到 X 必做动作 Y，不依赖自觉」（手册 §0:11），但七份降级触发条件大量使用「任务目标**完全**无法映射」（recon:67、arch:72）、「规格与现有代码**根本**冲突 / 规格**完全**无法理解」（impl:61）、「与实际代码结构**根本**不匹配」（guard:71）——是否「完全/根本」由 agent 裁量，同场景不同实例可能一个 DEGRADED 一个硬扛。对照良好范式：documenter:51「锚点在目标文档中不存在」、validator:62「代码 diff 为空」均为字面可判定。
- **修复方向**：触发条件优先改写为可检测条件（文件不存在/读权限失败/输入键缺失），保留的裁量项显式标注「判断性兜底」。

### [Low] F12 documenter 的 origin 三值枚举未给选择规则

- **触发场景**：documenter:33 要求草稿头部标 `origin=<review-round|reversal|wrapup>`，但 documenter 仅执行收官通道（SKILL.md:46-49），其补产草稿按语义应恒为 wrapup；三值枚举无选择规则，agent 面对源材料是审查报告时可能按「来源特征」误标 review-round（该值语义属即时通道、产出者为主对话），污染 origin 统计与呈报协议对接。
- **修复方向**：documenter 定义内明示「本 agent 产出恒 origin=wrapup」。

### [Low] F13 DETERMINISTIC_LAYER 占位符无「无第一道防线」出口

- **触发场景**：目标项目无 PreToolUse Hook / CI / 预提交钩子（小项目常态）。用户填充 domain-guard 时 `{{DETERMINISTIC_LAYER}}`（guard:38）无「填『无』」的授权选项——对照集成段:63-67 各上下文声明键均有「无特殊约束写『无』」惯例，此占位符没有；用户要么卡住、要么虚构一道不存在的防线。若填「无」，「双层防御」叙事退化为单层，模板未说明该形态下的行为差异。
- **修复方向**：占位符说明补「项目无确定性防线时填『无』，此时本 agent 独担语义层审查」。

## 三、总体合理性与可用性结论

**可用性**：复制到目标项目后基本可用——frontmatter 全部合法（name 规范唯一、model 值为 Claude Code 合法枚举、YAML 无语法错误），档位三处零偏差，输出契约与手册逐字段同构，降级路径七份齐备且姿态统一，上下文注入键实存且带未声明兜底，关键判例引用实存。不存在「复制即断链」的阻塞级缺陷。

**合理性**：六环节+守卫的正面互斥边界清晰（L10），克制清单与 README 设计决策自洽（L13）。但按对抗标准未被说服的部分集中在三点——(1) F1：作为全体系路由键的判定字段没有生成规则，「机械路由」的地基是主观裁量；(2) F4：红线域防线在模板未填充态的「保护」依赖自觉阅读而非机械锚，违背体系自己的判例 4，且 README 已对外承诺该保护；(3) F6：体系被自己随包分发的判例 7 击中，负面约束全面不等长。加上 F2（测试真空）、F3（domain-guard 输入契约缺失）、F5（同名遮蔽），共 6 项 Medium。

**结论**：判定 FAIL（H0/M6/L7）——不是不可用，而是未达到 v1.1「实战转化定稿」的自我声明水准。建议按 F1 → F4 → F3 → F6 → F2 → F5 顺序修正后重审；13 项立证基线（第一节）在修正时不应被破坏。
