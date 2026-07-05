# Design Studio

> **Think together. Design together. Build it yourself.**
>
> A professional software engineering design workspace — maximizing engineering thinking instead of maximizing generated code.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## 设计理念

### 软件工程本质上是设计活动

好的软件诞生于好的决策，而非大量的代码。代码只是工程决策的最终表达，**不是工程本身**。

Design Studio 不是一个编码助手，也不是自主软件工程师，更不是实现代理。它是一个**协作式工程设计工作空间**。

```
┌─────────────────────────────────────────────────────┐
│               DESIGN STUDIO 的边界                    │
│                                                     │
│   开发者拥有代码       │    AI 拥有设计对话            │
│   Developer owns      │    AI owns the              │
│   the software        │    design conversation      │
│                                                     │
│   ✗ 不写实现代码       │    ✓ 探索问题空间              │
│   ✗ 不自动重构         │    ✓ 理清需求                  │
│   ✗ 不生成 PR         │    ✓ 挑战假设                  │
│   ✗ 不替代工程判断     │    ✓ 探索权衡                  │
│                       │    ✓ 产出规格说明              │
│                       │    ✓ 制定实施路线图            │
│                       │    ✓ 审查设计一致性            │
└─────────────────────────────────────────────────────┘
```

### 核心信念

| 信念 | 含义 |
|---|---|
| **思考和代码分离** | 想清楚再写代码。设计阶段不应被实现细节干扰 |
| **质疑优于服从** | AI 应该礼貌地质疑假设，而不是机械执行指令 |
| **深度胜过速度** | 花一小时理清设计，省下十小时错误实现 |
| **开发者始终掌控** | AI 提供思考框架，决策始终由工程师做出 |
| **设计文档是可交付物** | 一份好的设计规格说明，比一千行未审查的代码更有价值 |

### 为什么不是 Copilot

| Copilot 模式 | Design Studio 模式 |
|---|---|
| "帮我写一个支付重试模块" | "我们来理清：什么算临时失败？重试几次？幂等性怎么保证？" |
| 生成 500 行代码 | 产出一份 8 页设计规格说明 + 实施路线图 |
| 你审阅 AI 的代码 | AI 审阅你的设计理解 |
| 交付物：可运行功能 | 交付物：理解和决策清晰度 |
| AI 替你干活 | AI 帮你把活干得更明白 |

---

## 工作流：四阶段设计流程

```
  DISCOVERY ────────▶ DESIGN ────────▶ PLANNING ────────▶ VALIDATION
  (理解问题)          (制定规格)        (制定路线图)         (审查实现)
       │                   │                  │                  │
  探索问题空间        产出 Design Spec    拆分为任务和里程碑   评估代码与设计一致性
  建立 Design Context  定义接口/数据/协议  映射依赖关系       找出设计漂移
  消除不确定性        做出设计决策       编写验收标准         解释为什么对齐/偏差
```

每个阶段都有对应的深度指南、模板和示例，确保质量一致性。

---

## 仓库结构

```
design-studio/
├── README.md                           ← 项目文档（你在读）
├── LICENSE                             ← MIT 许可证
├── CLAUDE.md                           ← Claude Code 引导指令
├── .claude-plugin/
│   └── plugin.json                     ← Claude Code 插件清单
├── skills/
│   └── design-studio/
│       ├── SKILL.md                    ← Skill 入口 + 协调层
│       ├── stages/                     ← 四阶段深度操作指南
│       │   ├── 1-discovery.md          ← 问题探索方法论
│       │   ├── 2-design.md             ← 设计规格说明编写指南
│       │   ├── 3-planning.md           ← 实施路线图制定方法
│       │   └── 4-validation.md         ← 设计对齐审查方法
│       ├── templates/                  ← 标准化产出模板
│       │   ├── design-context.md       ← 设计上下文（持续更新）
│       │   ├── design-spec.md          ← 设计规格说明（18 章节）
│       │   ├── implementation-plan.md  ← 实施计划
│       │   └── validation-report.md    ← 验证报告
│       ├── examples/                   ← 带注释的真实案例
│       │   ├── discovery-example.md    ← 探索对话（支付重试系统）
│       │   ├── design-spec-example.md  ← 完整规格说明示例
│       │   └── validation-example.md   ← 验证报告示例
│       └── references/                 ← 参考知识库
│           ├── engineering-principles.md  ← 12 条第一性原则
│           ├── question-bank.md           ← 140+ 个深度问题
│           ├── anti-patterns.md           ← 16 个常见设计错误
│           └── code-generation-policy.md  ← 代码生成边界规则
└── docs/                               ← 额外文档
```

---

## 安装方法

Design Studio 是一个本地 Skill，不需要上传到任何插件市场。直接从仓库安装即可。

### 方法一：Git Clone 到用户技能目录（推荐，全局可用）

```bash
# 克隆到 Claude Code 的用户技能目录，所有项目都可以使用
git clone https://github.com/<your-org>/design-studio.git /tmp/design-studio
cp -r /tmp/design-studio/skills/design-studio ~/.claude/skills/design-studio
```

### 方法二：Git Clone 到项目内（仅当前项目可用）

```bash
# 在目标项目根目录下执行
cd /path/to/your-project
mkdir -p .claude/skills
git clone https://github.com/<your-org>/design-studio.git /tmp/design-studio
cp -r /tmp/design-studio/skills/design-studio .claude/skills/design-studio
```

### 方法三：直接使用本仓库

如果你已经有本仓库的本地副本：

**Linux / Mac：**
```bash
cd /path/to/your-project
mkdir -p .claude/skills

# 复制（独立副本）
cp -r /path/to/design-studio/skills/design-studio .claude/skills/design-studio

# 或软链接（自动同步更新）
ln -s /path/to/design-studio/skills/design-studio .claude/skills/design-studio
```

**Windows（PowerShell）：**
```powershell
cd D:\workspace\your-project
New-Item -ItemType Directory -Force .claude\skills

# 复制
Copy-Item -Recurse D:\workspace\designstudio\skills\design-studio .claude\skills\design-studio
```

### 验证安装

在 Claude Code 中输入：

```
/design-studio
```

看到 Design Studio 的欢迎信息和当前阶段状态即表示安装成功。

### 安装位置说明

| 安装位置 | 作用范围 | 适用场景 |
|---|---|---|
| `~/.claude/skills/design-studio/` | 全局 — 所有项目 | 个人使用，任何项目都能调用 |
| `<project>/.claude/skills/design-studio/` | 项目级 — 仅当前项目 | 团队共享，版本控制，项目专属 |

---

## 快速上手

### 基本命令

```
/design-studio              启动或继续设计会话
/design-studio stage        查看当前阶段和进度
/design-studio context      查看当前 Design Context
/design-studio jump <stage> 跳转到指定阶段（需确认）
```

### 典型使用流程

```
1. 开发者：/design-studio
   DS：    [进入 Discovery 阶段] 你要解决什么问题？

2. 开发者：我们需要在订单系统中加入幂等性保护
   DS：    好，让我理解一下。哪些操作需要幂等？重试的来源是什么？
           是网络重试、用户双击、还是消息重复投递？
           ...（持续探索 3-5 轮）

3. DS：   [Discovery → Design] 我已理清需求，现在产出 Design Spec
           → 输出完整的设计规格说明

4. DS：   [Design → Planning] 基于设计，我已制定实施路线图
           → M1: 幂等键基础设施 → M2: API 层接入 → M3: 监控面板

5. 开发者：（实施代码后）帮我审查一下
   DS：   [进入 Validation] 对比设计和实现...
           → 发现 2 个对齐，1 个设计漂移（缺少幂等键透传），无严重问题
```

### 产出物

每次设计会话后，你会得到：

| 产出物 | 描述 |
|---|---|
| **Design Context** | 持续更新的设计上下文 — 决策、约束、风险、未知项 |
| **Design Specification** | 结构化设计规格说明 — 可直接用作实现蓝图 |
| **Implementation Plan** | 带依赖图和验收标准的实施路线图 |
| **Validation Report** | 代码与设计一致性的审查报告 |

---

## 设计哲学：为什么选择 Design Studio

### 工程时间的花费

```
传统 AI 编码助手：
  5% 理解问题  →  95% 生成代码（然后修复生成的代码）

Design Studio：
  40% 理解问题  →  40% 设计方案  →  20% 规划路线
  然后开发者以更高的清晰度和更少的返工来编写代码
```

### 思考深度 > 代码数量

| 指标 | 传统方式 | Design Studio |
|---|---|---|
| 产出 | 大量代码 | 清晰的决策 |
| 返工率 | 高（理解偏差） | 低（设计先行） |
| 知识传递 | 代码注释 | 设计文档 + 规格说明 |
| 决策可追溯性 | 散落在 PR 评论中 | 集中在 Design Context |
| 新人上手 | 读代码猜意图 | 读设计理解系统 |

---

## 许可证

MIT — 详见 [LICENSE](LICENSE)

---

> *AI should accelerate engineering thinking — not replace it.*
