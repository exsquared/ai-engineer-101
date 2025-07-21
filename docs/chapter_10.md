# Make Your Model Trustworthy  
*Retries, Fallbacks, and Confidence-Aware APIs*

---

## 🧭 Why Are We Here?

In the real world, models fail.
- The API times out.  
- The model returns nonsense.  
- A user inputs garbage.  
- Or worse — it silently returns something wrong, and you don’t even notice.

> This chapter is about making your AI system **robust**, **resilient**, and **trustworthy**.

Your job isn’t just to serve the model — it’s to **protect the user from it** when things go wrong.

---

## 🎯 What You'll Build

You’ll take your working FastAPI service and add:
- ✅ Retry logic for flaky upstreams
- ✅ Fallback logic for unsafe outputs
- ✅ Caching (e.g. Redis) to avoid recomputation
- ✅ Safety checks and disclaimers based on confidence

This is where your AI app becomes **production-grade**.

---

## 🧱 Mental Model: Model-as-Unreliable Collaborator

Treat your model like a junior teammate:
- It’s smart, but not always reliable  
- Sometimes slow  
- Sometimes confused  
- You must **double-check its work**

Don’t just serve it. **Wrap it. Shield it. Monitor it.**

---

## ⚠️ Let’s Start With Retries

### The Problem:
> OpenAI (or any model API) might randomly fail. Your app shouldn’t crash.

```python
import backoff

@backoff.on_exception(backoff.expo, Exception, max_tries=3)
def call_openai(prompt):
    return client.chat.completions.create(...)
```

- This retries up to 3 times  
- Exponential backoff avoids hammering the server  
- It handles flaky upstreams without breaking your user experience

### When to Retry vs Fail Fast?
- Retry on timeouts, rate limits, 5xx errors  
- Fail fast on bad input, auth errors, model misconfig

---

## 🛡️ Add a Fallback Layer

### Problem:
> The model returns a low-confidence answer, or something suspicious.

Instead of letting that through:
```python
if confidence < 0.5:
    return {
        "label": "uncertain",
        "note": "Low confidence — escalating to human review."
    }
```

Or inject your own disclaimer:
```python
answer += "\n\nNote: This answer may be incomplete. Please verify."
```

You’re not suppressing the model — you’re making it safer.

---

## ⚡ Add Caching to Avoid Recomputing

### Problem:
> Same user asks the same question 10x. You pay 10x.

Solution:
```python
import redis, hashlib

r = redis.Redis()

def cache_key(prompt):
    return "cache:" + hashlib.sha1(prompt.encode()).hexdigest()

def call_or_cache(prompt):
    key = cache_key(prompt)
    if (cached := r.get(key)):
        return cached.decode()

    result = call_openai(prompt)
    r.set(key, result, ex=3600)
    return result
```

Use caching for:
- Expensive prompts  
- High-traffic public endpoints  
- User-specific queries where replay is safe

Don’t cache:
- Prompts with time-sensitive info  
- Personalized, user-authenticated tasks

---

## 🔒 Build in Confidence-Aware Safety

If your model returns confidence scores (e.g. via softmax):
- Set thresholds for auto-response vs human escalation
- Log low-confidence answers for review

```python
if confidence > 0.8:
    action = "auto-send"
elif confidence > 0.5:
    action = "show with disclaimer"
else:
    action = "route to human"
```

This is how modern AI systems blend automation and responsibility.

---

## 📦 Microproject: Harden Your FastAPI Model Server

Add all of the following to your previous `/predict` endpoint:

✅ Retry logic on the model call (simulated or real)  
✅ Caching layer using Redis  
✅ Confidence threshold (or simulated confidence logic)  
✅ Disclaimers or fallback messages for uncertain predictions

### Bonus:
- Log fallback usage to a file  
- Add a `/stats` endpoint that tracks:  
  - requests served  
  - fallbacks triggered  
  - cache hits

---

## 🧠 Reflection Questions

1. What kinds of errors are retryable in your current pipeline?
2. Where is your system most fragile under load?
3. How would you debug a sudden spike in fallback rate?

---

## 🧠 Best Practices for Trustworthy AI APIs

✅ Don’t expose raw model outputs — add structure and logic  
✅ Never trust one response — retry, rephrase, fallback  
✅ Use confidence scores to gate automation  
✅ Always cache expensive or repeated calls  
✅ Distinguish between user-safe failures (warn) and system-critical ones (raise)  
✅ Monitor and alert on fallback and retry rates  
✅ Build in human override paths — even if simulated

> 🛡️ When in doubt, fail safe. Not silent.

---

## ✅ You Now Know:

✅ How to make your model robust against real-world failures  
✅ How to retry and fallback gracefully  
✅ How to use caching and confidence to reduce cost and increase safety  
✅ How to build AI systems that **deserve user trust**