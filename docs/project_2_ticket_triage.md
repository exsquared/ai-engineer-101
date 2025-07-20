# ✅ Checkpoint Project #2: Smart Ticket Triage System

---

## 🧠 Why This Project?

You’ve now learned:

- How to train a binary classifier (urgent vs not urgent)
- How to go beyond accuracy (precision, recall, threshold tuning)
- How to handle multi-class outputs (ticket categories)
- How to debug model confidence and confusion

Now it’s time to build a complete mini-system that brings all of this together.

> This project simulates what you’d actually ship in a real product:  
> a lightweight, testable ML system for automatically prioritizing and routing support tickets.

---

## 🎯 Project Goal

Build a working system that:

1. Determines if a support ticket is **urgent**
2. Classifies it into a category (like **Billing**, **Tech**, **Feedback**, or **Other**)
3. Applies confidence thresholds to decide whether to:
    - Auto-route it to the right team
    - Escalate low-confidence tickets to human review

---

## 🧱 System Design Overview

Your system will:

- Take a raw support ticket (just a text string)
- Return:
  - `urgency` → Yes / No
  - `category` → Billing / Tech / Feedback / Other
  - `confidence` → Percentage
  - `action` → Auto-route / Human review

---

## 🗃️ Dataset (You Build It)

Create a small dataset of ~40–60 support tickets with two labels:

| Message | Urgent? | Category |
|--------|---------|----------|
| “HELP — I got double charged!!!” | 1 | Billing |
| “Just wanted to say the UI is much better now” | 0 | Feedback |
| “App crashes when I press ‘Sync’” | 1 | Tech |
| “What time is your office open?” | 0 | Other |
| ... | ... | ... |

Make sure to include:

- Urgent & non-urgent tickets in each category
- Calm, angry, polite, and vague phrasings
- A few hard-to-classify edge cases

---

## 🔨 Implementation Checklist

### Step 1: Feature Extraction

Extract simple features from text:

- Message length
- Word count
- Number of exclamation marks
- Keyword flags (e.g., “refund”, “broken”, “love”, “asap”)
- TF-IDF (optional for bonus NLP version)

---

### Step 2: Train Binary Classifier (Urgent?)

Train a `LogisticRegression` model to predict urgency:

```python
urgent_model = LogisticRegression()
urgent_model.fit(X_train, y_urgent)
```

Use:

- `predict_proba()` to get urgency confidence
- Threshold tuning (`0.3`, `0.5`, etc.) to decide final call

---

### Step 3: Train Multi-Class Classifier (Category)

Train a second `LogisticRegression(multi_class='multinomial')` model to predict ticket category:

```python
category_model = LogisticRegression(multi_class='multinomial', solver='lbfgs')
category_model.fit(X_train, y_category)
```

Evaluate with:

- `classification_report()`
- `confusion_matrix()`

---

### Step 4: Decision Logic Layer

Build a function like:

```python
def triage_ticket(text):
    features = extract_features(text)

    urgency_proba = urgent_model.predict_proba([features])[0][1]
    category_proba = category_model.predict_proba([features])[0]

    is_urgent = urgency_proba > 0.4
    top_category = category_model.classes_[category_proba.argmax()]
    confidence = category_proba.max()

    action = "auto-route" if confidence > 0.6 else "human-review"

    return {
        "urgent": is_urgent,
        "category": top_category,
        "confidence": round(confidence, 2),
        "action": action
    }
```

---

### Step 5: Build a Simple CLI

```python
while True:
    msg = input("Paste support ticket (or 'q' to quit): ")
    if msg == 'q':
        break
    result = triage_ticket(msg)
    print(result)
```

---

## 🧪 Bonus Features

- Add second-best category if confidence is low
- Let users give feedback (“correct category was ___”)
- Store predictions and allow re-training
- Export logs of all predictions and actions
- Add a Streamlit UI (optional)

---

## 📋 What to Submit

- Python script or notebook
- Sample outputs from 5 test messages
- Screenshots or text output from CLI
- 1–2 paragraph reflection:
  - What worked well?
  - What confused your model?
  - What might help improve it in a real system?

---

## ✅ What You’ve Practiced

- Designing a real-world ML flow with both binary and multi-class models
- Feature engineering and data labeling
- Confidence thresholds and decision logic
- Creating developer-friendly AI tools
- Thinking through tradeoffs between automation vs human-in-the-loop
