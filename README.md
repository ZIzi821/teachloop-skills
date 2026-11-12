# teachloop-skills

把实际教学产品中形成的设计判断，整理成可以继续使用的 AI skills。

本地工作根目录为 `F:\Skills`。每个 skill 使用一个独立子目录，目录中以 `SKILL.md` 为入口；以后新增技能沿用相同结构。

## 当前技能

| Skill | 用途 | 入口 |
| --- | --- | --- |
| `teacher-led-learning-design` | 教师主导的语言学习产品设计与迭代：学习证据、角色职责、下一步行动和个性化复习 | [SKILL.md](teacher-led-learning-design/SKILL.md) |

## 使用

让支持 skill 文件的助手读取目标目录的 `SKILL.md`，再提出具体任务。当前可以直接使用：

```text
请读取 F:\Skills\teacher-led-learning-design\SKILL.md，
按它的方法设计一个帮助老师处理课后录音的页面。
先给出角色、关键状态、页面内容和处理后的去向。
```

安装到所用工具的技能发现目录后，可以使用 `$teacher-led-learning-design` 调用。此仓库保存技能源文件；是否已安装和当前工具能否发现，要以实际环境为准。

核心方法和项目适配分别存放。用于其他教学产品时，沿用其本身的代码、身份和发布约定；只有明确匹配原小程序时才加载其项目契约。

## 验证与维护

[评估目录](evaluations/teacher-led-learning-design/) 保存场景与本次验证结果，和技能本体分开维护。入口、YAML、引用文件、公开内容检查与行为评估承担不同验证目的。

更新 skill 时修改拥有对应规则的文件，避免复制完整交接和重复维护常量。使用合成示例，保留数据来源及授权边界，不收录真实学生记录或私密配置。

GitHub 仓库采用 `teachloop-skills` 这个名称。远端创建、可见性和上传状态另行记录，不由本地文件存在推断。
