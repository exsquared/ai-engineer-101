# Chapter 7: Hybrid Systems — When Rules and Learning Work Together

---

## 🎯 Goal

To understand how to combine **rules** and **machine learning models** (including LLMs) in practical, production-friendly ways.

This is how real systems are built: not all-learning, not all-logic — but a smart, debug-friendly mix.

---

### 🤹 Why Blend Logic and Learning?

Let’s revisit our support ticket example:

You now have:

- A trained urgency classifier
- A model to assign categories

But what happens when:

- The message is empty?
- The user just says “Hi”?  
- The model’s confidence is 41%?
- A known VIP user contacts support?

> These aren’t training issues — they’re business rules.

> Real-world systems need **guardrails**. And models need help knowing when to defer.

---

### 🧠 Rules Still Matter

Here are examples of **pre-model rules**:

```python
if len(message.strip()) < 10:
    return {"action": "ignore", "reason": "Too short"}

if "vip_user" in user_tags:
    return {"action": "escalate", "reason": "High-value customer"}
```

Or **post-model overrides**:

```python
if model_confidence < 0.4:
    return {"action": "human_review"}
```

---

### 🔁 Logic + Model Pipeline

> 🧠 **Side Note:** What’s a *pipeline*?  
> Think of it like a chain of steps:  
> 1. You check the input  
> 2. You run it through your model  
> 3. You handle the result  
> 4. You decide what to do  

> In ML systems, we often call this flow a **pipeline** — because data flows through it like a factory line.  
> It helps us break things into clear, testable parts.

Build your system like a pipeline:

```python
def triage_pipeline(message):
    if not is_valid_message(message):
        return {"action": "ignore"}

    if matches_blocklist(message):
        return {"action": "block"}

    # Pass to model
    prediction = model.predict(message)

    if prediction.confidence < 0.4:
        return {"action": "human_review"}

    return {"action": "route", "category": prediction.category}
```

> This is *hybrid engineering*. Models make suggestions. Logic makes decisions.

---

### 🤖 Bonus: LLMs as Fallbacks

Let’s say your model says "I don’t know" (low confidence). Now what?

You can try asking an LLM to help — not to replace your model, but to **back it up intelligently**.

```python
if model_confidence < 0.4:
    prompt = f"Classify this message: '{message}'"
    llm_result = call_llm(prompt)
    return {"action": "fallback_llm", "category": llm_result}
```

Or use LLMs for specific cases:

- Detect tone
- Extract entities
- Explain intent

> LLMs are great at gray areas. Use them for *language nuance*, not business rules.

---

### 🧪 Exercise: Build a Hybrid System

Extend your ticket triage logic:

1. Add pre-checks (e.g., empty message, spam keywords)
2. Route clear tickets through your classifier
3. If confidence < 0.5, call an LLM as a backup classifier
4. Log all decisions and confidence levels

> Bonus: Show comparison between model vs LLM output

---

### 💭 Reflection

- What decisions can’t be learned — and must be hard-coded?
- When does it make sense to add an LLM instead of training a bigger model?
- Where do *you* draw the line between logic and learning?

---

### ✅ Exit Outcome

You now know how to:

- Use **rules before or after** ML systems
- Route edge cases safely
- Use LLMs to **extend and catch** when classic models fail

This is what most real production AI pipelines look like: part-logic, part-learning, part-LLM.

---

### ⏭️ Coming Up

Next, we’ll wrap our model into a real **API service** using FastAPI — so it can plug into any product or app you build.
