# Skills

个人 AI 编码 Agent 的 Skill 合集（面向 pi / 兼容 `.agents/skills` 与 `~/.pi/agent/skills` 约定）。

每个 skill 一个目录，内含 `SKILL.md`（带 `name` / `description` frontmatter）+ 可选的 `references/`、`docs/`、脚本资源。

## 目录

| Skill | 作用 | 适用场景 |
|---|---|---|
| [`odd-verify`](skills/odd-verify/) | ODD（Oracle 驱动开发）验货门禁：改动半径检查、oracle 存在性检查、快层/伪慢层/真慢层分层验货、红绿灰金与逃逸归因 | AI 生成或修改数据管线插件、adapter、数据转换代码时（Python / AI Flow / Go） |

```
skills/
└── odd-verify/
    ├── SKILL.md                          # 主流程：0 识别项目类型 → 1 改动半径 → 2 oracle 存在性
    │                                     #        → 3 分层路由 → 4 红/绿/灰/金 → 5 输出纪律
    ├── references/brownfield-playbook.md # 存量项目：依赖地图、边界收敛、绞杀者迁移、行为等价
    ├── references/greenfield-playbook.md # 增量项目：契约先行、CI 骨架先行、oracle 渐进扩展
    └── docs/ODD框架-v2-优化版.md          # 框架全貌：四个支柱
```

## 安装

整个合集一次性安装：

```bash
# 全局（所有项目可用）
git clone https://github.com/ricardo33550336/skills.git /tmp/skills
cp -R /tmp/skills/skills/* ~/.pi/agent/skills/

# 或项目内（随项目提交，团队共享）
mkdir -p .agents/skills
cp -R /tmp/skills/skills/* .agents/skills/
```

只装某一个：

```bash
git clone --depth 1 https://github.com/ricardo33550336/skills.git /tmp/skills
cp -R /tmp/skills/skills/odd-verify ~/.pi/agent/skills/odd-verify
```

> 也可以直接把本仓库 clone 到 `~/.pi/agent/skills`？**不要**——那样会多一层 `skills/` 嵌套导致 Agent 发现不到。请按上面拷贝单个目录（或用软链 `ln -s`）。

## 新增一个 skill

1. `mkdir -p skills/<name>`，写 `skills/<name>/SKILL.md`：

   ```markdown
   ---
   name: <name>              # 与目录名一致，小写连字符
   description: <一句话说明何时该用这个 skill，含触发场景与不适用场景>
   ---

   # 标题
   <可执行流程，而非泛泛原则>
   ```

2. 重内容拆到 `references/`（按分支/场景），大背景文档放 `docs/`，脚本放 `scripts/`。
3. 在根 `README.md` 的「目录」表里加一行。
4. 提交；在目标机器上重新拷贝该目录即可生效。

## 约定

- `description` 决定 Agent 是否加载该 skill，**必须写清触发条件**（什么时候用、什么时候别用）。
- 每个 `SKILL.md` 自身是完整可执行流程，`references/` 只在主流程需要时按需读取。
- skill 内部引用其它文件用**相对 skill 目录**的路径。
