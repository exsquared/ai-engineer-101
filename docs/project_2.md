# ✅ Checkpoint Project #2: AI-Powered Auto-Responder

---

## 🧠 Why This Project?

After learning:

- How to classify support messages (multi-class)
- How to interpret model confidence
- How to use thresholds and fallback logic

…it’s time to build a real-world decision system that blends AI prediction with human review.

This project is a lightweight simulation of how AI systems operate in high-stakes environments — with automation, thresholds, and fallbacks.

---

## 🎯 Project Goal

Build a simple system that:

1. Classifies incoming support messages into categories:
    - Billing
    - Technical
    - General Inquiry
    - Cancel/Unsubscribe
    - Other

2. Applies a confidence threshold:
    - If confidence > 0.7: send auto-response using a predefined template
    - Else: flag for human review

---

## 🧱 System Design Overview

Your system will:

- Take raw text input (support ticket)
- Return:
  - `category`
  - `confidence`
  - `action` → “auto-response” or “human-review”
  - (Optional) Suggested response if confident

---

## 🗃️ Dataset

Create a dataset of ~50–60 support messages manually labeled.

| Message | Category |
|--------|----------|
| “I want a refund on my last invoice” | Billing |
| “The app crashes when I try to sync” | Technical |
| “What are your office hours?” | General Inquiry |
| “Please cancel my subscription immediately” | Cancel |
| “Hi, just saying thanks!” | Other |

Include:

- Easy and hard-to-classify messages
- Polite, angry, vague, and edge-case phrasing

---

## 🔨 Implementation Checklist

### Step 1: Feature Extraction

Use:

- Word count
- Exclamation/question marks
- Keyword presence (e.g., “refund”, “crash”, “unsubscribe”)
- TF-IDF or CountVectorizer (recommended)

---

### Step 2: Train Multi-Class Classifier

Use logistic regression, random forest, or any scikit-learn classifier.

```python
from sklearn.linear_model import LogisticRegression
from sklearn.feature_extraction.text import TfidfVectorizer

# Train model
model = LogisticRegression(multi_class='multinomial')
model.fit(X_train, y_train)
```

Evaluate using:

- `classification_report()`
- `predict_proba()`

---

### Step 3: Build Confidence-Based Decision Logic

```python
def auto_responder(text):
    x = vectorizer.transform([text])
    proba = model.predict_proba(x)[0]
    top_category = model.classes_[proba.argmax()]
    confidence = proba.max()

    if confidence > 0.7:
        return {
            "category": top_category,
            "confidence": round(confidence, 2),
            "action": "auto-response",
            "response": lookup_template(top_category)
        }
    else:
        return {
            "category": top_category,
            "confidence": round(confidence, 2),
            "action": "human-review"
        }
```

---

## 🧪 Bonus Features

- Return second-best category if confidence is low
- Let user simulate adjusting the confidence threshold
- Track and log predictions over time
- Visualize confusion matrix using Seaborn

---

## 💻 Optional: Add a CLI

```python
while True:
    msg = input("Paste support message (or 'q' to quit): ")
    if msg == 'q':
        break
    print(auto_responder(msg))
```

---

## 📋 What to Submit

- Python script or notebook
- A CSV of your labeled dataset
- Output from 5–10 test inputs
- A 1–2 paragraph reflection:
  - What caused misclassifications?
  - Did the threshold help reduce bad predictions?
  - How would you improve it for production?

---

## ✅ What You’ve Practiced

- Multi-class classification with real decisions
- Confidence scoring and thresholds
- Designing automation vs fallback flows
- Thinking like a product-minded ML engineer