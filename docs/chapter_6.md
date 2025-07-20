# Chapter 6: Beyond Yes/No — Handling Multi-Class Predictions

---

## 🎯 Goal

To understand how models handle **more than two outcomes**, what changes in evaluation and debugging, and how to build and test a multi-class classifier — just like you’d ship in a real system.

---

### 🧠 Real-World Motivation

So far, you’ve predicted things like:

- Is this urgent or not?  
- Yes or no?  
- Spam or ham?

But many real-world problems aren’t binary.

Let’s take support tickets again. This time, you want to classify tickets into:

- `Billing`
- `Technical Support`
- `Product Feedback`
- `Other`

This is no longer a yes/no — it’s a **multi-class** decision.

---

### 🧩 What Changes?

You’re still training a model.
You still have features and labels.

But now:

- The label is not 0 or 1 — it’s one of several classes.
- The model has to **pick the best class** for each input.
- Evaluation gets trickier.

---

### 🧪 Code Walkthrough — First Multi-Class Model

Let’s reuse what we already know — with a twist.

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report
from sklearn.model_selection import train_test_split

# Example dataset
data = [
    ("Refund request for last month", "Billing"),
    ("App crashes on login", "Technical"),
    ("Love the new dashboard design!", "Feedback"),
    ("What time does support close?", "Other"),
]

# Convert to features (you can add NLP later)
# Example: use a simple vectorizer or dummy values
# X = ... 
y = [label for _, label in data]

# Placeholder for actual vectorization
X = [[1, 0], [0, 1], [1, 1], [0, 0]]  # Dummy features

# Train/test split
X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y)

model = LogisticRegression(multi_class='multinomial', solver='lbfgs')
model.fit(X_train, y_train)

y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
```

---

### 🧠 Debugging Multi-Class Is Different

You can’t just fix a threshold. You need to:

- **Look at the confusion matrix**
- **Identify which classes are overlapping**
- **Improve your features or data balance**

---

### 📊 Visual Debug: Confusion Matrix

```python
from sklearn.metrics import ConfusionMatrixDisplay, confusion_matrix

cm = confusion_matrix(y_test, y_pred, labels=model.classes_)
ConfusionMatrixDisplay(cm, display_labels=model.classes_).plot()
```

---

### 💡 Class Imbalance Matters More Now

If only 5% of tickets are “Feedback”, the model might:

- Predict “Billing” 70% of the time — and still look “accurate”
- Ignore rare classes

> Tip: Use `class_weight='balanced'` in `LogisticRegression` to help counter imbalance

---

### 💭 Reflection Questions

1. Have you shipped features with more than 2 possible outcomes?
2. What happens when one class is rare — but important?
3. Would you prefer your model to say “unsure” rather than guess incorrectly?

---

### 💻 Exercise: Your First Multi-Class Classifier

Build your own ticket classifier:

1. Create 20–30 example messages with 3–4 categories (Billing, Tech, Feedback, Other)
2. Extract features (start simple: keyword flags, message length)
3. Train a LogisticRegression model with `multi_class='multinomial'`
4. Print the classification report
5. Plot the confusion matrix

---

### 🏁 Small Project: Multi-Class Ticket Router

Use what you’ve learned to build a smart routing engine.

#### Scenario:

You’re automating ticket routing for a helpdesk. Tickets need to go to the right team:

- `Billing`
- `Tech`
- `Feedback`
- `Other`

#### Build:

- A labeled dataset (30–40 examples)
- Feature extractor (keywords, patterns, length)
- Multinomial model
- Evaluation: precision/recall + confusion matrix
- CLI: paste in a message → see predicted department

> Bonus: Show top-2 predictions if the model is unsure

---

### ✅ Exit Outcome

You now know:

- How to build a multi-class model
- How to evaluate and debug per class
- How to deploy a real-world routing tool

---

### ⏭️ Coming Up

Next, we explore how to **blend rules and learning** into hybrid systems — because sometimes the best solution isn’t pure AI or pure logic, but a smart mix of both.
