# Final Project: AI-Powered Microservice — From Model to Production

---

## 🎯 Goal

Build a complete AI system that:

- Classifies support tickets for urgency and category
- Uses both classical models and LLM fallback
- Applies rules and thresholds intelligently
- Exposes predictions via a FastAPI service
- Includes logging, validation, and observability

> This project simulates what you'd ship in a real-world application — only cleaner and more explainable.

---

## 🔧 What You’ll Build

| Component | Description |
|----------|-------------|
| 🧠 ML Model | Logistic regression to classify urgency and category |
| 📖 LLM Fallback | Use OpenAI (or mock) when confidence is low |
| 🧪 Rule Layer | Pre-checks, threshold overrides, human-review triggers |
| 🌐 API | FastAPI service to expose `/predict` |
| 📜 Logging | Save inputs, predictions, confidence, fallback source |
| 🧰 Bonus | Add CLI, streamlit UI, or feedback loop |

---

## 🧩 Inputs and Outputs

### Input:

```json
{
  "message": "Hi, I got charged twice — please fix this ASAP!"
}
```

### Output:

```json
{
  "urgent": true,
  "category": "Billing",
  "confidence": 0.91,
  "source": "model",
  "action": "route"
}
```

---

## 📁 File Layout Suggestion

```
/ticket_ai_microservice
├── model_logic.py         # Feature extraction + prediction logic
├── llm_fallback.py        # Call OpenAI or return simulated LLM response
├── api.py                 # FastAPI wrapper
├── utils.py               # Validation, confidence checks
├── logs.jsonl             # Append input/output logs here
├── data.csv               # Your labeled sample messages
└── requirements.txt
```

---

## 🛠️ Build Checklist

### ✅ Part 1: Rule + Model Pipeline

- Load trained `LogisticRegression` models
- Extract features (e.g., word count, exclamations)
- Predict `urgent` and `category`
- Log predictions and confidence

### ✅ Part 2: Add Fallback Logic

- If `confidence < 0.5`, call `llm_fallback(prompt)`
- Ensure LLM returns structured output (e.g., via regex or mock)

### ✅ Part 3: Serve with FastAPI

```http
POST /predict
{
  "message": "My plan is not working. Please help!"
}
```

Return:

- prediction
- confidence
- source ("model" or "llm")
- action ("route", "review", or "ignore")

### ✅ Part 4: Add Logging

- Append every request/response to `logs.jsonl`
- Include confidence, token usage (if LLM), and any errors

---

## 🧪 Bonus Features

| Feature | Description |
|--------|-------------|
| 🔁 Feedback Loop | Let user submit “correct category” |
| 📉 Rate Limiting | Prevent spam or rapid requests |
| 🧑‍💼 Admin View | Review predictions with low confidence |
| 🎛️ Config File | Tune thresholds without editing code |
| 🌍 Streamlit UI | Input message + show model/LLM response live |

---

## 💭 Reflection Prompts

- What decisions were hardest to trust the model for?
- What failure types did you catch with logging?
- How would you monitor this in production?
- What would you add to make this secure?

---

## ✅ What You’ve Practiced

- Real model training (LogisticRegression)
- Confidence-aware fallback to LLMs
- API design and structured inputs
- Logging, explainability, and observability
- Blending AI with production software skills

> You didn’t just build a model. You built a **shippable, testable, explainable AI service**.

---

### 🏆 Stretch Goal

Deploy your API on Render, Railway, or Hugging Face Spaces.  
Even a demo in Streamlit counts.
