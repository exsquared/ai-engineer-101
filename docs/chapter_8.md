
# Chapter 8: Serving Your Model — Building an API with FastAPI

---

## 🎯 Goal

To wrap your trained model into a **real, callable service** using FastAPI — so other apps, teammates, or products can use it like any other backend system.

---

### 🤔 Why This Matters

You’ve built a model. You’ve written logic around it.  
Now it’s time to make it useful — *outside your notebook*.

> In production, ML isn’t just about accuracy.  
> It’s about **access** — letting others call your model safely, reliably, and repeatedly.

That’s where **APIs** come in.

---

### 🧰 What You’ll Learn

- Basics of FastAPI (Python framework)
- How to wrap a model in a REST endpoint
- Input validation and response design
- Returning predictions and confidence scores
- Logging inputs and outputs

---

### 🧠 What Is FastAPI?

If you’ve used Flask or Django, this will feel familiar.

FastAPI is a lightweight Python web framework that makes it **fast to build APIs**, with:

- Built-in validation using type hints
- Easy JSON handling
- Auto-generated docs (`/docs`)

You’ll use it to expose your model to the outside world — like:

```http
POST /predict
{
  "message": "HELP! I got charged twice"
}
```

---

### 🚀 Your First Model API

Let’s wrap the classifier you built in Project 2.

#### Step 1: Install FastAPI

```bash
pip install fastapi uvicorn
```

#### Step 2: Build the service (`main.py`)

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Load your models and logic here
# from model_logic import triage_ticket

class TicketRequest(BaseModel):
    message: str

@app.post("/predict")
def predict_ticket(req: TicketRequest):
    result = triage_ticket(req.message)
    return result
```

> `triage_ticket()` is the logic you built in Chapter 7 — rule + model + LLM fallback.

#### Step 3: Run your API

```bash
uvicorn main:app --reload
```

Visit: [http://localhost:8000/docs](http://localhost:8000/docs) for auto-generated Swagger UI 🚀

---

### 📦 Sample Output

```json
{
  "urgent": true,
  "category": "Billing",
  "confidence": 0.82,
  "action": "route"
}
```

---

### 🛡️ Add Logging

Wrap your function to track input/output:

```python
import logging

@app.post("/predict")
def predict_ticket(req: TicketRequest):
    logging.info(f"Input: {req.message}")
    result = triage_ticket(req.message)
    logging.info(f"Output: {result}")
    return result
```

> Optional: log to a file or DB, and log confidence scores separately

---

### 🧪 Exercise: Build and Test Your Own Service

1. Load your model and logic
2. Create a FastAPI app
3. Add one `/predict` endpoint
4. Use a tool like Postman or curl to test it
5. Bonus: add `/health` and `/version` endpoints

---

### 💭 Reflection

- How does exposing an API change how you think about error handling?
- What’s the right amount of detail to return from a model?
- What would you log in production (inputs, outputs, confidence, user ID)?

---

### ✅ Exit Outcome

You now know how to:

- Wrap ML logic into an API
- Use FastAPI to receive structured input and return predictions
- Add logging and testability for real-world use

> This is the first step to **deployable AI** — not just code that works, but code that serves.

---

### 🚀 Going Further (Optional)

If you’re curious about **model tracking**, **versioning**, and **collaboration**, here are some tools worth exploring after this chapter:

| Tool | What It Does |
|------|---------------|
| MLflow | Track experiments, store models, compare results |
| Weights & Biases | Visualize training, track hyperparams & metrics |
| Hugging Face Hub | Upload and reuse pretrained models from others |
| BentoML / Seldon | Model packaging + deployment frameworks |

These tools become more useful as your models grow in complexity — and as your team needs to collaborate and ship responsibly.

---

### ⏭️ Coming Up

Now that your model is callable, we’ll explore **how things go wrong in production** — and how to build safe, observable AI with logging, alerts, and human-in-the-loop.
