# Serve Your Model Like an Engineer  
*Turning AI into a Real Backend Service*

---

## 🧭 Why Are We Here?

You’ve built a model. You’ve prompted it, tuned it, and tested it.

But that’s only half the battle.

In the real world, models don’t sit in notebooks. They live behind APIs, serve real users, and face real problems: timeouts, garbage input, unexpected load, and silent failures.

> This chapter is about turning your model into a **trustworthy, production-grade web service**.

And that means engineering it like you would any other backend service — but with some AI-specific twists.

---

## 🔧 What You'll Learn

By the end of this chapter, you’ll:

✅ Build a FastAPI service to wrap your model  
✅ Handle bad input with validation and error messages  
✅ Return clean, traceable responses  
✅ Think like an engineer, not a demo hacker

> This isn’t just “wrap a model in FastAPI.” This is: “make it usable, safe, and explainable.”

---

## 🏗️ The Minimal Viable Serving Layer

Let’s start with a simple example:

You have a classifier (say: spam vs not-spam). You want an API like:

```
POST /predict
{
  "message": "I love your product, give me a refund!"
}
```

And the response:

```
{
  "label": "spam",
  "confidence": 0.87
}
```

Let’s build it.

---

## 💻 Step 1: Define a Clean Schema with Pydantic

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Ticket(BaseModel):
    message: str

class Prediction(BaseModel):
    label: str
    confidence: float
```

Pydantic is FastAPI’s secret weapon — it validates, parses, and documents your input/output like an engineer’s dream.

---

## ⚙️ Step 2: Stub a Dummy Model Logic

You can replace this later with your real model or API call.

```python
def classify(text):
    if "refund" in text.lower():
        return "spam", 0.91
    return "not-spam", 0.64
```

---

## 🚀 Step 3: Build Your Prediction Endpoint

```python
@app.post("/predict", response_model=Prediction)
def predict(ticket: Ticket):
    label, confidence = classify(ticket.message)
    return Prediction(label=label, confidence=confidence)
```

Try it with `curl` or [Hoppscotch](https://hoppscotch.io):
```bash
curl -X POST http://localhost:8000/predict   -H "Content-Type: application/json"   -d '{"message": "refund please"}'
```

---

## 🧠 Let’s Engineer It Better

That basic example works — but it’s fragile.

Let’s now add the real engineering scaffolding:
- ✅ Input validation
- ✅ Error handling
- ✅ Logging
- ✅ Confidence-aware logic
- ✅ API metadata

---

## 🧱 Part 1: Input Validation

Add custom constraints:
```python
from pydantic import Field

class Ticket(BaseModel):
    message: str = Field(min_length=5, max_length=500)
```

Now FastAPI will reject bad input *before* your model even runs.

Try it:
```bash
curl -X POST http://localhost:8000/predict -d '{"message": "ok"}'
```
Returns:
```json
{"detail":[{"loc":["body","message"],"msg":"ensure this value has at least 5 characters"}]}
```

---

## 🧱 Part 2: Error Handling

Wrap model calls in try/catch and return clean errors:

```python
from fastapi import HTTPException

@app.post("/predict", response_model=Prediction)
def predict(ticket: Ticket):
    try:
        label, confidence = classify(ticket.message)
        return Prediction(label=label, confidence=confidence)
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

---

## 🧱 Part 3: Logging

Always log:

- What was asked
- What was returned
- What failed

```python
import logging
logger = logging.getLogger("uvicorn")

@app.post("/predict")
def predict(ticket: Ticket):
    logger.info(f"Predicting for: {ticket.message}")
    ...
```

Bonus:

- Structure your logs (use JSON format)
- Add request IDs for traceability

---

## 🧱 Part 4: Add Confidence-Based Output Logic

```python
@app.post("/predict")
def predict(ticket: Ticket):
    label, confidence = classify(ticket.message)

    if confidence < 0.6:
        return {
            "label": "uncertain",
            "confidence": round(confidence, 2),
            "note": "Low confidence — consider human review"
        }
```

This is where AI becomes usable. Don’t just return predictions — **wrap them in responsibility**.

---

## 📦 Microproject: Build a Real Prediction API

### Requirements:

- FastAPI with `/predict`
- Uses your real model (from Chapter 4 or 5)
- Logs every request
- Returns warning if confidence < 0.6
- Uses schema validation (e.g. message length)

### Bonus:

- Add a `/health` endpoint
- Track total requests processed
- Log to a file with rotation

---

## ❓Reflection Questions

1. What’s the difference between a working API and a production-ready one?
2. What input would break your current pipeline?
3. Where would you add versioning or metadata in the future?

---

## 🧠 Best Practices for Serving AI in Production

✅ Use structured input/output schemas (Pydantic)  
✅ Always log inputs and outputs (with request IDs)  
✅ Validate inputs at the API layer — not deep in your model logic  
✅ Avoid hardcoding thresholds — expose them as config  
✅ Never trust a model blindly — return confidence, warnings, disclaimers  
✅ Add health checks — let your API tell you when it’s sick  
✅ Plan for monitoring from day one — even if it’s just print logs for now  
✅ Prefer `POST` for inference — even if it feels like `GET`  
✅ Keep your endpoints single-purpose and stateless  
✅ Think versioning — models, prompts, API contracts

> 🛡️ If the model fails, the system shouldn’t.

---

## ✅ You Now Know:

✅ How to wrap your model behind a clean FastAPI API  
✅ How to validate, log, and handle errors like an engineer  
✅ How to think about “trustworthy outputs” with confidence logic  
✅ How to structure a serving layer for the real world

In the next chapter, we’ll make it fault-tolerant and cache-friendly. Because things will fail — and your system has to survive that.