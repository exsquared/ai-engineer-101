
# How LLMs Work — Context, Embeddings, and Reasoning

---

## 🌉 Why Are We Here?

In the last chapter, you built logic pipelines combining rules, models, and LLMs. But you’ve probably noticed something weird:

- Sometimes the LLM gives a brilliant answer.
- Sometimes it sounds confident but is dead wrong.
- Sometimes changing one word in your prompt *completely* changes the outcome.

What’s going on?

To build anything reliable with LLMs, you need to understand how they actually “think.” This chapter is that foundation.

> You’re not just learning how to prompt. You’re learning how the machine predicts.

---

## 🎯 Goal

To build a working mental model of how LLMs operate:

- What a "language model" is — and what it’s not
- How inputs are processed via tokens and context windows
- Why order, structure, and precision in prompts matter
- What embeddings are and how they underpin many AI systems
- When and why LLMs produce surprising or incorrect answers

---

## 🧠 What Is a Language Model?

A **language model** is not a reasoning engine, chatbot, or planning agent.

At its core, it is a system trained to do just one thing:
> **Given a sequence of text, predict the most likely next token.**

That's it.

```text
Input: "My favorite programming language is"
Model: ["Python" (78%), "JavaScript" (10%), "C++" (5%), ...]
```

Language models are trained on massive text datasets to learn:

- Word patterns and probabilities
- Syntax and semantics
- How different topics and tones tend to flow

They do not truly *understand* or *reason*. They excel at generating plausible continuations of text — and that’s what makes them powerful (and sometimes misleading).

---

### 🧠 How Does It Predict the Next Token?

Behind the scenes, most modern LLMs use a neural network architecture called a **Transformer**.

The Transformer has a key mechanism called **attention**, which allows the model to look at all the tokens in the prompt and weigh their importance differently.

> Think of it like reading a paragraph with a highlighter — the model "pays more attention" to the parts that matter most when choosing the next word.

This allows the model to:

- Understand long-range dependencies
- Make predictions based on relationships between words
- Scale to very large inputs and outputs

---

## 🧩 Tokens and Context Windows

LLMs don’t see whole paragraphs — they see **tokens**, which are fragments of text:

- A word: `dog`
- A subword: `un`, `believ`, `able`
- Punctuation: `.` or `?`

### ⏳ What’s a Context Window?

Think of it like the model’s short-term memory. It’s the number of tokens it can “see” at one time.

- GPT-3.5: ~4,000 tokens (~3,000 words)
- GPT-4 (turbo): up to 128,000 tokens (~100,000 words)

If your input + output exceeds this window:

- Earlier context is dropped
- The model starts forgetting what came before

> 🧠 Analogy: Imagine whispering into a tunnel. The model only hears the last X words you say.

---

## 🔠 Tokenization in Practice

```python
from transformers import GPT2Tokenizer

tokenizer = GPT2Tokenizer.from_pretrained("gpt2")
tokens = tokenizer.tokenize("Language models don't reason.")
print(tokens)
```

Output:
```text
['Language', 'Ġmodels', 'Ġdon', "'", 't', 'Ġreason', '.']
```

---

## 🧠 Embeddings: Meaning in Vector Space

Before LLMs predict anything, they convert text into **embeddings** — high-dimensional vectors that encode meaning.

Embeddings allow the model to:

- Measure similarity between texts
- Cluster related ideas
- Match prompts to known patterns

```python
from openai import OpenAI

response = openai.Embedding.create(input="How do I reset my password?", model="text-embedding-ada-002")
vector = response['data'][0]['embedding']
```

---

## 🧠 Intent vs Prompt — Not the Same Thing

What you *want* and what you *say* are different.

### Example:

❌ Prompt: "Tell me about our app."

- 🤖 Output: vague, promotional, possibly off-topic

✅ Prompt: "List 3 features of our app in bullet points. Include only 1 sentence per point."

- 🤖 Output: clear, usable, and structured

---

## 🧱 System vs User Roles

Chat-based models use structured messages:
```python
messages = [
  {"role": "system", "content": "You are a precise technical assistant."},
  {"role": "user", "content": "Summarize this customer complaint."}
]
```

- **System message**: defines tone, identity, rules
- **User message**: the actual task

---

## ⚙️ Temperature and Top-p: Controlling Output Behavior

### 🔥 Temperature

- `0.0`: deterministic
- `1.0+`: exploratory

### 🎲 Top-p

- Select from the top `p%` of possible next tokens

```python
temperature=0.2, top_p=0.8  # Reliable for structured tasks
```

---

## ❗ A Word on Hallucination (At the End, Where It Belongs)

LLMs don’t “know” facts or have external memory. They guess what *sounds* right.

That’s why:

- They invent URLs
- Misquote legal clauses
- Fabricate names or facts

### How to reduce it:

- Give more structure
- Add documents to prompt (RAG)
- Ask for sources, but verify

---

## 🧠 Final Analogy: A Genius Autocomplete

Imagine a super-smart autocomplete:

- It’s read billions of documents
- But it only guesses what comes next — based on what it’s seen
- It forgets everything the moment it responds

> Powerful? Yes. Grounded? Only if you design it that way.

---

## ✅ Exit Outcome

You now:

- Know what a language model really is
- Understand tokens, context windows, and embeddings
- Can structure prompts with intention and control
- Have tools to start building reliable, debuggable LLM systems

> In the next chapter, we’ll turn this theory into hands-on prompting — building classifiers, transformers, and smart assistants. Let’s go.
