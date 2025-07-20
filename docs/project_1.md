# 🧠 Checkpoint Project 1 : From Rules to Predictions

---

## 🪜 Why Now?

You’ve just learned how models make predictions — and more importantly, how to handle uncertainty using confidence scores and fallback logic.

Now it’s time to zoom out and compare three different ways to solve the same problem:

- Hard-coded logic
- Pattern matching
- Machine learning

This is your chance to see where each technique shines — and where it breaks.

---

## 🎯 Goal

Use everything you’ve learned so far to compare three ways of solving a real-world classification problem:

1. Rule-based logic
2. Pattern-based heuristics (like regex)
3. Machine learning (your first trained model)

By the end, you’ll reflect on where each approach worked — and where it fell short.

---

### 🧠 The Scenario

Imagine you’re building a system that tags incoming messages (emails, tickets, chats) as **urgent** or **not urgent**.

Your team has a growing inbox, and needs a better way to prioritize what to handle first. Your job is to prototype and evaluate different solutions.

---

### 🛠️ What You'll Build

A Jupyter notebook or script with these parts:

#### Part 1: Rule-Based Classifier

- Write simple `if`/`else` logic like:

```python
if "urgent" in message.lower():
    return True
```

- Add more rules based on exclamation marks, capital letters, or known keywords

#### Part 2: Regex / Pattern-Based

- Use regular expressions to match variants like:

```python
\b(asap|immediately|refund|angry)\b
```

- Tune the pattern matching based on message phrasing

#### Part 3: ML-Based Classifier

- Create a dataset of 10–20 example messages with a label (`urgent` = 1 or 0)
- Extract simple features:
  - Word count
  - Number of exclamations
  - Presence of urgent-sounding words
- Train a `LogisticRegression` model
- Print weights, accuracy, and predictions

#### Part 4: Reflection

- Where did each approach do well?
- Where did rules break down?
- Where did the model make mistakes?
- Which system would you trust in production (and why)?

---

### 🧪 Sample Dataset (Start Here If You Want)

| Message                                | Urgent? |
|----------------------------------------|---------|
| I need help with this ASAP!            | 1       |
| Can you check this when you’re free?   | 0       |
| THIS IS BROKEN. I’M ANGRY.             | 1       |
| Just a heads up on the new pricing     | 0       |
| Please call me back immediately!!!     | 1       |

Create more — mix calm, polite, angry, confusing messages.

---

### 🌟 Bonus Challenges

- Try visualizing your model’s predictions (scatterplot with matplotlib)
- Let users paste in a message and see which system flags it as urgent
- Build a feedback loop: update the model when a human tags a message differently

---

### ✅ Self Check

- [ ] I implemented all 3 systems (rules, regex, ML)
- [ ] I evaluated each one and noted strengths/weaknesses
- [ ] I trained and interpreted a model with at least 10–15 examples
- [ ] I reflected on tradeoffs and explained which system I’d use when
- [ ] Bonus: I added UI or feedback interaction

---

### 🏁 What You’ve Practiced

- Framing a problem for ML (vs logic)
- Designing features
- Training a model from scratch
- Comparing rule-based vs learning systems
- Thinking like a builder who *chooses the right tool for the job*