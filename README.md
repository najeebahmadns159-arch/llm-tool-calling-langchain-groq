# LLM Tool Calling with LangChain & Groq

A hands-on Jupyter notebook that demonstrates **tool calling (function calling)** with LangChain and Groq's `openai/gpt-oss-20b` model. The LLM decides which tool to call, the tools run in Python, and the results are fed back to the model to produce a final answer.

The demo use case is a **currency converter**: the model fetches a live USD → INR exchange rate and uses it to convert an amount.

## What you'll learn

- Creating custom tools with the `@tool` decorator
- Inspecting a tool's schema with `.args`
- Binding tools to an LLM using `.bind_tools()`
- Reading the model's `tool_calls` and executing them manually
- Passing tool results back to the model as `ToolMessage`s
- Using `InjectedToolArg` so the LLM does not fill in certain arguments itself (e.g. a value fetched by another tool)

## Tools in this notebook

| Tool | Description |
|------|-------------|
| `multiply(a, b)` | Returns the product of two integers (simple warm-up example) |
| `get_conversion_factor(base_currency, target_currency)` | Fetches a live exchange rate from [ExchangeRate-API](https://www.exchangerate-api.com/) |
| `convert(base_currency, conversion_rate)` | Multiplies an amount by the conversion rate. `conversion_rate` is an `InjectedToolArg`, so it is hidden from the LLM and injected by our code |

## How it works

```
User question
     │
     ▼
LLM (with tools bound) ──► decides to call get_conversion_factor(USD, INR)
     │
     ▼
Python executes the tool ──► returns JSON with conversion_rate
     │
     ▼
ToolMessage appended to the message history
     │
     ▼
LLM is invoked again ──► final natural-language answer
```

**Example**

> **Q:** What is the conversion factor between USD to INR? Based on that, convert 10 USD to INR.
>
> **A:** The current conversion factor from USD to INR is 95.2911, so 10 USD = 952.911 INR.

## Tech stack

- Python 3
- [LangChain](https://python.langchain.com/) (`langchain-core`, `langchain-groq`)
- [Groq](https://groq.com/) API with `openai/gpt-oss-20b`
- [ExchangeRate-API](https://www.exchangerate-api.com/) for live rates
- Google Colab / Jupyter Notebook

## Getting started

### 1. Clone the repo

```bash
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>
```

### 2. Install dependencies

```bash
pip install langchain-groq langchain-core requests
```

### 3. Get your API keys

- **Groq API key:** https://console.groq.com/keys
- **ExchangeRate-API key (free tier available):** https://www.exchangerate-api.com/

### 4. Set the keys as environment variables

```bash
export GROQ_API_KEY="your_groq_key"
export EXCHANGE_RATE_API_KEY="your_exchange_rate_key"
```

In Colab, the notebook asks for the Groq key using `getpass`. Do the same for the exchange-rate key instead of hardcoding it in the URL.

### 5. Run the notebook

```bash
jupyter notebook Tool_calling.ipynb
```

Or open it directly in Google Colab and run the cells top to bottom.

## Project structure

```
.
├── Tool_calling.ipynb   # Main notebook
└── README.md
```

## Security note

Never commit API keys to GitHub. Read them from environment variables (or Colab secrets) and clear the notebook outputs before pushing if they contain sensitive data. If a key was ever committed, revoke it and generate a new one.

## Possible improvements

- Wrap the manual tool loop in a LangChain agent (`create_react_agent` / LangGraph) so tool calls are chained automatically
- Add error handling for failed API requests and invalid currency codes
- Support more currency pairs and multi-step conversions
- Add more tools (weather, web search, calculator)

## License

This project is licensed under the MIT License. Feel free to use it for learning.
