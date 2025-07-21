# Beyond Yes/No — Handling Multi-Class Predictions

## 🌟 Why Are We Here?

In the real world, most problems are **not** just about saying "yes" or "no." Imagine building a support system. It's not enough to say whether a message is urgent. You also need to **route** it: Should it go to the billing team? Tech support? Product feedback?

This chapter is about building models that make **real decisions**, not just binary ones. These are the kinds of systems you actually deploy.

---

## 🎯 Goal

To understand how models handle **more than two outcomes**, what changes in evaluation and debugging, and how to build and test a multi-class classifier — just like you’d ship in a real system.

---

## 🧠 Real-World Motivation

So far, you’ve built classifiers to answer yes/no questions:

- Is this message urgent?
- Is it spam?
- Is the feedback positive?

But in actual product workflows, you need your AI to decide among **multiple options**. For example:

### 🎯 Helpdesk Ticket Routing

| Message | Desired Routing |
|--------|------------------|
| "App crashed on checkout." | Technical Support |
| "Can I get a refund?" | Billing |
| "Love the new UI!" | Product Feedback |
| "Are you open on weekends?" | General Inquiry |

You now need a model that doesn't just say yes or no — it has to **pick the right bucket**.

---

## 🧩 What's New with Multi-Class?

You still:

- Clean your data
- Create features
- Train a model

But now:

- Labels are not binary (`0`/`1`), they are **categories**.
- The model outputs a **probability distribution** over all possible classes.
- You evaluate how well it performs for **each class individually**, not just overall.

---

## 🧪 Code Walkthrough: First Multi-Class Model

Let's take a simple dataset of support tickets and build a multi-class classifier.

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report
from sklearn.model_selection import train_test_split

# Simple dataset
data = [
    ("Refund request for last month", "Billing"),
    ("App crashes on login", "Technical"),
    ("Love the new dashboard design!", "Feedback"),
    ("What time does support close?", "Other"),
    ("My card was charged twice", "Billing"),
    ("Can't reset my password", "Technical"),
    ("UI is much smoother now", "Feedback"),
    ("Need help logging in", "Technical"),
]

# Labels and dummy features (just for structure)
y = [label for _, label in data]
X = [[1, 0], [0, 1], [1, 1], [0, 0], [1, 0], [0, 1], [1, 1], [0, 1]]

X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y)

model = LogisticRegression(multi_class='multinomial', solver='lbfgs')
model.fit(X_train, y_train)

y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
```

---

## 🧠 Let’s Visualize What’s Going On

Once you've trained your multi-class model and printed the classification report, it’s time to visualize where your model shines — and where it fumbles.

### 🔍 Confusion Matrix

```python
from sklearn.metrics import ConfusionMatrixDisplay, confusion_matrix

cm = confusion_matrix(y_test, y_pred, labels=model.classes_)
ConfusionMatrixDisplay(cm, display_labels=model.classes_).plot(cmap="Blues")
```
> This **confusion matrix** shows exactly which classes your model tends to confuse. For example, are “Billing” and “Other” frequently mixed up? This tells you what to fix next.

---

## 📈 Visualize Per-Class Precision & Recall

Let’s look at how your model performs on each class individually:

```python
from sklearn.metrics import classification_report
import pandas as pd
import matplotlib.pyplot as plt

report = classification_report(y_test, y_pred, output_dict=True)
df = pd.DataFrame(report).transpose().drop(['accuracy', 'macro avg', 'weighted avg'])

df[['precision', 'recall', 'f1-score']].plot(kind='bar', figsize=(10,6))
plt.title('How well are we doing per class?')
plt.ylim(0, 1.05)
plt.xticks(rotation=45)
plt.grid(axis='y')
plt.show()
```

> This helps you see which class is getting the best precision, which one has poor recall, and where your model might be over- or under-sensitive.

---

## 🔬 Want to Go Deeper? Try Visualizing Feature Space

If you're curious whether your inputs are even *separable*, try visualizing them in 2D using t-SNE:

```python
from sklearn.manifold import TSNE

X_proj = TSNE(n_components=2, random_state=42).fit_transform(X_test)
plt.scatter(X_proj[:, 0], X_proj[:, 1], c=[model.classes_.tolist().index(label) for label in y_test], cmap='tab10')
plt.title('t-SNE Projection of Your Feature Space')
plt.show()
```

> If “Billing” and “Technical” are overlapping blobs, your model will struggle too — it’s not magic.

---

## 🧠 Why Evaluation Gets Harder

With binary classification, your mistake is simple: right or wrong.

In multi-class, mistakes become **directional**:

- Predicting "Billing" when it was actually "Feedback" is very different than predicting "Technical" when it was "Other."

---

## 🔎 Real Debugging Tips

- **Label Overlap:** Are your classes too similar? (e.g., "Technical" vs. "Login Issues")
- **Imbalanced Data:** Do you have very few examples for some categories?
- **Generic Language:** Is the input too vague? (e.g., "It doesn’t work")

Try printing low-confidence predictions:

```python
for probs in model.predict_proba(X_test):
    print(f"Confidence: {max(probs):.2f}, Prediction: {model.classes_[probs.argmax()]}")
```

---

## 📊 Understanding Class Imbalance

Let’s say 80% of your data is "Technical." Your model may just learn to **default to that class** to optimize accuracy.

Solution:

- Use `class_weight='balanced'`
- Collect more examples for rare classes
- Downsample dominant classes

---

## 🎓 Real-World Analogy: Doctor Diagnoses

Imagine an AI diagnosing diseases:

- 80% of patients are healthy
- 15% have cold
- 5% have something rare

A model that always predicts "healthy" might look accurate — but it’s dangerous.

That’s why **per-class evaluation** and **confidence scores** matter.

---

## 🧠 Bonus: Top-K Predictions

Sometimes your model isn't confident. In those cases, showing the **top 2 guesses** can help:

```python
import numpy as np

probs = model.predict_proba(X_test)
for p in probs:
    top2 = np.argsort(p)[-2:][::-1]
    print("Top-2:", [(model.classes_[i], round(p[i], 2)) for i in top2])
```

> This is a great setup for using fallback logic or even LLMs in Chapter 7.

---

## 💡 Reflection Questions

1. Have you built or used systems with multiple possible outcomes?
2. When does it make sense to say "I’m not sure"?
3. What’s the cost of predicting the wrong class?
4. How can you improve class separation?
5. Can you think of cases where Top-2 prediction might be safer?

---

## 💻 Hands-On Project: Smart Ticket Router

Build a full pipeline:

- Create 30–40 sample messages (Billing, Tech, Feedback, Other)
- Extract simple features (keyword flags, text length)
- Train a `LogisticRegression` model
- Evaluate with confusion matrix + precision/recall
- Print confidence scores and top-2 guesses
- If max confidence < 0.5, label as "uncertain"

> Bonus: Add a basic rule like "If the message has fewer than 3 words, skip classification"

---

## 🌟 Exit Outcome

You now:

- Understand how to build and debug multi-class models
- Can evaluate performance by class
- Know how to detect ambiguity and confidence issues

Up next: We’ll combine everything you’ve learned into a **hybrid system** that mixes logic + learning — and knows when to ask for help.

Let’s keep going!
