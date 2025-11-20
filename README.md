# LangChain SQL Agent - Retail Store Inventory

An AI powered SQL agent that converts natural language questions into SQL queries and fetches data from MySQL database. Built with LangChain and OpenAI GPT-4o-mini.

## 🎯 What It Does

Ask questions in plain English instead of writing SQL. The agent automatically:
1. Understands your question
2. Generates the correct SQL query
3. Validates the query
4. Executes it against the database
5. Returns the result in natural language

## 💻 Tech Stack

- **LLM**: OpenAI GPT-4o-mini
- **Framework**: LangChain
- **Database**: MySQL (PyMySQL)
- **Export**: Excel with Pandas
- **Python**: 3.8+

## 📋 Real Examples

| Question | SQL Generated | Answer |
|----------|---------------|--------|
| How many t-shirts are in stock? | `SELECT SUM(stock_quantity) FROM t_shirts;` | 4,484 t-shirts |
| How many white Levi t-shirts? | `SELECT SUM(stock_quantity) FROM t_shirts WHERE brand='Levi' AND color='White';` | 301 t-shirts |
| Revenue from Levi sales with discounts? | `SELECT SUM((price * (1 - pct_discount/100)) * stock_quantity) FROM t_shirts JOIN discounts...` | $10,000.00 |
| Total inventory value for size S? | `SELECT SUM(price * stock_quantity) FROM t_shirts WHERE size='S';` | $22,125 |

## 🗄️ Database

**t_shirts** table: product_id, brand (Van Huesen, Levi, Nike, Adidas), color (Red, Blue, Black, White), size (XS-XL), price (10-50), stock_quantity

**discounts** table: discount_id, t_shirt_id (foreign key), pct_discount (0-100%)

## 🚀 Quick Start

```python
from langchain_openai import ChatOpenAI
from langchain_community.utilities import SQLDatabase
from langchain_community.agent_toolkits.sql.toolkit import SQLDatabaseToolkit
from langchain_community.agent_toolkits.sql.base import create_sql_agent

# Setup
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0.4, api_key="your-key")
db = SQLDatabase.from_uri("mysql+pymysql://root:password@localhost/atliq_tshirts")
toolkit = SQLDatabaseToolkit(db=db, llm=llm)

# Create agent
agent = create_sql_agent(llm=llm, toolkit=toolkit, verbose=True, agent_type="openai-tools")

# Query
response = agent.run("How many white Levi t-shirts do we have?")
print(response)
```

## 🤖 Agent Output

> Entering new SQL Agent Executor chain...

Invoking: `sql_db_list_tables` with `{}`
discounts, t_shirts

Invoking: `sql_db_schema` with `{'table_names': 't_shirts'}`
CREATE TABLE t_shirts (
    t_shirt_id INTEGER NOT NULL AUTO_INCREMENT,
    brand ENUM('Van Huesen','Levi','Nike','Adidas'),
    color ENUM('Red','Blue','Black','White'),
    size ENUM('XS','S','M','L','XL'),
    price INTEGER,
    stock_quantity INTEGER
)
3 rows from t_shirts table:
t_shirt_id | brand | color | size | price | stock_quantity
1 | Van Huesen | Red | XS | 25 | 97
2 | Van Huesen | Red | S | 39 | 72
3 | Van Huesen | Red | M | 21 | 43

Invoking: `sql_db_query_checker` with `{'query': 'SELECT SUM(stock_quantity) AS total_stock FROM t_shirts;'}`
✓ Query validated

Invoking: `sql_db_query` with `{'query': 'SELECT SUM(stock_quantity) AS total_stock FROM t_shirts;'}`

> Finished chain.

There are a total of 4,484 t-shirts in stock.
Generated SQL:
sqlSELECT SUM(stock_quantity) AS total_stock FROM t_shirts;
Final Answer: There are a total of 4,484 t-shirts in stock.

## 📊 Output

<img width="615" height="73" alt="image" src="https://github.com/user-attachments/assets/62b5d906-5b3f-4f8b-bb32-34f8d610e727" />


Results are automatically saved to `query_result.xlsx`:
- **Result**: The answer from the database
- **User Prompt**: Your original question
- **Timestamp**: When the query was executed

## ✨ Key Features

- ✅ Natural language to SQL conversion
- ✅ Automatic query validation before execution
- ✅ Handles joins and complex calculations
- ✅ Verbose mode shows AI reasoning
- ✅ Excel export with metadata
- ✅ URL-encoded password support
- ✅ Sample data shown to LLM for context

## ⚙️ Installation

```bash
pip install langchain langchain-openai langchain-community pymysql pandas openpyxl
```

