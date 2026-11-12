# 修改、验证与交付

代码相关任务读取此参考。当前小程序约定仅适用于目标项目；其他仓库遵循其现有规则。普通设计讨论不需要执行 Git 或发布流程。

## 范围与事实

先查工作区和受影响代码。说明本次实际涉及前端、业务云函数、提醒、数据库、存储或外部服务中的哪些部分，保留原有工作。

用户当前要求优先于旧交接；已有授权持续有效。明确的产品变更直接推进，不把“先确认”解释成每个步骤都重新询问。缺少必需的环境信息或操作权限时，完成不依赖它的工作并说明具体缺口。

当前原项目约定直接在 `main` 做小步修改，按具体文件暂存、审查后提交推送；保留当前会话授权和现有改动。该约定不自动扩展到其他仓库。技能发布仓库如何分支、提交与共享由其自己的要求决定。

## 数据兼容与可靠性

已有学生配置优先于兼容默认值。算法和字段演进保留有效历史，优先惰性迁移；不通过清空真实学习记录来简化实现。

写入检查身份、对象归属、任务版本和必要幂等性。云端事务繁忙复用 `transactionRetry.js`，前端统一调用经 `miniprogram/utils/cloud.js`；保留重试识别需要的错误信息，对用户显示清楚的恢复动作。

删除或迁移先确定精确对象、共享引用和恢复方式；生产迁移按授权范围限定目标并准备备份。部署、上传、发布和生产数据变更是不同动作，不能从代码同步推断已执行。

## 根据改动选择验证

| 改动领域 | 当前已有检查入口 |
| --- | --- |
| 课程、任务、课程量与排序 | `tests/courseWorkflow.test.js`、`tools/validate-course-workspace.js`、`tools/validate-student-learning-flow.js` |
| 学习目标、证据与阶段 | `tests/learningRadar.test.js`、`tools/validate-learning-radar.js` |
| 记忆与校准 | `tests/englishMemoryScheduler.test.js`、`tests/englishMemoryAnalytics.test.js`、`tests/englishMemoryCalibration.test.js`、对应 `tools/validate-english-memory-*.js` |
| 教师待办与录音反馈 | `tests/teacherTodos.test.js`、`tests/recordingFeedback.test.js`、相应验证器 |
| 身份、预览、功能开关 | `tools/validate-identity-and-vocabulary-flow.js`、`tools/validate-language-feature-settings.js` |
| 页面、样式与文案 | `tools/validate-miniprogram-structure.js`、`tools/validate-design-tokens.js`、`tools/validate-ui-copy.js` |
| 资料与错题导入 | `tests/materialUpload.test.js`、`tests/batchImportTransactionRetry.test.js`、两个本机助手验证器 |
| 跟读、KET、日程、法语与激励 | 对应模块测试和 `tools/validate-*.js`；法语内容另有 `tools/french-audio/validate-content.js` |

`tests/` 位于 `cloudfunctions/quickstartFunctions/`。从仓库根运行单文件测试时补上这个目录。先确认文件和测试脚本仍存在；读取测试入口后执行需要的检查。

原小程序代码修改还要求完整基线：`npm --prefix cloudfunctions/quickstartFunctions test`、根目录全部 `tools/validate-*.js` 与 `git diff --check`。只修改本 skill 或设计文档时，不因此运行整个小程序的回归。不要添加只复述实现文字的测试。

涉及状态与权限的有用情景包括：真实学生、教师和教师预览；新学生空数据；提交后待检查与退回；任务版本改变；弱网失败；旧记录读取。选择与改动有关的情景，不机械执行全部。

## 记录完成范围

交付说明以实际证据为准：改了什么、为何影响学习或教师操作、运行了哪些检查，以及尚待完成的必要步骤。

涉及上线时分别记录：代码实现、本地验证、Git 同步、云函数与资源部署、前端上传/发布、生产数据与存储、设备验收。使用“已执行、用户报告、待验证”等准确状态，并标明覆盖的版本或范围。

交接只追加本次有效结论、规则变化与未完成事项。不要把某天的部署、统计数量或远端快照固化成永久 skill 指令。
