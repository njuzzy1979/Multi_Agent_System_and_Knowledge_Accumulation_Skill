# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目性质（先读这个）

这不是可执行代码项目，而是一套**可移植的 Claude Code 配置资产集**（v1.0）：7 个多 agent 工程管线定义 + 知识沉淀 skill 体系，设计为复制进任何目标项目的 `.claude/` 目录使用（三步接入见 [README.md](README.md)）。

- **没有 build / lint / test 命令**——产物是 markdown 资产本身
- 唯一代码：[六件套管线手册.md](skills/knowledge-capture/references/六件套管线手册.md) §6 内嵌的 `agent_reconcile.py`（分派对账脚本，纯标准库；供目标项目提取使用，不是本仓库的工具）
- 非 git 仓库；全部文件为 UTF-8 中文 markdown

## 架构大图景（需跨文件理解的部分）

### 分发模型

```text
agents/*.md                → 目标项目 .claude/agents/    （即复制即用；domain-guard 须先填占位符）
skills/knowledge-capture/  → 目标项目 .claude/skills/    （知识沉淀机制总纲）
skills/methodology/        → 目标项目 .claude/skills/    （首批领域 SOP 三件）
```

知识库根 = 目标项目 `.claude/skills/` 整体——总索引 README 与 drafts/ 建在 skills/ 根下，不设库中库（规则见 [索引与路由模板.md](skills/knowledge-capture/references/索引与路由模板.md) §〇）。

### 六件套管线（agents/ 七个定义间的关系）

①侦查（eng-recon ×N 并行，tech/data/research 三子模式）→ ②设计（eng-arch，复杂战役才走）→ ③对抗审查（adversarial-reviewer，先立后破）→ ④实施（eng-implementer，模仿铁律）→ ⑤交叉验证（eng-validator，MISSING/OVERSTEP/DEVIATION 三分法）→ ⑥归档（eng-documenter，锚点写入 + 提炼机械路由）。任何阶段发现触碰红线域 → domain-guard 强制插入（不因改动量小降档）。

**编排器是主对话的职能（depth-0），不是可分派的子 agent**——编排器剧本 agent 形态已被实战退役（嵌套委托困境）。

### 三大跨切面机制（全部 agent 定义共享的契约）

1. **输出契约（落盘即遗忘）**：子 agent 详情 Write 到战役目录，final report 只允许极简三行（`[AGENT-OUTPUT-START]` 包裹的判定字段 + 报告路径）。编排器按判定机械路由：PASS/DONE→下一环节、FAIL→回炉、DEGRADED→人工。改输出格式前先问：**内容到底进不进主对话上下文？**
2. **分派对账**：dispatch/done/expect/check 四事件 JSONL 协议；完成判定**锚产物文件存在性，不锚自述**
3. **知识沉淀闭环**：四模板（修复/设计/审查/认知）按来源特征机械路由；双时机提炼（即时通道 origin=review-round|reversal + 收官通道 sop_hint）；drafts/ 人工采纳门禁（未采纳零污染）；机械锚（收到 X 必做动作 Y，不依赖自觉）

### 档位体系

agent frontmatter 的 `model: sonnet|opus` 是轻/重档建议：判断密集环节（设计/审查/验证/红线守卫）重档，执行密集环节（侦查/实施/归档）轻档；domain-guard 重档且独占运行不并行。

## 修改本仓库的纪律（体系自身规则约束编辑行为）

1. **单一权威源（三类分治）**：索引/行数/滚动类数值唯一权威=知识库总索引 README 维护规则节（本仓库分发态由[索引与路由模板.md](skills/knowledge-capture/references/索引与路由模板.md) §二承载）；知识诞生门槛唯一权威=knowledge-capture SKILL.md Phase B 节；管线运行类数值（并行上限/重试次数）唯一权威=[CLAUDE.md集成段.md](skills/knowledge-capture/references/CLAUDE.md集成段.md) 硬性规则——其余任何位置单向引用**不自报数字**。历史判例：曾有体系因两处数字不一致（18 vs 10）实测翻车
2. **[domain-guard.md](agents/domain-guard.md) 保持模板态**：正文 `{{占位符}}` 不可填充；触发点清单唯一权威位在目标项目 CLAUDE.md，本模板不维护副本（防分叉过期）
3. **元信息块四字段同构**：每个 SKILL.md 尾部 `状态 | 最后验证 | 适用版本 | 源材料` 不可缺；实质性修改机制后更新「最后验证」与文件头部的版本标记（`+ 版本 v1.0`）
4. **交叉引用一致性**：机制规则散布于 knowledge-capture SKILL.md 总纲 ↔ references/（五文件 + README 登记表） ↔ README ↔ 集成段——改一处必须 Grep 全库核对其余引用点同步（防体系自身批判的知识漂移/契约冲突）
5. **adversarial-reviewer 随包分发是设计决策**：管线③直接引用它，不随包则接入后首次派发即断链；同名双轨时项目版优先
6. **克制清单（不可违反）**：不引入外部框架（无 LangChain/LangMem 依赖）、不建向量知识库（markdown + grep + 索引优于 RAG）、不做全自动采纳（人工门禁是安全机制）、新增 agent 角色必须有调用痕迹预期
7. 新增/修改 skill 必须遵循 knowledge-capture [SKILL.md](skills/knowledge-capture/SKILL.md) 的三级渐进披露规范（L1 description 窄触发、L2 正文行数上限、L3 references/ 带 README 登记表）

## 改完资产后的自查

- **引用实存**：改动涉及 agent/skill/文件名时，Grep 全库确认无断链（review-patterns 的知识漂移核查）
- **占位符清点**：`{{` 只应出现在 [domain-guard.md](agents/domain-guard.md)、[CLAUDE.md集成段.md](skills/knowledge-capture/references/CLAUDE.md集成段.md) 与 README.md 的接入说明中（索引与路由模板的数值占位用 `<...>` 尖括号）
- **数值一致性**：全库 Grep 具体数字（行数上限/阈值），确认只在权威源出现
