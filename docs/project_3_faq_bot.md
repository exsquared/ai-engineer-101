# ✅ Checkpoint Project #3: LLM-Powered FAQ Bot

---

## 🧠 Why This Project?

After learning:
- How LLMs work (tokens, context windows, hallucination)
- How to write and structure prompts
- How to inject role instructions and fallbacks

…it’s time to build something useful and test your prompting skills.

---

## 🎯 Project Goal

Create a working command-line or Streamlit assistant that answers questions based on a company FAQ — but **only** using information in the FAQ.

The assistant should:
- Receive a question
- Inject relevant FAQ content into the prompt
- Ask the LLM to answer
- Fall back gracefully if unsure

---

## 🧱 System Design Overview

Your pipeline will:

1. Take a user question  
2. Search your FAQ for relevant content (manually or via keyword match)  
3. Inject it into a prompt  
4. Call OpenAI (e.g., `gpt-3.5-turbo`)  
5. Return the answer **or** fallback if not confident

---

## 🗃️ Dataset

Use or create a small `faq.txt` or `faq.json` file with ~10–20 questions and answers. Example:

```json
[
  {
    "question": "How do I reset my password?",
    "answer": "Click 'Forgot password' on the login screen and follow the instructions."
  },
  {
    "question": "How can I cancel my subscription?",
    "answer": "Go to Billing → Manage Subscription and select Cancel."
  }
]
```

---

## 🔨 Implementation Checklist

### Step 1: Build a Simple Keyword Search

```python
def search_faq(question, faq_list):
    return [entry for entry in faq_list if any(word in entry["question"] for word in question.lower().split())]
```

---

### Step 2: Design the Prompt Template

```python
PROMPT_TEMPLATE = '''
You are a helpful assistant answering questions about our company FAQ.
Only use the information below to answer. If unsure, say “Sorry, I’m not sure.”

FAQ:
{faq_chunks}

Question: {question}
Answer:
'''
```

---

### Step 3: Call OpenAI

```python
import openai

def ask_llm(prompt):
    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content.strip()
```

---

### Step 4: Build the Loop

```python
while True:
    q = input("Ask a question (or 'q' to quit): ")
    if q == 'q': break

    faq_hits = search_faq(q, faq_data)
    faq_context = "\n".join([f"- {x['answer']}" for x in faq_hits[:3]])

    prompt = PROMPT_TEMPLATE.format(faq_chunks=faq_context, question=q)
    answer = ask_llm(prompt)
    print("Answer:", answer)
```

---

## 🧪 Bonus Features

- Add a fallback if `faq_hits == []`
- Track if the answer contains “Sorry” → count as fallback
- Let the user give feedback (thumbs up/down)
- Add a confidence disclaimer automatically (e.g. “Based on our FAQ…”)

---

## 💻 Optional: Streamlit UI

```python
import streamlit as st
st.title("FAQ Assistant")
q = st.text_input("Ask a question:")
if q:
    # same flow as CLI
    st.write(answer)
```

---

## 📋 What to Submit

- Python script or notebook
- Your `faq.json` or `.txt` file
- Screenshots or console output from 5–10 test questions
- A 1–2 paragraph reflection:
  - When did the model hallucinate?
  - What made answers better or worse?
  - How did your prompt affect the result?

---

## ✅ What You’ve Practiced

- Prompt templating
- Manual context injection
- Hallucination prevention strategies
- Building a minimal retrieval-based LLM tool