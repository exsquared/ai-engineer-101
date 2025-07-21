# Let Your Model Think and Act  
*Agents, Tool Use, and the Reasoning Loop*

---

## 🧭 Why Are We Here?

You’ve built models. You’ve served them with FastAPI. You’ve retrieved documents with RAG.

But real-world questions don’t always have obvious answers or clean one-shot responses. Users ask things that require multi-step thinking, calling tools, searching, calculating, retrying.

> This is where **agents** shine — giving LLMs the ability to think step-by-step, take actions, observe outcomes, and adapt.

We’re now entering the era of **agentic systems**.

---

## 🔁 Before Agents: The Intent Routing Phase

Even after LLMs arrived, we still built logic-heavy, brittle systems.

The pattern looked like this:

1. Ask the LLM to return an intent:
```json
{"intent": "get_weather", "location": "London"}
```

2. Manually route in code:
```python
if intent == "get_weather":
    return get_weather(location)
elif intent == "convert_currency":
    return convert_currency(from_curr, to_curr)
else:
    return "Sorry, I can't help with that."
```

✅ This worked.  
❌ But it was rigid:
- Every new tool = more branching logic  
- You had to trust the intent parser completely  
- Multi-intent or follow-up tasks? Forget it  
- Hard to retry, inspect, or trace reasoning

The LLM was just an **intent extractor** — all logic lived in your backend.

This was better than rules, but still far from agentic thinking.

---

## ⚡ ReAct + Function Calling Changed Everything

**ReAct (Reason + Act + Observe)** lets the model:

- Think out loud
- Decide which tool to call
- Use the result to continue reasoning

**Function calling** gives the model access to real tools in a safe, structured way.

Instead of hardcoded logic, the model says:
```
Thought: I need to convert USD to INR.
Action: get_exchange_rate("USD")
Observation: 1 USD = 83 INR
Answer: You’ll get 83 rupees per dollar.
```

Now **LLMs can orchestrate logic — not just output text**.

---

## 🧪 Mini-Exercise: Before vs After

Build this system:

- If user asks for a joke → tell one
- If they ask for weather → call a fake API
- If they ask for exchange rate → call another function

### Step 1: Do it manually with `if`/`elif`

### Step 2: Refactor using:

- ReAct-style prompt
- Function calling with LangChain or OpenAI

### Reflect:

- What changed?
- Which version is easier to extend?
- Where does the reasoning now live?

---

## 🤖 What Is an Agent?

An **agent** is an LLM system that:

1. **Thinks** about what it needs to do
2. **Chooses a tool** to take action
3. **Observes the result**
4. **Repeats or finishes**

This is called an **agent loop**.

---

## 🧠 Agentic Thinking

| Traditional APIs | Agentic Systems |
|------------------|-----------------|
| Developer wires logic | Model decides flow |
| Tools are hardwired | Tools are exposed and selected dynamically |
| Input → logic → response | Input → thoughts → actions → response |

Agents blur the line between logic and language — and put decision-making inside the model.

---

## 🧱 Agent Design Patterns

### 🧠 ReAct Loop
```
Thought → Action → Observation → Thought → Answer
```

### 🧠 Planner–Executor

- Agent 1: plans steps
- Agent 2: executes tools

### 🧠 Agent-as-API-Router

Let a single LLM route requests to the right API or logic layer based on intent.

### 🧠 Agent-as-Backend-Brain

Instead of chaining APIs in microservices, the agent decides what to call, when, and in what order.

---

## 🔧 What Are Tools?

In LangChain or OpenAI:
> A **tool** is a function the model can call.

You define:
```python
@tool
def get_weather(city: str) -> str:
    return "Sunny and 23°C in Delhi"
```

And the model decides when to call it — with what args.

---

## 🛠️ Build a Tool-Using Agent

```python
from langchain.agents import tool

@tool
def get_exchange_rate(currency: str) -> str:
    return "1 USD = 83 INR"
```

Then:
```python
from langchain.chat_models import ChatOpenAI
from langchain.agents import initialize_agent, AgentType

llm = ChatOpenAI(model="gpt-4")
agent = initialize_agent(
    tools=[get_exchange_rate],
    llm=llm,
    agent=AgentType.OPENAI_FUNCTIONS,
    verbose=True,
)

agent.run("Convert $1 to INR")
```

✅ Now the model thinks + acts — not just responds.

---

## 🧠 When Do Agents Replace Microservices?

Sometimes the LLM + tool layer **is** your backend logic.

| Task | Traditional Microservice | Agentic Alternative |
|------|---------------------------|----------------------|
| Intent classification | Flask route or model | LLM with ReAct routing |
| Email triage | Rules or model → API call | Agent chooses tool based on message |
| Q&A + math + lookup | Separate endpoints | One agent choosing which to do |

> You’re no longer hardwiring decision trees — you’re exposing tools and letting the agent drive.

Agents let you **move logic from code into controlled language** — making systems more flexible.

---

## 🧪 Microproject: Agentify Your App

Take a mini FastAPI app with 2–3 tools:

- Joke generator
- Weather API stub
- Exchange rate calculator

Build:
✅ A hardcoded `if/elif` version  
✅ An agent-driven version using function calling  
✅ Log `thought`, `action`, `observation` at each step

Bonus:

- Add retry if a tool fails
- Add a loop limit (max 5 steps)

---

## 🧠 Reflection Questions

1. What logic can you move from backend code into a prompt?
2. When should you *not* let the model pick tools?
3. Where might agents introduce risk or fragility?

---

## ✅ Best Practices for Agent Design

✅ Tools must be deterministic, safe, and well-typed  
✅ Log every thought, action, observation  
✅ Use short tool names + descriptions for LLM clarity  
✅ Cap max steps — agents can loop forever if you’re not careful  
✅ Fallback to static logic when confidence is low  
✅ Let tools fail gracefully — model should adapt

> 🔐 Don’t give agents the kitchen sink. Give them power **with guardrails**.

---

## ✅ You Now Know:

✅ Why agents changed how we build AI systems  
✅ How to build a reasoning + acting loop  
✅ How to expose tools to the model  
✅ How to design agentic workflows that feel like dynamic APIs  
✅ When to replace brittle logic with LLM reasoning