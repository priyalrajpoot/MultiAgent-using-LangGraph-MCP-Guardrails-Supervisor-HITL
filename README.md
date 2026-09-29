# MultiAgent-using-LangGraph-MCP-Guardrails-Supervisor-HITL


# 🤖 Multi-Agent E-Commerce Assistant using AutoGen & ChromaDB

A multi-agent e-commerce customer-support system built with **Microsoft AutoGen** and **ChromaDB**.

The system uses specialized AI agents to search product and order information from separate ChromaDB collections and then uses a dedicated Writer Agent to generate the final response.

---

## 🚀 Project Overview

This project demonstrates how multiple AI agents can work together to answer e-commerce-related questions.

Instead of sending every question directly to a single LLM, the system divides the work between specialized agents:

```text
                         User Question
                              │
                              ▼
                    ┌──────────────────┐
                    │   Search Agents  │
                    └────────┬─────────┘
                             │
                ┌────────────┴────────────┐
                │                         │
                ▼                         ▼
       Product Search Agent       Order Search Agent
                │                         │
                ▼                         ▼
       Product ChromaDB             Order ChromaDB
                │                         │
                └────────────┬────────────┘
                             │
                             ▼
                    Retrieved Information
                             │
                             ▼
                       Writer Agent
                             │
                             ▼
                       Final Answer
```

---

## ✨ Key Features

* 🤖 Multi-agent architecture using **AutoGen**
* 🛍️ Specialized Product Search Agent
* 📦 Specialized Order Search Agent
* 🗄️ ChromaDB for product and order information
* 🔎 Semantic search over stored documents
* 👥 AutoGen GroupChat for agent communication
* ⚙️ GroupChatManager for conversation orchestration
* 📝 Dedicated Writer Agent for final responses
* 🔄 Asynchronous execution of multiple agent conversations
* 🌡️ Deterministic LLM configuration using low temperature
* 🧩 Tool registration using AutoGen decorators

---

## 🏗️ Architecture

The project contains several important components.

### 1. Product Search Agent

The Product Search Agent is responsible for retrieving information about products.

For example:

```text
What is Artisanal Air?
```

The agent can use the product search tool to query the product collection in ChromaDB.

---

### 2. Order Search Agent

The Order Search Agent is responsible for retrieving customer order information.

For example:

```text
What did Ned Noodle order?
```

It searches the order collection in ChromaDB.

---

### 3. ChromaDB

The project uses two separate ChromaDB collections:

```text
products_collection
orders_collection
```

The product collection contains product information, while the order collection contains customer order information.

Example product:

```text
Artisanal Air
100% genuine Brooklyn oxygen...
39.99
```

Example order:

```text
ORD1028
Earl Eclair
28.97
Unnecessarily Complex Can Opener...
```

---

## 🔎 Search Tools

The agents use Python functions as tools to interact with ChromaDB.

### Product Search

```python
async def search_products(search_query):
    results = products_collection.query(
        query_texts=[search_query],
        n_results=2
    )
    return results
```

### Order Search

```python
async def search_orders(search_query):
    results = orders_collection.query(
        query_texts=[search_query],
        n_results=2
    )
    return results
```

These functions act as the bridge between the AI agents and ChromaDB.

```text
AI Agent
   ↓
Search Tool
   ↓
ChromaDB
   ↓
Search Results
   ↓
Agent Conversation
```

---

## 👥 AutoGen GroupChat

The project uses AutoGen `GroupChat` and `GroupChatManager` to coordinate the specialized agents.

### Product GroupChat

```text
Product Search Assistant
          ↕
Product Search Executor
          ↕
Product GroupChat Manager
```

### Order GroupChat

```text
Order Search Assistant
          ↕
Order Search Executor
          ↕
Order GroupChat Manager
```

The conversations use a round-robin speaker selection strategy with a limited number of rounds.

---

## 🔄 Complete Execution Flow

Suppose the user asks:

```text
What is Artisanal Air?
```

The system follows this flow:

### Step 1 — User Question

The question is passed to the product search workflow.

```text
"What is Artisanal Air?"
```

### Step 2 — Product Agent

The Product Search Agent determines that it needs information from the product database.

### Step 3 — Tool Call

The agent uses:

```python
search_products("Artisanal Air")
```

### Step 4 — ChromaDB Search

ChromaDB searches the product collection and returns matching documents.

```text
ChromaDB
   ↓
Artisanal Air
   ↓
Matching product information
```

### Step 5 — AutoGen Conversation

The retrieved information becomes part of the AutoGen agent conversation.

The conversation history can be accessed through:

```python
products_search_assistant_agent.chat_messages
```

---

### Step 6 — Order Search

The order workflow can similarly search the order collection.

```python
orders_search_assistant_agent.chat_messages
```

stores the corresponding conversation history.

---

### Step 7 — Collect Retrieved Data

The application collects the search-agent conversation histories:

```python
retrieved_product_data = products_search_assistant_agent.chat_messages

retrieved_order_data = orders_search_assistant_agent.chat_messages
```

These are combined into:

```python
retrieved_data
```

---

### Step 8 — Writer Prompt

The retrieved information is inserted into a prompt for the Writer Agent.

Conceptually:

```text
User Question
      +
Retrieved Product Data
      +
Retrieved Order Data
      ↓
Writer Prompt
```

---

### Step 9 — Writer Agent

The Writer Agent receives the prompt and generates the final response.

Its instructions tell it to:

* Use the provided retrieved information
* Avoid relying on its own knowledge
* Say that it does not know when the provided information is insufficient

---

### Step 10 — Final Answer

The final response is extracted from the Writer Agent's conversation and printed for the user.

```text
Search Agents
      ↓
Retrieved Information
      ↓
Writer Agent
      ↓
Final Answer
```

---

## 🧠 Data Flow

The most important data flow in this project is:

```text
ChromaDB
    ↓
Search Function
    ↓
AutoGen Search Agent
    ↓
Agent chat_messages
    ↓
retrieved_product_data
retrieved_order_data
    ↓
retrieved_data
    ↓
writer_prompt
    ↓
WriterUserProxy
    ↓
Writer Agent
    ↓
Final Answer
```

There is no direct connection between ChromaDB and the Writer Agent.

The retrieved information is first collected from the search-agent conversations and then inserted into the Writer Agent's prompt.

---

## 📂 Project Structure

A typical project structure is:

```text
.
├── products.csv
├── orders.csv
├── <main Python file>
├── README.md
└── requirements.txt
```

The exact Python file names may vary depending on the project setup.

---

## 🛠️ Technologies Used

* **Python**
* **Microsoft AutoGen**
* **OpenAI GPT-4o**
* **ChromaDB**
* **AsyncIO**
* **Pydantic / typing utilities**

---

## 📋 Prerequisites

Before running the project, make sure you have:

* Python 3.10+
* OpenAI API key
* pip
* A virtual environment

---

## ⚙️ Installation

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd <your-repository-folder>
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

On macOS/Linux:

```bash
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

## 🔐 Environment Variables

Do not hard-code your API key inside the Python source code.

Create a `.env` file:

```env
OPENAI_API_KEY=your_api_key_here
```

Then load it in Python:

```python
from dotenv import load_dotenv
import os

load_dotenv()

api_key = os.getenv("OPENAI_API_KEY")
```

Add `.env` to `.gitignore`:

```text
.env
```

Never commit your real API key to GitHub.

---

## ▶️ Running the Project

After installing the dependencies and configuring the environment variables:

```bash
python <main-python-file>.py
```

The application will create/load the product and order collections, run the configured AutoGen conversations, retrieve the required information, and generate the final response through the Writer Agent.

---

## 💡 Example

### Product Question

```text
What is Artisanal Air?
```

### Internal Flow

```text
User
 ↓
Product Search Agent
 ↓
search_products()
 ↓
ChromaDB
 ↓
Product Information
 ↓
Writer Agent
 ↓
Final Response
```

---

### Order Question

```text
What did Ned Noodle order?
```

### Internal Flow

```text
User
 ↓
Order Search Agent
 ↓
search_orders()
 ↓
ChromaDB
 ↓
Order Information
 ↓
Writer Agent
 ↓
Final Response
```

---

## 🎯 What This Project Demonstrates

This project is mainly designed to demonstrate the following **multi-agent concepts**:

1. **Specialized Agents**
   Different agents are responsible for different types of information.

2. **Tool Calling**
   Agents can call Python functions to retrieve external information.

3. **Database Retrieval**
   ChromaDB is used as the knowledge source.

4. **Agent Communication**
   AutoGen agents communicate through GroupChat.

5. **Conversation Management**
   GroupChatManager controls the agent conversations.

6. **Asynchronous Agent Workflows**
   Multiple conversations can be initiated using AutoGen's asynchronous chat functionality.

7. **Final Response Generation**
   A separate Writer Agent converts retrieved information into a user-friendly response.

---

## 🔑 Core Architecture

The central idea of the project can be summarized as:

```text
        Specialized Agents
               │
               ▼
          Tool Calling
               │
               ▼
           ChromaDB
               │
               ▼
       Retrieved Information
               │
               ▼
          Writer Agent
               │
               ▼
          Final Answer
```

---

## 📌 Important Note

The search agents are responsible for **finding information**.

The Writer Agent is responsible for **presenting that information as the final answer**.

This separation makes the architecture easier to understand and demonstrates how specialized agents can be combined into a larger AI workflow.

---

## 📄 License

This project is intended for educational and demonstration purposes.

If a `LICENSE` file is included in the repository, refer to that file for the applicable license terms.
