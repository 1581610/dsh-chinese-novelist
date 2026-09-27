<div align="center">

# dsh-chinese-novelist

### chinese-novelist skill 的 DeepSeek Harness 插件版

[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Upstream](https://img.shields.io/badge/upstream-chinese--novelist--skill-blue)](https://github.com/PenglongHuang/chinese-novelist-skill)
[![dsh](https://img.shields.io/badge/dsh-Cordis%20Plugin-3E63DD)](https://github.com/deepseek-ai/deepseek-harness)

> **把上游 [PenglongHuang/chinese-novelist-skill](https://github.com/PenglongHuang/chinese-novelist-skill)（3.2k★，MIT）的中文小说创作 skill 封装成一个 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) Cordis 插件。**
> 18 篇创作方法论文档原样打包，随插件加载，无需手动复制目录。

</div>

---

## ⚠️ 先读这段：本仓库是什么

**本仓库不是创作方法论本身，而是一个「适配层」（adapter）。**

```
上游 chinese-novelist-skill（创作方法论，18 篇文档）  ← 内容来自 PenglongHuang
                    │
                    │  封装
                    ▼
        dsh-chinese-novelist（本仓库）                ← DSH 能装的插件
```

| 归属 | 内容 |
|---|---|
| **上游 PenglongHuang** | `skills/chinese-novelist/` 下的全部内容：`SKILL.md`、18 篇 `references/`、`scripts/check_chapter_wordcount.py` |
| **本仓库贡献** | 插件四件套（`package.json` / `cordis.patch.yml` / `src/index.ts` / `lib/index.js`）、路径重排、description 适配、README |

创作方法论、问答设计、文档正文**全部来自上游**，版权归原作者。本仓库只是让它能被 DSH 装载。

---

## 📦 安装

### 方式一：DSH 插件管理器（推荐）

在 DSH 的插件管理界面输入仓库名：

```
1581610/dsh-chinese-novelist
```

管理器会读取本仓库根目录的 `package.json` 与 `cordis.patch.yml`，自动完成安装与挂载。

### 方式二：pnpm 手动安装

```powershell
pnpm add --dir "$env:USERPROFILE\.dsh\profiles\web" github:1581610/dsh-chinese-novelist
```

然后在 `profiles/web/package.json` 的 `dsh.profile.bundles[]` 中追加：

```json
"dsh": {
  "profile": {
    "bundles": [
      "@deepseek-ai/dsh-base",
      "@deepseek-ai/dsh-web-app",
      "dsh-chinese-novelist"
    ]
  }
}
```

**注意**：`pnpm` 只会写 `dependencies`，`bundles[]` 必须手动补——不补则插件不加载。

### 方式三：本地开发

```powershell
pnpm add --dir "$env:USERPROFILE\.dsh\profiles\web" file:C:\path\to\dsh-chinese-novelist
```

**安装后需重启 dsh**，插件注册仅在启动时发生，没有热重载。

---

## 🎬 装好之后能做什么

在 DSH 中说：

```
使用 chinese-novelist 帮我写一部小说
```

插件会把 `chinese-novelist` 这个 skill 注册进 `ctx.skills`，模型可自动调用，你也可以按名字手动触发。

### 上游封装的能力

| 能力 | 说明 |
|---|---|
| **三层递进式问答** | L1 核心定位（必答）→ L2 深度定制 → L3 标题生成，支持跳过与随机生成 |
| **创作记忆** | 跨会话学习偏好：题材、叙事风格、章节数倾向、文字密度 |
| **中断续写** | 自动检测未完成项目，从断点继续 |
| **三种写作模式** | 逐章串行（`serial`）/ 子 Agent 并行（`subagent-parallel`）/ Agent Teams（`agent-teams`） |
| **自动校验修复** | 完成后检查字数与连贯性，不合格章节自动重写（最多 3 轮） |
| **悬念钩子** | 每章开头建立冲突，结尾留钩子 |

### 创作流程

```
Phase 0  初始化 ────── 加载偏好 / 检测中断
Phase 1  三层问答 ──── 核心定位 → 深度定制 → 标题
Phase 2  规划确认 ──── 大纲 + 人物档案 + 写作计划 JSON
Phase 2.5 模式选择 ─── 串行 / 并行 / Teams
Phase 3  疯狂创作 ──── 逐章执行，全程不向用户确认
Phase 4  校验修复 ──── 字数检查，不合格自动重写
```

---

## 🗂 目录结构

```
dsh-chinese-novelist/
├── package.json                    # 插件清单（dsh.bundle.patch 指向 cordis.patch.yml）
├── cordis.patch.yml                # DSH bundle 挂载声明
├── src/index.ts                    # provider 源码（TypeScript）
├── lib/index.js                    # provider 编译产物（插件实际入口）
├── LICENSE                         # MIT（与上游一致）
├── README.md                       # 本文件
└── skills/
    └── chinese-novelist/
        ├── SKILL.md                # 技能定义（路由与流程索引）
        ├── references/
        │   ├── flows/              # 8 篇阶段流程文档
        │   │   ├── phase0-initialization.md
        │   │   ├── phase1-layer1-core.md
        │   │   ├── phase1-layer2-customize.md
        │   │   ├── phase1-layer3-title.md
        │   │   ├── phase2-planning.md
        │   │   ├── phase3-writing.md
        │   │   ├── phase4-validation.md
        │   │   └── shared-infrastructure.md
        │   └── guides/             # 9 篇创作技法文档
        │       ├── chapter-guide.md
        │       ├── chapter-template.md
        │       ├── character-building.md
        │       ├── character-template.md
        │       ├── content-expansion.md
        │       ├── dialogue-writing.md
        │       ├── hook-techniques.md
        │       ├── outline-template.md
        │       ├── plot-structures.md
        │       └── title-guide.md
        └── scripts/
            └── check_chapter_wordcount.py   # 章节字数校验
```

---

## 🔧 技术实现

### provider 注册机制

DSH 加载 skill 有两条路：把 `SKILL.md` 编进 `app.asar`（内置技能走这条），或者**由插件调用 `ctx.skills.registerProvider()` 注册**。本插件走后者。

```typescript
export const name = 'chinese-novelist'
export const inject = ['skills']          // 声明依赖 DSH 的 skills 服务

export function apply(ctx: Context): void {
  ctx.skills.registerProvider(() => provider)
}
```

`provider` 提供两个方法：

- `list()` — 递归扫描 `skills/` 树，返回所有候选技能
- `get(candidate)` — 按需返回某个 `SKILL.md` 的完整正文

**递归扫描**意味着嵌套子技能也能被发现。本仓库目前只有 1 个 `SKILL.md`，所以注册 1 个技能；若日后在 `skills/` 下新增子目录并各放一份 `SKILL.md`，会自动一起注册，无需改动 provider 代码。

### 三处适配改动

转换过程中对上游内容做了三处非功能性调整：

| 改动 | 原因 |
|---|---|
| **`description` 拍平为单行** | 上游用 YAML 块标量（`description: \|`）写多行描述。DSH 的 frontmatter 解析器是简单正则 `^([A-Za-z0-9_-]+):\s*(.*)$`，遇到块标量只取到空串，会丢掉整段描述。故合并为单行。 |
| **目录重排至 `skills/chinese-novelist/`** | provider 扫描根是 `skills/`，且 `resourceBase` 取 `dirname(SKILL.md)`。把 `SKILL.md` + `references/` + `scripts/` 整体平移后，包内所有相对链接（`references/flows/*.md`、`../guides/*.md`）**无需重写**即可继续生效。 |
| **一处措辞中性化** | `SKILL.md` 原文提到「Claude Code 多 Agent 协作模式」，改为「Agent 多 Agent 协作模式」，避免在 DSH 插件中点名其他 CLI。 |

**除此之外，上游文档内容逐字未改。**

---

## ⚠️ 已知限制

- **`metadata.trigger` / `metadata.source` 不会被解析** — DSH 的解析器只读 `name` / `description` / `user-invocable` 三个字段，其余字段作为技能正文一并返回。上游这两个字段保留在 frontmatter 中，但不会被用于触发匹配。
- **`user-invocable` 默认为 `true`** — 上游未声明该字段，按 DSH 解析逻辑默认允许用户手动调用。
- **字数校验脚本需要 Python** — `scripts/check_chapter_wordcount.py` 是 Python 脚本，运行前需确保环境有 Python。不运行它不影响创作流程，只影响自动字数检查。
- **`assets/` 截图未包含** — 上游仓库的 `assets/` 目录（流程图截图）未纳入本插件，因为它与技能运行时无关。需要看截图请访问上游仓库。
- **写作模式中的「Agent Teams」依赖宿主能力** — 该模式的可用性取决于 DSH 本身是否支持多 Agent 协作。

---

## 🙏 致谢

本项目基于以下开源项目：

- **[PenglongHuang/chinese-novelist-skill](https://github.com/PenglongHuang/chinese-novelist-skill)** — 中文小说创作 skill，3.2k★，MIT License，Copyright (c) 2026 PenglongHuang

创作方法论、三层问答设计、18 篇技法文档、字数校验脚本均来自上游作者。请给原作者点星。

---

## 📄 License

**MIT License**，与上游保持一致。

```
Copyright (c) 2026 PenglongHuang        # 上游内容
```

上游内容的版权归原作者所有；本仓库的适配层代码（`src/`、`lib/`、插件四件套与 README）由 GitHub 用户 [1581610](https://github.com/1581610) 整理封装。

---

<div align="center">

**README 由 [Little Code Sauce](https://github.com/1581610) 撰写**

<sub>基于 DSH 插件契约与上游仓库的实际结构核对后编写 · 2026-09-27</sub>

</div>
