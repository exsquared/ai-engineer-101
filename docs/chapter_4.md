# When to Trust the Model

*How smart systems know their limits — and act accordingly*
---

## 🎯 Why This Chapter Exists

In Chapter 3, you trained a model and saw how it drew a decision boundary — a line between “yes” and “no.”

But not all decisions are clear.

Some inputs land close to that boundary.
> What happens when the model isn’t confident?

This chapter is about designing systems that can say:
> “I think it’s spam… but I’m only 60% sure.”

And then act accordingly.

---

## 🧠 What Is Model Confidence?

When you call `.predict_proba()` on a model like logistic regression or Naive Bayes, it gives you a **probability**.

```python
model.predict_proba([[4.2, 1]])
# Output: [0.13, 0.87] → 87% confidence in class 1
```

That number isn’t magic. It’s just how far the point is from the decision boundary.

- Far from the line = confident
- Close to the line = uncertain

This is critical for real systems.

---

## 📉 Visualizing Confidence Zones

Imagine your decision boundary as a line:
```
← 0% confident ———|——— 100% confident →
             boundary
```
Everything near the middle (45–55%) is **dangerous territory**.
That’s where misclassifications are most likely.

---

## 💡 What Should a Smart System Do?

> 🤔 If the model is 51% confident, should it act like it’s 100% sure?

Of course not.

Here’s a better plan:
```python
if confidence > 0.85:
    auto_accept()
elif confidence < 0.6:
    flag_for_review()
else:
    fall_back_to_rules()
```

You’re designing not just a model — but a system that **knows its own limits**.

---

## 🔄 Threshold Tuning — A Developer’s Superpower

Most models default to a 50% cutoff:
```python
if prob > 0.5: predict class 1
```
But you can shift that line to make the system more conservative or aggressive.

### Example:

```python
y_scores = model.predict_proba(X_test)[:, 1]  # probability for class 1

y_custom = (y_scores > 0.7).astype(int)  # only predict 1 if very confident
```

Tuning this threshold changes everything:

- Higher threshold → fewer false positives, but more misses
- Lower threshold → more flags, but more risk

---

## 🧪 Mini-Experiment: Try Threshold Tuning

Train a model using your support ticket data. Then try these:
```python
from sklearn.metrics import classification_report

for t in [0.3, 0.5, 0.7]:
    y_custom = (y_scores > t).astype(int)
    print(f"\nThreshold = {t}")
    print(classification_report(y_test, y_custom))
```
You’ll quickly see how your model behaves under different confidence strategies.

---

## 🧠 Quick Intro: What Is Naive Bayes?

Before we dive into the next classifier, here's a quick intro:

**Naive Bayes** is a fast and effective algorithm for classifying text. It works by calculating how often certain words appear in each category — then makes a guess based on probability.

It’s called “naive” because it assumes each word contributes independently to the outcome — even though that’s not always true.

Despite the name, it works surprisingly well for things like spam detection or support ticket classification.

🧠 Want to dig into how it works behind the scenes? We’ve got a clear explanation in the [Fun ML Toolbox](#).

---

## 🎮 Real-World NLP Classifier with Confidence

Let’s apply this to a real NLP example using Naive Bayes.

```python
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.naive_bayes import MultinomialNB

# Sample training data
tickets = ["help ASAP!", "urgent issue", "payment failed", "slow loading"]
labels = ["urgent", "urgent", "non-urgent", "non-urgent"]

vectorizer = CountVectorizer()
X_train = vectorizer.fit_transform(tickets)

model = MultinomialNB().fit(X_train, labels)

# Predict with confidence
new_ticket = vectorizer.transform(["can't login, help now"])
prediction = model.predict(new_ticket)
prediction_proba = model.predict_proba(new_ticket)

print("Prediction:", prediction)
print("Confidence scores:", prediction_proba)
```

🎉 You’ve now used `.predict_proba()` on real text. This is how modern AI systems decide when to be bold — and when to back off.

---

## ⚙️ Confidence in Production

Here’s how real teams use confidence scores:

| Confidence | Action                            |
|------------|-----------------------------------|
| > 0.9      | Auto-process, log result          |
| 0.6–0.9    | Flag for human review             |
| < 0.6      | Don’t act — trigger fallback rule |

This is **how mature AI systems operate** — not just “guess and go,” but “guess, evaluate, then act.”

---

## 🔥 Real-World Case: SmartAssist Co.

SmartAssist had an urgency classifier.

- If the model was 95%+ confident, it auto-routed tickets
- If below 60%, it flagged the ticket and asked a support agent

That simple confidence logic reduced routing errors by 70%.

---

## 📚 Optional Deep Dive: “How Does It Know That?”

Feeling curious?

Ever wondered:

- How does `.predict_proba()` calculate that number?
- Why does logistic regression give smooth scores?
- How does Naive Bayes combine probabilities?

Then take a detour to the next chapter:

### 👉 [🛠️ The Fun ML Toolbox](fun_ml_toolbox.md)

> Get clear, visual explanations of vectorization, logistic regression, Naive Bayes, and more.

Not essential — but very helpful if you want to peek under the hood.

---

## 🧠 Reflection Corner

- When should a model’s output be trusted without question?
- Where in your own product could confidence thresholds improve performance?
- Can you recall a time a “maybe” prediction should’ve been stopped or escalated?

---

## 📌 Quick Summary

- `.predict_proba()` shows how confident the model is
- Confidence ≠ correctness, but it helps design safer systems
- Use thresholds and fallbacks to handle low-certainty predictions
- Great AI systems don’t just predict — they **know when they’re unsure**

Up next: If you're curious how models calculate confidence, jump into the **Fun ML Toolbox**.
Otherwise, we’ll move on to evaluating *how well* your model is performing — beyond just being “accurate.”