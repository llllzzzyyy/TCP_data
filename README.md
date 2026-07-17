# TCP_data
文件结构
deepseek_flash与deepseek_pro文件结构相同

一、导出 AI 轨迹:使用社区工具cclog (@standard-software/cclog),导出为 Markdown
1. 项目名_with/without(pro).md:
名称解读：对应项目名中，输入with_non_regression/without_non_regression给flash(或者pro)模型
输入：
- Candidate testcase list
- Commit diff
- Changed files
- Discussion files
- commit_message.txt
- commit_api.json
- Repository root
log记录内容：
[Thinking]:模型的思考过程
[Tool: Read xxx]:调用了哪个工具 + 参数
[ToolResult]:工具返回的结果
(assistant):模型的文字回复
大致流程：
（1）. 读candidate_testcase.csv\commit_full.diff\commit_changed_files.txt\commit_message.txt\commit_api.json五个文件
（2）. Understand code change in AbstractRegion.processConsumerControl
（3）. Agent探索repo与变更关联
（4）. 分析candidates and construct JSON ranking
（5）. Save ranked_testcases.json and convert to CSV

2. 会话ID
记录了该文件夹下agent会话的时间、名称、ID

二、查看每次 token 消耗:每次调用 DeepSeek API 后，响应 JSON 中的 usage 字段会返回本次请求的精确tokens消耗
3. deepseek_pro_record.csv 
原始记录

4. deepseek_pro_per_round_detail.csv 
每轮明细

5. 各会话对话轮次与 Token 消耗汇总
统计每个项目的轮次、输入tokens、输出tokens

三、模型运行评估结果
6. evaluation_summary.json
