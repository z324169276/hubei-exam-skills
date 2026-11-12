# 湖北技能高考 · 计算机类 Agent Skills

面向 **中职技能高考（计算机类）** 教师的三个 Agent Skill，依据 [湖北省 2026 年技能高考计算机类考试大纲](https://www.hbea.edu.cn) 整理：9 个模块、103 小题、490 分、120 分钟。把 AI 编程助手变成懂湖北考纲的出题老师、组卷老师和阅卷老师。

本仓库提取自开源项目 [hubei-cs-exam-coach](https://github.com/z324169276/hubei-cs-exam-coach) 的考纲数据与判分逻辑，做成纯提示词 Skill——不需要数据库、不需要跑服务，任何支持 Agent Skills 的助手都能直接用。

## Skill 一览

| Skill | 用途 | 适合场景 |
| --- | --- | --- |
| [`hubei-exam-author`](./hubei-exam-author/) | 按考纲出题：单选 / 判断 / 操作三类题，带知识点编号与解析 | 日常练习题、专题训练、错题变式 |
| [`hubei-exam-paper`](./hubei-exam-paper/) | 按真实卷面结构整卷组卷：9 模块 103 小题 490 分 | 模拟考、月考整卷 |
| [`hubei-exam-grader`](./hubei-exam-grader/) | 按考纲规则判分 + 八类错因标签 + 复习建议 | 批改答卷、错因统计、讲评准备 |
| [`hubei-exam-diagnose`](./hubei-exam-diagnose/) | 按知识点依赖图谱做学情诊断：掌握度 → 回溯错因根源 → 补测与复习顺序 | 学情分析、找病根、制定补差计划 |

四个 Skill 共享同一份考纲数据源（内置在各自 `references/` 中），知识点编号（如 `5.4 常用函数`）在出题、组卷、判分、诊断之间完全对齐。诊断额外使用 `references/graph.md` 中的 65 条前置依赖边。

## 安装与多平台分发

### Claude Code

```bash
# 全局（对所有项目生效）
git clone https://github.com/z324169276/hubei-exam-skills.git
cp -r hubei-exam-skills/hubei-exam-author ~/.claude/skills/

# 或项目级（只对当前项目生效）
cp -r hubei-exam-skills/hubei-exam-author .claude/skills/
```

### Trae

```bash
cp -r hubei-exam-skills/hubei-exam-author .trae/skills/
```

也可在 Trae 的「技能管理中心」通过本地上传方式导入对应技能目录。

### SkillHub / WorkBuddy 技能市场

三个 Skill 均为标准 Agent Skill 格式（`SKILL.md` + `references/`），可直接上传到 SkillHub、WorkBuddy 技能市场等支持该格式的平台：

1. 把对应技能目录压缩为 zip（zip 内顶层为技能文件夹，如 `hubei-exam-author/SKILL.md`）。PowerShell 一行打包：

```powershell
Compress-Archive -Path hubei-exam-author -DestinationPath hubei-exam-author.zip
```

2. 在目标平台网页端上传 zip，按提示填写名称、描述、分类后提交审核。

### DeepSeek Harness（DSH）

本仓库同时是标准 DSH 插件：根目录 `package.json` 声明 `dsh.bundle` manifest，`cordis.patch.yml` 复用 DSH 内置的文件系统 skill 提供方（`@deepseek-ai/dsh-skill-filesystem`）注册三个技能，安装后即可在 dsh 里直接使用。安装命令：

```sh
dsh plugin --profile web add github:z324169276/hubei-exam-skills
```

本插件已提交 [awesome-dsh-plugin](https://github.com/awesome-dsh-plugin/awesome-dsh-plugin) 精选列表，合并后会自动同步到 dsh 插件市场（**插件 → 插件市场**，搜索 `hubei-exam-skills` 一键安装）。

四个 Skill 独立安装，按需取用；判分与诊断建议跟出题一起装，形成「出题 → 组卷 → 判分 → 诊断」闭环。

## 使用示例

安装后直接对 AI 助手说：

```text
出 10 道 WPS 表格函数的单选题，知识点 5.4，难度中等偏难
组一份完整模拟卷，先出模块 1 的前 20 题
批改下面这份答卷……（贴入答案）
诊断一下张三这份答卷，问题到底出在哪几章
```

Skill 会自动触发，按考纲约束输出结构化结果（JSON 或 Markdown）。

## 考纲数据

`references/syllabus.md` 由 [hubei-cs-exam-coach](https://github.com/z324169276/hubei-cs-exam-coach) 的 `data/knowledge.json` 生成，覆盖：

- 模块 1 计算机基础知识（50 题单选 · 150 分）
- 模块 2–9 操作系统 / 网络 / WPS 三件套 / C99 / MySQL / Photoshop CS6（操作题）

判分规则内置了操作题的**三档评分锚点**（1 / 0.5 / 0）与**八类错因标签**（概念混淆、审题不清、计算失误、记忆不牢、步骤遗漏、参数错误、操作顺序错误、不会做），保证不同学生、不同批改会话得到一致的判分口径。

诊断规则把答卷换算成知识点掌握度（≥0.8 掌握 / 0.6–0.8 待巩固 / <0.6 薄弱），再沿前置边算 `(0.8 − 上游掌握度) × 边强度` 作为拖累贡献，按贡献度排序给出病根。上游知识点没有作答记录时**不给结论**，只列为"待补测"——宁可不猜，也不给假诊断。

## 为什么做成 Skill 而不是应用

- **零依赖**：不需要 API Key、不需要服务端，纯指令文件
- **可组合**：出题与判分可以分别装给不同用途
- **可审计**：所有规则都是明文 Markdown，教师可以直接改

如果你需要完整的 Web 工作台（题库管理、打印、学生在线答题、班级错题统计），请使用 [hubei-cs-exam-coach](https://github.com/z324169276/hubei-cs-exam-coach)。

## 免责声明

考纲知识点依据 2026 年湖北省教育考试院公开发布的考试大纲整理；AI 生成内容可能存在错误，正式备考请以官方考纲与教材为准。

## License

[MIT](./LICENSE)
