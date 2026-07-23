批量执行以及直接排序结果
一、batch_summary文件夹
所有项目的评估结果

二、5个项目中每个项目具体内容
1、candidate_testcase.scv
模型输入的候选集=100个失败+regression
备注：文件结构(candidate_id,testcase_class,source_file)，没有测试结果

2、model_prompt.txt
输入的prompt

3、raw_output.txt、ranked_testcases.json和ranked_testcases.csv
raw_output.txt：模型输出的排序结果（用candidate_id展示排序结果+对排序的解释）
ranked_testcases.json和ranked_testcases.csv:由raw_output.txt中candidate_id顺序与candidate_testcase.scv对应，生成(rank,candidate_id,testcase_class,source_file)结果，用于计算评估指标。

4、运行轨迹记录
4.1. 总体结构
dataset_variants/with_non_regression/
├── trajectory.jsonl               ← 原始事件流
├── trajectory.md                  ← 可读轨迹
└── token_usage_per_turn.json      ← 总tokens与每轮tokens明细
 
4.1.1 trajectory.jsonl(重点，机器分析、提取 token/耗时/轨迹，其余文件均是从该文件提取出来的)
system / thinking_tokens：推理过程 token 估计（噪声，每几 ms 发一次）
system / task_progress：子任务进度更新（Agent/Bash 等长时间操作）
system / init：会话初始化：模型、工具列表、session_id 等
system / task_started：子任务开始（如 Agent 调用）
system / task_notification：子任务完成通知
assistant (n次)
  └─ content[]
       ├── type: "thinking"    → 思考推理过程
       ├── type: "text"        → 回答文本
       └── type: "tool_use"    → 工具调用（name + input + id）
  └─ message.usage             → 这一轮的 token
user (n次)
  └─ content[]
       └── type: "tool_result" → 工具执行结果（tool_use_id + content + is_error）
result (1次，最后1行)
  ├─ usage                     → 总 token
  ├─ total_cost_usd            → 总费用
  ├─ result                    → 最终输出文本
  └─ terminal_reason           → "completed" / "max_turns" / "error"

4.1.2 trajectory.md
从trajectory.jsonl 事件中跳过system / thinking_tokens，其余保留（只转换了格式，可读性变高）

4.1.3 token_usage_per_turn.json
(1)result(1个)
总量 usage + total_cost_usd + num_turns。权威汇总，API 返回的真实总量
示例：
"total": {
    "input_tokens": ,
    "output_tokens": ,
    "cache_read_input_tokens": ,
    "num_turns": 
  }
(2)assistant(N个)	
每轮的 message.usage。看 token 消耗分布：哪轮最多、cache 命中率
示例：
"per_turn": [
    {
      "turn": 1,    #第几个 assistant 事件
      "input_tokens": ,   
      "output_tokens": ,
      "cache_read_input_tokens": ,
      "tools": [],  #这一轮调了哪些工具
      "tool_count": ,
      "step": 2     #trajectory.md 中的 Step 编号
    }
    ...
]
