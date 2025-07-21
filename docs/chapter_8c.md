
# 🚀 Prompting Strategies — Few-Shot, Chain-of-Thought, and Intent Extraction

---

## 🌉 Why Are We Here?

In Chapter 8B, you built LLM-based tools using well-structured prompts. But what happens when:
- Your prompt works inconsistently?
- The task requires examples or reasoning?
- You want more control over how the model reasons?

This chapter introduces **advanced prompting strategies** used in real-world systems:
- Zero-shot vs. few-shot prompting
- Chain-of-thought and train-of-thought prompting
- Intent and entity extraction
- Prompt routing and multi-stage chaining

---

## 🎯 Goal

You will:
- Learn when and why to use few-shot and chain-of-thought
- Extract structured data like intent and urgency
- Design and debug modular prompt pipelines
- Build LLM systems that handle complex reasoning

---

## 🧠 Zero-Shot Prompting (Quick Review)

Most prompts so far have been zero-shot:
> Give instructions, expect the LLM to figure it out

```text
Classify this message: "I was charged twice."
```

Zero-shot is fast but:
- Brittle on vague or ambiguous input
- Often inconsistent across edge cases

---

## ✨ Few-Shot Prompting: Show, Don’t Just Tell

Few-shot prompting includes **examples** to guide behavior:

```text
Classify each message into: Billing, Technical, Feedback, Other

Example 1: "I was charged twice" → Billing  
Example 2: "App crashes at login" → Technical  
Example 3: "Love the new UI" → Feedback

Message: "Why am I still being billed after cancelling?"
```

Few-shot helps:
- Reduce hallucinations
- Improve consistency
- Anchor the format you want returned

---

## 🧠 Chain-of-Thought Prompting

Sometimes, models guess too quickly. Chain-of-thought encourages deliberate reasoning:

```text
Q: I gave away 2 apples from a basket of 5. How many are left?  
A: Let's think step by step.
```

Output:
```
- Started with 5 apples  
- Gave away 2  
- Remaining: 3
```

Useful for:
- Math
- Multi-hop logic
- Debuggable output

---

## 🧠 Train-of-Thought Prompting

Similar to CoT, but:
- Explores **multiple perspectives**
- Ideal for ideation, ambiguity, “what-if” scenarios

```text
What are 3 different ways to interpret this customer message:  
"I’m not happy with my recent experience."  
```

---

## 🧩 Intent Extraction — Your LLM’s First Real Skill

Intent = the user’s goal  
Entities = key fields, names, amounts, plans, etc.

### 🔁 Example Prompt:
```text
Extract the following from this message:
- intent
- urgency (low, medium, high)
- product

Message: “I was double charged for my Premium plan. Please fix this today!”

Respond in JSON.
```

### ✅ Output:
```json
{
  "intent": "billing_issue",
  "urgency": "high",
  "product": "Premium plan"
}
```

### 🧪 Code Snippet
```python
def extract_intent(message):
    return f"""
Extract intent, urgency, and product from:
"{message}"

Respond in JSON with:
- intent
- urgency (low | medium | high)
- product
"""
```

---

## 🧠 Prompt Composition Patterns

Real systems often:
- Combine prompts in stages
- Route different queries to different templates
- Chain LLM calls (e.g., classification → summarization)

### Pattern Examples:
- Stage 1: Classify → Stage 2: Explain
- Stage 1: Extract → Stage 2: Ask follow-up
- Stage 1: Rewrite → Stage 2: Translate

Use frameworks like:
- LangChain
- Guidance
- PromptLayer

---

## 🧰 Prompt Debugging Cheatsheet

| Problem                        | Solution                              |
|-------------------------------|---------------------------------------|
| Output isn’t consistent       | Add few-shot examples                 |
| Output isn’t valid JSON       | Repeat format instructions twice      |
| Output too verbose            | Use “Respond only with...”            |
| LLM flips between labels      | Lower temperature, anchor few-shot    |

---

## 🎮 Prompt Comparison Grid

Try running these variants:

| Input                         | Strategy     | Prompt                              | Output   |
|------------------------------|--------------|-------------------------------------|----------|
| “I need help logging in”     | Zero-shot    | Classify this message               | ?        |
|                              | Few-shot     | With labeled examples               | Technical|
| “App is too slow at night”   | CoT          | Think step by step                  | ?        |

Compare outputs side-by-side. This is how LLM engineers tune behavior.

---

## 💭 Reflection

- Where in your product could intent classification help?
- Which pattern felt most reliable for debugging?
- Would you build one mega-prompt or break into modules?

---

## ✅ Exit Outcome

You now:
- Understand and apply few-shot and chain-of-thought prompting
- Can extract structured fields like intent and urgency
- Are ready to chain LLM prompts and route them modularly

> Next: Wrap your logic into a scalable, production-ready FastAPI service.
