# 湖北技能高考 · 计算机类 Agent Skills

面向 **中职技能高考（计算机类）** 教师的三个 Agent Skill，依据 [湖北省 2026 年技能高考计算机类考试大纲](https://www.hbea.edu.cn) 整理：9 个模块、103 小题、490 分、120 分钟。把 AI 编程助手变成懂湖北考纲的出题老师、组卷老师和阅卷老师。

本仓库提取自开源项目 [hubei-cs-exam-coach](https://github.com/z324169276/hubei-cs-exam-coach) 的考纲数据与判分逻辑，做成纯提示词 Skill——不需要数据库、不需要跑服务，任何支持 Agent Skills 的助手都能直接用。

## Skill 一览

| Skill | 用途 | 适合场景 |
| --- | --- | --- |
| [`hubei-exam-author`](./hubei-exam-author/) | 按考纲出题：单选 / 判断 / 操作三类题，带知识点编号与解析 | 日常练习题、专题训练、错题变式 |
| [`hubei-exam-paper`](./hubei-exam-paper/) | 按真实卷面结构整卷组卷：9 模块 103 小题 490 分 | 模拟考、月考整卷 |
| [`hubei-exam-grader`](./hubei-exam-grader/) | 按考纲规则判分 + 八类错因标签 + 复习建议 | 批改答卷、错因统计、讲评准备 |

三个 Skill 共享同一份考纲数据源（内置在各自 `references/` 中），知识点编号（如 `5.4 常用函数`）在出题、组卷、判分之间完全对齐。

## 安装

### Claude Code

```bash
# 全局（对所有项目生效）
git clone https://github.com/<your-name>/hubei-exam-skills.git
cp -r hubei-exam-skills/hubei-exam-author ~/.claude/skills/

# 或项目级（只对当前项目生效）
cp -r hubei-exam-skills/hubei-exam-author .claude/skills/
```

### Trae

```bash
cp -r hubei-exam-skills/hubei-exam-author .trae/skills/
```

三个 Skill 独立安装，按需取用；判分建议与出题一起装，形成「出题 → 组卷 → 判分」闭环。

## 使用示例

安装后直接对 AI 助手说：

```text
出 10 道 WPS 表格函数的单选题，知识点 5.4，难度中等偏难
组一份完整模拟卷，先出模块 1 的前 20 题
批改下面这份答卷……（贴入答案）
```

Skill 会自动触发，按考纲约束输出结构化结果（JSON 或 Markdown）。

## 考纲数据

`references/syllabus.md` 由 [hubei-cs-exam-coach](https://github.com/z324169276/hubei-cs-exam-coach) 的 `data/knowledge.json` 生成，覆盖：

- 模块 1 计算机基础知识（50 题单选 · 150 分）
- 模块 2–9 操作系统 / 网络 / WPS 三件套 / C99 / MySQL / Photoshop CS6（操作题）

判分规则内置了操作题的**三档评分锚点**（1 / 0.5 / 0）与**八类错因标签**（概念混淆、审题不清、计算失误、记忆不牢、步骤遗漏、参数错误、操作顺序错误、不会做），保证不同学生、不同批改会话得到一致的判分口径。

## 为什么做成 Skill 而不是应用

- **零依赖**：不需要 API Key、不需要服务端，纯指令文件
- **可组合**：出题与判分可以分别装给不同用途
- **可审计**：所有规则都是明文 Markdown，教师可以直接改

如果你需要完整的 Web 工作台（题库管理、打印、学生在线答题、班级错题统计），请使用 [hubei-cs-exam-coach](https://github.com/z324169276/hubei-cs-exam-coach)。

## 免责声明

考纲知识点依据 2026 年湖北省教育考试院公开发布的考试大纲整理；AI 生成内容可能存在错误，正式备考请以官方考纲与教材为准。

## License

[MIT](./LICENSE)
