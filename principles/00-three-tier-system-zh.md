# 0. 三级 CLAUDE.md 体系

[English version](00-three-tier-system.md)

## 为什么需要

大多数项目最初只有一个放在项目根目录的 `CLAUDE.md`。它在你只有一个项目时够用，但当你有了多个项目，或者发现有些规则适用于所有地方（编码风格、语言习惯）而另一些只适用于某个项目（构建命令、架构）时，就力不从心了。

三级体系把关注点分离开：通用规则只写一次，项目特定规则保持精简。

## 三个层级

| 层级 | 位置 | 作用范围 | 示例 |
|------|------|---------|------|
| **用户级** | `~/.claude/CLAUDE.md` | 所有项目、所有工作区 | 编码风格、语言偏好、行为准则 |
| **工作区级** | `<workspace>/.claude/CLAUDE.md` | 此工作区下的所有项目 | 覆盖输出路径、共享约定 |
| **项目级** | `<project>/.claude/CLAUDE.md` | 仅此项目 | 构建命令、架构、项目专属约定 |

## 它们如何交互

Claude Code 加载所有适用的 CLAUDE.md 文件并合并它们。基本原则：

- **用户级**提供默认值 —— 适用于所有地方的通用行为准则。
- **工作区级**覆盖或扩展用户级规则中工作区有不同需求的部分。例如，多项目工作区可以将输出路径固定为共享的 `Claude_Code_Files/` 目录，而非每个项目各自创建。
- **项目级**添加项目专属指令 —— 技术栈、构建命令、架构说明。

当同一条规则出现在多个层级时，更具体的层级胜出：项目级覆盖工作区级，工作区级覆盖用户级。

## 决策框架

当需要添加新规则时，问自己：

```
这条规则适用于所有项目吗？
├── 是 → 放入用户级 ~/.claude/CLAUDE.md
└── 否 → 它适用于此工作区下的所有项目吗？
    ├── 是 → 放入工作区级 <workspace>/.claude/CLAUDE.md
    └── 否 → 放入项目级 <project>/.claude/CLAUDE.md
```

## 实战案例：Rule 6（输出工作区）

**之前** —— Rule 6 存在于项目级 CLAUDE.md 中：

```markdown
## 6. Output Workspace
- `Claude_Code_Files/` 是专用目录……
```

这在一个项目时工作正常，但引发了困惑：每个项目都创建了自己的 `Claude_Code_Files/` 文件夹，而用户实际上希望所有制品放在 `D:\MYPROJS\Claude_Code_Files\`。

**之后** —— Rule 6 被提升到用户级，并加入了委托机制：

用户级 `~/.claude/CLAUDE.md`：
```markdown
## 6. Output Workspace
- 默认输出路径为 `<workspace-root>/Claude_Code_Files/`。
  如果工作区级 `.claude/CLAUDE.md` 指定了自定义路径，则使用该路径。
- 每次会话创建带日期的子文件夹：`YYYYMMDD-short-description`。
```

工作区级 `D:\MYPROJS\.claude\CLAUDE.md`：
```markdown
## Output Workspace (Override Rule 6)
输出目录固定为：D:\MYPROJS\Claude_Code_Files\
```

**结果**：全部九条行为规则现在驻留在用户级文件中（跨所有工作区共享）。工作区级文件只有 18 行、一条覆盖。`D:\MYPROJS` 下的所有项目将制品写入同一个共享目录。

## 与 .claude/rules/ 和 auto memory 的关系

三级 CLAUDE.md 体系是更大指令架构的一部分：

| 组件 | 谁写 | 粒度 | 最适合 |
|------|------|------|--------|
| 用户级 CLAUDE.md | 你 | 所有项目 | 通用行为规则 |
| 工作区级 CLAUDE.md | 你 | 工作区下所有项目 | 共享路径/配置覆盖 |
| 项目级 CLAUDE.md | 你 | 单个项目 | 构建命令、架构 |
| `.claude/rules/*.md` | 你 | 项目中按路径匹配 | 语言特定或目录特定的规则 |
| Auto memory | Claude | 按项目积累 | 构建命令、调试经验、偏好发现 |

## 从单文件 CLAUDE.md 迁移

如果你目前有一个庞大的项目级 CLAUDE.md，迁移路径如下：

1. **识别通用规则。** 关于编码风格、简洁性、语言习惯、通用行为的规则属于用户级。
2. **识别工作区覆盖。** 如果你的工作区有自定义输出路径、共享约定或全局配置，放入工作区级。
3. **保留项目特定规则。** 构建命令、技术栈细节、架构说明留在项目级。
4. **拆分路径作用域规则。** 仅适用于 `src/api/` 或 `*.test.ts` 的规则应迁移到 `.claude/rules/` 中加上 `paths:` frontmatter。
5. **验证。** 运行 `brain-admin diagnose` 检查冲突、过时引用和大小限制。
