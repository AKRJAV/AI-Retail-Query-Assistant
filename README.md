# LangChain SQL Agent - Retail Store Inventory

An AI powered SQL agent that converts natural language questions into SQL queries and fetches data from MySQL database. Built with LangChain, OpenAI GPT-4o-mini, and Langfuse for LLM monitoring and analytics.

## 🎯 What It Does

Ask questions in plain English instead of writing SQL. The agent automatically:

1. Understands your question
2. Generates the correct SQL query
3. Validates the query
4. Executes it against the database
5. Returns the result in natural language
6. **Tracks performance metrics via Langfuse**

## 💻 Tech Stack

- **LLM**: OpenAI GPT-4o-mini
- **Framework**: LangChain
- **Database**: MySQL (PyMySQL)
- **Monitoring**: Langfuse (LLM tracing & analytics)
- **Export**: Excel with Pandas

## 📋 Real Examples

| Question | Answer |
|----------|--------|
| How many t-shirts are in stock? | 4,484 t-shirts |
| How many white Levi t-shirts? | 301 t-shirts |
| Revenue from Levi sales with discounts? | $20,240.75 |
| Total inventory value for size S? | $22,125 |

## 🗄️ Database Schema

**t_shirts table:**
- `brand` (Van Huesen, Levi, Nike, Adidas)
- `color` (Red, Blue, Black, White)
- `size` (XS, S, M, L, XL)
- `price` (10-50)
- `stock_quantity`

**discounts table:**
- `t_shirt_id` (foreign key)
- `pct_discount` (0-100%)

## 🚀 Quick Start

### Installation

```bash
pip install langchain langchain-openai langchain-community pymysql pandas openpyxl langfuse
```

### Basic Usage

```python
from langchain_openai import ChatOpenAI
from langchain_community.utilities import SQLDatabase
from langchain_community.agent_toolkits.sql.base import create_sql_agent
from langfuse.callback import CallbackHandler
import os

# Set Langfuse credentials
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-xxxx"
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-xxxx"
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com"

# Initialize Langfuse handler
langfuse_handler = CallbackHandler()

# Setup
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0.4, api_key="sk-xxxx")
db = SQLDatabase.from_uri("mysql+pymysql://user:password@localhost/db_name")

# Create agent
from langchain_community.agent_toolkits.sql.toolkit import SQLDatabaseToolkit
toolkit = SQLDatabaseToolkit(db=db, llm=llm)
agent = create_sql_agent(llm=llm, toolkit=toolkit, verbose=True, agent_type="openai-tools")

# Query with Langfuse tracking
response = agent.invoke(
    {"input": "How many white Levi t-shirts do we have?"},
    config={"callbacks": [langfuse_handler]}
)
print(response["output"])
```

## ✨ Key Features

- ✅ Natural language to SQL conversion
- ✅ Automatic query validation before execution
- ✅ Handles joins and complex calculations
- ✅ Real-time LLM tracing with Langfuse
- ✅ Performance analytics & cost tracking
- ✅ Error tracking and debugging
- ✅ Excel export with metadata
- ✅ Verbose mode shows AI reasoning

## 📈 Langfuse Dashboard

All queries are automatically logged to Langfuse. View at `https://cloud.langfuse.com`:
- **Traces**: Complete execution flow
- **Tokens**: Input/output token usage
- **Latency**: Response times
- **Costs**: API expenses
- **Errors**: Failed queries

## 🔧 Configuration

### Environment Setup

```python
os.environ["LANGFUSE_SECRET_KEY"] = "your_secret_key"
os.environ["LANGFUSE_PUBLIC_KEY"] = "your_public_key"
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com"
```

### Database Connection

```python
db = SQLDatabase.from_uri("mysql+pymysql://user:password@host/database")
```

## 📝 Example Queries

```
"How many t-shirts are in stock?"
"What's the total inventory value?"
"How many white Levi t-shirts do we have?"
"If I sell all Levi t-shirts with discounts, what revenue would I generate?"
"Which brand has the highest stock quantity?"
```

## ⚠️ Troubleshooting

**Database Connection Error**: Verify credentials and ensure MySQL is running

**Langfuse Not Recording**: Check credentials at Langfuse dashboard

**OpenAI API Error**: Verify API key and quota


## OUTPUT Excel
<img width="626" height="92" alt="image" src="https://github.com/user-attachments/assets/b1ffb637-150c-4189-a9bf-100b3e9f3a61" />
