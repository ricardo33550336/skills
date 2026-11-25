# odd-verify

一个面向 AI 编码 Agent 的 **Skill**：把 ODD（Oracle-Driven Development，Oracle 驱动开发）框架变成可执行的操作纪律。

**一句话**：当写代码的边际成本趋零，"知道什么算对"成为唯一稀缺资源。没有 oracle（判定对错的机制）的代码不许静默进入主路径。

## 它解决什么问题

AI 生成代码很快，但常见失败模式同样稳定：

- 盲迭代：没有判定标准就反复改实现，改到"看起来能跑"
- 假绿：放任过期断言、过期录制通过
- 宽依赖裸奔：插件直连新 SDK，超时/重试/错误分类各处各写
- 只验代码不验口径：类型全过，但数据口径（金额、行数、主键）错了
- 把失败一律归因成"实现 bug"，逃避 oracle 缺失或过期的问题

这个 skill 用**硬门禁 + 分层验货 + 强制归因**把这些兜住。

## 内容

```
SKILL.md                       # 主流程：0 识别项目类型 → 1 改动半径 → 2 Oracle 存在性
                               #        → 3 分层路由 → 4 红/绿/灰/金 → 5 输出纪律
references/brownfield-playbook.md   # 存量项目路线：依赖地图、边界收敛、绞杀者迁移、行为等价
references/greenfield-playbook.md   # 增量项目路线：契约先行、CI 骨架先行、oracle 渐进扩展
docs/ODD框架-v2-优化版.md            # 框架全貌：四个支柱（知识层 / 验货层 / 双侧契约 / 飞轮案例）
```

## 核心机制

| 机制 | 作用 |
|---|---|
| 改动半径表 | 明确 AI 可自主改（adapter 内部、core 纯逻辑、快层断言）与必须停下叫人（契约/schema、跨层重构、对依赖方提要求） |
| Oracle 存在性检查 | 按 显式断言 > 旧实现差分 > 蜕变关系 的优先级确认判定机制；都没有则先写 oracle，或标 `exploratory: true` + 到期时间 |
| 分层验货 | 快层（pyright/ruff/pytest/冒烟）→ 伪慢层（VCR 回放、testcontainers、差分）→ 真慢层（影子运行、对账、契约探针） |
| 红绿灰金 + 归因 | 每层失败必须归入：oracle 缺失 / oracle 过期 / 样本过期 / 实现 bug；前两类必须写回 oracle 注册表 |
| Oracle 元数据 | `id / assertion / source / owner / decay_condition / last_verified / confidence / exploratory`，防止 oracle 悄悄腐化 |

## 安装

作为 pi（或兼容 `.agents/skills` 约定的 Agent）的 skill 使用：

```bash
# 全局安装（所有项目可用）
git clone https://github.com/ricardo33550336/odd-verify.git ~/.pi/agent/skills/odd-verify

# 或项目内安装（随项目一起提交，团队共享）
git clone https://github.com/ricardo33550336/odd-verify.git .agents/skills/odd-verify
```

## 使用

在会话里直接提与验货相关的问题即可触发，例如：

- "给这个数据管线插件加一个熔断重试" → 先走改动半径 + oracle 存在性检查
- "这个 adapter 改完了，帮我过一遍验货" → 按分层路由给出每层结果
- "老插件迁移到新 SDK" → 走 brownfield playbook（旧实现即 oracle，行为等价优先）
- "新插件从零写起" → 走 greenfield playbook（契约 + CI 骨架先行）

## 适用 / 不适用

- **适用**：Python / AI Flow / Go 混合的数据管线、插件、adapter、数据转换工程；存量与增量都覆盖
- **不适用**：一次性脚本、整个项目都处于探索期的原型

## 配套阅读

- 框架全貌：[`docs/ODD框架-v2-优化版.md`](docs/ODD框架-v2-优化版.md)
- 存量路线：[`references/brownfield-playbook.md`](references/brownfield-playbook.md)
- 增量路线：[`references/greenfield-playbook.md`](references/greenfield-playbook.md)
