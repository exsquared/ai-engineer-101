
# Serving Your AI — FastAPI for Real-World Integration

---

## 🌉 Why Are We Here?

You’ve now built:

- Classical ML models
- Rule + logic pipelines
- LLM-powered classifiers, extractors, and transformers

It’s time to ship. Because a model that lives in a notebook… isn’t a product.

This chapter shows you how to turn your hybrid system — models + rules + prompts — into a real, usable API using FastAPI — with logging, fallbacks, caching, versioning, and testability.

---

## 🎯 Goal

- Serve your hybrid AI logic as a versioned web API
- Apply engineering best practices (logging, caching, error handling)
- Prepare your AI component for team use and production deployments

---

## 🧠 Why FastAPI?

FastAPI is a developer-friendly web framework for building modern Python APIs:

- ✅ Pydantic-powered request validation
- ✅ Built-in OpenAPI docs at `/docs`
- ✅ Type hints + editor autocomplete
- ✅ Async-ready for future scaling

Perfect for wrapping LLM pipelines into testable, monitorable services.

---

## 🗂️ Recommended Project Structure

```
/ai_service
├── main.py               # FastAPI entrypoint
├── api/                  # Routes and endpoints
│   └── routes.py
├── services/             # Your ML / LLM logic
│   └── triage_pipeline.py
├── schemas/              # Request/response models
│   └── models.py
├── config.py             # App settings and secrets
└── tests/                # Unit + integration tests
```

---

## ⚙️ Inference Logic (services/triage_pipeline.py)

```python
def triage_message(message):
    if not message or len(message.strip()) < 5:
        return {"action": "ignore", "reason": "Empty message"}

    if "vip" in message.lower():
        return {"action": "escalate", "reason": "VIP customer"}

    try:
        label = call_llm_classifier(message)
        urgency = extract_urgency(message)
    except Exception as e:
        return {"action": "error", "reason": str(e)}

    return {
        "action": "route",
        "category": label,
        "urgency": urgency
    }
```

---

## 🧾 Pydantic Schemas (schemas/models.py)

```python
from pydantic import BaseModel

class TriageRequest(BaseModel):
    message: str

class TriageResponse(BaseModel):
    action: str
    category: str | None = None
    urgency: str | None = None
    reason: str | None = None
```

---

## 🚀 FastAPI Endpoint (api/routes.py)

```python
from fastapi import APIRouter
from schemas.models import TriageRequest, TriageResponse
from services.triage_pipeline import triage_message
import logging

router = APIRouter()

@router.post("/v1/triage", response_model=TriageResponse)
def handle_triage(req: TriageRequest):
    logging.info(f"Input: {req.message}")
    result = triage_message(req.message)
    logging.info(f"Result: {result}")
    return result
```

---

## 📦 Entrypoint (main.py)

```python
from fastapi import FastAPI
from api.routes import router
import uvicorn
import logging

app = FastAPI()
app.include_router(router)

logging.basicConfig(level=logging.INFO)

if __name__ == "__main__":
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```

Test in your browser at: `http://localhost:8000/docs`

---

## 💡 Caching & Performance Tips

- Cache repeat queries using `functools.lru_cache` or Redis
- Cache embeddings or LLM responses to save tokens

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def get_label(message):
    return call_llm_classifier(message)
```

---

## 🛡️ Error Handling + Fallbacks

- Always catch LLM exceptions
- Return consistent schema with `action: "error"`
- Add retries using `tenacity` if needed

---

## 🔐 API Versioning

- Use route prefixes like `/v1/` and `/v2/`
- Add `/version` endpoint with logic details

---

## 📊 Logging Best Practices

- Log: request ID, model, token usage, latency
- Don’t log raw PII unless masked
- Use `logging` levels to control output

---

## ✅ Health and Metadata Endpoints

```python
@app.get("/health")
def health():
    return {"status": "ok"}

@app.get("/version")
def version():
    return {"version": "1.0.1", "model": "gpt-4", "llm_strategy": "few-shot"}
```

---

## 🔬 Unit Tests with FastAPI

```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_triage():
    res = client.post("/v1/triage", json={"message": "I was charged twice"})
    assert res.status_code == 200
    assert "category" in res.json()
```

---

## ✅ Exit Outcome

You now:

- Can serve your LLM-powered logic via FastAPI
- Understand logging, testing, versioning, and caching
- Have all the tools to turn AI from notebook into deployable product

> Next: Let’s scale this with observability, queues, and containers.
