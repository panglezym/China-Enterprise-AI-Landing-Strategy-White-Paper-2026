# 3.2 Agent（智能体）工作流：大模型的手和脚

上级 项目: 3.0第三章： 技术路径演进——通往落地的最短路径 (3%200%E7%AC%AC%E4%B8%89%E7%AB%A0%EF%BC%9A%20%E6%8A%80%E6%9C%AF%E8%B7%AF%E5%BE%84%E6%BC%94%E8%BF%9B%E2%80%94%E2%80%94%E9%80%9A%E5%BE%80%E8%90%BD%E5%9C%B0%E7%9A%84%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84%202d59dae32cda804c8bfae8880c2b758c.md)
状态: 完成
结束时间: 2025年8月8日

大模型本身是一个“缸中之脑”。它知道怎么写 SQL 语句，但它无法连接数据库去执行那条语句；它知道怎么写邮件，但它无法点击发送按钮。

**Agent（智能体）** 的出现，填补了这一空白。它是大模型走向物理世界和数字世界的桥梁。

我们定义 Agent 的公式为：

Agent=LLM (大脑)+Planning (规划)+Memory (记忆)+Tools (工具)

### **3.2.1 Agent 的解剖学：从“嘴炮”到“实干”**

要构建一个企业级的数字员工，必须具备以下四大组件：

1. **感知与规划 (Perception & Planning)：** 这是 Agent 的核心大脑。基于 **ReAct (Reason + Act)** 范式，Agent 不再是即兴回答，而是先思考、再行动。
    - *场景：* 用户指令“帮我把这周销售额低于 50万 的门店数据导出并发给大区经理”。
    - *Thinking：* 第一步调取数据库 → 第二步筛选数据 → 第三步生成 Excel → 第四步调用邮件接口发送。
2. **工具使用 (Tool Use / Function Calling)：** 这是 Agent 的手和脚。通过 API 接口，Agent 可以操作 ERP、CRM、OA 等企业系统。
    - *关键技术：* **Function Calling**。模型输出的不再是自然语言，而是精准的 JSON 格式参数（如 `{"action": "send_email", "params": {"to": "boss@co.com"}}`），由中间件触发真实代码执行。
3. **记忆系统 (Memory)：**
    - *短期记忆：* 记住刚才聊了什么（Context）。
    - *长期记忆：* 记住用户的偏好、历史任务结果（Vector DB）。

### **3.2.2 进阶架构：多智能体协作 (Multi-Agent Orchestration)**

在 2024 年的实践中，我们发现试图用一个“超级 Agent”解决所有问题是行不通的（容易精神分裂，指令遵循能力下降）。

2025 年的主流趋势是 **SOP（标准作业程序）的数字化**，即 **Multi-Agent（多智能体）协作**。

我们将复杂的业务流程拆解为“流水线”，每个 Agent 只扮演一个角色：

- **Role A (产品经理 Agent)：** 负责理解需求，输出 PRD 文档。
- **Role B (程序员 Agent)：** 负责根据 PRD 写代码。
- **Role C (测试 Agent)：** 负责运行代码，报错反馈给 Role B。
- **Controller (管理员 Agent)：** 负责流程把控和最终验收。

> 商业价值： 这不再是辅助员工，这是在重构组织。一个人类管理者，指挥一支由 10 个 AI Agent 组成的虚拟团队，其效能是传统的 10 倍。
> 

### **3.2.3 代码实战：定义一个能够“查库存”的 Agent**

为了让 CTO 直观理解 Agent 是如何工作的，我们展示一段基于 Python 的伪代码。这段代码展示了 AI 如何通过“思考”来决定调用哪个函数。

Python

`# [Agent 实战：智能库存查询助手]

from llm_framework import Agent, Tool, LLM

# 1. 定义工具 (Tools) - 这是 AI 的"手"
# 就像给新员工开通 ERP 权限一样
def query_erp_inventory(product_name: str):
    """
    当用户询问库存数量时调用此函数。
    返回：JSON格式的库存数据。
    """
    # 模拟连接真实数据库
    sql = f"SELECT qty, location FROM stock WHERE name='{product_name}'"
    return db.execute(sql)

def send_restock_alert(product_name: str):
    """
    当库存不足时，调用此函数发送补货通知。
    """
    email_system.send(f"警报：{product_name} 库存不足！")

# 2. 初始化 Agent - 装载大脑和工具
inventory_agent = Agent(
    role="库存管理员",
    goal="准确查询库存，并在缺货时自动报警",
    llm=LLM(model="wenxin-4.0"), # 使用具备逻辑推理能力的大模型
    tools=[query_erp_inventory, send_restock_alert] # 挂载工具
)

# 3. 执行任务 (Execution)
# 用户指令
user_command = "帮我查一下'华为Mate70'还有多少货？如果少于10台，赶紧通知采购部。"

# Agent 的内部思考过程 (Chain of Thought):
# > Thought: 用户有两个意图。首先我需要查库存。
# > Action: 调用 query_erp_inventory("华为Mate70")
# > Observation: 返回 {"qty": 5, "location": "深圳仓"}
# > Thought: 库存是5，小于10，满足报警条件。接下来我要发通知。
# > Action: 调用 send_restock_alert("华为Mate70")
# > Final Answer: 已查询，当前库存5台。因低于安全水位，已自动发送补货通知。

result = inventory_agent.run(user_command)
print(result)`

**小结：RAG 让 AI 有了“脑子”，Agent 让 AI 有了“手脚”。** 企业应用正在从 **Read-Only（只读/问答型）** 向 **Read-Write（读写/任务型）** 进化。 对于企业 IT 部门而言，未来的核心工作将不再是开发一个个独立的 App，而是封装一个个标准的 **API Tools**，供 AI Agent 随时调用。**API 经济（API Economy）将在 AI 时代迎来第二春。**