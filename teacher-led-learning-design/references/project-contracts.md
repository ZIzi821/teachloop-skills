# 原微信教学小程序适配

本参考记录现有项目契约。先确认目标仓库具有 `miniprogram/app.json`、`cloudfunctions/quickstartFunctions/` 及匹配的业务模块，再使用此参考。其他项目沿用自己的技术和数据模型。

## 事实来源与代码地图

当前用户要求优先。实现查拥有规则的源码与测试；历史原因查最新交接及追加记录。`HANDOFF-CODEX-CLAUDE.md` 是原项目交接入口，两个日期版交接用于追溯。交接可能被 Git 忽略，克隆代码后缺少它们不妨碍读取本参考并检查代码。

| 需要处理的领域 | 相对于目标仓库的路径 |
| --- | --- |
| 页面、分包与路由 | `miniprogram/app.json` |
| 云端权限与业务入口 | `cloudfunctions/quickstartFunctions/index.js` |
| 学生下一步与首页 | `miniprogram/utils/studentLearningFlow.js`、`miniprogram/pages/index/` |
| 课程、任务分组与课程量 | `cloudfunctions/quickstartFunctions/courseWorkflow.js`、`miniprogram/pages/lesson-detail/` |
| 共用课程表单与选项 | `miniprogram/components/lesson-task-fields/`、`miniprogram/utils/lessonTaskOptions.js` |
| 教师待办与录音反馈 | `teacherTodos.js`、`recordingFeedback.js`、`miniprogram/pages/teacher-todos/` |
| 学习目标、证据与阶段 | `learningRadar.js`、`data/learning-plans.json`、`miniprogram/components/learning-plan/` |
| 英语词库、小测、记忆 | `vocabulary.js`、`vocabularyQuiz.js`、`englishMemoryScheduler.js`、`englishMemoryAnalytics.js` |
| 学生记忆校准界面 | `miniprogram/insights/pages/memory-evaluation/` |
| 学生练习与音频 | `miniprogram/pages/vocabulary-study/`、`miniprogram/utils/vocabularyAudio.js`、`reliableAudio.js` |
| 跟读与 KET | `followReading*.js`、`ketMock*.js`、`miniprogram/follow-reading/`、`miniprogram/ket-mock/` |
| 错题导入与导出 | 云端 `index.js` 中的错题操作、`mistakeExport*.js`、`miniprogram/pages/batch-import-mistakes/` |
| 课程资料与本机助手 | `materialUpload.js`、`tools/local-material-uploader/`、`tools/local-mistake-importer/` |
| 日程与单次提醒 | `schedule*.js`、`miniprogram/schedule/`、`cloudfunctions/lessonReminder/` |
| 积分、动态与 PK | `studentEngagement.js`、`miniprogram/engagement/` |
| 法语 | `frenchLearning.js`、`frenchReviewEngine.js`、`frenchAnalytics.js`、`data/french-*.json` |

表中未带目录的云端 JS、`data/` 和 `tests/` 位于 `cloudfunctions/quickstartFunctions/`。使用函数名和业务名定位当前代码，避免依赖过期行号。

## 身份、开关与预览

学生通过访问码绑定，教师身份由云端判断。学生数据与写入再次验证归属；隐藏按钮不能替代服务端校验。真实配置存放在被忽略的私密文件或平台控制台。

英语课程、英语错题、英语背词和法语分别由 `englishCourseEnabled`、`mistakeLearningEnabled`、`vocabularyEnabled`、`frenchLearningEnabled` 控制。旧字段兼容读取现有函数，避免增加功能时给所有学生开启入口。

教师学生预览使用 `mode=teacherPreview` 和目标学生标识复用真实页面，保留教师身份与只读标志。体验流程在本地演练；不上传预览录音，不写入学生学习进度、已读、任务状态或日程，不为现有预览额外创建云端存储。教师自己的法语进度采用独立学习者键。

## 作业、学习计划与记忆

- 整项作业完成由 `reviewLessonHomeworkCompletion` 确认；上传录音和单条录音反馈各有状态。
- `getHomeworkListGroup` 与相关汇总负责一致分组。学生可处理数量排除等待教师检查的任务。
- 学习观察与真实错题分开。未关联能力的历史观察保留，不自动冒充当前阶段证据。
- `buildStageReadiness` 计算晋级建议；教师确认阶段切换。`learning-plan` 组件把 `basic` 与 `stable` 都显示在已达标目标组，晋级另外检查服务端条件。
- 小测选择正确、主动输入、同日重复和答错对记忆的影响由 `buildQuizProgress` 定义。
- 按学生校准读取 `englishMemoryAnalytics.js`，应用接口为 `updateEnglishMemoryCalibration`。建议由服务端重新计算并核对，支持恢复默认，保留历史。不要将较早“只能诊断”的交接当作当前实现。
- 算法版本、证据阈值、反馈权重、校准范围与样本门槛从源码读取，不在 skill 中维护第二套常量。

## 完整文字错题契约

每次最多 50 条完整文字记录。`questionText` 包含题干、全部选项和必要上下文。共享同一文章的多个相关错题合并，文章保留一次，答案逐小题对应。

不输出图片关联字段，也不另建 `options`、`subQuestions` 或 `sharedText`；它们不属于当前保存与渲染契约。新整理内容默认 `reviewStatus` 为待复习、`reviewPriority` 为中，优先级取高/中/低。

常用整理字段：

```text
questionNo, paperSection, questionType, questionText, sourcePage,
skillTags, mistakeReasonType, studentAnswer, correctAnswer,
explanation, reason, reviewStatus, reviewPriority
```

按本次需求可整理 `vocabularyWords`，其结构查看 `normalizeVocabularyWords`。已知来源页码和学生答案依据原资料；无法核验的内容留空或请求补充，不编造。

输出导入内容时使用严格 JSON 数组。运行时的学生 ID、课程 ID、批次标识由导入流程绑定，不猜测。导入前核对学生与课程；重试沿用批次，避免重复记录。

## 资料、音频、日程与 AI

课程板书和图片属于资料流程，与错题纯文字规则分开。资料保留原图及预览图，删除或替换检查共享引用。不能将清理错题图片扩展为删除课程资料、录音或示范音。

本机两个助手只在回环地址临时交接材料，实际上传和导入仍通过教师端完成。工具正在运行时避免另启同端口实例；本机助手不会因此获得生产后台权限。

当前学生日程只读；提醒为主动授权的单次 18:00 未完成任务提醒。已删除的学生预约和长期订阅不是现成功能。用户明确要求重做时，按新产品变更评估数据和状态，继续已授权的设计与实现。

本 skill 建立时原项目 AI 仍是规划。以后任务先搜索当前实现再判断是否已集成；模型名称、资源购买和部署可用性重新核实。默认小程序经云函数鉴权、私有材料短期访问、异步分析与教师复核，模型故障不影响提交。

## 适配维护

此参考依据 2026-11-12 本地研究形成，保存的是规则来源与语义，不是生产在线状态。发现用户已改变产品规则时更新对应参考；保留解释所必需的历史，不复制原始交接、学生名单、访问码、真实环境标识或密钥。
