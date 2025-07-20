# Chapter 5: Evaluation Beyond Accuracy — How to Know If a Model Is *Actually* Working

---

## 🎯 Goal

To learn how to **evaluate models intelligently** — beyond just "how many did it get right?"  
We’ll explore *why accuracy can lie*, and how to use **precision**, **recall**, and **false positive/negative rates** to debug and improve your model like an engineer — not just a data scientist.

---

### ⚖️ The Accuracy Trap

Let’s say you built a model that predicts whether a support ticket is **urgent**.

Your model says:
> “I’m 95% accurate!”

Sounds great, right?

But here’s the catch:

- Only **5%** of your tickets are actually urgent.
- Your model is just saying **“not urgent” every time** — and still getting 95% right.

That’s not intelligence — that’s cheating.

> **High accuracy doesn’t mean your model is useful.**

---

### 🔍 The Real Questions

What you *really* care about is:

- 🧨 **Did it miss any urgent tickets?** (That’s bad.)
- 🚨 **Did it raise too many false alarms?** (Also bad.)
- 🎯 **Did it correctly flag the ones we needed to act on?** (That’s the sweet spot.)

---

### 🧠 Think Like a Dev, Not a Statistician

Let’s bring this into your world.

```python
def is_ticket_urgent(text):
    return model.predict(text)  # Returns True or False
```

If your model messes up:

- A *true urgent* ticket goes unnoticed (bad customer experience).
- A *non-urgent* ticket clogs the queue (wasted time).

So instead of “accuracy”, let’s ask:

| Type             | What it means                            | Impact                                |
|------------------|------------------------------------------|----------------------------------------|
| ✅ True Positive | Correctly marked urgent                  | Good — success                         |
| ❌ False Positive| Said urgent, but wasn’t                  | Bad — wasted priority                  |
| ❌ False Negative| Missed an actual urgent message          | Worse — customer suffers               |
| ✅ True Negative | Correctly marked not urgent              | Good — low priority handled right      |

---

### 🏷️ Precision and Recall — In Plain English

#### 🎯 Precision = “Of all the things I said were urgent, how many actually were?”

- High precision = You don’t cry wolf
- Low precision = You flag lots of things that aren’t urgent

#### 🕵️ Recall = “Of all the things that *were* urgent, how many did I actually find?”

- High recall = You catch most real urgent cases
- Low recall = You miss lots of real issues

---

### ⚖️ The Tradeoff

Think of this like a **spam filter**:

> You can tweak your model to be more cautious or more aggressive — but you can’t max both.

---

### 🧪 Let's Code It

```python
from sklearn.metrics import classification_report

y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
```

Sample output:

```
              precision    recall  f1-score   support

    NotUrgent       0.96      0.98      0.97       80
       Urgent       0.85      0.75      0.80       20
```

---

### 🧩 Exercise: Tuning the Threshold

```python
y_scores = model.predict_proba(X_test)[:, 1]  # Probabilities for 'urgent'

threshold = 0.3  # Be more aggressive
y_custom = (y_scores > threshold).astype(int)

print(classification_report(y_test, y_custom))
```

Try thresholds: `0.3`, `0.5`, `0.7`.

---

### 🔄 When to Use What

| You care about...        | Focus on       |
|--------------------------|----------------|
| Avoiding false alarms    | Precision       |
| Not missing real cases   | Recall          |
| Balanced performance     | F1 Score        |

---

### 💭 Reflection Questions

1. When is high accuracy *not* useful?
2. Can you think of a product where false positives are dangerous?
3. What’s more painful in your system: missing an error, or flagging too many?
4. How would you explain precision/recall to a PM?

---

### 💻 Hands-On Exercise

Extend your earlier project (checkpoint #1):

1. Split your message dataset into train/test
2. Train your `LogisticRegression` model
3. Use `.predict_proba()` to extract confidence scores
4. Try different thresholds (`0.3`, `0.5`, `0.7`)
5. Print precision/recall/F1 at each threshold

> Bonus: Add a user-facing confidence score — and route low-confidence tickets to human review.

---

### ✅ Exit Outcome

- Go beyond “did it get it right?”
- Measure *how well* your model is doing in the ways that matter
- Debug performance using confusion matrices and probability scores

---

### ⏭️ Up Next

In the next chapter, we’ll shift from binary to **multi-class** predictions — and what changes when you have more than two possible answers.
