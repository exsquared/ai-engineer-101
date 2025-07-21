# Hybrid Systems — When Rules and Learning Work Together

## 🌉 Why Are We Here?

In the last chapter, we built a model that could route tickets across multiple departments — from billing to tech support to feedback. It was smart. But not smart enough.

Because here’s the thing about real-world AI systems:
> They *don’t just guess* — they *decide with caution*.

What happens when the model isn’t confident? Or when it sees something it’s never encountered before? What about business rules — like escalating VIP users, or ignoring empty messages?

This is where hybrid systems shine.

In this chapter, you’ll learn how to combine:

- 🧠 Machine learning (classifiers, confidence scores)
- 🧾 Hand-written rules (edge cases, exceptions)
- 🤖 LLMs (as a backup brain for ambiguous inputs)

This is how production AI really works: **not all-learning, not all-logic — but a smart, explainable blend.**

---

## 🎯 Goal

To design a robust, flexible pipeline that combines classical models, business rules, and fallback strategies — and knows when to ask for help.

---

## 🧠 The Setup: Why Multi-Class Isn’t Enough

Remember our ticket classifier?
It could choose from `Billing`, `Technical`, `Feedback`, or `Other`.

But what happens when:

- The message is: “Hello?”
- The message is blank, or just an emoji
- A new ticket type appears: “I want to delete my account” (which doesn’t fit any current label)
- The model is only **41% confident** in its guess
- The message is from a **VIP customer**

> None of these are classification problems. They’re design problems.

We’ve now moved from *“what class?”* to *“what should we do?”*

That’s the key shift: from prediction → to action. And it’s why we need a hybrid approach.

---

## 🏗️ What’s a Hybrid System?

A hybrid system is like a traffic cop:

- Sometimes it lets the model take the wheel.
- Sometimes it steps in to redirect, override, or pause.

> Think of it as a smart AI assistant with a supervisor — the rules.

You might:

- Use rules **before** the model to catch garbage inputs
- Use the model to make predictions
- Use rules **after** to decide what to do with the prediction
- Use an LLM when the model’s answer isn’t confident or convincing

---

## 🔧 Building the Logic + Model Pipeline

Let’s sketch the core idea:

```python
def triage_pipeline(message, user_tags):
    if not message or len(message.strip()) < 5:
        return {"action": "ignore", "reason": "Empty or too short"}

    if "vip" in user_tags:
        return {"action": "escalate", "reason": "VIP customer"}

    prediction = model.predict(message)
    confidence = max(model.predict_proba([message])[0])

    if confidence < 0.5:
        return {"action": "defer_to_llm", "input": message, "confidence": confidence}

    return {"action": "route", "label": prediction, "confidence": confidence}
```

You’ve just built:

- ✅ Pre-model logic (message validation, VIP handling)
- ✅ Model prediction
- ✅ Post-model routing
- ✅ Fallback hook for LLMs

This is real-world AI plumbing — flexible, transparent, and safe.

---

## 🤖 When to Use an LLM

LLMs are not magic. But they *are* useful when things get messy:

- The model has low confidence
- The user says something open-ended
- You want to explain, rephrase, or reclassify

```python
if confidence < 0.5:
    prompt = f"Classify this message: '{message}'"
    llm_response = call_llm(prompt)
    return {"action": "llm_fallback", "suggested_label": llm_response}
```

Think of the LLM as a junior analyst — smart, flexible, and verbose. But not in charge of critical decisions without supervision.

---

## 🔍 Real-World Analogy: Triage Nurse

In hospitals, the triage nurse doesn’t diagnose — they decide where a patient should go:

- Chest pain? → Cardiologist
- Broken bone? → Ortho
- Unclear symptoms? → Doctor review

Your pipeline does the same:

- Obvious input? → Model predicts
- Sensitive case? → Rule escalates
- Ambiguous? → LLM suggests

This analogy helps product teams, PMs, and engineers alike reason about AI behavior.

---

## 🧪 Build Your Own Hybrid System

Take your ticket classifier from Chapter 6 and wrap it in a pipeline:

1. Pre-check for empty messages
2. Route VIPs straight to escalation
3. Run the model and extract confidence
4. Defer to an LLM if confidence < 0.5
5. Log every decision and why it happened

> Bonus: Print a side-by-side comparison of model vs. LLM label.

---

## 💬 Reflection Corner

- What’s a case in your product where you’d want the model to *not* decide?
- Where might you use hard rules to override model behavior?
- What’s your threshold for “low confidence”? Why?
- When does it make sense to blend prediction + human judgment?

---

## ✅ Exit Outcome

You now understand:

- Why real AI systems are not just models
- How to build a safe, explainable ML pipeline
- When to rely on logic, and when to ask for help

This is the foundation for **trustworthy AI in production.**

---

## ⏭️ Coming Up

In the next chapter, we’ll take a bold step forward into the world of Large Language Models (LLMs) — the powerful engines behind tools like ChatGPT and GitHub Copilot.

You’ve already used LLMs as fallbacks. Now, you’ll learn how to work with them directly:

- Crafting smart prompts
- Automating workflows
- Using LLMs for classification, summarization, and structured reasoning

This is your entry into the modern AI toolkit — fast, flexible, and incredibly powerful.

Let’s unlock it together. 🚀
