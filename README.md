# 老年人助手 Agent 框架

> 基于 LangChain Memory + Mem0 记忆框架的智能对话系统

## 📋 项目概述

本框架专为老年人设计，提供一个温暖、耐心、易用的智能助手。该助手具有以下特点：

- **记忆持久化**：使用 Mem0 长期存储对话历史，记住用户的偏好和重要信息
- **大语言模型支持**：基于 OpenAI GPT 等模型提供智能对话能力
- **LangChain 集成**：利用 LangChain 的 Memory 模块管理对话上下文
- **友好交互**：专为老年人优化的交互界面和对话风格

---

## 🏗️ 架构设计

```
┌─────────────────────────────────────────────────────────────────┐
│                        用户交互层                                │
│                   (CLI / Web 界面)                              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Agent 核心层                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌────────────────┐  │
│  │   LangChain     │  │     Mem0        │  │   Prompt      │  │
│  │   Memory        │  │   Memory        │  │   Template    │  │
│  └─────────────────┘  └─────────────────┘  └────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     LLM 服务层                                   │
│                    (OpenAI GPT)                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 项目结构

```
mem0_test/
├── .env                      # API密钥配置
├── requirements.txt          # Python依赖
├── README.md                 # 项目说明
├── main/
│   ├── __init__.py
│   ├── agent.py              # Agent核心实现
│   ├── memory_manager.py     # 记忆管理
│   ├── prompts.py            # 提示词模板
│   └── main.py               # 程序入口
└── data/                     # 数据存储目录
    └── mem0/                 # Mem0记忆存储
```

---

## 🚀 快速开始

### 1. 环境准备

```bash
# 克隆或创建项目后，安装依赖
pip install -r requirements.txt
```

### 2. 配置环境变量

复制 `.env.example` 为 `.env` 并填入你的 API Key：

```bash
cp .env.example .env
```

编辑 `.env` 文件：

```env
# OpenAI API 配置
OPENAI_API_KEY=your_openai_api_key_here
OPENAI_BASE_URL=https://api.openai.com/v1

# Mem0 配置 (可选，使用默认设置)
MEM0_API_KEY=your_mem0_api_key_here

# 模型配置
MODEL_NAME=gpt-4
TEMPERATURE=0.7
```

### 3. 运行程序

```bash
cd main
python main.py
```

---

## 💻 代码说明

### 核心组件

#### 1. 记忆管理系统 (`memory_manager.py`)

Mem0 提供智能记忆功能：
- **自动存储**：对话内容自动保存到记忆
- **语义检索**：根据上下文智能检索相关记忆
- **用户画像**：记录用户的偏好、健康状况、重要日期等

```python
from mem0 import Memory

# 初始化记忆
m = Memory()

# 存储对话
m.add(messages=[{"role": "user", "content": "我有糖尿病"}], user_id="elderly_user")

# 检索记忆
related_memories = m.get_all(user_id="elderly_user")
```

#### 2. LangChain Memory

提供短期对话上下文管理：

```python
from langchain.memory import ConversationBufferMemory
from langchain.chat_models import ChatOpenAI

# 创建记忆
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

# 添加到链
conversation_chain = LLMChain(
    llm=ChatOpenAI(),
    prompt=prompt_template,
    memory=memory
)
```

#### 3. Agent 核心 (`agent.py`)

结合 LangChain 和 Mem0 的完整 Agent 实现：

- 管理对话流程
- 协调记忆系统
- 生成响应

### 提示词设计

针对老年人优化的提示词：

```python
ELDERLY_ASSISTANT_PROMPT = """你是一位温暖、耐心的老年人助手。
请用简单、温和的语言与用户交流。
特点：
1. 语速适中，不要使用过于复杂的词汇
2. 主动关心用户的健康和生活
3. 记住用户告诉你的重要信息
4. 适当使用emoji让对话更生动
5. 回答要详细，但不要过于冗长
"""
```

---

## 🔧 配置选项

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `OPENAI_API_KEY` | OpenAI API 密钥 | 必填 |
| `MODEL_NAME` | 使用的模型 | gpt-4 |
| `TEMPERATURE` | 生成随机性 | 0.7 |
| `MAX_TOKENS` | 最大token数 | 2000 |
| `MEMORY_RETRIEVAL_LIMIT` | 记忆检索数量 | 10 |

---

## 📝 使用示例

### 首次对话

```
👤 用户: 你好，我想找个人聊聊天
🤖 助手: 你好呀！很高兴见到你。今天过得怎么样？有什么想聊的吗？
```

### 记住重要信息

```
👤 用户: 我今年75岁了，有高血压
🤖 助手: 谢谢告诉我这些。我会记住的。高血压要记得按时测量血压、按时吃药哦！平时也要注意休息，保持心情愉快。

👤 用户: 今天的天气怎么样？
🤖 助手: 今天天气很好呢！阳光明媚，适合出去走走。不过现在是春天，早晚温差较大，出门记得带件外套哦！
```

### 检索记忆

```
👤 用户: 你还记得我有什么健康问题吗？
🤖 助手: 当然记得！你告诉我你有高血压。要记得定期测量血压，保持良好的生活习惯哦！
```

---

## 🛡️ 隐私保护

- 所有对话数据存储在本地
- API 密钥妥善保管，不外泄
- 可随时清除记忆数据

---

## 📞 支持与反馈

如有问题或建议，请提交 Issue 或联系开发团队。

---

## 📄 许可证

MIT License
