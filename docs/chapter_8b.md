
# Prompting in Action — LLMs as Classifiers, Transformers, and Smart Assistants

---

## 🌉 Why Are We Here?

In Chapter 8A, you learned how language models actually work — token by token, prediction by prediction. You also saw how their behavior is shaped by structure: context windows, embeddings, and prompts.

Now it’s time to put that knowledge into action.

This chapter is about **how to talk to LLMs** so they do what you want. Whether it’s classifying a message, rewriting text, or extracting structured data — the key is great prompting.

---

## 🎯 Goal

By the end of this chapter, you’ll be able to:
- Write prompts that produce consistent, structured results
- Use LLMs for classification, summarization, tone-shifting, and extraction
- Debug and refine prompt behavior
- Build reusable prompt templates that plug into real systems

---

## 🧠 The Big Idea: Prompts Are Interfaces

Prompts are not one-off strings. They’re **contracts**:
- Between you and the model
- Between your system and the response
- Between your logic and what downstream code expects

> A good prompt = consistent output + minimal surprises

---

## ✍️ Prompt Recipe: Structure First

When prompting for apps, you want:
- Determinism
- Parsability (JSON or structured text)
- Clarity of task

Here’s a best-practice recipe:

```python
SYSTEM_PROMPT = "You are a helpful and structured AI assistant. Always respond in JSON."

USER_PROMPT = """
Classify the following support message:
- Choose one: Billing, Technical, Feedback, Other
- Respond in JSON using a single key: category

Message: "I was charged twice for my subscription."
"""
```

---

## 🧪 Prompt Use Cases in Action

### 🟩 1. Classification

```text
Classify this message as one of:
- Billing
- Technical
- Feedback
- Other

Message: “App keeps crashing when I open it.”
```

Output:
```json
{ "category": "Technical" }
```

---

### 🟦 2. Summarization

```text
Summarize this in one sentence:
“I can’t log in after resetting my password. Keeps saying ‘invalid credentials.’”
```

Output:
```text
User can’t log in after password reset due to invalid credential errors.
```

---

### 🟨 3. Tone Shifting / Rewriting

```text
Rewrite the following in a friendly, casual tone:

“We regret to inform you that we cannot approve your request at this time.”
```

Output:
```text
Sorry, but we can’t approve that request right now.
```

---

### 🟥 4. Structured Extraction (Entities, Intent, Urgency)

```text
Extract the following fields from this message:
- intent
- urgency (low, medium, high)
- product

Message: “I was double charged for my Premium plan. Please fix this today!”

Respond in JSON only.
```

Output:
```json
{
  "intent": "billing_issue",
  "urgency": "high",
  "product": "Premium plan"
}
```

---

## 🧩 Prompt Template = Reusable Component

Rather than hardcoding prompts everywhere, define templates with:
- Parameters (`{message}`)
- Examples (few-shot)
- Format enforcement

```python
def classify_message(message):
    return f"""
Classify the message: '{message}'

Choose from:
- Billing
- Technical
- Feedback
- Other

Respond in JSON.
"""
```

Use the same template in tests, logs, and services.

---

## 🐛 Debugging Prompt Failures

### Common Problems:
- Output format changes randomly
- LLM “explains” instead of responding in JSON
- Hallucinated fields or incorrect keys

### Fixes:
- Use system prompts for behavioral control
- Set temperature = 0.2 for deterministic outputs
- Use “ONLY respond in JSON” language (repeat if needed)
- Add output examples as few-shot anchor

---

## 🎮 Mini Project: Smart LLM Triage Assistant

Build a function that:
1. Classifies a message into one of 4 categories
2. Extracts urgency
3. Returns JSON with:
   - `category`
   - `urgency`
   - `needs_review` flag if confidence is low
   - `explanation` field (optional chain-of-thought)

Bonus:
- Let the user query: “Why did you classify it that way?”

---

## 💬 Reflection Corner

- What prompt structures worked best for you?
- When would you use an LLM instead of a trained classifier?
- How might you productionize these prompts as a service?

---

## ✅ Exit Outcome

You now:
- Can craft practical, structured prompts for real use cases
- Know how to turn LLMs into useful utilities
- Understand how to debug and standardize output
- Have patterns you can reuse across your apps

> In the next chapter, we’ll explore advanced prompting strategies like few-shot, chain-of-thought, and intent detection. Let’s level up!
