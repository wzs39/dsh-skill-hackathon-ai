# hackathon-ai · 黑客松 AI 执行流程

一个给 AI agent 用的黑客松技能：把 24–48 小时从零到可演示系统的过程拆成有门禁的七段，重点是**砍需求**和**防止"看起来完成"**。

目标不是把产品做完，是 build the smallest real system that proves the idea。

## 为什么需要它

黑客松里真正杀死项目的不是技术难度，是三类系统性失真：

| 失真 | 表现 | 后果 |
|---|---|---|
| **假设当事实** | agent 把你的一句猜测写成架构决策，一路错到第 20 小时 | 推倒重来 |
| **看起来完成** | 编译绿、单测绿、agent 说 done | demo 现场挂 |
| **时间无门禁** | 一直加功能，没在关键节点停手验证 | 没有可演示的东西 |

这个技能用三个硬锚点 + 一条带门禁的时间轴 + 一套验证分档，把这三类失真挡在流程里。

## 三个硬锚点

任何决策先过这三条，冲突时按序：

1. **评委看 demo，不看架构。** 判据只有一句：`Does this make the demo better?` 答不上"是"的活儿一律不做。
2. **"看起来完成"是最大陷阱。** 编译绿 ≠ 功能在；agent 说 done ≠ done。每个完成声明必须配一个 5 秒内能复现的运行时证据。
3. **时间是硬约束。** 第 4 小时端到端通 / 第 16 小时冻结功能 / 第 20 小时录完 demo / 截止前 30 分钟提交完成。

## 七段流程

| 阶段 | 时间点 | 产出 | 门禁 |
|---|---|---|---|
| 0 · 建 rails | 开赛 15 分钟内 | `HACKATHON.md` + `docs/hackathon/` 七份文件 | 每份含 Confirmed / Assumptions / Known Gaps / Next 3 Actions |
| 1 · 赛前杠杆 | 开赛前 | Go Bag：auth、UI 骨架、部署、agent scaffold、数据层、演示兜底 | 每件"拿起来 10 分钟能跑通" |
| 2 · 想法裁决 | 第 0–1 小时 | 六维打分表（demo clarity 权重 ×2） | 总分 < 18 或 demo clarity ≤ 2 → 换想法 |
| 3 · 执行 | 第 1–16 小时 | 一条端到端主流程 | **4h 端到端通 / 16h 功能冻结** |
| 4 · 验证门禁 | 持续 | 每功能三项答复 + 验证分档 | **完成门槛是 L2** |
| 5 · Demo 与 Pitch | 第 16–20 小时 | 五段式讲稿 + 60–90s 视频 | **20h 视频录完** |
| 6 · 提交 | 截止前 | Devpost 七段 + README 七要素 | **提前 30 分钟提交** |
| 7 · 赛后 | 24h / 2–3d / 1w / 1–6m | 私信、公开、开源清理、里程碑 | 提交过的 key 必须轮换 |

## 验证分档（本技能的核心）

`阶段 4` 的判据。按风险选档，不许用低档冒充高档：

| 档 | 做法 | 能证明什么 | 不能证明什么 |
|---|---|---|---|
| **L1 自测** | 跑构建 / 类型检查 / 单测 | 代码能编译，已知用例通过 | 功能真的在，接口真的通 |
| **L2 独立复现** | 一条命令或一次真实点击，从入口走到结果 | 端到端路径真的通 | 边界、并发、他人视角的盲区 |
| **L3 换视角审查** | 另一个会话（干净上下文）审查代码与证据 | 第一个会话的盲区、隐含假设 | 运行时行为（仍需 L2） |

**完成声明的门槛是 L2。** 只有 L1 的"已完成"不算完成；L3 是补盲，不替代 L2。

技能里另附「假绿」对照表：构建通过但资产没同步、安装静默失败、单测绿但真接口不通、并行写同一状态、本地绿 CI 红 —— 每条给出真症状与防法。

## 安装

DSH 读六个技能根（`dsh-skill-filesystem/lib/index.js:149-187`），最常用的两个：

```bash
# 用户级（所有项目可用）
git clone https://github.com/wzs39/dsh-skill-hackathon-ai \
  ~/.agents/skills/hackathon-ai

# 或放进 ~/.dsh/skills
git clone https://github.com/wzs39/dsh-skill-hackathon-ai \
  ~/.dsh/skills/hackathon-ai
```

克隆下来就是技能目录本身（`SKILL.md` 在根上），不需要再套一层。

重启 DSH 或等热重载后，技能列表里会出现 `hackathon-ai`。

## 触发方式

frontmatter 里的 `description` 与 `whenToUse` 覆盖这些说法：

> 黑客松 / hackathon / 48 小时比赛 / demo day / 参赛项目 / 提交 Devpost / 打比赛做产品 / 要在极短时限内用 AI agent 从零交付可演示系统

也可以在会话里直接点名：`用 hackathon-ai 帮我排时间轴`。

## 与其他技能的关系

- **`edit-fidelity`** — 阶段 4 里「能自动判的不要靠眼睛」那一半交给它：`char_audit`（不可见字符）、`enc_audit`（编码/乱码）、`edit_guard`（截断/漏写/占位符/重复块）、`dep_check`（幻觉依赖）。赛时时间紧，机械判定优先。
- **`writing-plans` / `executing-plans`** — 阶段 0 的 rails 与阶段 3 的 sprint 可以用它们承载；本技能负责赛时特有的取舍与门禁。

## 文件结构

```
hackathon-ai/
├── SKILL.md      # 技能正文（236 行，自包含）
├── README.md     # 本文件
└── LICENSE       # MIT
```

`SKILL.md` 里提到的 `BRIEF.md` / `RULES.md` / `JUDGING.md` / `BUILD_PLAN.md` / `DEMO_SCRIPT.md` / `SUBMISSION.md` / `DECISIONS.md` **不是本仓库的文件** —— 它们是技能要求你在**目标项目**里生成的 rails 文档。

## 自包含声明

本技能不依赖任何外部 playbook。若目标项目里另有 `docs/hackathon-ai-playbook.md`，以项目内那份为准（它可能有该赛事的定制规则）；没有就按 `SKILL.md` 执行。

## 校验

`SKILL.md` 通过了 `edit-fidelity` 的四道闸：

```
verdict  PASS
  ok [1/4] char  PASS     # 无不可见字符/同形字/混合换行
  ok [2/4] enc   PASS     # UTF-8 无 BOM，无二次编码
  ok [3/4] guard PASS     # 无截断、无占位符、无重复块
  ok [4/4] deps  PASS     # 无编造依赖
```

## License

MIT
