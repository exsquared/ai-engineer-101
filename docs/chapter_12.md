# Let LLMs Act, Not Just Answer  
### _Agents, Reasoning Loops, and Tool Use_

---

## 🧭 Why Are We Here?

Up until now, we’ve used LLMs like really smart parrots.

You feed them some context.  
They give you a great answer.  
That’s it. One-shot, one-turn.

But what if we want more?

> “Search Google for flight options, check the weather, compare it to my calendar, and suggest the best time to travel.”

This isn’t just Q&A.  
This is **multi-step reasoning + tool use**.  
You don’t want the model to just answer.  
You want it to **think**, **decide**, and **act**.

That’s the world of **agents**.

---

## 🤖 What Is an Agent?

An **Agent** is an LLM system that:
1. Reads the user's intent  
2. Plans a series of actions  
3. Uses tools (APIs, functions, calculators, etc.)  
4. Loops until it reaches a final answer

Think of it like:
- The LLM is the brain  
- The tools are its hands  
- The agent system is the “nervous system” orchestrating decisions

---

## 🧠 Real-World Analogy

You: “What’s the cheapest non-stop flight from NYC to SF next Friday under 5 hours?”

Your intern:
1. Opens Google Flights  
2. Applies filters  
3. Looks at durations  
4. Picks the cheapest one  
5. Summarizes it for you

They don’t know the answer _a priori_ — they **figure it out by taking actions**.

---

## 🔁 Agent Loop in Plain English

Every time the agent thinks:
1. **Observe**: What’s the current task or question?  
2. **Think**: What tool do I need?  
3. **Act**: Use a tool (e.g., search API, calculator)  
4. **Read result**: Did it solve the task?  
5. **Repeat**: Until it reaches an answer

This is called a **Reasoning-Acting Loop**.

---

## 🧰 Tools You Can Give an Agent

| Tool Type     | Example                 |
|---------------|-------------------------|
| Calculator    | `"4 * 17"`              |
| Python code   | `"Sort by date"`        |
| Web search    | `"Scrape this page"`    |
| File reader   | `"Read the invoice PDF"`|
| Custom API    | `"GET /user/profile"`   |

Each tool is just a **function** the agent is allowed to call.

---

## 🛠️ Frameworks That Help

| Framework             | Purpose                                    |
|------------------------|--------------------------------------------|
| **LangChain**          | Build agents from pre-built toolkits       |
| **OpenAI Function Calling** | Let GPT “choose” and call your functions |
| **CrewAI / AutoGen**   | Multi-agent orchestration (advanced)       |

We’ll focus on **LangChain + OpenAI Function Calling** for now.

---

## ⚙️ How Function Calling Works (OpenAI)

1. You define functions you want to expose  
2. The LLM is told about their names and parameters  
3. It chooses one and sends a structured JSON payload  
4. Your code runs the function and feeds the result back  
5. The model continues reasoning with the new info

> The model “thinks” and _asks_ to use tools — your code runs the tool for it.

---

## 💻 Let’s Build a Simple Agent

### 🧮 Step 1: Define Tools

```python
from langchain.agents import tool

@tool
def get_exchange_rate(currency: str) -> str:
    if currency == "USD":
        return "1 USD = 83 INR"
    return f"No data for {currency}"
```

---

### 🧠 Step 2: Create the Agent

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

agent.run("What is the exchange rate for USD?")
```

---

### 🔍 What’s Happening Under the Hood?

1. The LLM reads your query  
2. It decides to call `get_exchange_rate()`  
3. It sends the input `"USD"`  
4. Your Python function runs  
5. The result is injected back into the prompt  
6. The LLM completes the answer using the result

You didn’t hardcode any logic — the LLM figured it out.

---

## 🧠 Agents vs RAG

| Use Case        | RAG                            | Agent                                 |
|------------------|----------------------------------|----------------------------------------|
| Need doc info?   | ✅ Use vector search             | ➖ Only if you combine with RAG         |
| Need to act?     | ➖ No tool usage                 | ✅ Call APIs, calculate, fetch, etc.    |
| Need both?       | ✅ Combine RAG + Agents          | ✅ Smartest choice                      |

---

## ⚠️ Gotchas & Design Tips

- **Tool reliability** matters — bad APIs = broken agents  
- **Loop limits** prevent infinite reasoning  
- **Logging** is essential — observe the agent’s thought chain  
- **Prompting still matters** — agents need structure and constraints

---

## 🔬 What Advanced Agents Can Do (Preview)

- Chain tools together  
- Maintain short-term memory  
- Work as multiple agents with roles  
- Execute plans from scratch (planner/executor)  
- Monitor their own uncertainty and retry

---

## 📦 Patterns Emerging from Agents

### 🔹 Toolformer

Models trained not just to generate text — but to **learn when and how to use tools** during pretraining.  
Instead of hard-coding tool use, Toolformer _predicts_ when a tool is needed and automatically integrates API usage into its generation.  
Great for automating complex decision + data workflows.

### 🔹 ReAct

Short for **Reasoning + Acting**. A prompt strategy where the model moves step by step:

```
Thought: I need to check the current exchange rate  
Action: get_exchange_rate("USD")  
Observation: 1 USD = 83 INR  
Thought: Now I can respond  
Answer: The rate is 83 INR
```

ReAct is helpful when you want **transparent, debuggable steps** and looping behavior.

### 🔹 Planner-Executor

One LLM **plans the high-level steps**, another **executes** them.  
For example:
- Planner: “Step 1: Get exchange rate. Step 2: Calculate budget. Step 3: Suggest destination.”  
- Executor: Runs each step using tools or APIs.

This pattern:
- Separates concerns  
- Enables modular debugging  
- Scales better in complex tasks

---

## 🧭 What’s Coming Next: Think Beyond the Model

Now that you’ve mastered retrieval and reasoning, the next frontier is **system-level intelligence**. Here are key architecture patterns you'll encounter:

### 🧱 MCP — Model Context Protocol

A **spec for how you feed models information**. MCP defines:
- What context types a model should receive (e.g. user history, environment, goals)
- How that context is prioritized, formatted, and trimmed to fit the token budget
- How to trace which context influenced which outputs

It’s like OpenAPI — but for prompts.

### 🔁 A2A — Agent-to-Agent Communication

Just like services talk via APIs, agents can talk via language.
- A Planner Agent can send instructions to Worker Agents
- Agents with different capabilities or memory scopes can cooperate

You’ll learn how to **route subtasks**, track ownership, and design protocols between agents.

### 🧠 Context Engineering

LLMs are context-hungry. The trick isn’t just more tokens — it’s **better tokens**.

You’ll learn:
- How to select the right document chunks
- How to rank/filter based on user intent
- How to format context (inline vs. structured)

This is where **retrieval meets UX meets architecture**.

---

## 🧠 Recap: You Now Know…

✅ What agents are and why they matter  
✅ How tool-calling works using function APIs  
✅ How the reasoning-act loop works  
✅ How to build agents with LangChain  
✅ When to use agents vs RAG  
✅ Common agent patterns  
✅ What’s coming next at the system level

---

## ❓Reflection Questions

1. When should you prefer RAG over agents? When should you combine both?  
2. What real-world apps in your org could benefit from tool-calling?  
3. What risks should you monitor in agent loops and tool chaining?  
4. How would you explain the ReAct pattern to a teammate?  
5. How might MCP help your engineering team make LLM use more robust and auditable?

---

## 🧪 Mini Quiz

**Q1.** What does an agent do that a normal LLM call doesn’t?  
✅ c) Plans and calls tools

**Q2.** Why are function-calling APIs safer than raw prompting?  
✅ b) You get structured outputs

---

## 🧪 Microproject

Build a **Currency & Travel Advisor Agent**:
- Ask the user: “What currency do you want to check?”  
- Use `get_exchange_rate()` to respond  
- Add another tool: `get_travel_advice(country)`  
- Chain them: currency → visa → safety tips  
- Log the agent’s full reasoning steps

---

## 🎉 Core Track Finale: What You’ve Built

You’ve now created:
- A confident classifier  
- A doc-aware assistant (RAG)  
- A tool-using agent  
- And built the foundation of **real LLM applications**

---

## 🔜 Next Chapter: Let the Model See

Next, you’ll explore **GPT-4V and multimodal models** — where LLMs can read images, screenshots, graphs, and more.

Welcome to the GenAI Playground.